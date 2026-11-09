# Diagnose a Codex stream that disconnects before completion

## Symptoms

Codex retries a request and eventually reports `stream disconnected before completion`. The message may include a more specific reason after the prefix.

## Applies to

Interrupted Codex responses in Desktop or CLI. The prefix covers several failures, so the detailed error and the scope of the failure determine what to try.

## Diagnosis

1. Preserve the short error reason, time, client version, and whether the failed request came from a fresh or established chat. Keep raw logs and credentials private.
2. Check your working tree for partial edits before asking Codex to repeat the task.
3. Check the [OpenAI status page](https://status.openai.com/) if you use the OpenAI service. A provider incident or a widespread account failure can look like a local connectivity issue.

## Fix

| What you see | What to try |
| --- | --- |
| An explicit context-window error | Reduce the request or use the [context recovery guide](codex-cli-context-window-exceeded.md). |
| Quota, billing, or rate-limit text | Check the usage and limits for the account or API project actually selected by the client. Retry when that limit allows it. |
| An authorization error | Check the active account or provider. In CLI, use `codex login status` before changing credentials. |
| Only one long-running chat fails | Start a fresh chat in the same checkout and hand off the task with a short summary. Ask it to inspect the working tree before repeating any edits. |
| A transport error also affects a short request in a fresh chat | Retry once after the connection stabilizes. If allowed by your organization, compare on another trusted network or another client with the same account. If it follows the account across clients, retain the error details and contact support. |

## Verify

A small request should finish without reconnecting, then a normal request should complete in the affected client. For a previously interrupted coding task, inspect the existing diff before continuing.

## Rollback

The fresh-chat test leaves the original conversation available. If you changed networks for diagnosis, return to the original network after testing.

## Evidence and limits

This procedure narrows the failure by reason and scope. A generic disconnect alone cannot establish a cause. [OpenAI error guidance](https://help.openai.com/en/articles/7996703-troubleshooting-chatgpt-error-messages)
