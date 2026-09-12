# Aura Tesla Opt — abstract and full operation prediction

**Device:** Aura Tesla Opt — DIN-rail home energy hub (27 BOM rows, $1,072) · **Agent:** Aura (local LLM, resident)
**Status:** hardware design closed at round 5 (pending: guide CT steps, visual regen, schematic edges)

---

## Abstract

Aura Tesla Opt is a wall-mounted, DIN-rail energy hub that turns a European household's largest loads into a scheduleable resource. A certified Shelly Pro 3EM meters all three phases through CT clamps; four certified Shelly Pro 1PM switches (16 A each) control the water heater, wall heater, EV charger and an auxiliary circuit; a fanless N100-class mini-PC hosts **Aura**, a resident agent built on a local LLM whose proposals pass a deterministic safety policy before anything is switched. The electronics sit on a 24 V DC bus backed by a 240 Wh LiFePO4 pack — roughly 19 h of host ride-through — with a DC-DC converter feeding the 12 V host and network switch. Aura runs a closed loop: hourly planning ticks against live telemetry and tariff windows, nightly deterministic habit extraction, and a weekly governed fine-tuning cycle that bakes accumulated human feedback into the model behind an evaluation gate with one-command rollback. The model proposes, policy disposes, the outbox executes — and every decision lands in a journal the owner can read. Predicted outcome: no new behaviour required from the household; 150–400 €/yr of scenario savings from load shifting in year one, rising as habit profiles and adapter quality mature.

---

## 1. How the hardware works — three physical planes

**Energy plane (mains, isolated).** Mains enters through an ABB DS201 RCBO (16 A, 30 mA Type A) — one device does overcurrent and earth-leakage protection. Behind it sit finger-safe DIN terminal blocks feeding: the 24 V power supply, the Shelly Pro 3EM's voltage reference, and each Shelly Pro 1PM's L/N input. Each Shelly's switched output returns to the terminal block on its dedicated load line (SW1–SW4), so every load keeps its own breaker in the main distribution board and the hub switches downstream of it. The three CT clamps clamp the incoming phases within 1 m of the meter, arrow toward the load. Nothing in the hub generates or modifies a load current — it only breaks and makes certified paths.

**Compute plane (24 V SELV).** Mean Well HDR-100-24 rectifies to a 24 V bus; the DR-UPS40 floats the LiTime 24 V 10 Ah LiFePO4 (240 Wh) on that bus and hands load output to a fused terminal (4 A) and the DDR-60G-12 converter, which makes the 12 V rail for the MeLE Quieter3Q (~6–10 W idle, ~25 W peak) and the 8-port DIN switch. Predicted ride-through: ≈19 h at idle draw, ≈8 h at full load, from 80% useable DoD — mains can die and the agent keeps observing, planning and journaling until it shuts down cleanly at ~20% state of charge.

**Control plane (data only).** The PC touches nothing electrically: one Ethernet cable to the switch, plus links to the 3EM, the four Shelly 1PMs, and out to the tailnet. The switched loads are commanded purely over LAN (HTTP/MQTT). There is no GPIO, no coil wire, no soldering anywhere in the design.

---

## 2. How the software works — the hourly loop

The 3EM reports whole-house power every few seconds into SQLite samples; hourly rollups compress history. Once an hour, **Aura's tick** runs five steps:

1. **Observe** — tariff window, next-24 h prices, yesterday's rollups, current load states, and a compact habit digest (top-N hours per load, occupancy windows; ≤800 tokens, enforced by test).
2. **Propose** — the local LLM (1–3B Q4 on the CPU tier, larger on GPU tiers) emits a structured JSON plan: per-load on/off/keep, with a plain-language `why` for each action. It has no credentials, no device addresses, no network tools.
3. **Validate** — deterministic `skill_policy` checks every proposal: inside a tariff/safety window, load known and rated, never fail-safe off, within per-relay 3.68 kW contract, well-formed action. Rejections are recorded with reasons.
4. **Decide + queue** — acceptance writes the decision and its command in ONE transaction (an injected failure produces neither row).
5. **Execute** — the outbox drainer POSTs to the Shelly modules, retries 3×, marks failures visibly. The model can never touch a device; only the drainer speaks to hardware.

Twice 100% policy rejection in one day → the deterministic v2 planner takes over and a degraded banner appears. Nightly, `habits.py` rebuilds a versioned habit profile purely in SQL — habits never train into the model, they stay auditable rows. A daily journal renders every decision, `why`, and override into dated Markdown.

