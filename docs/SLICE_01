# Vertical Slice 001: One Ping, One Finding, Three Explanations

> **Make the architecture breathe.**

## Purpose

Vertical Slice 001 is the first executable proof that the VNA common contracts work outside documentation.

The slice deliberately uses the existing Telemetry4Toddlers / MARVIN network telemetry because MARVIN already performs real network discovery and ICMP probing.

The goal is intentionally tiny:

```text
MARVIN
   |
   v
DISCOVER ONE DEVICE
   |
   v
ASSET
   |
   v
PERFORM ONE ICMP PROBE
   |
   v
OBSERVATION
   |
   v
EVALUATE
   |
   v
FINDING
   |
   v
T4
   |
   +----------+----------+
   |          |          |
   v          v          v
T4Tots     T4Teens    T4Techies
```

One device.

One real observation.

One Finding.

Three explanations.

No more architecture is required to prove this slice.

---

# Repository

Vertical Slice 001 should be implemented primarily in:

```text
Telemetry4Toddlers
```

The canonical contracts remain in:

```text
VNA
└── docs/
    └── contracts/
        ├── ASSET.md
        ├── OBSERVATION.md
        ├── FINDING.md
        ├── STORAGE_LOCATION.md
        ├── RELATIONSHIP.md
        ├── SNAPSHOT.md
        ├── EVENT.md
        ├── PROPOSED_ACTION.md
        └── VERIFIED_RESULT.md
```

T4T consumes the VNA contracts.

It should not maintain incompatible copies of their semantics.

---

# Why T4T First?

T4T already has a working evidence source:

```text
MARVIN
   |
   +── IPv4 neighbour discovery
   |
   +── ICMP probing
   |
   +── optional reverse DNS
   |
   `── optional SNMP
```

Vertical Slice 001 does not require inventing a new collector.

It refactors one existing flow into the common VNA grammar.

The purpose is not to add more telemetry.

The purpose is to prove that existing telemetry can become:

```text
Asset
Observation
Finding
Explanation
```

cleanly.

---

# Scope

Vertical Slice 001 includes:

```text
ONE discovered network device

ONE VNA Asset

ONE ICMP observation type

ONE evaluation rule

ZERO or ONE active Finding

THREE T4 explanation views

ONE evidence drill-down path
```

That is enough.

---

# Explicit Non-Goals

Vertical Slice 001 does NOT include:

```text
Charkive integration
FileCity integration
SysMedic integration
BoreDOOM! integration
Binary4Babies integration
VNA gameplay
OSI Tower
Oracle reasoning
SNMP redesign
WebSockets
historical telemetry
database persistence
action execution
Proposed Actions
Verified Results
automatic remediation
professional competency
LinkedIn credentials
```

Do not broaden the slice because another feature looks easy.

The objective is to prove the information flow.

---

# The Question This Slice Must Answer

> **Can one piece of real technical evidence travel from MARVIN through the VNA contracts and emerge as three different explanations without changing what actually happened?**

If yes, the T4 family architecture is real.

If no, fix the architecture before expanding it.

---

# Technical Scenario

Use ICMP reachability and response time as the first vertical slice.

Conceptually:

```text
MARVIN knows about device
        |
        v
Create / resolve Asset
        |
        v
Send ICMP probe
        |
        v
Record Observation
        |
        v
Evaluate Observation
        |
        +---------------------------+
        |                           |
        v                           v
Normal                         Noteworthy
        |                           |
        v                           v
No Finding                   Create Finding
        |                           |
        +-------------+-------------+
                      |
                      v
                     T4
