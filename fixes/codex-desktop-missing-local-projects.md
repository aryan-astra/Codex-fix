# Restore missing local projects in Codex Desktop on Windows

Your project folders still open in File Explorer, but their groups have disappeared from the Codex sidebar and their entries are missing from the Projects page. This guide covers one cause of that symptom: the desktop app lost the live copy of its project cache while the underlying project records survived.

**Close Codex → back up its data → confirm the records and snapshot agree → restore five cache keys → reopen Codex**

## Does this match your case?

This procedure comes from a recovery on Windows with Codex Desktop build `26.1103.4239.0`. Apply it only when all of the following are true.

- Your project folders still exist and their drives are mounted.
- `state_5.sqlite` passes an integrity check and contains the expected local projects and threads. If you already have app-server diagnostics, a successful `project/list` response with the expected projects is additional evidence.
- The five live cache keys listed below are missing or empty. If any contain current data, stop before overwriting it.
- An inactive renderer snapshot for your own account and Codex data directory contains a complete, matching project cache. If several snapshots could be the right one, stop until you can identify the correct snapshot.
- The live migration record for your local host has `threadAssignmentsMigrated` set to `false` and no `threadAssignmentsReadMigrated` key. Different migration state, multiple host mappings, missing project rows, or a changed schema need a separate diagnosis.

### Where the records live

| Layer | Role in this build |
| --- | --- |
| `state_5.sqlite` | Stores the durable project and thread records. |
| Local app-server | Serves the project inventory through `project/list`. |
| `.codex-global-state.json` | Gives the renderer its project list, order, ID mappings, and chat groups. The file may also contain older account snapshots. |

The cache and SQLite use different project IDs. The host-scoped mapping connects them. In the observed build, the app can import a cached project when its legacy ID lacks a mapping, so recreating IDs can introduce duplicates. Chat grouping also came from the renderer in this case. With `localProjectTaskMembership` disabled, `threads.project_id` could be `NULL` even for chats displayed under a project. The repair preserves migration settings and leaves SQLite open in read-only mode for diagnosis.

## Fix

### 1. Close Codex and back up its data

Save your work and exit Codex completely. In a separate PowerShell window, inspect the remaining processes.

```powershell
Get-CimInstance Win32_Process |
  Where-Object { $_.Name -match 'ChatGPT|codex' } |
  Select-Object ProcessId, ParentProcessId, Name, ExecutablePath
```

If any Codex Desktop helpers or its local app-server remain, identify them by path and parent process before ending those specific processes. Leave other apps, CLI sessions, and the separate Codex Windows sandbox service alone. Keep Codex closed until the repair is complete.

Run the backup in PowerShell. If your desktop installation uses a custom data directory, set `$data` to that directory before proceeding. The backup contains private app data, so keep it outside the repository in a private location.

```powershell
$data = Join-Path $env:USERPROFILE '.codex'
$backup = Join-Path $env:LOCALAPPDATA ('CodexBackups\pre-repair-' + (Get-Date -Format 'yyyyMMdd-HHmmss'))

if (!(Test-Path -LiteralPath (Join-Path $data '.codex-global-state.json') -PathType Leaf) -or
    !(Test-Path -LiteralPath (Join-Path $data 'state_5.sqlite') -PathType Leaf) -or
    (Test-Path -LiteralPath $backup)) {
    throw 'Check the Codex data path and choose a fresh backup directory.'
}

robocopy $data $backup /E /COPY:DAT /DCOPY:DAT /R:1 /W:1
if ($LASTEXITCODE -ne 1) { throw 'Review the copy summary before proceeding.' }

foreach ($file in '.codex-global-state.json', 'state_5.sqlite', 'logs_2.sqlite', 'sqlite\codex.db') {
    $source = Join-Path $data $file
    if (Test-Path -LiteralPath $source -PathType Leaf) {
        $original = Get-FileHash -LiteralPath $source -Algorithm SHA256 -ErrorAction Stop
        $copy = Get-FileHash -LiteralPath (Join-Path $backup $file) -Algorithm SHA256 -ErrorAction Stop
        if ($original.Hash -ne $copy.Hash) { throw "Backup hash mismatch for $file" }
    }
}
$backup
```

Review the Robocopy summary for failed or skipped data and keep the printed backup path. If two tools report different file counts, compare relative paths with a tool that can read long Windows paths. A recursive PowerShell count missed deeply nested files in the original recovery.

### 2. Check SQLite and identify the matching snapshot

With a local SQLite CLI, open the database explicitly read-only. Run the same queries later to check the durable records after restart. Keep the output private.

```powershell
sqlite3 -readonly (Join-Path $data 'state_5.sqlite')
```

```sql
PRAGMA integrity_check;
PRAGMA table_info(projects);
SELECT COUNT(*) FROM projects;
SELECT id, name FROM projects ORDER BY position;
SELECT COUNT(*) FROM threads;
SELECT id FROM threads;
```

