# VNA Implementation Slices

> **Build the smallest thing that proves the next part of the architecture. Then stop and look at what reality taught us.**

## Purpose

This document turns the VNA ecosystem architecture into a sequence of deliberately small implementation slices.

The contracts already define the common language:

```text
ASSET
What is the thing?

STORAGE LOCATION
Where does its data live?

OBSERVATION
What did we see?

FINDING
What noteworthy condition exists?

RELATIONSHIP
How do things fit together?

SNAPSHOT
What reality did we capture?

EVENT
What happened?

PROPOSED ACTION
What change is being requested?

VERIFIED RESULT
What actually happened, and did reality confirm it?
```

The implementation slices prove that language incrementally.

The rule is:

> **Do not implement the whole ecosystem at once.**

Each slice should:

1. prove one important architectural idea
2. use as much existing working code as possible
3. introduce the minimum new infrastructure necessary
4. have an obvious demonstration
5. have explicit success criteria
6. remain independently testable
7. teach us something before the next slice begins

---

# Implementation Philosophy

The documentation phase established where the projects are going.

Implementation now works in the opposite direction.

```text
NOT

build enormous platform
        |
        v
hope architecture was right


INSTEAD

small real slice
      |
      v
learn
      |
      v
adjust
      |
      v
next slice
```

The contracts are now the starting hypothesis.

Implementation is allowed to prove that parts of them need revision.

What we should avoid is changing the contracts merely because another abstraction sounds interesting.

---

# Contract Freeze

The v0.1 contracts should now be treated as provisionally frozen.

```text
vna/docs/contracts/
├── ASSET.md
├── STORAGE_LOCATION.md
├── OBSERVATION.md
├── FINDING.md
├── RELATIONSHIP.md
├── SNAPSHOT.md
├── EVENT.md
├── PROPOSED_ACTION.md
└── VERIFIED_RESULT.md
```

Changes are justified when:

```text
implementation exposes ambiguity

two real producers need incompatible semantics

validation cannot express a real case

a safety problem is discovered

a contract requires information that cannot realistically be produced

a contract prevents a legitimate real workflow
```

Changes are NOT justified merely because:

```text
another field might be useful someday

a future game could possibly need it

an abstraction looks elegant

we thought of another noun
```

---

# Slice Progression

The initial implementation programme is:

```text
SLICE 001
One Ping, One Finding, Three Explanations

        |
        v

SLICE 002
Charkive Finding → FileCity

        |
        v

SLICE 003
SysMedic Snapshot → VNA

        |
        v

SLICE 004
Synthetic OSI Tower

        |
        v

SLICE 005
Finding Lifecycle

        |
        v

SLICE 006
First Dry-Run Action

        |
        v

SLICE 007
First Verified Reversible Action

        |
        v

SLICE 008
FileCity Verified Movement

        |
        v

SLICE 009
Snapshot → Playable Investigation

        |
        v

SLICE 010
First End-to-End VNA Mission
```

These are implementation slices, not product releases.

Several slices may ultimately contribute to one release.

---

# Vertical Slice 001

## One Ping, One Finding, Three Explanations

### Repository

Primary:

```text
Telemetry4Toddlers
```

Contracts:

```text
VNA
```

### Goal

Prove:

```text
REAL TELEMETRY
      |
      v
ASSET
      |
      v
OBSERVATION
      |
      v
FINDING
      |
      v
T4
 ┌────┼──────┐
 v    v      v
Tots Teens Techies
```

### Evidence Source

Use the existing MARVIN ICMP telemetry.

Do not introduce another telemetry source.

### Build

- [ ] Resolve one discovered device into `vna.asset/0.1`
- [ ] Convert one ICMP result into `vna.observation/0.1`
- [ ] Keep Observation separate from interpretation
- [ ] Apply one explicit T4T evaluation rule
- [ ] Produce `vna.finding/0.1` only where warranted
- [ ] Render the same Finding as T4Tots
- [ ] Render the same Finding as T4Teens
- [ ] Render the same Finding as T4Techies
- [ ] Drill down from friendly wording to real evidence
- [ ] Preserve the same Asset, Observation, and Finding IDs across views
- [ ] Preserve existing T4T behaviour where practical