```

Not every Observation needs a Finding.

That distinction must survive implementation.

---

# Slice Rule 1: Asset First

Before recording telemetry, identify the logical thing being observed.

Example:

```json
{
  "schema": "vna.asset/0.1",
  "id": "asset:marvin-device-example",
  "kind": "unknown",
  "name": "Example Network Device",
  "source": {
    "kind": "live",
    "producer": "t4t"
  },
  "status": "active"
}
```

The Asset gives measurements a stable subject.

Do not make the IP address the permanent Asset identity.

---

# Asset Discovery

Existing MARVIN neighbour discovery can supply the information from which T4T creates or resolves the Asset.

For Slice 001, the identity strategy may remain deliberately simple.

The implementation must nevertheless respect the contract rule:

> **Address is not identity.**

It is acceptable for early development to generate an internal stable identifier using existing T4T device information.

The important architectural point is that downstream code refers to:

```text
asset:...
```

rather than treating:

```text
192.168.x.x
```

as the conceptual Asset ID.

---

# Slice Rule 2: Observe Before Interpreting

The ICMP probe produces an Observation.

Example:

```json
{
  "schema": "vna.observation/0.1",
  "id": "observation:icmp-rtt-example",
  "producer": {
    "id": "t4t",
    "type": "collector"
  },
  "source": {
    "kind": "live",
    "id": "marvin:scan:example"
  },
  "observed_at": "2026-10-30T07:00:00Z",
  "type": "network.icmp.rtt",
  "subject": {
    "type": "asset",
    "asset_id": "asset:marvin-device-example"
  },
  "method": {
    "type": "icmp_echo"
  },
  "data": {
    "value": 187,
    "unit": "ms"
  },
  "production_changed": false
}
```

This Observation says:

> The ICMP response time was 187 ms.

It does not say:

```text
the network is bad
the Wi-Fi is bad
the device is overloaded
the cable is faulty
the Internet is slow
```

Those would require interpretation and supporting evidence.

---

# Slice Rule 3: Failed Probe Is Still Evidence

A failed ICMP probe must not become:

```text
RTT = 0
```

or:

```text
device definitely offline
```

The Observation should honestly represent the failed measurement.

Conceptually:

```json
{
  "schema": "vna.observation/0.1",
  "id": "observation:icmp-example",
  "producer": {
    "id": "t4t",
    "type": "collector"
  },
  "source": {
    "kind": "live",
    "id": "marvin:scan:example"
  },
  "observed_at": "2026-10-30T07:00:00Z",
  "type": "network.icmp.rtt",
  "subject": {
    "type": "asset",
    "asset_id": "asset:marvin-device-example"
  },
  "method": {
    "type": "icmp_echo"
  },
  "data": {
    "status": "unavailable"
  },
  "production_changed": false
}
```

The evaluation layer decides what Finding, if any, that supports.

---

# Slice Rule 4: Observation Does Not Automatically Equal Finding

If MARVIN records:

```text
RTT = 18 ms
```

the system may decide:

```text
No noteworthy Finding.
```

That is valid.

The pipeline is:

```text
Observation
    |
    v
Evaluation
    |
    +---- noteworthy? ---- NO ----> stop
    |
   YES
    |
    v
Finding
```

This proves that Findings are interpretations rather than dressed-up telemetry records.

---

# Slice Rule 5: One Explicit Evaluation Rule

Do not build a general-purpose rules engine for Slice 001.

Use one explicit, inspectable rule for determining whether the selected ICMP condition deserves a Finding.

Conceptually:

```text
INPUT

ICMP Observation

        |
        v

EVALUATION

known rule / threshold

        |
        v

OUTPUT

No Finding

or

network.response.degraded Finding
```

The implementation should make the rule easy to locate and inspect.

Do not hide the first rule inside UI presentation code.

---

# Thresholds

The exact threshold values should come from the existing T4T behaviour or be introduced explicitly as T4T configuration.

Do not pretend the VNA contract defines what constitutes:

```text
good RTT
bad RTT
critical RTT
```

It does not.

That is producer policy.

Therefore:

```text
VNA contracts
define the shape of evidence and Findings.

