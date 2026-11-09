# Run npm when PowerShell blocks the npm.ps1 shim in Codex

## Symptoms

A Windows Codex task fails before npm starts. PowerShell names `npm.ps1` and says that running scripts is disabled on the system.

## Diagnosis

In the same terminal or shell used by the task, run `Get-Command npm -All` and `Get-ExecutionPolicy -List`. If PowerShell resolves the command to the script shim and the same trusted Node.js installation provides `npm.cmd`, the task can invoke that launcher directly, where allowed by your organization's policy.

## Fix

1. Confirm `Get-Command npm.cmd -All` resolves to the intended installation. If it is absent, repair the project's expected Node.js installation or PATH first.
2. Try `npm.cmd --version` in the affected PowerShell session. If it works, use `npm.cmd` with the same arguments in the failing task or Windows-specific Codex action.
3. If both launchers fail, read the new error. Missing executables, permission denials, and dependency install failures need different fixes.

## Verify

Re-run the affected npm command in the same Codex environment. Confirm npm starts and that your project task completes with the expected output.

## Rollback

Undo any project action edit if you switch to another shell or installation. This procedure needs no PowerShell execution-policy change.

## Evidence and limits

The workaround applies to the npm launcher selection on Windows. It does not make arbitrary PowerShell scripts runnable. [Windows app troubleshooting](https://learn.chatgpt.com/docs/app/windows) · [PowerShell command precedence](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_command_precedence?view=powershell-7.6)