### Must Not Build

```text
database
event architecture
actions
history
Charkive
FileCity
SysMedic
VNA game
Oracle
```

### Definition of Done

> One real MARVIN measurement becomes one VNA Observation, may produce one VNA Finding, and that exact Finding can be explained at three T4 depths.

### Motto

> **One ping. One truth. Three explanations.**

---

# Vertical Slice 002

## Charkive Finding → FileCity

### Repositories

```text
Charkive
FileCity
VNA contracts
```

### Goal

Prove the first genuine cross-repository interchange.

```text
REAL FILESYSTEM
      |
      v
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
VISIBLE WORLD CONDITION
      |
      v
USER INSPECTION
      |
      v
ORIGINAL EVIDENCE
```

### Build

- [ ] Define one real `vna.storage_location/0.1`
- [ ] Associate it with an Asset where appropriate
- [ ] Charkive scans that Storage Location
- [ ] Create one real filesystem Observation
- [ ] Produce one real Charkive Finding using `vna.finding/0.1`
- [ ] Include stable Storage Location and logical path
- [ ] FileCity ingests the Finding
- [ ] FileCity resolves Storage Location
- [ ] FileCity resolves logical path into existing geography
- [ ] Relevant world object changes visual state
- [ ] Selecting the world object exposes Finding details
- [ ] User can inspect the actual evidence
- [ ] No real storage modification occurs

### First Finding

Prefer something simple and already supported by Charkive.

For example:

```text
storage.disposable_candidate
```

The precise condition should come from real Charkive behaviour rather than inventing a detector solely for the slice.

### FileCity Presentation

Start boring.

Use:

```text
normal

notice

warning

critical
```

Do not start with:

```text
fire engines
police
explosions
emergency helicopters
```

Those come after the semantic loop works.

### Definition of Done

> Charkive identifies something real and FileCity shows exactly where it is without reproducing Charkive's detection logic.

### Motto

> **No siren without evidence.**

---

# Vertical Slice 003

## SysMedic Snapshot → VNA

### Repositories

```text
SysMedic
VNA
```

### Goal

Prove the captured-evidence pathway.

```text
REAL MACHINE
     |
     v
SYSMEDIC
     |
     v
PORTABLE EVIDENCE
     |
     v
IMPORT
     |
     v
VNA SNAPSHOT
     |
     +---------+
     |         |
     v         v
  ASSETS    STORAGE
     |
     v
OBSERVATIONS
     |
     v
FINDINGS
```

### Build

- [ ] Map one existing SysMedic collection into `vna.snapshot/0.1`
- [ ] Preserve collection ID
- [ ] Preserve capture time
- [ ] Preserve collector provenance
- [ ] Represent captured machine as `vna.asset/0.1`
- [ ] Represent at least one captured Storage Location where available
- [ ] Represent captured Asset/Storage relationship
- [ ] Produce at least one Observation from existing collected evidence
- [ ] Produce at least one Finding if the evidence legitimately warrants one
- [ ] Preserve `source.kind = snapshot`
- [ ] Expose omissions or collection failures where available
- [ ] Preserve integrity information where available

### Critical Rule

No downstream client may translate:

```text
Snapshot says X
```

into:

```text
X is true now
```

without current evidence.

### Definition of Done

> A real disconnected machine can enter the VNA model as a trustworthy captured evidence set without pretending to be live infrastructure.

### Motto

> **This was true when we looked.**

---

# Vertical Slice 004

## Synthetic OSI Tower

### Repository

```text
VNA
```

### Goal

Build the smallest playable expression of the VNA gameplay model using the same contracts as the real tools.

```text
1960s L7 OFFICE
       |
       v
OSI ELEVATOR
       |
       v
SEVEN FLOORS
       |
       v
L1 PHYSICAL WORLD
       |
       v
SECOND ASSET
```