T4T
defines its current network-health evaluation.
```

---

# Slice Rule 6: Create the Finding

When the observation satisfies the chosen noteworthy condition, T4T emits:

```text
vna.finding/0.1
```

Example:

```json
{
  "schema": "vna.finding/0.1",
  "id": "finding:network-response-example",
  "producer": {
    "id": "t4t",
    "type": "application"
  },
  "source": {
    "kind": "live",
    "id": "marvin:scan:example"
  },
  "observed_at": "2026-10-30T07:00:00Z",
  "category": "network.response.degraded",
  "severity": "warning",
  "state": "active",
  "subject": {
    "type": "asset",
    "asset_id": "asset:marvin-device-example"
  },
  "summary": "Network response is slower than the configured expected range.",
  "observations": [
    {
      "type": "network.icmp.rtt",
      "observed_at": "2026-10-30T07:00:00Z",
      "data": {
        "value": 187,
        "unit": "ms"
      }
    }
  ],
  "production_changed": false
}
```

The precise summary and severity remain producer policy.

The technical evidence remains visible.

---

# Finding Identity

The same Finding is consumed by all T4 presentation levels.

Conceptually:

```text
finding:network-response-example
               |
               +---------------+
               |               |
               v               v
            T4Tots          T4Teens
               |
               v
           T4Techies
```

Do NOT create:

```text
finding:tots-001
finding:teens-001
finding:techies-001
```

for the same underlying condition.

The audience profile does not manufacture a new technical reality.

---

# Slice Rule 7: T4 Is Presentation

The T4 layer consumes:

```text
Asset
Observation
Finding
```

and produces an explanation.

The T4 layer must not recollect the telemetry.

Changing from:

```text
T4Tots
```

to:

```text
T4Techies
```

must not cause a new ping merely because the user requested more detail.

The evidence being explained is the same evidence.

---

# T4Tots View

The first view preserves the current Telemetry4Toddlers personality.

Example:

```text
🐌 THIS ONE IS ANSWERING A BIT SLOWLY!

It answered us, but it took longer than we'd like.

[ WHY? ]
```

The exact wording can evolve.

Important properties:

```text
short
clear
friendly
visually obvious
not technically false
```

---

# T4Teens View

The intermediate view introduces actual terminology.

Example:

```text
NETWORK RESPONSE IS SLOW

This device replied to our network check, but the response took longer than the configured expected range.

The time it takes for a response to come back is called latency.

Observed response:
187 ms

[ SHOW MORE ]
```

The learner now has:

```text
intuition
+
technical vocabulary
```

---

# T4Techies View

The technical view exposes the evidence.

Example:

```text
Finding:
network.response.degraded

Asset:
asset:marvin-device-example

Probe:
ICMP echo

RTT:
187 ms

Source:
MARVIN

Observation:
observation:icmp-rtt-example

Finding:
finding:network-response-example

Observed:
[timestamp]
```

Nothing in the technical view should require a second telemetry collection merely to display details already available in the existing evidence.

---

# Progressive Reveal

Ideally the three levels should also work as a drill-down path.

```text
T4Tots

"THIS ONE IS ANSWERING SLOWLY!"

        |
        v

WHY?

        |
        v

"We sent it a network message and measured how long the reply took."

        |
        v

WHAT'S THAT CALLED?

        |
        v

"Response time. You'll also hear engineers talk about latency."

        |
        v

SHOW TECHIE DETAILS

        |
        v

ICMP RTT:
187 ms

Observation ID:
...

Finding ID:
...
```

This proves T4's educational principle inside a real observable system.

---

# Existing UI Must Survive

Vertical Slice 001 is not an excuse to rewrite the entire colourful dashboard.

The preferred migration strategy is:

```text
EXISTING MARVIN COLLECTION
         |
         v
NEW NORMALISATION LAYER
         |
         v
VNA ASSET / OBSERVATION / FINDING
         |
         v
EXISTING T4T UI ADAPTED TO CONSUME THEM
```

Preserve what already works wherever practical.

---

# Suggested Internal Boundaries

The exact filenames should follow the repository's current structure, but the conceptual modules should become clear.

```text
COLLECTION

Existing MARVIN probes


IDENTITY

Device → VNA Asset


NORMALISATION

Probe result → VNA Observation


EVALUATION

Observation → optional VNA Finding


EXPLANATION

Finding → T4 profile


PRESENTATION

