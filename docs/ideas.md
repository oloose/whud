# whud ideas

Ideas that are not part of the [spec](spec.md). None of them is decided, built or tested.

## More sources for the `whud` skill

Cheap: each is a new file in `references/`, no new skill.

- **`code`:** quiz on existing code that is not a change (considered and left out of v1).
- **`doc`:** quiz on a document, article or PDF you read. For non-engineers: "did I understand what I read?"
- **`pr`:** quiz on a pull request you are about to review or merge.
- **`diff since:<date>`:** a weekly quiz over your recent commits. Likely a variant of `diff`, not a new source.

## Separate skills

Only worth a separate skill where the behaviour really differs from the quiz.

- **`whud-review`:** re-ask the questions you got wrong, as spaced repetition. Needs a stored queue and results, so it depends on the open storage question in the spec.
- **`whud-export`:** export a quiz as Anki cards or Markdown flashcards.
- **`whud-recap`:** write a short "what we did and decided" note from a session or diff. Not a quiz, but it serves the same need and helps with handoffs.

## Other forms

- The git hook (see the spec).
- A Claude Code hook that nudges you to run `/whud` after a long agent run, or after a number of commits.
- A GitHub Action that asks the PR author questions as a comment.

## Defaults and storage

- Skill defaults (mode, max questions) through a config file shared with the hook, or Claude's `userConfig`. See the open question in the spec.
- Results history and weak-area stats, once there is somewhere to store them.

## Distribution

- Submit to the Claude plugin directory (needs a README with usage examples, a valid `plugin.json`, an open-source license and a `CODEOWNERS` file).
- Open pull requests against awesome-agent-skills lists.
- A skills.sh listing appears through installs with `npx skills add`.
