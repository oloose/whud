# Source: diff

Questions about git changes to any file, not just code.

## Choose the change

From the user's arguments:

- A commit, range or branch (`abc123`, `HEAD~3..HEAD`, `main..feature`): use it.
- `staged`: `git diff --cached`.
- `wip`: `git diff HEAD` (staged and unstaged).
- Nothing given and `git status --short` shows changes: use them (`git diff HEAD`).
- Nothing given and no uncommitted changes: use the last commit (`git show HEAD`).

Ask the user instead of guessing when:

- The argument is not a valid ref, or could mean several things.
- The change is a merge commit (which side?) or a very large range (all of it, or part of it?).
- You are not in a git repository. Offer `session` instead.

Say which change you use, in one line.

## Gather

1. `git diff --stat <change>` (or `git show --stat`) for the overview.
2. Drop noise: lockfiles, `dist/`, `build/`, minified, generated and vendored files, snapshots. Use pathspec excludes, for example `-- . ':!*.lock' ':!dist'`.
3. Read the filtered diff. If it is large, read it file by file and focus on the most meaningful changes. Do not try to quiz about all of it.
4. Open the changed files around the hunks. Ask about how the surrounding code or text works, not only about what changed.

## Verify

Take reference answers from the actual code or text, not from the commit message. If you cannot confirm what something does, do not ask about it.

## Size

Measure by changed lines after dropping noise:

| Changed lines | Size |
| --- | --- |
| under 10 | trivial |
| 10-50 | small |
| 50-200 | medium |
| 200-500 | large |
| over 500 | very large |

Go one step up if the change touches many files or core logic, one step down if most lines are mechanical.

## Questions

Mix the kinds from `SKILL.md` and vary the openings. Not every question starts with "What".

Example questions, for reference only:

- Why does `parseDate` return `null` now instead of throwing?
- Walk me through what happens when a user logs in with an expired token.
- Which of the two pricing options did we keep in the proposal, and why?
- If the retry loop were reverted, what would break?
- Why did the README go from two install commands to one?
- What did you learn about how the migration treats existing rows?

Skip trivia: the author, file names for their own sake, line counts.
