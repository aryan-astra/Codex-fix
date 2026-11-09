# Recover from the Codex Windows startup spinner on build 26.924.2738.0

## Symptoms

The Windows desktop app opens and stays on its startup spinner. The same account still works in a browser. This was reported on Microsoft Store package build `26.924.2738.0` and nearby builds.

## Diagnosis

Check the installed version under **Settings → Apps → Installed apps** if the Codex window is unusable. Check for a supported Store update first. If the window closes by itself or Task Manager shows no desktop app-server child, use startup crash diagnostics for that symptom.

## Fix

1. Use this temporary recovery only when no Codex task on the affected desktop app is running. Ending its app-server can interrupt active work or connected sessions.
2. Leave the spinner window open and launch Task Manager with **Ctrl+Shift+Esc**. Expand the app's process group. If needed, enable the **Command line** column in **Details** and confirm that the `codex.exe` child belongs to this desktop instance and is running `app-server`.
3. End only that identified child process once. Give the desktop window time to restart its app-server and finish loading. Stop if you cannot identify the child confidently.
4. If the window remains stuck, close it normally if possible and collect the build, time, and startup symptom for support. Leave account files and local chat data in place.

## Verify

Confirm the project list loads and open a known chat. Send a short prompt, then check any feature you need, especially Computer Use and follow-ups in existing chats. Some reports describe the UI loading while these functions remained impaired.

## Rollback

Quit and relaunch the desktop app normally to return to its ordinary startup path. The spinner may recur until the underlying startup issue is resolved.

## Evidence and limits

Multiple reports describe recovery after a targeted app-server restart. Other reports describe incomplete recovery or no improvement, so treat it as a temporary workaround for this startup signature. [App-server restart reproduction](https://github.com/openai/codex/issues/48896) · [Build details and side effects](https://github.com/openai/codex/issues/48463)
