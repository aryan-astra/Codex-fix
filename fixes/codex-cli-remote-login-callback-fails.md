# Sign in to Codex CLI when the browser callback fails over SSH

## Symptoms

You start Codex CLI on a remote machine. The browser on your own computer accepts the sign-in, then opens a loopback address such as `localhost` and reports that the connection was refused.

## Applies to

Codex CLI using ChatGPT sign-in on an SSH host, container, or other machine separate from the browser. The CLI must offer device code login, and your account or workspace must allow it.

## Diagnosis

Run these commands in the terminal that actually runs Codex.

```sh
codex --version
codex login status
codex login --help
```

Confirm whether the browser and the CLI run on different machines. A browser's loopback address points back to the browser's machine. If the sign-in succeeded in the browser but the callback listener lives on the remote host, the local browser cannot reach it through its own loopback interface. A later 401 after a successful login needs a separate credential or provider check.

## Fix

1. Stop the failed interactive login and any duplicate attempts.
2. Check that device code login is available in your ChatGPT security settings or permitted by your workspace. For a managed account, follow your administrator's policy.
3. On the remote host, run `codex login --device-auth`.
4. Open the link printed by your own CLI in a trusted browser. Sign in to the intended account and enter the one-time code shown in your terminal. Keep the code private.

## Verify

Wait for the CLI to report success. Run `codex login status` on the remote host, then send a short prompt from that same environment.

## Rollback

If you signed into the wrong account, use `codex logout` in the affected environment after confirming which other sessions share its stored credentials. Then repeat login with the intended account. If device code login is unavailable, use an authentication method supported by your workspace.

## Evidence and limits

Device code login is documented for remote and headless environments. It requires account support and does not repair a rejected API key or a third-party provider's OAuth flow. [Codex authentication](https://learn.chatgpt.com/docs/auth)
