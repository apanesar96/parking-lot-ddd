# Parking Lot Kata: Requirements

## Requirements (as given)

1. System supports three vehicle types: Motorcycle, Car, Large Vehicle.
2. When a vehicle enters, the system automatically assigns an available compatible spot.
3. System issues a ticket at entry.
4. When a vehicle exits, the user provides the ticket ID:
   - System validates the ticket.
   - Calculates the fee based on time spent (hourly, rounded up).
   - Frees the spot for next use.
5. Pricing is hourly, with the same rate for all vehicles.
6. System rejects entry if no compatible spot is available.
7. System rejects exit if the ticket is invalid or already used.

**Out of scope:** payment processing, gate hardware, cameras/monitoring, UI/displays,
reservations/pre-booking. If the learner drifts toward these, ask whether that's
domain or infrastructure.

## Suggested TDD order

Don't hand this list over. Use it to propose "the smallest next behaviour" when
the learner asks what's next, and let them argue for a different order. Arguing
about ordering is useful too.

| # | Behaviour slice | Covers | Design pressure it should create |
|---|---|---|---|
| 1 | A car enters an empty lot with one spot and gets a ticket | R2, R3 | First names: what is a "ticket", a "spot"? Resist building types yet. |
| 2 | A car is rejected when the only spot is taken | R6 | Where does "taken" live? First chance for a stored `IsOccupied` flag, so let them add it if they want. |
| 3 | A car exits with its ticket and the spot is free again | R4 (frees) | The flag must now be kept in sync with tickets. Ask: "Fact or conclusion?" |
| 4 | Exiting with an unknown ticket ID is rejected | R4 (validates), R7 | Ticket identity, so Entity. What is a ticket ID: `Guid`, `string`, or a `TicketId` Value Object? |
| 5 | Exiting twice with the same ticket is rejected | R7 | Ticket lifecycle. `IsUsed` flag vs. "has an exit time" vs. distinct types for active and completed tickets. Strong computed-state moment. |
| 6 | Fee for 1 hour, then 2 hours | R4 (fee), R5 | Time enters the domain. How does the test control "now"? (`TimeProvider` / an injected clock) |
| 7 | Fee rounds up: 61 minutes is 2 hours, and 0 minutes is…? | R4 (rounding) | Edge cases need a domain decision. `Duration` / `BillableHours` / `Money` Value Objects. Fee as a pure function of (entry, exit, rate). |
| 8 | Vehicle types exist; a motorcycle enters | R1 | Enum vs. types. Is `VehicleType` a Value Object? Does the vehicle even need identity here? |
| 9 | Compatibility: a large vehicle rejected when only small spots are free | R2, R6 | Where do compatibility rules live: in the spot, the vehicle, or a policy? Spot sizes. |
| 10 | Assignment picks a compatible spot among several | R2 | The allocation rule. A domain service or an aggregate method? What's the aggregate boundary? |

### ZOMBIES map for this kata (for the mentor)

| Letter | Kata examples |
|---|---|
| **Z** | Lot with zero spots rejects entry; 0 minutes parked (charged 0 or 1 hour?); no tickets issued yet |
| **O** | One spot, one car, one ticket; exactly 1 hour's fee |
| **M** | Several spots, so assignment must choose; several tickets, so lookup by ID; multiple hours |
| **B** | Last free spot taken; 59 / 60 / 61 minutes; only incompatible spots free |
| **I** | `Enter(vehicle)` returns what: a ticket, a result, an ID? `Exit(ticketId)` returns what: a fee, a receipt? |
| **E** | No compatible spot (R6); unknown ticket and used ticket (R7) |
| **S** | Start with one vehicle type and one spot. Add types and compatibility last. |

## Ambiguities the learner must resolve

These are deliberate. **Don't resolve them yourself.** When the learner hits one,
ask "What would you ask the domain expert?", then play the expert if they want an answer.

- **Compatibility matrix:** can a motorcycle use a car spot? Can a car use a large spot?
  Are spots typed at all, or is capacity per vehicle type?
- **Rounding at the boundary:** is 0 minutes charged as 0 or 1 hour? Is exactly 60:00 one hour or two?
- **Assignment preference:** when several spots are compatible, which one? Smallest that fits (to save
  large spots), or the first available?
- **What "invalid ticket" means:** unknown ID only? A ticket from another lot?
- **The rate itself:** a constant, configuration, or a domain concept (`Tariff`)?
- **Same vehicle twice:** can a vehicle that's already parked enter again? (Not in the requirements. Ask whether it matters.)

## Computed-state opportunities in this kata

Keep coming back to these:

- **Spot availability:** `Spot.IsOccupied` stored vs. derived from "is there an active ticket for this spot?"
- **Lot is full / free count:** stored counters vs. computed from spots and tickets.
- **Ticket used:** `IsUsed` bool vs. `ExitedAt != null` vs. `ActiveTicket` → `CompletedTicket` types.
- **Fee:** stored at exit vs. computed from (entry time, exit time, tariff), and never stored at entry.
- **History:** if they stored only `VehicleEntered` and `VehicleExited` facts, could they derive everything above?

## Requirement changes for "Challenge me" mode

Introduce these only after the base requirements pass, one at a time, and each
only when the design looks settled. Each strains a specific design decision.

| Change request (as the domain expert) | What it strains |
|---|---|
| "Large vehicles now pay double the rate." | Breaks R5. Is pricing isolated in one place, a `PricingPolicy` or `Tariff`? |
| "First 15 minutes are free." | Rounding logic. Is the fee a pure, well-tested function? |
| "Daily cap: never charge more than 8 hours." | Closure of operations on `Money` / `BillableHours`. |
| "Lost ticket: charge a flat fee by plate." | Ticket vs. vehicle identity. Do you store the plate? |
| "We're adding a second floor." | Aggregate boundary: one lot aggregate, or one per level? |
| "Management wants to know peak occupancy yesterday." | Snapshot state can't answer it. Pressure toward keeping events. |
| "Motorcycles can share a car spot, up to 2 per spot." | Spot capacity as a concept, not a boolean. Does `IsOccupied` survive? |
