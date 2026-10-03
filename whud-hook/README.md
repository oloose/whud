# whud-hook

**W**hat **h**ave **y**ou **d**one?! A git hook that calls an LLM after (or optionally before) each commit and asks you questions about your changes, so you actually retain what you built.

**Status:** planned. See the [whud spec](../docs/spec.md). The hook is one of two forms of [whud](../README.md); the other is an AI skill.

## Why

AI now writes most of my code and text. That is great for speed, but it has a side effect: I can ship a change without ever having to think it through, and a week later I only half remember what it does or why it exists. Reviewing a diff feels like understanding, but reading is passive.

whud-hook makes me do the thinking after the fact. After a commit, an LLM looks at the change and asks me questions about it, and I have to answer them from my own head. The bet is that having to retrieve and explain something is what makes it stick. Answering correctly takes more mental effort than skimming, and that effort is what should lead to better memorization and understanding. (This is the idea behind retrieval practice: being tested on material helps you keep it better than re-reading it does.)

### What the questions are about

The goal is **knowledge, not commit metadata**. whud-hook is not a tool that asks "describe this commit" to generate a commit message or changelog.

It asks about the *substance* of the change. There are many possible kinds of questions, and the list will grow. For example:

- What actually changes in this commit?
- What is that change for? What problem does it solve?
- What does `xyz` do? How does this function/module/config behave?
- Why was it done this way rather than another way?
- What would break if this part were removed or changed?

Questions test understanding, not wording: they never just ask me to repeat the diff or the commit message. Looking something up to answer is fine, because not remembering it is exactly the gap. Getting one wrong is useful: it shows me what I don't understand yet.

### What it is not

- Not a commit-message generator.
- Not a gate that stops me from working. The default mode runs after the commit, so it never blocks.
- Not a replacement for code review. It is for my own understanding.

## Modes

You choose how you answer during setup, and can change it later:

- **`choice`:** multiple choice.
- **`free`:** type an answer, then compare it with a reference answer and grade yourself.
- **`graded`:** type an answer and let an LLM judge it.

The number of questions scales with the size of the commit, up to a configurable maximum (default 7).

## Privacy

Diffs are sent to the model provider you configure, unless it runs locally (Ollama). Making sure they contain no secrets or restricted information is your responsibility. Setup tells you which provider is used and asks you to confirm.

## Planned usage

```sh
uv tool install git+https://github.com/oloose/whud#subdirectory=whud-hook
cd your-repo
whud-hook init
```

After that, each commit gets a few questions about the change. Models are pluggable (Claude, Copilot, local models via Ollama, ...).