Existing colourful UI
```

Do not let these collapse back into one large:

```text
probeAndFigureOutEverythingAndRenderCard()
```

function.

---

# Suggested Data Flow

```text
GET /api/devices
      |
      v
MARVIN DISCOVERY
      |
      v
DEVICE DATA
      |
      v
ASSET NORMALISER
      |
      v
vna.asset/0.1
      |
      v
ICMP PROBE
      |
      v
OBSERVATION NORMALISER
      |
      v
vna.observation/0.1
      |
      v
T4T EVALUATOR
      |
      v
vna.finding/0.1
      |
      v
T4 EXPLAINER
      |
      +-------------+-------------+
      |             |             |
      v             v             v
   T4Tots        T4Teens      T4Techies
```

---

# API Compatibility

The existing:

```text
/api/devices
```

should remain functional during Slice 001 where practical.

The slice does not require T4T to expose a new public VNA API immediately.

Internally adopting the contracts is enough to prove the architecture.

A later slice can decide whether to expose:

```text
/api/assets
/api/observations
/api/findings
```

or another appropriate interface.

Do not expand the public API merely because the internal model now exists.

---

# Fixtures

Slice 001 should include deterministic fixtures.

At minimum:

```text
healthy response

degraded response

unavailable response
```

For example:

```text
Fixture A

RTT:
18 ms
```

```text
Fixture B

RTT:
187 ms
```

```text
Fixture C

ICMP response:
unavailable
```

The fixtures should exercise the transformation chain without requiring a real ping every time tests run.

---

# Synthetic Fixtures Must Remain Synthetic

Test fixtures should clearly use:

```text
source.kind = synthetic
```

or another explicit testing mechanism.

Do not create fixtures that appear indistinguishable from real MARVIN observations.

---

# Tests: Asset

Test that a discovered device produces a valid:

```text
vna.asset/0.1
```

object.

Verify at minimum:

```text
schema
id
kind
name
source
status
```

---

# Tests: Observation

Test that an ICMP measurement produces a valid:

```text
vna.observation/0.1
```

object.

Verify:

```text
Asset reference
observation type
measurement
unit
method
timestamp
production_changed = false
```

---

# Tests: No Fake Zero

A failed probe must not become:

```text
RTT = 0 ms
```

Test that an unavailable result retains its unavailable/failed semantics.

---

# Tests: Finding

Test that the configured noteworthy condition produces a valid:

```text
vna.finding/0.1
```

object.

Verify:

```text
same Asset
same evidence
correct category
correct severity
active state
production_changed = false
```

---

# Tests: No Finding

Test that a normal observation can produce:

```text
no active Finding
```

without discarding the Observation.

This is an important architectural proof.

---

# Tests: T4 Profiles

Given exactly one Finding object:

```text
finding:example
```

render:

```text
T4Tots
T4Teens
T4Techies
```

Verify that all three retain a route back to:

```text
same Finding ID
same Observation
same Asset
```

---

# Tests: Profile Switching

Switch:

```text
Tots
  ↓
Teens
  ↓
Techies
  ↓
Tots
```

The underlying:

```text
Asset ID
Observation ID
Finding ID
```

must not change solely because the explanation profile changed.

---

# Tests: Evidence Drill-Down

From the T4Tots representation, the user should be able to reach the actual measurement.

Conceptually:

```text
friendly explanation
      |
      v
technical concept
      |
      v
measurement
      |
      v
Observation ID
```

This is a core acceptance test, not optional polish.

---

# Tests: Production Safety

For every Slice 001 Asset, Observation, and Finding:

```text
production_changed = false
```

The slice must remain read-only.

No implementation path in Slice 001 should perform remediation.

---

# Error Handling

A failure in normalisation or evaluation must not crash the entire dashboard if the existing UI can continue safely.

Prefer:

```text
device discovered

telemetry unavailable

