# Codex Fix

A community-maintained collection of troubleshooting guides for Codex. Each guide describes a specific symptom, the conditions a fix applies to, and how to confirm recovery.

## Fix library

### Desktop

| Problem | Applies to | Guide |
| --- | --- | --- |
| Local projects disappear from the sidebar and Projects page, but the project folders still exist | Codex Desktop on Windows with a missing renderer project cache and intact local project records | [Restore missing local projects](fixes/codex-desktop-missing-local-projects.md) |

### Command line

| Problem | Applies to | Guide |
| --- | --- | --- |
| A chat stops at the model's context limit | Interactive CLI session with an explicit context-length error | [Recover from a context window error](fixes/codex-cli-context-window-exceeded.md) |
| An MCP server is unavailable at startup | CLI with a configured MCP server | [Diagnose MCP startup timeouts](fixes/codex-cli-mcp-server-startup-timeout.md) |
| The browser callback refuses the connection during sign-in | CLI on an SSH host or other machine separate from the browser | [Sign in with a device code](fixes/codex-cli-remote-login-callback-fails.md) |
| Codex rejects a legacy profile selector | CLI version that expects a separate profile file | [Migrate a legacy profile](fixes/codex-cli-legacy-profile-configuration-error.md) |
| A saved chat is missing from the resume picker | Interactive CLI chat saved in the same local environment | [Find a saved chat](fixes/codex-cli-session-missing-from-resume-picker.md) |

### Desktop and command line

| Problem | Applies to | Guide |
| --- | --- | --- |
| A response disconnects before completion | Desktop or CLI response interrupted during streaming | [Diagnose a disconnected stream](fixes/codex-stream-disconnected-before-completion.md) |

## Contribute a fix

Start with a GitHub issue. Describe the symptom and propose a guide filename. If an existing issue covers the same problem, add your findings there. When your fix is ready, open a pull request with the guide under `fixes/` and link the issue.

[Contribution guide](CONTRIBUTING.md)
