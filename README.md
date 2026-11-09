# Codex Fix

A community-maintained collection of troubleshooting guides for Codex. Each guide describes a specific symptom, the conditions a fix applies to, and how to confirm recovery.

## Fix library

### Desktop

| Problem | Applies to | Guide |
| --- | --- | --- |
| Local projects disappear from the sidebar and Projects page, but the project folders still exist | Codex Desktop on Windows with a missing renderer project cache and intact local project records | [Restore missing local projects](fixes/codex-desktop-missing-local-projects.md) |
| Creating a permanent worktree fails with an invalid `refs/heads/HEAD` ref | Desktop builds affected by the ref resolution bug | [Create the worktree directly with Git](fixes/codex-desktop-permanent-worktree-invalid-head.md) |
| A new worktree is missing local files or dependencies | Codex-managed worktrees | [Restore worktree setup](fixes/codex-desktop-worktree-missing-setup.md) |
| A connected host is missing from the phone | Codex Remote Control | [Restore the connection](fixes/codex-remote-control-host-not-visible.md) |
| The window stays on the startup spinner after an update | Windows Store package build `26.924.2738.0` and nearby affected builds | [Try a targeted app-server restart](fixes/codex-windows-startup-spinner-after-update.md) |

### Command line

| Problem | Applies to | Guide |
| --- | --- | --- |
| A chat stops at the model's context limit | Interactive CLI session with an explicit context-length error | [Recover from a context window error](fixes/codex-cli-context-window-exceeded.md) |
| An MCP server is unavailable at startup | CLI with a configured MCP server | [Diagnose MCP startup timeouts](fixes/codex-cli-mcp-server-startup-timeout.md) |
| The browser callback refuses the connection during sign-in | CLI on an SSH host or other machine separate from the browser | [Sign in with a device code](fixes/codex-cli-remote-login-callback-fails.md) |
| Codex rejects a legacy profile selector | CLI version that expects a separate profile file | [Migrate a legacy profile](fixes/codex-cli-legacy-profile-configuration-error.md) |
| A saved chat is missing from the resume picker | Interactive CLI chat saved in the same local environment | [Find a saved chat](fixes/codex-cli-session-missing-from-resume-picker.md) |
| Codex skips a repository instruction | CLI session with missing or unexpected `AGENTS.md` guidance | [Check instruction discovery](fixes/codex-cli-agents-md-instructions-missing.md) |
| A project-local provider definition is ignored | CLI with a custom model endpoint | [Activate the intended model provider](fixes/codex-cli-custom-provider-settings-ignored.md) |

### Desktop and command line

| Problem | Applies to | Guide |
| --- | --- | --- |
| A response disconnects before completion | Desktop or CLI response interrupted during streaming | [Diagnose a disconnected stream](fixes/codex-stream-disconnected-before-completion.md) |
| A request returns 401 after successful ChatGPT sign-in | Desktop or CLI using ChatGPT authentication | [Check and refresh the stored sign-in](fixes/codex-chatgpt-sign-in-succeeds-but-requests-return-401.md) |
| Starting a chat fails because a hook expects a sequence | Inline TOML hooks in Desktop or CLI | [Repair the hook configuration](fixes/codex-cli-hooks-configuration-map-expected-sequence.md) |
| Bubblewrap fails before a command or patch runs | Linux or WSL2 | [Diagnose the namespace failure](fixes/codex-linux-bubblewrap-namespace-failure.md) |
| Sandbox refresh fails before a file edit | Native Windows sandbox | [Diagnose the patch helper failure](fixes/codex-windows-sandbox-apply-patch-refresh-error.md) |
| HTTPS Git fails with `SEC_E_NO_CREDENTIALS` | Native Windows sandbox and Schannel | [Check Git from the host terminal](fixes/codex-windows-git-https-schannel-credentials.md) |
| PowerShell blocks the `npm.ps1` launcher | Windows with the Node.js npm launchers | [Use the intended npm launcher](fixes/codex-windows-powershell-blocks-npm-script.md) |

### Cloud

| Problem | Applies to | Guide |
| --- | --- | --- |
| A build or test cannot find a dependency in a new task | Codex Cloud environments | [Repair cloud setup](fixes/codex-cloud-setup-tool-missing.md) |

## Contribute a fix

Start with a GitHub issue. Describe the symptom and propose a guide filename. If an existing issue covers the same problem, add your findings there. When your fix is ready, open a pull request with the guide under `fixes/` and link the issue.

[Contribution guide](CONTRIBUTING.md)
