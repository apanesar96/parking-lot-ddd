# Question Bank

Pick one question and adapt it to the learner's actual code. Never paste a list.

## Before writing a test
- "What's the smallest behaviour you could prove next?"
- "Describe the scenario as a car park attendant would. Now, does your test name say that?"
- "What's the *input*, what's the *observable outcome*? Is the outcome state, a return value, or an event?"
- "Is this test about the domain, or about your implementation?"
- "Which requirement does this move you toward? Is there a simpler one first?"

## ZOMBIES (gap-finding)
- "Which ZOMBIES letter is this test? Which letter comes next?"
- "You've tested one. What does *many* force you to change?"
- "What's the zero case here? Is it even meaningful in the domain?"
- "Where's the boundary? Write the test on each side of it."
- "Which exceptional behaviour does the requirement mention that you haven't tested?"
- "Did the interface you chose in the first test still feel right by the fifth?"

## Red
- "What failure do you expect, and why? Run it. Were you right?"
- "Is it failing because the behaviour is missing, or because it doesn't compile? Which is more useful?"
- "If this test passed by accident tomorrow, would you notice?"

## Green
- "What's the most shameless code that passes?"
- "Did you write anything the test didn't ask for?"
- "Which test would force you to replace this hard-coded value?"

## Refactor
- "What duplication do you see? Is it duplicated *knowledge*, or just similar-looking code?"
- "Which primitive is carrying a rule it can't enforce?"
- "Read the test setup. Is it telling you the domain model is awkward to use?"
- "Is there a word you keep saying out loud that doesn't exist in the code?"
- "Which field here is a conclusion that could be computed?"
- "If you made this immutable, what would break? What does that tell you?"
- "Are your tests coupled to structure (getters, internal lists) or behaviour?"

## Modelling and language
- "What does the business call this? Is that the same thing in every context, e.g. entry vs billing?"
- "Is this an Entity or a Value Object? What question settles it?"
- "Who owns this rule?"
- "What's the invariant, in one sentence?"
- "Can you construct this object in an invalid state? Show me how."

## Computing state
- "Fact or conclusion?"
- "What's the source of truth for this number?"
- "If two fields can disagree, which one is lying?"
- "Could this be a pure function? What would its inputs be?"
- "What would you need to keep to answer this question about *last Tuesday*?"

## Senior engineering
- "What's the cost of this abstraction? Is that cost paid today for a benefit you'll get today?"
- "Which requirement forces this? If none, what would adding it later cost?"
- "What's the blast radius if this decision is wrong?"
- "Would this test survive a refactor that keeps behaviour the same?"
- "Exception, result type, or event: which one tells the caller the truth about this failure?"
- "Two requests hit this at the same moment. What happens?"
- "Where would you stop applying DDD if this were production code with a deadline? Why there?"
- "How would you explain this decision in a PR description?"
- "I'd just use a `bool` here. Convince me."

## Reflection (end of cycle or session)
- "What does the model know now that it didn't before?"
- "Which decision today are you least sure about? Why?"
- "If a new rule arrived tomorrow, where would it land? Is that where you want it?"
- "Which Evans concept did you meet today? Explain it back to me in your own words."
- "What would you do differently if you started this requirement again?"
