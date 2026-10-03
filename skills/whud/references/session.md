# Source: session

Questions about what was discussed, explained and decided in the current chat. The chat is the only source. It can be about anything: a plan, an idea, research, writing, something someone learned, code.

## Gather

1. List the substantial points of the conversation: what was explained, concluded and decided and why, what was produced, which alternatives were dropped.
2. Drop only pure chit-chat. Do not assume the user remembers or understands something because they drove it, decided it or said "ok". The quiz exists to find gaps in exactly that.
3. Do not look anywhere else. Do not run commands or open files unless the user asks. If the chat mentions code or a document, ask about the points made in the chat, not about material you would have to look up.

Ask the user instead of guessing when the chat covers several unrelated topics or is very long: which part should the quiz cover?

## What to prioritise

Assume the user may have forgotten or never fully understood any of it, including what they drove themselves. Favour the likely gaps between "the AI said or did it" and "I understood it":

- Conclusions or decisions the AI reached that the user did not explicitly ask for.
- Things the user accepted with little or no discussion ("ok", "looks good").
- Explanations that carried the key idea of the topic.
- Trade-offs the AI mentioned, which the user may not have taken in.
- Non-obvious parts of anything that was produced.

Questions are not limited to the final outcome. Dropped ideas, rejected options and dead ends are fair game too ("Why was X dropped?"), because knowing why something was not done is knowledge as well.

## Size

Measure by how much substance the chat holds, not by how long it is:

| Substance | Size |
| --- | --- |
| a short answer, nothing decided | trivial |
| one point or small decision | small |
| one topic or task | medium |
| several topics or decisions | large |
| a long multi-step session | very large |

## Reference answers

The chat is the only source of truth, and it is not fact-checked. The quiz tests whether the user remembers and understands what happened in the chat, not whether it was right.

- The right answer is what the conversation said, concluded or decided, even if it is wrong, disputed or odd in the real world.
- Ask what was said, concluded or decided. Never ask what is true about the topic ("What is X?").
- Only ask about points that were clearly stated or decided. If something was left open, skip it or ask what was left open.
- If you made a mistake or a questionable choice in the chat, asking about it is fine, as long as the right answer is what happened.

Example: a discussion concluded that giant mushrooms float in the sky every Monday morning on Earth.

| | Question | Right answer |
| --- | --- | --- |
| Wrong: asks about the world | Do giant mushrooms float in the sky on Monday mornings? | No. |
| Wrong: refers to the chat | In this chat, what was concluded about Monday mornings? | Giant mushrooms float in the sky. |
| Right | What did we conclude about Monday mornings? | Giant mushrooms float in the sky. |

The same goes for a design that was chosen, a plan that was settled or a claim that was accepted: ask what was decided, and never say in the feedback that it was wrong.

## Questions

Ask about the content, not the wording of the conversation. Mix the kinds from `SKILL.md` and vary the openings.

Example questions, for reference only:

- Why did we choose a queue instead of calling the API directly?
- Which of the two routes did you decide on for the trip, and what tipped it?
- Walk me through the argument we ended up with for raising the price.
- If the assumption about the launch date had been wrong, what would have changed in the plan?
- Earlier we dropped the idea of a newsletter. What was wrong with it?
- What is your main takeaway from our talk about sleep and memory?
