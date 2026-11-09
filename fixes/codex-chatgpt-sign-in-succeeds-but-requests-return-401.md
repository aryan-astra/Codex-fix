# Recover a Codex ChatGPT session that returns 401 after sign-in

## Symptoms

Sign-in completes, but the first Codex request returns `401 Unauthorized`, or a previously working chat starts reporting an invalidated refresh token.

## Diagnosis

In the affected CLI environment, run `codex login status`. In Desktop, check the account and workspace in the profile menu. Confirm the failed request uses ChatGPT sign-in. An API key, custom endpoint, or managed workload identity uses a different credential path.

Check the service status before changing local credentials. If a short request fails across multiple clients under the same account, record that scope and the time of the failure. [OpenAI status](https://status.openai.com/)

## Fix

1. Save work and close other Codex sessions that share the affected stored login.
2. Sign out through the client and sign back into the intended ChatGPT account and workspace. In CLI, use `codex logout` followed by `codex login`. This replaces the stored login for that environment.
3. Retry once from a fresh session. If the first request still returns 401 or a fresh refresh token is rejected, collect the client version, error code, timestamp, and affected clients for support. Keep tokens and authentication files private.

## Verify

Run `codex login status` again, then submit a short prompt. If the failure recurs when the session refreshes, include that timing in the report.

## Rollback

If you chose the wrong account, sign out of it and sign into the original account through the same client.

## Evidence and limits

Reauthentication can recover a stale login. A server-side rejection can persist after a fresh sign-in, as documented in the upstream report. [Codex authentication](https://learn.chatgpt.com/docs/auth) · [Refresh-token failure report](https://github.com/openai/codex/issues/41973)
