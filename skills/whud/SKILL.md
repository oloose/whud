---
name: whud
description: Ask the user questions about their changes, code diffs or the current chat session, to test whether they remember and understand them. Use when the user asks to be quizzed, or says "whud".
---

# whud

Ask the user questions about changes, code diffs or the current chat session.
Ask about the context: what was done, what was decided, why. Never judge whether something is true, correct or good. Never correct the user.
This is not a commit-message or changelog generator, not a code review. Never ask "describe this commit".

## Source

Read the file for the chosen source:

- `diff`, a commit, a range or a branch: `references/diff.md`
- `session`, "this chat": `references/session.md`
- None given: `diff` if you are in a git repository with changes or a recent commit, otherwise ask.

## Arguments

- Mode: `choice`, `free` or `graded`. Default `graded`.
- `max=<n>`: most questions to ask. Default 7.

## Steps

1. Gather the material as the source file says.
2. Ground questions in material and check it as the source file says. Never ask about something the material does not back up. The material is the only judge of the right answer, not your own knowledge of the topic.
3. Skip what is not worth asking about. If nothing substantial is left, say so and stop.
4. Pick the number of questions from the size of the material, never above the max:

   | Size | Questions |
   | --- | --- |
   | trivial | 0 |
   | small | 1-2 |
   | medium | 3-4 |
   | large | 5-6 |
   | very large | max |

5. Write the full set once: each question, its options (`choice`) and its reference answer.
6. Tell the user the source, the mode and the number of questions in one line.
7. Ask one question at a time. Wait for the answer. Give feedback. Ask the next.

## Questions

Aim for understanding, not recall of wording. Mix kinds from this open-ended list, and add others where they fit:

- **Behaviour:** what does `xyz` do, in which cases?
- **Meaning:** what does `xyz` mean?
- **Purpose:** what problem does this solve?
- **Decision:** why this way and not another?
- **Impact:** what would break if this were removed or different?
- **Explain:** walk me through this block or paragraph.
- **Concept:** the general idea behind it.
- **Learned:** what have you learned about `xyz`?

Rules:

- The user may look things up to answer. Fine.
- Do not take the answer 1:1 from the material. Do not ask for a commit message or a chat line word for word.
- Do not paste the passage that answers the question. Name the thing and let them recall.
- Ask directly about the content and phrase it naturally, like a person would.
- Personal wording ("we", "you") is fine, but do not overdo it. 
- Do not refer to the chat or commit itself ("in this chat", "in this commit").
- Make each question specific enough to have a checkable answer.
- Go from broad (what, why) to narrow (details).
- Take reference answers from the material only, never from your own knowledge.

Example questions, for reference only:

- Why did we put the budget talk before the schedule in the project plan?
- Which of the three slogans did you pick, and what put it ahead of the others?
- Walk me through what happens when the sync fails halfway.
- If the villain had never found the map, how would the story be different?
- Can you explain in your own words what "compound interest" meant in our example?
- What is your main takeaway from our talk about renting versus buying?

## The set is fixed

- Never add, drop, reword or reorder questions based on answers. No follow-ups.
- Keep reference answers hidden until the user has answered.
- If you lost a reference answer, derive it again from the material.

## Modes

- `choice`: give options A-D. Make every wrong option plausible, each a real misconception or a wrong-but-resonable design, no obvious nonsense. Randomise the position of the right one.
- `free`: the user types an answer. Show the reference answer. The user marks themselves right, partly right or wrong.
- `graded`: the user types an answer. Judge it against the reference answer: right, partly right (say what is missing) or wrong (say what is actually true). Be fair but do not accept vague hand-waving as correct.

If the answer is wrong or partly right, always give the right answer. Keep feedback to 2-3 sentences.

## Rules

- Stop when the user says stop.
- Do not quote secrets, tokens or credentials. Skip files that look like they hold them.
- Material stays in session, do not send it anywhere else.