Expect `ok` from the integrity check and confirm the project list matches your own inventory. Use the schema and project rows to check that each stored root points to an existing directory. Use the thread IDs to validate the snapshot's assignments. Exit SQLite with `.quit`.

Open the backed-up `.codex-global-state.json` in a local JSON editor. Locate the snapshot under `electron-account-renderer-state-v1` → `inactive` → the matching account entry → `globals`. The account entry name and host key come from your own file. A typical host key appears as `"local:C:\\Users\\<user>\\.codex"` in raw JSON, where doubled backslashes represent literal backslashes in the decoded path.

| Cache key | What to check before copying |
| --- | --- |
| `local-projects` | Each entry describes an expected project and its embedded legacy ID matches its object key. |
| `project-order` | Every legacy ID in `local-projects` appears exactly once. |
| `app-server-project-id-by-legacy-project-id-by-host` | The single host key matches your Codex data directory and live migration record. Its mapping covers every legacy ID exactly once and maps one-to-one onto the project IDs in SQLite. |
| `thread-project-assignments` | Each entry you keep has `projectKind` set to `"local"`, names a legacy `projectId` from `local-projects`, and has a thread ID present in SQLite. |
| `thread-project-membership-host-ids` | Each assignment you keep has a matching entry for the same thread ID with the value `"local"`. The retained thread IDs match in both objects. |

If you can account for a confirmed deleted thread, omit it from **both** assignment objects. In the original recovery, one snapshot entry pointed to a deleted thread. If missing threads are unexpected, stop and investigate. Also stop if a retained assignment conflicts with the live `projectless-thread-ids` list.

### 3. Stage and install the five-key repair

1. With Codex still closed, make a copy of the live `.codex-global-state.json` next to it and name the copy `.codex-global-state.repair.json`.
2. In that copy, set the five **top-level** keys to the values you checked in the matching snapshot. Replace any empty value under an existing key in place so each top-level key occurs only once. Apply the same deleted-thread exclusion to both assignment objects.
3. Save as UTF-8 and check the JSON with your editor. Compare the staged file with the live file. The only differences should be those five top-level values. Preserve the nested snapshot, migration record, `projectless-thread-ids`, and all other state.
4. In the same PowerShell session as the backup, install the staged copy with a same-directory file replacement. This example uses Python 3 for a syntax check before replacing the file. Review the staged values separately since valid JSON can still contain incorrect IDs.

```powershell
$state = Join-Path $data '.codex-global-state.json'
$staged = Join-Path $data '.codex-global-state.repair.json'
$before = Join-Path $backup '.codex-global-state.json'
$previous = Join-Path $data '.codex-global-state.before-replace.json'

if (!(Get-Command python -ErrorAction SilentlyContinue)) { throw 'Python 3 is required for this syntax check.' }
if (Test-Path -LiteralPath $previous) { throw 'Choose a fresh replacement backup path.' }
python -m json.tool $staged > $null
if ($LASTEXITCODE -ne 0) { throw 'The staged file did not pass JSON validation.' }

$liveHash = Get-FileHash -LiteralPath $state -Algorithm SHA256 -ErrorAction Stop
$backupHash = Get-FileHash -LiteralPath $before -Algorithm SHA256 -ErrorAction Stop
if ($liveHash.Hash -ne $backupHash.Hash) {
    throw 'The live state changed after backup. Stop and recheck the app and snapshot.'
}

[System.IO.File]::Replace($staged, $state, $previous)
```

## Verify

1. Launch Codex from the Start menu. Check that the expected project groups and chats appear in the sidebar.
2. Open **Projects** and search for a project outside the initial viewport. The page can sort by **Updated**, while the sidebar follows the saved project order.
3. Open a known chat from one of the restored groups. If you have app-server diagnostics, confirm `project/list` returns the expected inventory without duplicates.
4. After Codex has saved its state, check that the five live keys still contain the expected mappings and assignments. If you recheck SQLite, use read-only access and compare the inventory with your baseline. Keep the backup until normal use confirms the recovery.

The documented recovery restored nine projects and fourteen valid chat associations without database writes. Seven Projects-page entries were visible in the captured viewport. The two below it were confirmed through SQLite, the app-server, and renderer state. A representative project chat opened successfully.

## Rollback

Close Codex and its helpers again. In the same PowerShell session, stage a fresh copy of the backed-up `.codex-global-state.json` in the live directory. The replacement saves the current JSON beside the live file for diagnosis.

```powershell
$rollback = Join-Path $data '.codex-global-state.rollback.json'
$after = Join-Path $data '.codex-global-state.before-rollback.json'
if ((Test-Path -LiteralPath $rollback) -or (Test-Path -LiteralPath $after)) {
    throw 'Choose fresh rollback staging and backup paths.'
}
Copy-Item -LiteralPath $before -Destination $rollback -ErrorAction Stop
[System.IO.File]::Replace($rollback, $state, $after)
```

This returns the renderer state to the backup point, including any unrelated preferences saved in that JSON. Restart Codex. If the project or thread inventory also changed after launch, preserve both copies of the data before attempting any broader recovery.
