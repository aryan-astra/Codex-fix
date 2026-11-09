# Restore a missing Codex Remote Control host on mobile

## Symptoms

The phone no longer lists a previously connected computer, or setup reaches the QR code but the approval request does not appear.

## Diagnosis

Check that the computer is awake and online, the desktop app is running, and both devices use the same ChatGPT account and workspace. In the desktop connection settings, check whether **Allow other devices to connect** is enabled. Signing out turns off Remote Control, so check it again after signing back in.

## Fix

1. Turn Remote Control back on if it is disabled. Keep the desktop app open.
2. If the host remains absent, update both apps through their supported update paths. Start setup again from the host and scan the new QR code from Codex or Remote in the mobile app.
3. Complete any required account verification and approve only the connection you initiated on a device you control. For managed accounts, have the workspace administrator confirm Remote Control availability if approval never arrives.

## Verify

Open the host from the phone, check that the expected project is listed, and open a known chat. A dropped connection after setup warrants a separate check of host sleep and network availability.

## Rollback

Cancel an incomplete setup if you selected the wrong host or account. If you paired the wrong device, remove its connection through the desktop connection settings.

## Evidence and limits

These steps apply to Remote Control pairing and discovery. A reproducible approval loop after a fresh setup needs the app versions and exact failure state. [Codex remote connections](https://learn.chatgpt.com/docs/remote-connections)
