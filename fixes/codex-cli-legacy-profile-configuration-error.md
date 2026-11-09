# Fix the legacy profile configuration error in Codex CLI

## Symptoms

After a CLI update, starting or resuming a chat fails with a configuration error saying that a legacy `profile = "..."` setting is no longer supported. The error may name a `.config.toml` file and suggest `--profile`.

## Applies to

A CLI version that explicitly asks for the profile file layout. A generic TOML parse error or a managed policy rejection needs a different diagnosis.

## Diagnosis

Check `codex --version` and `codex --help` from the affected terminal. Locate your user-level `config.toml` in `CODEX_HOME`, normally `~/.codex`. Check the exact profile named by the error and whether an existing `<name>.config.toml` already contains its settings.

## Fix

1. Back up your user config and any existing profile file to a private directory.
2. If you still need the profile, place its supported settings in `<name>.config.toml` alongside `config.toml`. For example, move keys from a legacy `[profiles.example]` table into the corresponding settings in `example.config.toml`. Merge with an existing file deliberately and consult the configuration reference for renamed or retired keys.
3. Remove the unsupported top-level profile selector identified in the error. Once you have migrated the settings, remove the corresponding legacy profile definition if your version rejects it. Preserve base settings, security controls, and unrelated profile definitions unless the CLI reports that they also require migration.
4. Start Codex with `codex --profile example`, replacing `example` with your profile name. To use the base configuration, start it without a profile selector.

## Verify

Start a new chat, confirm the configuration error is gone, and check that the intended model and permission settings are active. Confirm an existing chat can still open from the same working directory.

## Rollback

Restore the backed-up configuration files if a setting was lost or migrated incorrectly. The restored legacy selector may reproduce the startup error on the current CLI. Use your backup to recover settings before trying a corrected migration.

## Evidence and limits

Profile files layer selected settings over the user configuration. This procedure addresses the specific legacy profile error. [Codex configuration reference](https://learn.chatgpt.com/docs/config-file/config-reference)
