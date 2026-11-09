# Work around Schannel credential errors in the Windows Codex sandbox

## Symptoms

An HTTPS Git operation started by Codex fails with `SEC_E_NO_CREDENTIALS`, while editing and local Git commands work. The same Git remote may be reachable from your ordinary Windows terminal.

## Diagnosis

Compare the same read-only operation in the Codex session and an ordinary terminal using the intended Windows account. For an existing HTTPS remote, use `git ls-remote origin HEAD`. Confirm that both environments use the expected Git binary and remote. If both fail, investigate network access, certificate trust, or remote authentication separately. If only Codex fails, check its network permissions and active sandbox implementation.

## Fix

1. Confirm the operation is permitted by your organization's network and Git policy.
2. If the ordinary terminal succeeds, do the necessary HTTPS Git step there using your normal credential manager. For a push, first review the branch and commits you intend to send.
3. Keep Codex on local edits and checks until the approved sandbox path can connect. If you report the problem, include the error, client version, sandbox implementation, and whether the host terminal succeeded. Review logs for credentials before sharing.

## Verify

Confirm the intended Git operation succeeds from the ordinary terminal. For a push, check the expected remote branch and commit afterward.

## Rollback

This workaround leaves Git's TLS and credential settings in place. Resume using the original Codex workflow after the read-only remote check passes inside its sandbox.

## Evidence and limits

The report documents a Windows sandbox and Schannel interaction. Host success is the condition for using this workaround. [Schannel reproduction](https://github.com/openai/codex/issues/17459) · [Codex Windows sandbox](https://learn.chatgpt.com/docs/windows/windows-sandbox)
