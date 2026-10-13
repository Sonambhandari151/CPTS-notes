# The Raven That Landed Twice — OSINT Writeup

**Category:** OSINT / Aviation Tracking
**Difficulty:** Easy
**Console:** Court-Eaves Console — Aerial Witness Desk (analyst: Miren Vale)



## Challenge Brief

A private jet lands at Crownspire Executive Field the night before three witnesses flip their testimony. The passenger manifest is sealed under a court order, but the technical identifiers weren't — a gate clerk logged the Mode-S hex `43E91C` and the callsign `VLR602`. The job is to prove both identifiers belong to the same airframe and figure out who's really operating it behind the leasing paperwork on file.

Flag format:
```
HTB{REGISTRATION_FROM_DEPARTURE_AERODROME_TO_PARKING_STAND}
```

Four things needed to build it:
1. Aircraft registration
2. Aircraft operator
3. Departure aerodrome
4. Parking stand at Crownspire

![Case briefing](./step1.png)

## Step 1 — Pull the airframe from the Mode-S hex

Every transponder hex code is permanently tied to one physical airframe, so that's the anchor point regardless of what callsign gets broadcast on a given flight. Dropped `43E91C` into the **Aircraft Registry** search bar.

![Aircraft registry search](./step2.png)

Came back with a single hit: a Dassault Falcon 2000EX registered as **2-RUNE**.

## Step 2 — Registered owner vs. actual operator

Scrolled down the same registry entry and this is where the leasing shell shows up:

![Registry detail — owner and operator](./step3.png)

- **Registered owner:** Black Keep Leasing SPC (Isle of Gravenmark) — the paper owner
- **Operator:** **Vaultrune Air Services Ltd** — the outfit actually flying it
- **Known callsigns:** VLR602, VLR600, VRUN2 — three different callsigns tied to one registration, which is exactly the "recorded twice" trick the brief is describing

So Black Keep Leasing is just the registration shell; Vaultrune is who's actually operating the aircraft.

## Step 3 — Movement Ledger for the flight itself

Registry gives ownership, not routing, so switched to the **Movement Ledger** and filtered on `VLR602`.

![Movement ledger — flight row](./step4.png)

One row: movement ID `CSE-2026-0718-0091`, registration `2-RUNE`, departing `SCF`, arriving `CSE`, on-block 2026-07-18 00:51 UTC, stand `4B`.

Expanded the record for the full detail:

![Movement ledger — expanded detail](./step5.png)

- **Departure aerodrome:** Suncourt Field (SCF)
- **Arrival aerodrome:** Crownspire Executive Field (CSE)
- **Parking stand:** 4B
- Passenger manifest still shows as restricted under CCO-2026-0441 — confirms the ledger is the same flight referenced in the brief, just accessible from the ground side instead of the sky side.

Between the registry and the ledger, the Mode-S hex, the callsign, and the registration all cross-reference to the same aircraft — confirming it wasn't two separate flights, just the same one logged twice under different identifiers.

## Step 4 — Confirm findings in Oath Submission

Ran all four answers through the **Oath Submission** module to lock them in:

![Oath submission — empty form](./step6.png)

![Oath submission — all four confirmed](./step7.png)

All four confirmed:
- Registration: `2-RUNE`
- Operator: `Vaultrune Air Services Ltd`
- Departure aerodrome: `Suncourt Field`
- Parking stand: `4B`

Case closes with Miren's line: *"The raven did not land twice. It was described twice — once by the sky, once by the ground."* Which is basically the challenge in one sentence — the same aircraft was on record under its flight callsign in the sky picture and under its tail number on the ground, and lining those two records up is what breaks the sealed-manifest cover story.

## Flag

```
HTB{2-RUNE_FROM_SUNCOURT_FIELD_TO_4B}
```