# DDD Progression: Smells → Pressure → Pattern

Use this to diagnose where the learner is and what question applies the right
pressure. The learner should find the pattern; this file tells *you* what to look for.

## 1. Primitive obsession → Value Objects

**Smells:** `int`, `string`, `decimal`, `DateTime` passed around raw. Validation
repeated in several places. Parameters that are easy to swap (`Park(string plate, string bay)`).

**Pressure questions:**
- "What stops someone passing a bay number where a plate is expected?"
- "Where does the rule 'a plate can't be empty' live? How many places?"
- "Are two £5 charges the *same* £5 or two *equal* £5s? Does that matter?"

**Evans' tests for a Value Object:** no conceptual identity; described by its attributes;
immutable; replaceable as a whole; side-effect-free behaviour; validates itself on construction.
C# hints (only if asked): `record` / `readonly record struct`, a private constructor plus a factory.

## 2. Anaemic objects → Behaviour-rich Entities

**Smells:** Public setters. Classes that are only bags of properties. Logic in a
"manager"/"service"/test that reaches in and mutates fields (`lot.Spaces--`).

**Pressure questions:**
- "Who is allowed to change `Spaces`? Who *could* change it today?"
- "If this rule changed, how many files would you touch?"
- "Tell, don't ask: what would you *tell* the lot to do instead of asking for its data?"
- "Does this thing stay *the same thing* while its attributes change? How do you know?"

## 3. Scattered rules → Aggregates and invariants

**Smells:** Two objects that must stay consistent but can be changed separately.
Collections exposed as `List<T>` that callers can `.Add` to. Invariants checked by the caller.

**Pressure questions:**
- "State the rule in one sentence. Which object is responsible for it always being true?"
- "Can I break that rule from outside without going through that object?"
- "What's the smallest cluster of objects that has to change together to keep the rule true?"
- "Should a `Bay` reference a `Vehicle` object, or just its identity? What changes if it holds the identity?"

**Evans:** an aggregate is a consistency boundary; external objects hold references to
the root only; invariants hold at the end of every operation on the root.

## 4. Stored state → Computed state (core theme)

**Smells:**
- Flags that duplicate other data: `IsFull`, `AvailableSpaces`, `TotalFee`, `Status`.
- Code that updates two fields in tandem (`occupied++; available--;`).
- A `Status` enum plus timestamps that could disagree (`Status = Exited` but `ExitTime == null`).
- Fees calculated and stored at entry, then patched later.

**Pressure questions:**
- "Is this a fact that happened, or a conclusion derived from facts?"
- "Write a test that leaves `available` and `occupied` disagreeing. Can you? Should you be able to?"
- "If `ExitTime` exists, is a separate `Status` telling you anything new?"
- "Could the fee be a pure function of (entry, exit, tariff)? What does that buy you in tests?"
- "If you stored only `VehicleParked` and `VehicleLeft` events, how would you compute occupancy? Is that a left fold?"
- "Where's the cost of computing? Is it a real problem *today*, or a premature worry?"

**Progression of sophistication:**
1. Stored counter → derived property (`Occupied => _sessions.Count(s => s.IsActive)`).
2. Mutable status → state from facts (a session is active iff it has no exit time), or better, distinct types per state (`ActiveSession` / `CompletedSession`) so illegal states can't be represented.
3. Snapshot state → event history + `Apply`/fold (event-sourced aggregate). Only go here if the learner is ready and the kata gives a reason (audit, history, reporting).

## 5. Logic with no home → Domain Services / Policies

**Smells:** A calculation that needs several aggregates, or a rule that varies
(pricing tiers, vehicle-size compatibility) and is bolted onto an entity with `if`/`switch`.

**Pressure questions:**
- "Does this rule belong to the Bay, the Vehicle, or the relationship between them?"
- "If the business ran a weekend discount, what would change? Can you make that the *only* thing that changes?"
- "Is this a noun the business talks about? ('tariff', 'pricing policy')"

**Evans:** a Domain Service is stateless, named in the ubiquitous language, and its interface is
defined in domain terms. Strategy/Policy pattern for varying rules.

## 6. Things that happened → Domain Events

**Smells:** Return values or flags added only so that "someone else" can react.
Requirements that mention history, audit, notifications, or "when X happens".

**Pressure questions:**
- "Phrase it in the past tense: what *happened*?"
- "Who cares that it happened? Should the aggregate know about them?"

## 7. Supple Design checklist (for Review mode)

- **Intention-revealing interfaces:** can you understand a method from its name and signature without reading it?
- **Side-effect-free functions:** are queries and commands separated? Do calculations return new values?
- **Assertions:** are post-conditions and invariants stated (in tests or guard clauses)?
- **Conceptual contours:** do changes tend to land in one place?
- **Standalone classes:** how many other concepts must you hold in your head to understand this one?
- **Closure of operations:** does `Money + Money` return `Money`? Does `Duration` combine with `Duration`?
- **Make illegal states unrepresentable:** can the type system reject the bug instead of a test?