explanation unavailable
```

over:

```text
dashboard dead
```

where possible.

Unknown remains a legitimate state.

---

# Unknown Is Better Than Invented

If T4T cannot establish:

```text
RTT
```

display:

```text
unknown / unavailable
```

rather than inventing an apparently valid number.

Likewise, if T4 cannot create a defensible Finding:

```text
don't create one
```

The ecosystem values epistemic honesty over dashboard completeness.

---

# Logging

Slice 001 logging should make the pipeline inspectable during development.

Useful development events might include:

```text
Asset created/resolved
Observation generated
Finding generated
Finding cleared/not generated
T4 profile rendered
```

Do not log sensitive device information unnecessarily.

The logging scheme does not need to become a formal Event implementation unless the existing EVENT contract is genuinely useful to the slice.

---

# No Database Yet

Vertical Slice 001 does not require persistent database storage.

In-memory objects are sufficient to prove:

```text
Asset
Observation
Finding
T4 explanation
```

Persistence should be introduced when a real feature such as:

```text
history
correlation
trend analysis
audit
```

requires it.

---

# No Event Pipeline Yet

Even though `EVENT.md` exists, Slice 001 does not need to implement the entire event architecture.

The objective is narrower.

If Events naturally help the existing T4T implementation, they may be used.

Otherwise, leave them for the slice that genuinely needs temporal lifecycle tracking.

---

# No Action Contracts Yet

Do not use:

```text
PROPOSED_ACTION
VERIFIED_RESULT
```

in Slice 001.

There is nothing to remediate.

This slice is:

```text
READ
UNDERSTAND
EXPLAIN
```

only.

---

# Definition of Done

Vertical Slice 001 is DONE when:

1. MARVIN discovers at least one real device.
2. T4T represents that device as a valid `vna.asset/0.1`.
3. T4T performs its existing ICMP observation against the device.
4. The result becomes a valid `vna.observation/0.1`.
5. The Observation remains distinct from interpretation.
6. One explicit evaluation rule determines whether a Finding is warranted.
7. A noteworthy condition produces a valid `vna.finding/0.1`.
8. A normal condition may produce no Finding.
9. The existing T4T experience can render the data.
10. T4Tots can explain the Finding.
11. T4Teens can explain the same Finding.
12. T4Techies can expose the same Finding's technical evidence.
13. The user can drill down from friendly explanation to real measurement.
14. Changing explanation level does not recollect or alter the evidence.
15. The same Asset, Observation, and Finding IDs survive profile switching.
16. The slice performs no remediation.
17. `production_changed` remains false throughout.
18. Deterministic tests cover the transformation path.

---

# Demo Script

The first demonstration should take only a minute or two.

```text
1. Open Telemetry4Toddlers.

2. MARVIN discovers a device.

3. Show:
   Asset ID

4. Trigger / observe BIG RE-SCAN.

5. MARVIN performs ICMP probe.

6. Show:
   Observation ID
   actual RTT

7. T4T evaluates the observation.

8. Show:
   Finding ID

9. Display T4Tots:

   "THIS ONE IS ANSWERING SLOWLY!"

10. Select:
    WHY?

11. Show:
    simple explanation

12. Select:
    SHOW TEEN VIEW

13. Show:
    real terminology

14. Select:
    SHOW TECHIE DETAILS

15. Show:
    ICMP RTT
    observation ID
    finding ID
    source

16. Switch back to T4Tots.

17. Demonstrate:
    same Asset
    same Observation
    same Finding
```

Then show a healthy device:

```text
Observation exists.

No warning Finding exists.
```

That second demonstration is just as important as the first.

---

# What This Slice Proves

If Vertical Slice 001 succeeds, we have proved:

```text
VNA contracts are implementable

T4T can speak VNA

real telemetry can become a VNA Observation

interpretation can remain separate from evidence

Findings can be created only when meaningful

T4 can present one Finding at multiple depths

friendly explanation need not corrupt technical truth
```

That is a substantial result from a deliberately tiny amount of implementation.

---

# What This Slice Does Not Prove

It does not prove:

```text
cross-repository interoperability

persistent history

live remediation

FileCity geography

SysMedic import

Charkive Findings

multi-user authority

VNA gameplay

professional assessment