### Scenario

```text
Application in Office B is unavailable.
```

Actual synthetic root cause:

```text
Physical connection disconnected at Layer 1.
```

### Build

- [ ] Create two synthetic Assets
- [ ] Create their synthetic Relationship
- [ ] Model seven OSI layers visually
- [ ] Create Layer 1 Observation
- [ ] Create Layer 1 root-cause Finding
- [ ] Create propagated symptom Findings where useful
- [ ] Connect Findings through Relationships
- [ ] Present symptom at Layer 7
- [ ] Let player descend through evidence
- [ ] Reach Layer 1
- [ ] Identify physical fault
- [ ] Complete symbolic repair
- [ ] Generate synthetic Verified Result
- [ ] Generate post-action Observation
- [ ] Re-evaluate Findings
- [ ] Propagate recovery upwards
- [ ] Produce mission report

### Important

All objects must remain:

```text
source.kind = synthetic
```

No live integration.

### Definition of Done

> The player experiences a Layer 7 symptom, discovers a Layer 1 root cause, repairs it symbolically, and watches verified recovery propagate through the stack.

### Motto

> **Where you see the problem isn't necessarily where the problem is.**

---

# Vertical Slice 005

## Finding Lifecycle

### Repositories

Start with whichever existing producer provides the cleanest implementation.

Likely:

```text
T4T
or
Charkive
```

### Goal

Prove:

```text
ACTIVE
  |
  v
CONDITION CHANGES
  |
  v
RE-OBSERVE
  |
  v
RESOLVED
```

Then add:

```text
ACKNOWLEDGED
SUPPRESSED
```

only after the active/resolved path is working.

### Build

- [ ] Create stable Finding identity
- [ ] Emit active Finding
- [ ] Remove underlying condition
- [ ] Re-observe reality
- [ ] Change same logical Finding to resolved
- [ ] Emit lifecycle Event
- [ ] Consumer reacts to state change
- [ ] Preserve historical Finding
- [ ] Reactivate or create recurrence according to explicit producer policy

### If Using FileCity

Ideal demo:

```text
ACTIVE FINDING

Building shows warning

        |
        v

REAL CONDITION DISAPPEARS

        |
        v

CHARKIVE RE-SCANS

        |
        v

FINDING RESOLVED

        |
        v

FILECITY CLEARS WARNING
```

### Definition of Done

> A visual alert disappears because reality changed and the producer re-observed that change, not because a user clicked "dismiss".

### Motto

> **Reality resolves Findings. Buttons don't.**

---

# Vertical Slice 006

## First Dry-Run Action

### Suggested Provider

```text
Charkive
```

### Suggested Action

```text
charkive.quarantine
```

with:

```text
mode = dry_run
```

### Goal

Cross the action boundary without changing production.

```text
FINDING
   |
   v
RECOMMENDATION
   |
   v
PROPOSED ACTION
   |
   v
CHARKIVE VALIDATION
   |
   v
DRY RUN
   |
   v
VERIFIED RESULT
   |
   v
production_changed = false
```

### Build

- [ ] Select one real reviewed Finding
- [ ] Create `vna.proposed_action/0.1`
- [ ] Provider validates action type
- [ ] Provider validates target
- [ ] Provider validates Finding still active
- [ ] Provider evaluates relevant safety checks
- [ ] Provider performs dry run
- [ ] Produce lifecycle Events
- [ ] Produce `vna.verified_result/0.1`
- [ ] Result clearly states no production change
- [ ] UI clearly distinguishes preview/dry-run from execution

### Definition of Done

> One application can request a Charkive operation through the common action boundary without acquiring Charkive's filesystem authority.

### Motto

> **Ask first. Touch nothing.**

---

# Vertical Slice 007

## First Verified Reversible Action

### Suggested Provider

```text
Charkive
```

### Suggested Action

```text
charkive.quarantine
```

### Why Charkive?

