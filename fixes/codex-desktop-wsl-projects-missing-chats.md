## Problem

After a Windows Codex Desktop installation moves from the legacy WSL agent to a WSL connection, a project can open at the right Linux directory and still show **No chats**. An old chat opened from Recents may run on the Windows host and resolve `/home/…` as `C:\home\…`. I'm proposing this in response to your [invitation for other fixes](https://www.reddit.com/r/OpenaiCodex/comments/1xvwnoe/fix_for_the_recent_codex_desktop_issue_where/). Your existing local-project guide assumes the expected threads are available on the target host. The first diagnostic here is whether the Ubuntu host can read even one of them.

## Proposed guide filename

`fixes/codex-desktop-wsl-projects-missing-chats.md`

## Environment and reproduction

Windows desktop 26.1117.31558 (MSIX 26.1117.3838.0), Ubuntu on WSL 2, bundled CLI 0.171.0-alpha.13. I had existing chats created through the legacy WSL agent. After the update, the new Ubuntu connection had a fresh index; Windows had picked up the old transcripts. My automatic migration was blocked by two ChatGPT-linked projects. [Another user on the same package](https://github.com/openai/codex/issues/62766) recorded a completed project migration with thread assignments still unmigrated.

## Diagnosis

On backup copies, trace one known thread ID. Is its transcript present? Does the Ubuntu index contain that ID and point at a readable copy? Does the Desktop assignment name the Ubuntu host and the intended remote project? A new chat in the same Ubuntu project makes a useful control. These checks distinguish an unavailable thread on the target host from a thread that is readable there but missing from the sidebar.

## Proposed fix and verification

I backed up both Codex data stores and the desktop state, copied the legacy WSL transcripts to ext4, and cloned the old index with its rollout paths pointing to those copies. I matched the old single-root WSL projects to the remote projects by distribution and Linux path, then restored their thread assignments. I verified that an old chat opened from its project and could continue in the Ubuntu connection and the WSL CLI. My resulting WSL index contained about 850 threads across an installation with 13 remote projects. A pre-migration backup preserves the original data for rollback. If the prior assignments are missing, the transcript path alone doesn't establish the original project membership.

I maintain the [version-specific scripts and recovery notes](https://github.com/rbnrong/codex-wsl-isolation/tree/afe2739c36088d1dd94af8699bc2d6c176063e1c). The migration scripts have dry-run modes. The separate backup command writes a snapshot immediately. This case doesn't establish recovery for synced automations.

- [x] I removed personal information, credentials, and private identifiers from the examples and attachments.
