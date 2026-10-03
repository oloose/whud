# whud

**W**hat **h**ave **y**ou **d**one?! Quiz yourself on what your AI just did. whud has an LLM ask you questions about your own changes or chats, so you keep understanding what you ship instead of only skimming it.

AI writes a lot of the code. Reviewing a diff feels like understanding, but reading is passive. whud makes you retrieve and explain the change from your own head, which is what makes it stick.

## What whud is not

whud does not ask whether something is true, correct or good. It asks about the context: what was done, what was decided, and why. It tests whether you remember and understand your own work and conversations, not whether they were right. If you in a chat concluded that the Earth is flat, whud asks "what did the chat conclude about the shape of the Earth?" and the right answer is "flat". It never corrects you, grades the quality of your decisions, or acts as a fact-checker or code review.

## Forms

whud comes in two forms:

| Form | What it is | Status |
| --- | --- | --- |
| [`skills/whud`](skills/whud/) | An AI skill. Run `/whud diff` or `/whud session` in your agent and get quizzed in the chat. The quick way. | scaffolded |
| [`whud-hook`](whud-hook/) | A git hook that asks questions after each commit. | spec |

## The skill

One skill, several sources. The core rules (what makes a good question, the answer modes, how many questions) live in [`SKILL.md`](skills/whud/SKILL.md). What gets quizzed is defined per source in `references/`:

- [`diff`](skills/whud/references/diff.md): git changes (a commit, a range, staged or working-tree changes).
- [`session`](skills/whud/references/session.md): what was discussed and decided in the current chat. Independent of code, so it works for any conversation.

The questions are generated once and asked in order, without adapting to your answers. If you get one wrong, you are told the right answer.

Answer modes: `choice` (multiple choice), `free` (type an answer, then grade yourself) and `graded` (the agent judges your answer). The default is `graded`.

The skill is a plain Agent Skills folder, so any tool that supports the format can load it. `.claude-plugin/plugin.json` additionally makes this repo installable as a Claude plugin.

### Install

Pick the route that fits your tool. All of them install the same `skills/whud/` folder.

**1. Claude Code plugin.** The repo is its own marketplace. Inside Claude Code:

```text
/plugin marketplace add oloose/whud
/plugin install whud@whud
```

You get updates when you update the marketplace. Run `/whud diff` or `/whud session`.

**2. `npx skills` (most coding agents).** Copies the skill into your project, where you can edit it. It asks which agents to install for and writes to each agent's own skills folder:

```sh
npx skills@latest add oloose/whud
```

**3. Manual copy.** Copy the `skills/whud/` folder (the whole folder, not just `SKILL.md`) into your tool's skills folder:

| Tool | Project | User |
| --- | --- | --- |
| Claude Code | `.claude/skills/` | `~/.claude/skills/` |
| VS Code / GitHub Copilot | `.github/skills/` | `~/.copilot/skills/` |
| Codex | `.agents/skills/` | `~/.agents/skills/` |

### Tools that support skills

whud is a standard [Agent Skills](https://agentskills.io) folder, so it should work in any tool on [that list](https://agentskills.io/clients). Not all of them have been tried.

| Kind | Tools |
| --- | --- |
| Coding agents and editors | Claude Code, VS Code with GitHub Copilot, Codex, Cursor, Gemini CLI, JetBrains Junie, Kiro, Goose, OpenCode, Roo Code |
| For everyone, no code needed | Claude (claude.ai, desktop and mobile apps), ChatGPT |
| Personal assistants | Hermes Agent, OpenClaw |

The `session` source needs no code, no git and no repository, so it is the one for non-engineers: quiz yourself on any chat, whatever it was about.

## Design notes

The design, reasoning and open questions are in the [spec](docs/spec.md). Ideas for further skills and sources are in [ideas](docs/ideas.md).

## License

MIT, see [LICENSE](LICENSE).