The SANitorium philosophy already provides the property we want for the first real integrated action:

> **Reversible removal before irreversible deletion.**

### Goal

Prove the complete operational loop.

```text
FINDING
   |
   v
PROPOSED ACTION
   |
   v
VALIDATION
   |
   v
AUTHORISATION
   |
   v
EXECUTION
   |
   v
VERIFICATION
   |
   v
VERIFIED RESULT
   |
   v
RE-OBSERVATION
   |
   v
FINDING RESOLVED
```

### Build

- [ ] Start from reviewed active Finding
- [ ] Execute successful dry-run first
- [ ] Create separate execute proposal
- [ ] Revalidate target
- [ ] Revalidate Finding
- [ ] Require applicable confirmation
- [ ] Execute Charkive quarantine through Charkive
- [ ] Verify destination
- [ ] Verify expected source state
- [ ] Produce Verified Result
- [ ] Record reversibility
- [ ] Record rollback action type
- [ ] Create/update SANitorium Relationships
- [ ] Re-scan/re-observe
- [ ] Resolve Finding only when reality supports it
- [ ] Preserve complete audit chain

### Failure Test

Deliberately test:

```text
execution completes
verification fails
```

The Result MUST NOT report success.

### Definition of Done

> VNA can request one real reversible change through the responsible specialist provider and report success only after the expected real-world result is independently observed.

### Motto

> **Don't trust the command. Look at reality.**

---

# Vertical Slice 008

## FileCity Verified Movement

### Repositories

```text
FileCity
VNA
Storage provider / existing transfer mechanism
```

### Goal

Connect FileCity's transport metaphor to authenticated operational truth.

```text
SOURCE STORAGE
      |
      v
PROPOSED COPY / MOVE
      |
      v
PREVIEW
      |
      v
AUTHORISE
      |
      v
TRANSFER
      |
      v
VERIFY DESTINATION
      |
      v
VERIFIED RESULT
      |
      v
WORLD CHANGES
```

### Build

- [ ] Select one low-risk transfer scenario
- [ ] Represent source Storage Location
- [ ] Represent destination Storage Location
- [ ] Preview requested transfer
- [ ] Show planned route in FileCity
- [ ] Execute through responsible provider
- [ ] Produce Events during movement
- [ ] Verify destination
- [ ] Produce Verified Result
- [ ] Update FileCity only from verified outcome
- [ ] Show failure honestly where injected

### Visual Rule

```text
Vehicle departing
=
execution started

Vehicle arriving
=
transfer process reached destination phase

Successful delivery celebration
=
verification passed
```

### Definition of Done

> FileCity's animation reflects the operational lifecycle rather than optimistically predicting it.

### Motto

> **No fireworks before verification.**

---

# Vertical Slice 009

## Snapshot → Playable Investigation

### Repositories

```text
SysMedic
VNA
T4
```

### Goal

Turn one real SysMedic Snapshot into a safe playable diagnostic mission.

```text
REAL MACHINE
     |
     v
SYSMEDIC
     |
     v
SNAPSHOT
     |
     v
GENERALISE
     |
     v
VNA WORLD
     |
     v
PLAYER INVESTIGATION
     |
     v
DIAGNOSIS
     |
     v
REPORT
```

### Build

- [ ] Select one useful SysMedic Snapshot
- [ ] Import Assets
- [ ] Import Storage Locations
- [ ] Import Observations
- [ ] Import Relationships
- [ ] Generate defensible Findings
- [ ] Generalise identifying information
- [ ] Create read-only VNA mission
- [ ] Expose evidence through world interaction
- [ ] Use T4 for explanation depth
- [ ] Allow player diagnosis
- [ ] Record confidence
- [ ] Produce mission report
- [ ] Make snapshot provenance visible
- [ ] Perform no production remediation

### Definition of Done

> A player investigates a real captured machine through a VNA world and produces a useful evidence-linked diagnosis without connecting to or modifying the source machine.

### Motto

> **Real problem. Safe world.**

---

