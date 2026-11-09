# Contributing to Codex Fix

## 1. Open an issue

Search existing issues and the [fix library](README.md#fix-library). For a new problem, choose **Report a Codex issue or propose a fix** under **Issues → New issue**.

Start with the problem users can see. Include the Codex surface (Desktop, CLI, or another integration), version, operating system, steps to reproduce, and expected and actual results. If you have a fix, include a short outline and the evidence that it worked.

Propose a guide path such as `fixes/codex-desktop-missing-local-projects.md`. Use lowercase, hyphen-separated words that describe the affected surface and symptom. If discussion changes the path, update the issue so the pull request uses the agreed filename.

## 2. Write the guide

Put the problem statement near the top of the Markdown file. Put the solution in a numbered **Fix** section so readers can follow it in order. Use this outline as a starting point.

```markdown
# <Symptom and affected Codex surface>

## Symptoms
<What the reader sees and how to reproduce it.>

## Applies to
<Observed versions, operating systems, and conditions that distinguish this case.>

## Diagnosis
<Read-only checks, expected results, and when to stop.>

## Fix
<Prerequisites, backup steps, and numbered instructions.>

## Verify
<What should work afterward and how you checked it.>

## Rollback
<How to undo changes if the fix fails.>
```

Keep examples reproducible and label any hypotheses. If the fix changes local state, name the files it changes and show how to back them up before the first write. Use generic paths and synthetic examples. Redact names, account identifiers, project and thread IDs, private repository URLs, and credentials. Share short, sanitized excerpts in place of raw state files, databases, logs, or chat exports.

## 3. Open a pull request

- Use the guide path agreed in the issue and add it to the table in [README.md](README.md#fix-library).
- Link the issue in the pull request. Use `Closes #123` if merging the guide resolves it.
- Describe which versions and environments you checked, how you verified the result, and any limits of that verification.
