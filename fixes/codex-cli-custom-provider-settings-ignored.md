# Fix a custom Codex model provider that stays inactive

## Symptoms

Codex continues using the default endpoint after you add custom provider settings under a repository's `.codex/config.toml`, or startup warns that provider keys are ignored.

## Diagnosis

Check the active configuration files, `CODEX_HOME`, and selected CLI profile. Identify which provider ID should be active and where it is defined. Codex treats provider selection and endpoint settings as machine-local configuration. Project-level trust alone does not activate provider keys.

## Fix

1. Confirm the intended endpoint and authentication scheme with the provider or your administrator. Back up the user configuration privately.
2. Put the supported provider definition and selection in the user-level configuration or its selected profile. Use the provider ID in `model_provider` and the matching `[model_providers.<id>]` definition when defining a custom provider. Preserve managed restrictions.
3. If the provider uses `env_key`, supply the corresponding variable through your approved secret mechanism in the environment that launches Codex. Check its presence without printing its value.
4. Restart Codex. If you use a profile, start it explicitly with the supported `--profile` flag.

## Verify

Check the active model and provider in the new session, then submit a short prompt containing no sensitive repository information. If a 401 remains, verify the provider's credential and endpoint independently.

## Rollback

Restore the previous user configuration and remove any temporary environment setting from the launch shell. Restart Codex and verify the intended default provider.

## Evidence and limits

The fix addresses project-local provider settings that Codex ignores. A network error or a gateway that lacks the selected model requires separate checks. [Codex advanced configuration](https://learn.chatgpt.com/docs/config-file/config-advanced) · [Supported environment variables](https://learn.chatgpt.com/docs/config-file/environment-variables)