# Vertical Slice 010

## First End-to-End VNA Mission

### Goal

Combine the proven pieces into one coherent VNA experience.

This is not yet the whole VNA product.

It is the first time the ecosystem behaves like the thing we have been describing.

```text
SOURCE
   |
   v
ASSET
   |
   v
OBSERVATION
   |
   v
FINDING
   |
   v
RELATIONSHIPS
   |
   v
T4 EXPLANATION
   |
   v
VNA WORLD
   |
   v
PLAYER INVESTIGATION
   |
   v
PROPOSED ACTION
   |
   v
PROVIDER
   |
   v
EXECUTION
   |
   v
VERIFICATION
   |
   v
VERIFIED RESULT
   |
   v
RE-OBSERVATION
   |
   v
FINDING RESOLVED
   |
   v
MISSION REPORT
```

### Source

Use whichever previous slice has demonstrated sufficient safety.

Prefer a deliberately low-risk scenario.

Do not choose a dramatic production operation merely to make the demo impressive.

### Build

- [ ] Asset identity
- [ ] Observation
- [ ] Finding
- [ ] Relationship
- [ ] T4 explanation
- [ ] VNA spatial representation
- [ ] Player investigation
- [ ] Evidence review
- [ ] Proposed Action
- [ ] Provider validation
- [ ] Authorisation boundary
- [ ] Execution
- [ ] Events
- [ ] Verification
- [ ] Verified Result
- [ ] Post-action Observation
- [ ] Finding lifecycle update
- [ ] Mission completion
- [ ] Audit/report

### Definition of Done

> A useful technical condition enters VNA as evidence and leaves as an honestly verified piece of completed work with a traceable audit chain.

### Motto

> **Observe. Understand. Act carefully. Verify.**

---

# Slice 011 Candidate

## FileCity + Charkive City Services

Only after the underlying integration is real should the playful representation become richer.

Possible additions:

```text
Charkive Sanitation
Charkive Police
Charkive Fire
Archaeology Service
SANitorium
```

At this point:

```text
warning
```

may become:

```text
vehicle dispatch
```

because actual structured Findings and Events exist beneath it.

---

# Slice 012 Candidate

## T4 Across the Ecosystem

Take one Finding each from:

```text
T4T / network
Charkive / storage
SysMedic / machine diagnostics
VNA / synthetic OSI
```

and prove:

```text
T4Tots
T4Teens
T4Techies
```

for all four.

### Goal

Prove T4 is now genuinely an explanation layer rather than a redesigned network dashboard.

---

# Slice 013 Candidate

## OSI Mission 2: Not the Cable

Use exactly the same Layer 7 symptom as the first tutorial.

This time:

```text
Layer 1 is healthy.
```

The player must continue investigating.

### Goal

Destroy the accidental beginner heuristic:

```text
application broken
=
cable unplugged
```

and replace it with:

```text
symptom
    |
    v
evidence
    |
    v
investigation
    |
    v
root cause
```

---

# Slice 014 Candidate

## Two Real Assets

Extend VNA beyond one-machine reasoning.

```text
ASSET A
   |
   | connected_to
   v
ASSET B
```

Introduce:

```text
route
dependency
cross-asset observations
cross-asset Findings
```

This begins turning VNA from machine diagnostics into infrastructure diagnostics.

---

# Slice 015 Candidate

## Historical Charkive Investigation

Use:

```text
historical Storage Location
Charkive observations
source-device Asset
Relationships
Temporal Anomaly Finding
```

to create a read-only archaeological mission.

### Goal

Prove that VNA missions do not need to involve repair.

Sometimes the useful outcome is:

```text
"We understand this collection better now."
```

---

# Slice 016 Candidate

## Generalised Multiplayer Investigation

Take an existing Snapshot or synthetic estate and generalise it.

Then allow two players to investigate the same evidence.

### Build

Possible roles:

```text
Network investigator
Systems investigator
```

or:

```text
Trainee
Mentor
```

### Goal

Prove that:

