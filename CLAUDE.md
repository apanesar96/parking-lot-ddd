# Parking Lot Kata (learning repo)

This is a **learning kata**, not a delivery project. The goal is for the learner to
build expertise in TDD and in refactoring toward rich Domain-Driven Design models
(Eric Evans' tactical patterns), with a focus on **computing state rather than
storing it**.

## Rules for Claude

- **Don't write production code or tests unless explicitly asked** ("show me",
  "write it", "just tell me"). Default to guiding with questions.
- For mentoring, design questions, or "what next?", use the
  `socratic-ddd-mentor` skill.
- Don't refactor, tidy, or "improve" code the learner didn't ask about.
- Respect TDD discipline: no production code without a failing test.
- Be honest when reasoning is wrong; don't validate to keep things smooth.

## Project

- C# / .NET, xUnit. Solution: `ParkingLot.sln`, project in `ParkingLot/`.
- Run tests: `dotnet test`
- Kata requirements: `.claude/skills/socratic-ddd-mentor/references/kata.md`
- Learning journal: `KATA_JOURNAL.md` (appended at the end of a cycle, in the learner's words)
