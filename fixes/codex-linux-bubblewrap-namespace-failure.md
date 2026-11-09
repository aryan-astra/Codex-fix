# Restore Codex sandbox startup when Bubblewrap cannot create a namespace

## Symptoms

On Linux or WSL2, Codex reports a Bubblewrap namespace error or an `RTM_NEWADDR` loopback error before a sandboxed command or patch runs. The same operation may work in an ordinary shell.

## Diagnosis

Run `command -v bwrap` and `bwrap --version` in the shell that launches Codex. Check the distro version and whether the environment is a container or managed devbox. A minimal Codex test is `codex sandbox linux /bin/true` if that subcommand is available in your build. Compare the error from that test with the patch error. If only one target file fails, check the file path and workspace permissions before changing sandbox setup.

## Fix

1. If Bubblewrap is absent, install the distro package using the platform instructions. Restart Codex from the same shell and check which `bwrap` executable is on PATH.
2. On Ubuntu with AppArmor namespace restrictions, check whether your release supplies the `bwrap-userns-restrict` profile. On Ubuntu 24.04, follow the documented procedure to install and load that specific profile. If an active profile already exists, have the administrator review it before replacing anything.
3. In a managed host or container that blocks nested namespaces, give the administrator the exact error and ask for a supported environment or scoped policy adjustment. Keep host-wide protections in place.

## Verify

Repeat the simple sandbox command, then retry a small patch inside a disposable workspace. Confirm both succeed under the intended permission profile.

## Rollback

Have the administrator restore the previous profile if the profile change causes a regression. Keep a copy of the original configuration and test the normal sandbox again after rollback.

## Evidence and limits

The same error family can arise from AppArmor or a container restriction. Compare the failure point and active environment before choosing the remedy. [Codex sandbox setup](https://learn.chatgpt.com/docs/sandboxing) · [Ubuntu reproduction](https://github.com/openai/codex/issues/29908)
