# whud spec

Working spec and ideation document. Nothing here is final: it records the design, the reasoning and the open questions, and it changes as whud is built.

## Goal

Have an LLM generate questions about your own work, for knowledge retention. The questions are about the substance (what changes, what for, how it works), not about commit metadata. See the [hook README](../whud-hook/README.md#why) for the motivation. Ideas that are not part of this spec yet are in [ideas.md](ideas.md).

## Forms

whud ships in two forms that share one design:

- **Skill (first).** An AI skill that quizzes you in your agent's chat. The quick way. It also settles how good questions are written, which the hook reuses.
- **Hook (second).** A git hook that asks questions after each commit, with its own provider layer and a queue.

The skill is built first. The shared design below applies to both; the sections after it are specific to each form.

## Shared design

### Question design

- Having to reread the material (diff, commit message, chat) to answer is fine. If the user does not remember and has to look it up, that already helps to retain it. What a question must not do is take its answer 1:1 from the material, for example by asking for the commit message again.
- The question types are an open-ended list, not a fixed one, and more are expected to be added. Starting set:
  - conceptual questions
  - design decisions ("why this way and not another?")
  - explaining specific lines of code or passages of text
  - what changes in this commit
  - what is a change for
  - what does `xyz` do
  - what does `xyz` mean
  - what have you learned about `xyz`
  - impact ("what would break if this were removed?")
- The prompt asks for a mix drawn from this list rather than hard-coding a few categories.
- Every question must rest on something verified against its source: the real code for a diff, or what the chat actually said for a session. A session is never fact-checked against the real world.

### The question set is fixed

The full set (wording, options, reference answers) is generated once, before the first question. The questions are then asked in order, and answers never change what is asked next: no follow-ups, no added, dropped or reordered questions. A wrong answer does not adapt anything, except that the right answer is always given afterwards.

Why: a fixed set is predictable, can be generated in a single call, can be queued and asked later (the hook needs that), and keeps results comparable. Adaptive quizzing may be a later experiment, not part of v1.

### Answer modes

- **`choice`:** multiple choice with plausible distractors. The user commits to an answer before anything is revealed. Cheap to check; questions, options and the correct answer come from the same LLM call. Guessing is possible, but having to decide still engages more than reading.
- **`free`:** the user types an answer, then the tool shows a reference answer and the user marks themselves right or wrong (like Anki). It forces recall and needs no second LLM call.
- **`graded`:** the user types an answer and the LLM judges it against the reference answer. Most objective feedback, but the judgement can be wrong. In the hook it costs a second LLM call.

In every mode, a wrong or partly right answer is followed by the right answer. The skill defaults to `graded` (the agent is already in the conversation, so judging is free); the hook defaults to `free`.

### Question count

- `max_questions` is configurable, default 7.
- The actual number is between 1 and `max_questions`, scaled by the size of the material (changed lines and files for a diff). In the hook it is computed before the LLM call, which is told how many questions to produce. In the skill the agent decides from a size table in the source's reference file.
- Trivial material gets no questions.
- Scaling by the complexity of the change would be better but is hard to measure. It is a possible later refinement, not part of v1.

### Privacy notice

Making sure the material contains no secrets or restricted information is the responsibility of the user. If someone uses a model or setup without restrictions (e.g. no enterprise filtering), that is their decision. The tool makes sure they know.

- **Hook:** setup names the configured provider, states that diffs are sent to it, and asks for explicit confirmation (`ack_data_notice`).
- **Skill:** the material stays in the session with the user's own AI provider and goes nowhere else. The skill does not quote secrets in questions or feedback.

## Skill

### Sources

One skill, `whud`, with a source argument. The core rules live in `SKILL.md`; each source has a reference file that the agent reads only when that source is chosen.

- **`diff`:** git changes: a commit, a range or branch, `staged`, `wip` (working tree), or by default the working-tree changes, else the last commit. Noise (lockfiles, generated files) is filtered and the size is measured in changed lines. Reference answers come from the real code.
- **`session`:** the current chat. Independent of code and git: it works for any conversation, even one with no code at all. The chat is the only source and it is not fact-checked: the quiz tests memory and understanding of the chat, not whether the chat was right. Questions ask what was said, concluded or decided, phrased naturally ("What did we conclude about X?", never "What is X?"). Personal wording ("we", "you") is fine in moderation, but questions never refer to the chat itself ("In this chat, ..."). The reference answer is what the conversation established, even if it is wrong in the real world. The skill never corrects the content.

A third source, `code` (quiz about existing code that is not a change), was considered and left out for now.

### Layout and distribution

The repo layout:

```text
whud/                               repo root = plugin root
├── .claude-plugin/                 plugin.json and marketplace.json
├── skills/whud/
│   ├── SKILL.md
│   └── references/{diff,session}.md
└── whud-hook/
```

- A plain Agent Skills folder: any tool that supports the format can load it. Manual install means copying or symlinking `skills/whud/` into the tool's skills folder (`.claude/skills/`, `.github/skills/`, `.agents/skills/`).
- `npx skills add oloose/whud` finds the skill (it checks `skills/` first).
- Claude Code plugin: the repo root is the plugin root, and the repo is its own marketplace (`whud`): `.claude-plugin/marketplace.json` lists the `whud` plugin with `source: "./"`. Install with `/plugin marketplace add oloose/whud` and `/plugin install whud@whud`.
- Claude (claude.ai and the desktop app) and ChatGPT take a zipped skill folder through their settings, no terminal needed.
- A native Codex plugin is not planned for now. Its manifest takes a single `skills` path and drops symlinks on install, so `npx skills add` is the Codex route.

### Invocation

- **Default:** the agent may start whud on its own when the user asks to be quizzed in natural language ("quiz me on this commit"). The description is narrow ("only when the user explicitly asks") to limit accidental runs.
- **Option:** add `disable-model-invocation: true` to the `SKILL.md` frontmatter so only an explicit `/whud` starts it. Stricter, but it can stop natural-language triggers in some tools. Not set by default. Check how each tool treats the key before relying on it.

### Differences from the hook

- No provider layer, no authentication, no queue, no config file: the agent and the chat do all of that.
- Settings are arguments (`choice | free | graded`, `max=<n>`), not stored configuration.
- Results are not stored. A summary is shown at the end of the quiz.

## Hook

A git hook that calls an LLM around a commit to generate the questions.

### Hook types

- **`post-commit` (default).** Runs after the commit, so a slow or failed API call never blocks it. Reads `git show HEAD` and sends the diff to the model.
- **Pre-commit mode (optional).** For when you want to be asked questions *before* the commit goes through.

### Providers

First-class providers, behind a common provider interface:

- Ollama (local models)
- Claude
- Copilot

Others can be added later.

### Authentication

Where possible, do not authenticate with API keys. Prefer existing login sessions by calling the CLI of the respective tool that the user is already logged into. Ollama needs no auth at all. API keys remain a fallback. How exactly this works for Claude and Copilot is still to be worked out (see open questions).

### Timing and queue

Asking questions needs a terminal. A git hook is a child process of `git`, and it only has a keyboard and screen if git was started from a shell. GUI clients, IDE commit buttons, AI agents and CI have none.

- The installed shim reattaches `/dev/tty` when one is available.
- **Ask now, queue as fallback (default).** If a terminal is available, ask right after the commit. If not, or if the user skips, put the questions in a queue and ask later with `whud-hook quiz`.
- Config `ask_now = false` makes the hook only generate and queue.
- The queue is the essential piece of storage. It also enables re-asking wrong answers later (spaced repetition), weak-area stats, and keeping results of `graded` answers. Anything beyond the queue is optional.

### Config

TOML, so setup choices can be changed without reinstalling the hook. A global `~/.config/whud-hook/config.toml` holds defaults; an optional per-repo `.whud-hook.toml` overrides them. The setup wizard writes it.

```toml
provider = "ollama"          # ollama | claude | copilot
model = "llama3.1"
mode = "free"                # choice | free | graded
max_questions = 7            # actual count is 1..max, scaled by diff size
trigger = "post-commit"      # or "pre-commit"
ask_now = true               # false = only queue, quiz later
skip = ["*.lock", "dist/**"] # ignored paths
min_diff_lines = 10          # skip trivial commits
ack_data_notice = true       # user confirmed diffs go to this provider
```

### Interactive setup

`whud-hook init` is a guided wizard. The idea comes from the interactive install/setup of the "grillme" skill in [mattpocock/skills](https://github.com/mattpocock/skills), which is nice to use. (Not reviewed yet; the details to copy still need to be pinned down.)

Steps:

1. Detect available providers (Ollama running, `claude` and Copilot CLIs present and logged in).
2. Choose provider and model.
3. Choose answer mode.
4. Set `max_questions`.
5. Choose timing (ask now or queue only, post-commit or pre-commit).
6. Show the privacy notice and ask for confirmation.
7. Install the hook, backing up an existing one.
8. Optionally run a test on the last commit.

Every step also has a CLI flag so setup can be scripted non-interactively.

### Distribution

A Python package installed with uv:

```sh
uv tool install git+https://github.com/oloose/whud#subdirectory=whud-hook
whud-hook init      # inside a repo
```

- **Alternative:** a single-file PEP 723 script run with `uv run --script` (lighter).
- **Also considered:** a curl-piped install script, and the `pre-commit` framework.
- **Python needed?** No. uv is a standalone binary that downloads Python itself, so users only need uv, git, and a model or credentials.

### Split of responsibilities

- The logic lives in Python: SDK calls, JSON, diff filtering, config.
- The installed hook is a tiny `sh` shim. It works on every platform (including Windows via Git for Windows) and exits silently if `whud-hook` isn't installed.
- Upgrading the tool never requires touching installed hooks.

### Good practices

- Pin installs to a tag.
- Back up existing hooks on install and provide an `uninstall` command.
- Truncate large diffs; skip lockfiles, merge commits and trivial commits.
- Consider a `whud-hook review` command for spaced repetition over the queue.

## Open questions

- **Prompt design:** how to get questions that need real understanding rather than rereading the material, and good multiple-choice distractors. A first draft of the rules is in the skill's `SKILL.md`; it still has to be tried on real commits and sessions, and then reused for the hook.
- Exact storage location and format of the queue and results (hook).
- How authentication works in practice for Claude and Copilot without API keys (hook).
- Pre-commit UX when no terminal is available (hook).
- Hook: share the question prompt with the skill (one file read by both) or keep two copies.
- Package and CLI naming.
- Whether the skill should ever store results (for example a local log), or stay stateless.
- **Skill defaults (mode, max questions):** the Claude plugin manifest has a `userConfig` section that prompts for values when the plugin is enabled and substitutes them into skill content as `${user_config.KEY}` (non-sensitive values only). It is Claude Code only: other tools, and a plain `npx skills add` install, would show the literal placeholder text. Options are a portable config file shared with the hook (same keys as the hook's TOML, read by the agent if present), `userConfig` as a Claude-only convenience, or arguments only (current state). Leaning towards arguments only until defaults become annoying.
