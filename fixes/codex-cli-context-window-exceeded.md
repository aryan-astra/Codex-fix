# Recover when a Codex CLI chat exceeds its context window

## Symptoms

An established Codex CLI chat stops with an explicit message that the input exceeds the model's context window. The message may appear after a generic stream-disconnect prefix. A long transcript, a large tool result, or a newly attached file can make the next request too large.

## Applies to

Interactive CLI sessions with a context-length error. Read the reason after the stream error. Quota, sign-in, and service errors call for different checks.

## Diagnosis

1. Note whether the error occurred immediately after a large paste, file attachment, or command output.
2. Save your current working directory, task goal, and the smallest set of file paths needed to continue. In a Git checkout, inspect `git status --short` and `git diff --stat` to see whether the interrupted turn changed files.
3. If even a tiny request in a fresh chat reports the same error, check whether a custom provider or model override is active and whether the service is experiencing an incident. A fresh chat can still inherit project instructions and provider settings. [OpenAI status](https://status.openai.com/)

## Fix

1. If the conversation is still responsive and `/compact` appears in its command list, try it once. Wait for it to finish and send a short follow-up with no large attachment.
2. If compaction fails or the next request still exceeds the limit, use `/new` in the same checkout or start a fresh CLI session from that directory. Hand off only the goal, relevant paths, completed work, and the next check. Ask Codex to read narrow file ranges and limit long command output.
3. If a single file or tool result caused the error, split the work by subsystem or pass a small excerpt. Preserve existing changes and tell the new session to inspect them before repeating edits.

## Verify

Send a small request tied to the current checkout. Confirm Codex answers, can see the expected files, and recognizes any changes left by the interrupted turn. Reintroduce relevant context in small pieces.

## Rollback

The fresh-chat workaround leaves the original transcript available through `/resume`. If you return to it, the same context limit may recur. Keep any in-progress changes until you have compared them with the new session's work.

## Evidence and limits

A fresh chat with a compact handoff is a recovery path for a specific oversized conversation. It does not increase the model's context window or resolve service-wide compaction failures. [Codex developer commands](https://learn.chatgpt.com/docs/developer-commands?surface=cli)
