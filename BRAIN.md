# CENTER = brain and memory for the RubikaAgent project

Target repo: hosseinasgari898989-dev/rubika-agent. Owner: Hossein (writes Persian, free plan, wants short replies).
The coding agent (ChatGPT) works like a careful senior engineer. Prompts from Claude/owner say WHAT; this file says HOW.

## Hard rules (never break)
1. Never delete or empty a file/test/feature to make something pass. A failing test means find the root cause. Deleting a broken part is allowed only after backup and only if the owner is told in the final report.
2. Never touch secrets, repo variables/gates, migrations, quiet_hours values. Never ask for secret values. No manual workflow dispatch.
3. Publishing: test channel only. Main channel stays locked until the owner gives explicit approval in plain words.
4. Per task: new branch + `backup/pre-<task>-<date>` branch + small PR. Prefer new modules over rewriting big files. Patch from LIVE repo content, never from stale copies.
5. Do not change structure unless the task says so. Keep existing behaviour (TikTok edit captions unchanged; Mob X news exempt from daily cap and quiet hours; text-only Mob posts never published).
6. Never claim success without evidence (test output, CI link, SHA).

## Merge policy by repository
- `CENTER` only: when every changed file in a PR has the `.md` extension and the PR contains documentation-only edits (no code, workflow, configuration, or other non-Markdown changes), CI may be skipped because CENTER has no CI workflow. Read and review the complete diff first; verify that all changed paths are Markdown, the scope is limited to the intended documentation, there are no unrelated deletions or rewrites, and the PR is mergeable. Then it may be merged without CI.
- `rubika-agent`: the rule remains strict and unchanged — merge only when all required CI checks are green. The CENTER documentation-only exception never applies to rubika-agent.
- Any non-documentation-only PR in CENTER must not use this exception; require its applicable CI checks to be green before merging.

## Working loop (repeat until done, do not skip steps)
1. READ: open the real files involved and their tests; list what you do NOT know and look it up instead of guessing.
2. THINK: write the root cause in 3 lines, then 2 possible fixes, choose the smallest safe one.
3. CHANGE: smallest diff. One concern per PR.
4. TEST: run the full existing test suite plus tests for the new behaviour. Test the unhappy path (empty, stale, duplicate, already-processed).
5. REVIEW YOURSELF: re-read the diff as a hostile reviewer. Look for: dropped features, silent catches, wrong SQL guards, race conditions, import problems (some tests load modules from `data:` URLs, so relative imports need the test harness to know them).
6. If CI is red: read the actual error, fix the cause, re-run. Max 3 attempts per hypothesis, then stop and report the exact error with your best theory. No guess-edits.
7. MERGE: follow the repository-specific merge policy above. In `rubika-agent`, merge only when CI is green. In `CENTER`, only all-Markdown documentation-only PRs may skip CI after full diff review. After a `rubika-agent` merge, check the deploy run result.
8. REPORT in Persian, very short: what changed, CI status, main SHA, deploy status, owner's to-dos.
9. LOG: append a dated entry to STATE.md (what was done, decisions, open items).

## Style of final report
Short. Facts only. Links to runs. List anything skipped or risky.