Oracle reasoning
```

Those come later.

Do not move their success criteria into Slice 001.

---

# Vertical Slice 002 Candidate

Once Slice 001 works, the strongest next candidate is:

```text
CHARKIVE
   |
   v
STORAGE LOCATION
   |
   v
OBSERVATION
   |
   v
FINDING
   |
   v
FILECITY
   |
   v
WORLD OBJECT ALERT
   |
   v
CLICK
   |
   v
REAL FINDING + EVIDENCE
```

That proves cross-repository interchange.

But Vertical Slice 002 should not begin until Slice 001 genuinely works.

---

# Implementation North Star

During implementation, keep asking:

```text
Did we observe that?

Or did we infer it?


Is this an Observation?

Or is it a Finding?


Is this technical state?

Or is it presentation?


Did changing the explanation change the evidence?

If so, why?


Did we just invent another abstraction we don't yet need?

If so, delete it.
```

---

# Slice 001 North Star

The whole slice should fit mentally into one line:

> **One ping. One truth. Three explanations.**

Or, in the full VNA grammar:

```text
THING
  |
  v
ASSET
  |
  v
PING
  |
  v
OBSERVATION
  |
  v
INTERPRETATION
  |
  v
FINDING
  |
  v
T4
  |
  +------ TOTS
  |
  +------ TEENS
  |
  `------ TECHIES
```

If we can make that work end to end with MARVIN's real telemetry, we stop having an architecture diagram.

We have an ecosystem.

---

# Reconciliation With Existing T4T Code

Added after inspecting the Telemetry4Toddlers code at `84b0139`.

Principle: where this document and the working T4T code disagree, the existing code wins. The spec is adjusted to match it, except where noted.

| Topic | Decision | Source in T4T |
|---|---|---|
| Evaluation threshold | `network.response.degraded` when RTT is 160 ms or more. Below 160 ms produces no Finding, including the existing 35-160 ms "ok" band. | `tierFromPing`, `server/lanScan.mjs`: under 35 good, under 160 ok, else help |
| Unavailable ping | Produces a Finding, category `network.response.unavailable`. It must not claim the device is offline, because T4T's probe cannot tell "no reply" from "ping unavailable". | `pingRttMs` returns `null`, and the server maps `null` to `help` |
| Fixtures | 18 ms: no Finding. 187 ms: degraded. Unavailable: unavailable Finding. | Follows the two rows above |
| Asset identity | Derived from the existing device `id` (MAC when known, else IP), prefixed `asset:` and treated as opaque. MAC is kept as an alias. IP is kept as an attribute. A device with no MAC currently falls back to an IP-derived ID. This breaks "address is not identity" and is a known limitation of Slice 001. | `id: row.mac \|\| row.ip` |
| Audience levels | T4T keeps its five levels. They map onto three explanation profiles: `tot` and `toddler` use Tots, `tween` and `teen` use Teens, `techTitan` uses Techies. | `LEVEL_ORDER` and `LEVEL_COPY`, `src/main.js` |
| Probe | `pingMs` and the `/api/devices` shape do not change. A richer probe result is added beside `pingRttMs`, and `pingMs` is derived from it. | `pingRttMs` |
| Scan ID | T4T has none today. One is generated per `scanLan()` call for `source.id`. | `scanLan` |
| Observation ID inside a Finding | Items in `observations[]` carry an additive `id`. The contract permits unknown fields. This is not a v0.1 change. | `FINDING.md` compatibility rules |
| Tests | `node:test`, no new dependency. Validators are hand-written from each contract's Validation Rules. | No test runner exists |

## One place the spec wins over the code

Changing the audience level currently re-runs a full LAN scan (`applyLevel` calls `refresh()` in `src/main.js`). Definition of Done items 14 and 15 forbid that. The front end must cache the last payload and re-render from it when the level changes. The spec wins here because it adds a requirement the old code never had.

## Left alone

- `/api/devices`, `tier`, `message` and `pingLabel` keep their current values and wording.
- `neighState` still feeds the existing server tier and stays out of the Slice 001 evaluation rule.
- The client `deriveTier` fallback stays as it is. The server tier remains authoritative.
