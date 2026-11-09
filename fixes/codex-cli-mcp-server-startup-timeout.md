# Fix an MCP server that times out during Codex CLI startup

## Symptoms

Codex opens, but an expected MCP server is unavailable. The startup message names the server and reports a timeout or a failed handshake.

## Applies to

Codex CLI with a configured MCP server. These checks separate a server that starts slowly from one that exits early, needs authorization, or points at an unreachable endpoint.

## Diagnosis

Run `codex mcp list` in the terminal where the failure occurs. Inspect the named server's settings locally. For stdio, check that the executable is on that terminal's PATH, its arguments match the vendor's instructions, and any required working directory exists. For HTTP, confirm the configured URL, whether the server is running, and whether authorization is required. Keep credentials and full config output private.

## Fix

1. If the server is disabled, correct that setting only after confirming it is intended for this project. If a trusted server command cannot be found, repair its installation or point the existing entry at the correct executable.
2. For an HTTP server that supports OAuth and reports an authentication failure, run `codex mcp login <server-name>`. Complete sign-in through the server's expected provider.
3. If the server reliably starts but takes longer than the configured startup window, back up the active `config.toml` to a private location. Add or change `startup_timeout_sec = 30` inside the existing `[mcp_servers.<server-name>]` section. Preserve the existing transport and authentication settings and restart Codex. Use a value that fits the observed startup time.
4. If the server still exits or returns a handshake error, inspect the server's own diagnostics. A longer timeout only helps a slow startup. `tool_timeout_sec` controls a different stage, after startup.

## Verify

Open a new CLI session in the same directory. Run `/mcp` and confirm the server is active. Try a harmless read-only tool from that server and check that its result returns.

## Rollback

Restore the previous server settings from the backup or undo the specific timeout change, then restart the CLI. If the server is optional and remains broken, `enabled = false` in its existing section can temporarily isolate it while retaining the settings for later repair.

## Evidence and limits

The procedure follows the documented MCP transport, authentication, and timeout settings. Vendor-specific runtime failures require the server's own troubleshooting steps. [Codex MCP documentation](https://learn.chatgpt.com/docs/extend/mcp?surface=cli)
