# Fix a missing dependency in a Codex Cloud environment

## Symptoms

A test or build runs locally but fails in Codex Cloud because a command, package, or generated dependency is missing. The same failure can recur in each new task even after installing the tool once.

## Diagnosis

Open the setup conversation for the environment selected by the failing task. Find the first command that failed. Check the repository's lockfile, required runtime version, and package registry. Compare the package install logs from setup with the failing command in a new task.

## Fix

1. Put the project’s supported runtime and dependency installation steps into the environment setup. If the command failed because the repository needs a manifest or lockfile fix, make that change in the repository and test setup again.
2. If a registry or download host is blocked, check the exact destination and whether setup or runtime needs access. Review registry authorization separately. Use the environment’s supported secret mechanism for required credentials.
3. After setup passes, publish or republish the intended environment. Start a new task using that environment.

## Verify

In the new task, check the missing tool's version and rerun the original build or test. If the setup preview succeeds but a new task still fails, confirm the task selected the republished environment and check its runtime permissions.

## Rollback

Restore the previous reviewed setup settings and republish if the new setup breaks other tasks. Keep existing tasks open if their working files are needed.

## Evidence and limits

A one-off install in a task affects that task. The reusable setup controls the starting state of future tasks. [Codex Cloud environments](https://learn.chatgpt.com/docs/environments/cloud-environments)
