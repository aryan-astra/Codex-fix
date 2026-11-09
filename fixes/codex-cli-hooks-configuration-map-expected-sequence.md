# Repair a Codex hook configuration that expects a sequence

## Symptoms

A new or resumed chat fails to start. The configuration error says `invalid type: map, expected a sequence` and identifies `hooks` or a hook event.

## Diagnosis

Open the file named in the error. Check user-level `config.toml` and trusted project-level `.codex/config.toml` if the message omits the path. Locate the hook event and compare its structure with the documentation for your installed version. In inline TOML, the event and its command handlers are arrays of tables.

## Fix

1. Back up the affected file privately. Keep existing hook commands, matchers, and any security policy intact.
2. For inline TOML, use the documented event and handler array syntax. For example, the shape of a shell-command hook is:

   ```toml
   [[hooks.PreToolUse]]
   matcher = "^Bash$"

   [[hooks.PreToolUse.hooks]]
   type = "command"
   command = "your-reviewed-hook-command"
   ```

   Substitute your existing reviewed command and matcher. If an installer generated the malformed section, fix or update its generator so the next install does not recreate it. For a managed hook, ask the administrator to repair the configuration.
3. Restart the client and review the exact updated definition through `/hooks` or the desktop hook settings before trusting it.

## Verify

Start a chat, inspect the hook's status, and trigger its matching event in a disposable checkout. Check the expected effect, especially for hooks that enforce a policy.

## Rollback

Restore the backup if the migration loses a handler or changes the event match. The original syntax error may return, so keep the backup while correcting the hook for the active version.

## Evidence and limits

This procedure addresses a structure error in an inline hook configuration. A command that starts and then exits unsuccessfully needs its own diagnostics. [Codex advanced configuration](https://learn.chatgpt.com/docs/config-file/config-advanced) · [Codex hooks](https://learn.chatgpt.com/docs/hooks)
