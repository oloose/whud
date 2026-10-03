# whud

**W**hat **h**ave **y**ou **d**one?! Quiz yourself on what your AI just did and keep understanding what you create.

whud is an AI skill that asks you questions about your own work or about a chat whether conversations, discussions, learning sessions or code and text changes in your repository. Answering from memory is what makes it stick. Reading is passive.

## Install

In Claude Code:

```text
/plugin install whud --marketplace oloose/whud
```

In most other agents (Copilot, Codex, Cursor, Gemini CLI and more):

```sh
npx skills@latest add oloose/whud
```

Then run `/whud` in your agent.

More ways to install:

- **Copy it:** put the whole `skills/whud/` folder into your tool's skills folder, for example `.claude/skills/`, `.github/skills/` or `.agents/skills/`.
- **Upload it:** zip the `whud` folder and add it in the skills settings of Claude (claude.ai, desktop and mobile apps) or ChatGPT. No terminal needed.

## Use it

- `/whud session`: questions about the current chat, whatever it was about. No code or git needed.
- `/whud diff`: questions about git changes, such as a commit, a range or staged changes.
- Options: a mode (`choice`, `free`, `graded`) and `max=<n>` questions, for example `/whud session choice max=3`.

## How it works

- Questions are generated once and asked in order. Your answers never change what comes next.
- If an answer is wrong, you are told the right one.
- The number of questions scales with how much there is to ask about (7 at most by default).
- Answer modes:
  - `choice`: multiple choice
  - `free`: type an answer, then grade yourself
  - `graded`: type an answer and the agent judges it (default)

## What whud is not

whud asks about the context: what was done, what was decided and why. It never asks whether something is true, correct or good, and it never corrects you. It is not a fact-checker or a code review.

For example, if a brainstorm ended with "giant mushrooms float in the sky every Monday morning on Earth", whud asks "What was concluded about Monday mornings?" and the right answer is "giant mushrooms float in the sky".

## Works with

whud is a standard [Agent Skills](https://agentskills.io) folder, so it should work in any tool on [that list](https://agentskills.io/clients). Not all of them have been tried.

- **Everyone, no code needed:** Claude (claude.ai, desktop and mobile apps), ChatGPT
- **Coding agents and editors:** Claude Code, VS Code with GitHub Copilot, Codex, Cursor, Gemini CLI, JetBrains Junie, Kiro, Goose, OpenCode, Roo Code
- **Personal assistants:** Hermes Agent, OpenClaw

## Forms

- **Skill** (this repo): the quick way, in your agent's chat.
- **[Git hook](whud-hook/)** (planned): asks questions after each commit.

Design, reasoning and open questions are in the [spec](docs/spec.md). Ideas for more skills and sources are in [ideas](docs/ideas.md).

## License

MIT, see [LICENSE](LICENSE).