```text
shared evidence
+
shared Findings
+
Relationships
```

can support genuinely cooperative investigation.

---

# Slice 017 Candidate

## Vendor Equipment

Import one vendor-defined synthetic Asset.

Add:

```text
equipment layout
observations
known normal state
one synthetic fault
```

Create a guided mission.

Do not attempt certification yet.

### Goal

Prove that vendor-specific equipment can participate in the common VNA grammar.

---

# Slice 018 Candidate

## Competency Evidence

Only after several playable technical missions genuinely work should VNA begin recording structured competency evidence.

Potential evidence might include:

```text
correctly identified root cause

reviewed appropriate evidence

avoided unnecessary action

selected appropriate action

respected authority boundary

verified outcome

documented result
```

This is not simply:

```text
mission complete
```

### Goal

Begin separating:

```text
game achievement
```

from:

```text
demonstrated technical competence
```

---

# Slice 019 Candidate

## Qualification-Gated Capabilities

Once competency evidence exists, experiment with capabilities.

```text
OBSERVE
   |
   v
INVESTIGATE
   |
   v
RECOMMEND
   |
   v
PREVIEW
   |
   v
REQUEST LOW-RISK ACTION
```

Higher-impact capabilities should not be implemented until governance and safety requirements are understood.

---

# Slice 020 Candidate

## External Credential

Only after the assessment model is meaningful should external professional-profile integration be considered.

The sequence must remain:

```text
PROVE COMPETENCE
      |
      v
ISSUE CREDENTIAL
      |
      v
USER CHOOSES TO SHARE
```

The game score is not itself the credential.

The player's explicit choice remains part of external publication.

---

# Implementation Order Rules

## Rule 1: Finish the Slice

Do not begin Slice N+1 because Slice N has become slightly boring.

Boring integration work is still integration work.

---

## Rule 2: Demonstrate It

Every completed slice needs a small repeatable demonstration.

If the implementation cannot be demonstrated clearly, it probably is not finished.

---

## Rule 3: Test the Failure Path

A happy-path demonstration is insufficient.

Each slice should deliberately test at least one important failure case.

Examples:

```text
ping unavailable

Finding not warranted

Storage path cannot resolve

Snapshot partial

precondition fails

action rejected

execution succeeds but verification fails

transfer partially succeeds
```

The system should remain honest when things go wrong.

---

## Rule 4: Don't Hide Unknown

Use:

```text
unknown
unavailable
partial
unverified
```

where appropriate.

Do not convert uncertainty into prettier UI state.

---

## Rule 5: Preserve Provenance

Every slice should maintain the route:

```text
PRESENTATION
     |
     v
FINDING
     |
     v
OBSERVATION
     |
     v
SOURCE
```

where the user is authorised to inspect that evidence.

---

## Rule 6: Simulation Must Declare Itself

All:

```text
tutorials
test fixtures
procedural worlds
training scenarios
```

must remain explicitly synthetic.

---

## Rule 7: Snapshot Must Remain Snapshot

Captured evidence must never silently become current-state truth.

---

## Rule 8: Recommendations Don't Execute

Keep:

```text
Finding
Recommendation
Proposed Action
```

separate.

---

## Rule 9: Proposal Doesn't Execute

Keep:

```text
Proposed Action
Authorisation
Execution
```

separate.

---

## Rule 10: Execution Doesn't Prove Success

Keep:

```text
Execution
Verification
Verified Result
```

separate.

---

## Rule 11: Result Doesn't Automatically Resolve the Finding

Re-observe reality where appropriate.

Then resolve.

---

## Rule 12: Game State Follows Reality

For live operations:

```text
REALITY
   |
   v
Verified Result
   |
   v
GAME WORLD
```

not:

```text
ANIMATION FINISHED
   |
   v
ASSUME REALITY CHANGED
```

---

# Testing Strategy

Each slice should contain tests at the contract boundaries it exercises.

The aim is not merely:

```text
function returns expected value
```

but:

