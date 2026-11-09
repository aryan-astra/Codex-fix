# Find why Codex CLI missed an AGENTS.md instruction

## Symptoms

A fresh Codex session omits a repository instruction or reports rules from an unexpected directory.

## Diagnosis

Check the working directory, Git root, and active `CODEX_HOME`. Inspect the instruction files along the path from the root to that working directory. Each directory contributes at most one instruction file. A nonempty `AGENTS.override.md` takes precedence over `AGENTS.md` in the same directory. Instructions deeper in the path come later, and the combined project instructions have a byte limit.

## Fix

1. Start a new session from the directory whose guidance you need. For example, use `codex --cd path/to/subdirectory` from the project root.
2. If an override hides the intended file, reconcile the two with the repository owner. Preserve project security rules and avoid weakening global policy.
3. If you use a nonstandard filename, add its name to `project_doc_fallback_filenames` in the appropriate configuration. If the size cap is the cause, shorten duplicated guidance or use targeted nested files. If necessary, back up the user config before changing `project_doc_max_bytes`.
4. Restart the session to rebuild its instruction chain.

## Verify

In a new session, ask Codex to identify the active instruction files and summarize a distinctive rule. For an exact check, compare the loaded instruction text in your local diagnostics with the intended file, keeping transcripts private.

## Rollback

Restore any edited instruction and configuration files from Git or your backup. Start a fresh session after restoring them.

## Evidence and limits

This checks instruction discovery and precedence. If the correct instructions load and the model still ignores them, investigate that behavior separately. [Codex instruction discovery](https://learn.chatgpt.com/docs/agent-configuration/agents-md)
