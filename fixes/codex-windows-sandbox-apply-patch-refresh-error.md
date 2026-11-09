# Diagnose a Windows sandbox refresh failure before apply_patch runs

## Symptoms

Codex reports `windows sandbox: setup refresh failed` when it tries to edit a file in the workspace. PowerShell outside Codex may still read and write the same checkout.

## Diagnosis

1. Record the app or CLI version, active Windows sandbox implementation, exact error, and whether the target is inside the workspace.
2. Check whether Codex can read the same file and run a simple command in that workspace. If a patch reports a context mismatch after reaching the file, refresh the patch against the current contents.
3. Compare the failure in a disposable checkout on a local NTFS path. A failure in only one checkout is useful evidence when checking inherited permissions. A failure in both places points toward sandbox startup or the installed build.

## Fix

1. Restart Codex and retry with the current supported app or CLI update.
2. For a repository-specific failure, test a fresh linked checkout under a directory with the intended inherited permissions. Preserve the original checkout and transfer uncommitted changes only after checking the new location. Ask the system owner to review any ACL repair required on the original tree.
3. If the same refresh error persists, use the documented Windows sandbox diagnostics and check compatibility for your selected implementation. For immediate work, apply the reviewed edit in your editor and return to Codex with the resulting diff.

## Verify

Use a disposable file within the intended workspace. Confirm Codex can edit and read it using the same permissions that failed before. Check that ordinary shell commands and the repository's tests still work.

## Rollback

Keep the original checkout until all work is accounted for. If the test checkout is no longer needed, save any changes before removing it.

## Evidence and limits

This targets a helper failure before the patch is applied. Windows sandbox errors with different text can have different causes. [Codex Windows sandbox](https://learn.chatgpt.com/docs/windows/windows-sandbox) · [ACL reproduction](https://github.com/openai/codex/issues/14585)