```text
Does the object we emit actually satisfy the agreed ecosystem semantics?
```

---

# Contract Fixtures

Maintain small deterministic fixtures for each implemented contract.

For example:

```text
fixtures/
├── asset/
├── storage-location/
├── observation/
├── finding/
├── relationship/
├── snapshot/
├── event/
├── proposed-action/
└── verified-result/
```

The exact physical location can evolve.

The important point is to have known-good examples that can be reused across repositories.

---

# Valid and Invalid Fixtures

Where useful, provide:

```text
valid/
invalid/
```

Examples:

```text
valid finding

finding missing schema

observation with impossible structure

action without target

result claiming success without required verification
```

This turns the documentation rules into executable expectations.

---

# Shared Test Philosophy

Eventually each consuming repository should be able to prove:

```text
I can ingest the contract version I claim to support.

I reject structurally invalid objects.

I preserve unknown fields where required.

I do not silently reinterpret unknown semantics.
```

Do not create a complex central conformance framework before multiple repositories actually need it.

---

# Contract Versioning During Slices

The contracts currently use:

```text
0.1
```

Do not increment versions for trivial wording changes in documentation.

Consider a schema revision when implementation requires a real semantic or structural change.

Examples:

```text
new required field

changed field meaning

changed allowed values

changed lifecycle rule

removed field

incompatible target representation
```

During early development, revisions are expected.

Make them explicit.

---

# Implementation Learning Log

For each slice, record briefly:

```text
What worked?

What contract assumptions were wrong?

What was awkward?

What did we deliberately defer?

What should the next slice know?
```

This can live in:

```text
docs/implementation/
```

or inside pull-request/commit history initially.

Do not turn it into bureaucracy.

Its purpose is to prevent us forgetting why a contract changed.

---

# Definition of a Good Slice

A good implementation slice is:

```text
small
real
demonstrable
testable
useful
honest
```

It should preferably connect at least two layers of the architecture.

Examples:

```text
real measurement
→ Observation
→ Finding
→ explanation
```

or:

```text
real Finding
→ FileCity
→ visual state
```

or:

```text
Proposed Action
→ provider
→ verification
→ Result
```

---

# Definition of a Bad Slice

Avoid:

```text
"Build VNA backend"

"Implement digital twin"

"Add AI"

"Do multiplayer"

"Build FileCity"

"Create game engine"

"Support cloud"

"Make enterprise-ready"
```

Those are programmes, not slices.

A slice should answer one concrete architectural question.

---

# Slice Gate

Before moving to the next slice, ask:

```text
Does it work?

Can we demonstrate it?

Can we test it?

Does it behave honestly when it fails?

Did we learn anything that invalidates the contracts?

Did we accidentally broaden the scope?

Do we actually need the next abstraction?
```

If the current slice exposed a contract problem, fix that problem before building more implementation on top of it.

---

# Cross-Repository Rule

A cross-repository slice must prove genuine interoperability.

It is not enough for both repositories to coincidentally contain similar JSON.

For example, Slice 002 succeeds only when:

```text
Charkive emits a real Finding

AND

FileCity consumes that exact Finding contract

AND

the Finding affects FileCity behaviour

AND

the original evidence remains inspectable
```

That is interoperability.

---

# No Shared Library Yet

Do not immediately create:

```text
vna-contracts npm package

vna-contracts Python package

vna-contracts Rust crate

vna-contracts shared SDK
```

just because several Markdown contracts now exist.

First prove the objects in real implementations.

Once two or more repositories demonstrate repeated validation/serialisation duplication, extract a shared library if doing so genuinely reduces complexity.

---

# No New Umbrella

VNA is the umbrella.

Do not introduce:

```text
Filekive Core

VNA Core Core

Universal Data Thing

Ecosystem Engine

Shared Everything Framework
```

unless implementation provides an overwhelming reason.

The current ownership model is enough.

---

# Repository Boundaries Remain

