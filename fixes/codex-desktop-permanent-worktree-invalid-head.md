# Work around `refs/heads/HEAD` when creating a permanent Codex worktree

## Symptoms

**Create permanent worktree** fails with `fatal: invalid reference: refs/heads/HEAD`. Ordinary Git commands still resolve the current commit.

## Applies to

The exact error was reported on macOS and Windows in August 2026 desktop builds. Use this workaround only when the repository resolves its current commit and the app fails on the literal `refs/heads/HEAD` ref.

## Diagnosis

From the original checkout, run `git status --short --branch` and `git rev-parse --verify HEAD`. If Git itself cannot resolve `HEAD`, check the repository or initial commit first. Record the branch and any uncommitted changes before creating another checkout.

## Fix

1. Try the app's supported update path and retry once. If the same error persists, create a linked checkout directly with Git.
2. From the original checkout, choose an unused branch and a new sibling directory. Replace the names below if either already exists.

   ```sh
   git worktree add -b codex/worktree-fix ../project-codex-worktree HEAD
   ```

3. Open the new folder as a project in Codex and start a Local chat there. Git uses the committed starting point. Bring over any required uncommitted edits deliberately and run the repository's setup steps in the new checkout.

## Verify

Run `git worktree list` in the original checkout. Confirm the new directory and branch are listed, then check that the Codex chat is running in the new directory.

## Rollback

Save or commit work from the linked checkout before removing it. Close its chats and use `git worktree remove ../project-codex-worktree` from the original repository after confirming the worktree is clean. The new branch remains available.

## Evidence and limits

The manual checkout works around the app's permanent worktree creation path. It is a Git-managed checkout opened as a Local project. [Reproduction and discussion](https://github.com/openai/codex/issues/38517) · [Git worktree manual](https://git-scm.com/docs/git-worktree)