---

## 3. How it learns — feedback, bake, gate, promote

The household teaches Aura three ways: **overrides** ("hot water off tonight — guests"), **approvals** (one tap on the dashboard/PWA), and **complaints** in the journal. All become feedback rows. Weekly, `build_dataset.py` curates decision/outcome/feedback pairs into JSONL with a hard `data_cutoff` — raw telemetry never trains. A QLoRA bake runs on home hardware (offloaded to a bigger tier if the agent host is CPU-only), producing an adapter. The **eval gate** must pass safety and quality suites in full; a single failure refuses promotion. Promotion flips a partial unique index atomically — only one active adapter ever exists — and `--rollback` restores the previous one in one command. Every step lands a row in `runs`.

Prediction: the first bake adds little (the base model already handles obvious tariff shifts); the value compounds from bake 3 onward, when household-specific patterns — the 02:00–05:00 water-heat habit, leaving-with-EV-charging preferences, "no noisy heater during the work call window" — are in the weights, and the deterministic habit digest shrinks because the model no longer needs to be told.

---

## 4. A predicted day, hour by hour (day 90, habits mature)

- **00:30** Cheap window opens. Aura sees the heater's usual 02:00–05:00 duty, starts the water heater at 01:00 instead — `why`: "water heated inside trough, 6.8 ct/kWh cheaper; expected reheat loss offset." Rollup math is deterministic; only the *choice* came from the model.
- **06:30** Morning ramp; peak tariff. Wall heater held off (occupancy digest: house empty 09:00–16:00). If indoor temperature feedback existed it would weigh more; without sensors, Aura defaults to the conservative choice and explains that.
- **09:15** House empty; EV plugged in. Aura defers charging to the 13:00–15:00 trough and notes the next tick will re-check whether the car is still connected (a stale connection = skip, fail-safe).
- **18:00** Kitchen spike; the 3EM sees 11 kW total. Aura declines to add wall heating; `why` cites the fuse envelope.
- **19:40** Human override: heater off — guests, noise. One row in feedback; plan re-plans on the next tick; journal records "human wins" verbatim.
- **23:00** Peak ends; night plan written for tomorrow, `why`s in plain language, journal closed.
- **02:00 (nightly)** Habit profile v37 built; the week's dataset ticked toward 3,000 pairs; Sunday's bake scheduled.

---

## 5. When things break — predicted failure behaviour

- **Mains outage** → loads die with the mains (Shellies are mains-powered); the host rides the LiFePO4 for up to ~19 h, logs the event, and shuts down cleanly at ~20% SoC. Relays keep their last safe state — loads stay off.
- **LLM down / garbage output** → policy rejects malformed or violating proposals; two straight all-reject days engage the deterministic planner with a visible banner. Never a silent fallback.
- **Network broken** → the outbox retries 3×, then marks commands failed; the dashboard shows them. No buffered actuation waiting for anyone. Remote access (Tailscale / `aura serve --tailscale`) is observability only — the box keeps optimizing offline.
- **Meter/sensor drift** → CT clamps (the low-accuracy element) are user-checkable; disagreements between 3EM totals and the four 1PM meters surface as a dashboard anomaly, not a silent error.
- **EV charger above 16 A** → predicted constraint: a 3.7 kW single-phase ceiling for direct switching; larger EVSEs must be enabled via control input or an interposed contactor. The design's `auxiliary` channel is the parking spot for that.

## 6. Predicted economics and long-term evolution

Assumptions: two heating loads shift ~4–8 kWh/day out of a 20–30 ct peak into a 10–14 ct trough; EV adds 5–10 kWh on the same logic.

- **Year 1**: 150–400 € saved (shifted energy × 8–14 ct spread), minus ~25 €/yr extra standby (≈6 W average) — net positive by month 3 once habits stabilize.
- **Year 2–5**: savings drift up 10–20% as adapters improve decisions; LiFePO4 untouched (BMS keeps it at partial SoC; 10-year-rated).
- **Year 10**: battery at end of service; PC probably replaced once; the certified DIN electronics (RCBO, PSU, UPS, Shellies, meter) remain in-service and reusable — the hub is field-replaceable by design.

**The one-line prediction:** the household changes nothing, the box watches everything, and the bill quietly bends toward cheap hours — with a journal proving exactly why, every single time.
