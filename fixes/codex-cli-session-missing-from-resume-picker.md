# Find a saved Codex CLI chat missing from the resume picker

## Symptoms

A chat you worked on earlier is absent from the resume picker, or `codex resume --last` opens a different chat. This can happen after moving to another checkout, using a linked worktree, or opening the CLI from a different directory.

## Applies to

Previously saved interactive CLI sessions in the same local Codex environment. Chats created under a different machine, OS user, container, or Codex data directory may be stored elsewhere.

## Diagnosis

Check the directory you are in and whether it is the original checkout or worktree. Confirm that you are running Codex under the same user and `CODEX_HOME` as before. Keep the original session ID private if you have it.

## Fix

1. In Codex, run `/resume` and change the picker scope from **Cwd** to **Repo** or **All**, where supported. Select the chat by title and timestamp. If you know the original session ID, you can use `codex resume <session-id>` from the shell.
2. If Codex asks which directory to use, choose the checkout that contains the files for this task. Review any branch or working-tree changes before continuing.
3. If the session is still missing, reopen the CLI in the original environment and repeat the lookup. A scope change searches the saved sessions visible to that environment.

## Verify

Check the reopened transcript and current working directory before sending a new request. If the task involved edits, review `git status --short` in that checkout.

## Rollback

Exit the reopened session if you chose the wrong chat or directory, then select the intended one. Opening the picker requires no changes to session storage.

## Evidence and limits

The documented resume scopes address directory filtering. A genuinely deleted session or unavailable data directory needs a separate recovery path. [Codex developer commands](https://learn.chatgpt.com/docs/developer-commands?surface=cli)