```text
VNA

Infrastructure model
Contracts
Orchestration
Technical gameplay


T4

Explanation


SysMedic

Machine evidence collection
Offline/rescue diagnostics


Charkive

Storage health
Preservation
Data Archaeology
SANitorium


FileCity

Storage geography
Movement representation


BoreDOOM!

Domestic chore gameplay


Binary4Babies

Computing concepts learned through play
```

Implementation slices are allowed to connect these projects.

They should not erase their boundaries.

---

# Safety Progression

The slices deliberately move from low-risk to higher-risk behaviour.

```text
READ REAL TELEMETRY
      |
      v
READ REAL STORAGE FINDINGS
      |
      v
READ CAPTURED MACHINE EVIDENCE
      |
      v
CHANGE SYNTHETIC WORLD
      |
      v
DRY-RUN REAL ACTION
      |
      v
EXECUTE REVERSIBLE REAL ACTION
      |
      v
VERIFY
      |
      v
ONLY THEN CONSIDER BROADER LIVE OPERATIONS
```

This progression is intentional.

We're teaching the ecosystem to walk before giving it the keys to the van.

---

# Implementation Horizons

## Horizon 1: Make the Contracts Real

```text
Slices 001-003
```

Prove:

```text
Asset
Storage Location
Observation
Finding
Relationship
Snapshot
```

against real data.

---

## Horizon 2: Make VNA Playable

```text
Slices 004-005
```

Prove:

```text
synthetic VNA world
OSI Tower
Finding relationships
finding lifecycle
gameplay from contracts
```

---

## Horizon 3: Cross the Action Boundary

```text
Slices 006-008
```

Prove:

```text
proposal
validation
authorisation
execution
verification
result
world update
```

carefully.

---

## Horizon 4: Make Real Evidence Playable

```text
Slices 009-010
```

Prove:

```text
real captured system
→ playable investigation

real condition
→ verified useful work
```

---

## Horizon 5: Enrich the World

Only after the foundations work:

```text
city services
advanced T4
additional OSI missions
cross-asset worlds
archaeology
multiplayer
vendor worlds
competency
credentials
```

---

# Architecture End State

The slices are gradually building towards this:

```text
                         REALITY
                            |
                            v
                          ASSET
                            |
              +-------------+-------------+
              |                           |
              v                           v
      STORAGE LOCATION              RELATIONSHIP
              |                           |
              +-------------+-------------+
                            |
                            v
                       OBSERVATION
                            |
                            v
                         FINDING
                            |
              +-------------+-------------+
              |                           |
              v                           v
             T4                     VISUAL WORLD
              |                           |
              +-------------+-------------+
                            |
                            v
                          HUMAN
                            |
                            v
                    PROPOSED ACTION
                            |
                            v
                        PROVIDER
                            |
                            v
                        EXECUTION
                            |
                            v
                      VERIFICATION
                            |
                            v
                    VERIFIED RESULT
                            |
                            v
                     RE-OBSERVATION
                            |
                            v
                    FINDING LIFECYCLE
```

Snapshots allow captured reality to enter the same model:

```text
REALITY
   |
   v
SNAPSHOT
   |
   v
ASSETS / STORAGE / OBSERVATIONS / RELATIONSHIPS / FINDINGS
```

Events provide the temporal history:

```text
WHAT HAPPENED
AND WHEN?
```

---

# North Star

The implementation programme should take us from:

```text
"Wouldn't it be cool if..."
```

to:

```text
"Look. It just did it."
```

without jumping directly from vision to an unmaintainable monster.

Every slice should make one more part of the ecosystem undeniable.

First:

> **One ping. One truth. Three explanations.**

Then:

> **Charkive found it. FileCity showed it.**

Then:

> **SysMedic captured it. VNA understood it.**

Then:

> **The player walked through it.**

Then:

> **The player requested a change.**

Then:

> **The correct provider performed it.**

Then:

> **Reality was checked.**

Then, and only then:

> **Mission complete.**

That is how the epic version gets built.

One stubbornly small, demonstrably real slice at a time.
