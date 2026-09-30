---
name: implementation-handoff
description: Use in Cursor after the spec's Done-when checks pass, when the user explicitly requests a reviewed PR with Cursor reviewer subagents and Bugbot.
---

# Implementation Handoff

Turn a verified implementation into a reviewed PR. Independent reviewers see the diff and the spec with fresh context; you fix what they find; the PR carries the record.

This skill is Cursor-specific and requires its Task tool and configured reviewer agents. Do not invoke it in pi or silently substitute unavailable reviewers. Run it only from the main agent or a direct subagent; grandchildren cannot dispatch reviewers.

## Preconditions

Stop and report if any fails. Do not improvise around them.

- The user authorized committing, pushing, creating a PR, and triggering external review. An implementation request alone is not that authorization.
- The request names the base branch, or the user agreed one. If not, ask.
- The current branch is a feature branch created from the base branch, not the base itself.
- Every change to be committed belongs to this spec. Inspect staged and unstaged changes; unrelated edits mean the user must decide what to include. The working tree must be clean after the implementation commit and before reviewer dispatch.
- `origin` exists and `gh auth status` succeeds.
- The spec's Done-when commands have been run and pass.

## Procedure

1. Commit all work on the feature branch. Reviewers diff `<base>...HEAD`; uncommitted changes are invisible to them.
2. If the spec exists only in conversation, write it to a file outside the repository (for example under `$env:TEMP`) so the path in the dispatch prompt resolves.
3. Dispatch reviewers in parallel, one Task call each:
   - `spec-conformance-reviewer` (always)
   - `architecture-style-reviewer` (always)
   - `security-review` (when the trigger below applies)
4. Triage findings. `blocking` and `advisory` are defined in the `review` skill.
5. Run the fix loop below.
6. Use the `review` skill's closeout and knowledge-capture guidance; useful follow-ups go into the PR body.
7. `git push -u origin <branch>`, then `gh pr create --base <base> --head <branch> --title ... --body-file ...` using the PR body below.
8. Post the Bugbot trigger: `gh pr comment <number> --body "@cursor review"`.

Never merge, force-push, or rebase. Those are the user's decisions.

## Reviewer Dispatch Prompt

Custom reviewers get exactly this. Do not attach the conversation, and do not ask them to "double-check"; fresh context is what they contribute.

```text
Repository: <absolute path>
Base branch: <name>
Head: HEAD
Specification: <absolute path to spec file>
Round: <1|2>
Previously reported findings (round 2 only): <list>
```

`security-review` uses the prompt shape from Cursor's `review-security` skill and MUST include `Base Branch: <name>`; it otherwise assumes the repository default branch.

## Security Review Trigger

Run `security-review` when the diff touches any of: authentication, authorization, session or token handling; parsing or deserialization of external input; subprocess, shell, or command-string construction; filesystem paths derived from user input; network clients or servers; secrets, credentials, or configuration that holds them; cryptography; SQL or query construction; dependency manifests. When unsure, run it. Record the decision and reason in the PR body under Verification.

## Fix Loop

```text
round = 1
dispatch applicable reviewers in parallel
loop:
  if no blocking findings: break
  if round == 2: stop; report remaining blocking findings to the user; do not push or open a PR
  fix blocking findings (advisory optional); re-run Done-when commands; commit
  round = 2
  re-dispatch only reviewers whose blocking findings were touched, passing their previous findings
```

Two rounds is the cap because an uncapped loop does not terminate, and a PR with known blocking findings is not merge-ready. Unfixed advisory findings are not dropped; they go into the PR body with a one-line reason each.

## PR Body

```markdown
## Goal
## Verification
<command, working directory, result; security review run or skipped, and why>
## Review findings fixed
## Deferred review findings
<finding — reason>
## Follow-ups
<useful follow-ups from the review skill's closeout check>
```

## Bugbot Comment

The comment body is exactly `@cursor review`, posted as a top-level PR comment. Any other text after `@cursor` is routed to a Cloud Agent that will start acting on the PR.
