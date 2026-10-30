# VNA Integration

## Purpose

This document defines the integration boundaries between Virtual Network Admin
(VNA) and the wider family of related projects.

The goal is not to merge those projects into one application.

The goal is to let independently useful tools exchange trustworthy information
through a small set of common concepts.

The guiding rule is:

> **Nothing owns everything. Everything can speak VNA.**

VNA provides the common reality model.

Specialist projects remain responsible for their own domains.

---

# Integration Principles

## Reality first

Real or explicitly synthetic evidence enters the ecosystem before it becomes a
visualisation, explanation, mission, or game mechanic.

```text
REAL / SYNTHETIC SOURCE
          |
          v
      OBSERVATION
          |
          v
       FINDING
          |
          v
    INTERPRETATION
          |
          v
  VISUALISATION / GAME
```

A representation must not silently become the source of truth.

---

## Observation is not inference

Integrations should preserve the distinction between:

```text
Recorded evidence
Observed state
Machine inference
Human conclusion
Recommended action
Executed action
Verified result
```

These may eventually be related, but they must not be silently collapsed into
one another.

---

## Source provenance matters

Every imported observation or finding should retain enough information to
identify where it came from.

Possible source classes include:

```text
live
snapshot
historical
synthetic
vendor
user
```

A synthetic training problem must never masquerade as a live production
incident.

---

## Read before write

The first integrations between projects should favour read-only data exchange.

```text
SOURCE
   |
   v
FINDING
   |
   v
CONSUMER
```

Real-world modification is a separate concern.

Future write-capable integration should follow:

```text
REQUEST
   |
   v
VALIDATE
   |
   v
AUTHORISE
   |
   v
EXECUTE
   |
   v
VERIFY
   |
   v
AUDIT
```

---

## Gameplay is not authorisation

A player completing an action in a virtual world does not implicitly authorise
the equivalent operation on a real system.

Game clients can initially produce:

- investigation results
- diagnoses
- recommendations
- annotations
- mission results
- reports

Any real-world operation remains behind an explicit operational boundary.

---

# Common Concepts

The shared integration vocabulary should remain intentionally small.

The initial model should focus on:

```text
Asset
Observation
Finding
Recommendation
Relationship
Snapshot
```

Later requirements may justify additional objects such as:

```text
Action
Mission
MissionResult
Annotation
Credential
```

Do not introduce those concepts until real integrations require them.

---

# Asset

An `Asset` represents something VNA can identify within an estate.

Examples might include:

```text
Computer
Server
Storage device
Filesystem
Network device
Service
Cloud resource
Virtual machine
Application
```

An asset should have a stable identifier independent of its display name.

Conceptually:

```json
{
  "id": "asset-example",
  "type": "computer",
  "name": "example-host",
  "source": "sysmedic"
}
```

The exact schema remains to be defined.

---

# Observation

An `Observation` represents something that was directly observed or recorded.

Examples might include:

```text
A filesystem is 91% full
A service is stopped
A network interface reports no carrier
A file reports a particular timestamp
A disk reports a SMART condition
A scan found a particular path
```

Observations should avoid introducing conclusions unnecessarily.

For example:

```text
OBSERVATION

Network interface:
No carrier
```

is different from:

```text
INFERENCE

Cable probably disconnected
```

Both may be useful.

They are not the same thing.

---

# Finding

A `Finding` represents something judged worthy of attention.

It should retain a relationship with the observations or evidence that caused
it.

Conceptually:

```json
{
  "id": "finding-example",
  "asset": "asset-example",
  "category": "storage.capacity",
  "severity": "warning",
  "confidence": 0.97,
  "evidence": [],
  "production_changed": false
}
```

The schema above is illustrative.

A finding should answer:

```text
What was found?

Where?

By whom or what?

Why does it matter?

What evidence supports it?

How certain is the conclusion?
```

---

# Recommendation

A `Recommendation` describes a possible response.

It is not an instruction that has automatically been authorised.

For example:

```text
Finding:
Filesystem capacity critically low

Recommendation:
Review disposable-data candidates
```

or:

```text
Finding:
Network interface has no carrier

Recommendation:
Inspect physical network connection
```

Recommendations should remain distinct from actions actually performed.

---

# Relationship

A `Relationship` describes how assets or other objects relate to one another.

Possible relationships include:

```text
connected-to
stored-on
mounted-from
hosted-by
contains
depends-on
observed-by
derived-from
associated-with
```

The vocabulary should evolve from real requirements.

Relationships allow different applications to understand the same estate
without requiring every application to understand every other's internal
models.

---

# Snapshot

A `Snapshot` represents captured evidence from a point in time.

A snapshot is particularly important for systems that are:

- offline
- isolated
- intermittently available
- historical
- unsafe to connect directly
- intentionally disconnected from VNA

A snapshot should record provenance.

Examples include:

```text
source
collector
collector version
capture time
evidence included
evidence omitted
collection failures
integrity information
```

A snapshot should not claim to represent current state after the fact.

---

# SysMedic Integration

SysMedic provides one of the primary ways of bringing real machines into VNA
without requiring a permanent VNA connection.

The intended relationship is:

```text
RUNNING / BROKEN / OFFLINE MACHINE
               |
               v
           SYSMEDIC
               |
               v
       CAPTURED EVIDENCE
               |
               v
       SYSMEDIC SNAPSHOT
               |
               v
              VNA
```

SysMedic remains responsible for:

- evidence collection
- local safety controls
- write protection
- diagnostic tooling
- snapshot provenance
- SysMedic findings
- engineer-facing job reports

VNA remains responsible for incorporating deliberately imported information
into the wider estate model.

---

# SysMedic Portable Snapshot

The SysMedic portable snapshot is distinct from a human-readable job report.

Conceptually:

```text
EVIDENCE
   |
   v
ENCRYPTED PORTABLE SNAPSHOT
   |
   v
SYSMEDIC IMPORT
   |
   v
raw/
   |
   +----> findings/
   |
   +----> job report
   |
   `----> optional VNA export
```

Raw evidence, machine findings, and human conclusions should remain
distinguishable.

Portable snapshots may be moved physically rather than transmitted across a
network.

This preserves SysMedic's offline and no-call-home design.

---

# SysMedic Exchange Boundary

SysMedic may collect evidence from running machines using its exchange
partition and platform-specific collector.

The exchange mechanism should remain separate from VNA.

VNA should not need to know:

```text
which USB partition was used
which drive letter Windows assigned
how the collector was launched
how the encrypted portable package was transported
```

The integration boundary begins with a validated, deliberately imported
snapshot.

---

# Charkive Integration

Charkive provides specialist storage-health, hygiene, preservation, and
data-archaeology findings.

Conceptually:

```text
FILESYSTEM
    |
    v
CHARKIVE
    |
    v
OBSERVATIONS / FINDINGS
    |
    v
VNA
```

Charkive remains responsible for:

- scanning
- classification
- protected data
- candidate data
- SANitorium state
- storage-health findings
- Data Archaeology
- Temporal Anomaly detection
- reversible quarantine
- restoration
- Charkive safety controls

VNA should consume structured Charkive findings rather than reproduce
Charkive's filesystem logic.

---

# Charkive Data Archaeology

Charkive may produce findings describing historically interesting patterns.

These can include:

```text
activity clusters
possible preservation targets
source-device relationships
historical relationships
Temporal Anomalies
```

VNA should preserve the distinction between:

```text
recorded time
observed time
inferred time
user-confirmed time
```

An inferred historical period must not silently replace original metadata.

---

# TEMPORAL ANOMALY Integration

A Charkive `TEMPORAL ANOMALY` or `TA` finding represents evidence that recorded
time appears inconsistent with surrounding context.

Conceptually:

```text
ORIGINAL METADATA
       |
       v
RECORDED TIME
       |
       +----------+
                  |
          OTHER EVIDENCE
                  |
                  v
           TA DETECTION
                  |
                  v
       POSSIBLE INFERRED PERIOD
```

The original recorded timestamp remains evidence.

A consumer may display the anomaly, explain it, or use it in an archaeological
mission.

Consumers must not silently rewrite the underlying timestamp.

---

# FileCity Integration

FileCity provides storage geography.

It may consume:

```text
Assets
Storage relationships
Capacity observations
Charkive findings
VNA findings
Transfer state
```

and produce a spatial representation.

Conceptually:

```text
VNA / STORAGE SOURCE
          |
          v
       FILECITY
          |
          v
      WORLD MODEL
          |
          v
VISUAL REPRESENTATION
```

FileCity remains responsible for concepts such as:

```text
District
Block
Building
Floor
Route
Storage geography
```

VNA does not need to understand how FileCity draws the city.

---

# FileCity World References

Visual objects should retain a reference to the underlying VNA or specialist
object where appropriate.

For example:

```text
FILECITY BUILDING

world-id:
building-42

represents:
asset-104

findings:
finding-32
finding-47
```

The building is not the asset.

It represents the asset.

This distinction allows the visual world to change without changing
operational identity.

---

# Telemetry4Toddlers Integration

Telemetry4Toddlers, or T4, provides an explanation layer.

It should be able to take the same underlying observation or finding and
explain it differently according to the intended audience.

Conceptually:

```text
VNA FINDING
     |
     v
     T4
     |
  +--+-------+---------+
  |          |         |
  v          v         v
T4Tots    T4Teens   T4Techies
```

T4 must not create different realities for different audiences.

The explanation changes.

The underlying finding does not.

---

# T4 Example

Underlying finding:

```text
Network interface reports no carrier.
```

Possible explanations:

```text
T4Tots

"The computers can't talk because their connection is broken."


T4Teens

"The physical network connection is down, so the higher networking layers
cannot communicate."


T4Techies

[original interface state, evidence, timestamps and diagnostic context]
```

---

# Binary4Babies Integration

Binary4Babies provides educational material and conceptual foundations.

Its role is different from T4.

T4 answers:

> **What does this current observation mean at my level?**

Binary4Babies can answer:

> **Teach me the underlying concept.**

A finding may therefore link to educational material without embedding that
teaching material directly into VNA's core schema.

---

# BoreDOOM! Integration

BoreDOOM! is fundamentally a domestic chore game.

Its core responsibility is not IT administration.

However, it develops useful gameplay concepts that may be reused by VNA:

```text
World
Player
Scale
Mission
Objective
Tool
Vehicle
Interaction
Progress
Result
Report
Multiplayer
Generalisation
```

VNA may use similar interaction models for IT learning and administration
without moving VNA-specific logic into the BoreDOOM! repository.

---

# VNA Gameplay Boundary

For VNA-specific interactive worlds:

```text
VNA
 |
 | structured reality
 v
GENERALISATION
 |
 v
GAME WORLD
 |
 v
PLAYER
 |
 v
MISSION RESULT
 |
 v
VNA REPORT
```

The game client need not receive unrestricted operational information.

The game world may be a generalised representation of the real estate.

---

# OSI World Integration

The OSI model belongs primarily to VNA's learning and diagnostic world.

A networked asset can be represented through seven conceptual layers:

```text
L7  Application
L6  Presentation
L5  Session
L4  Transport
L3  Network
L2  Data Link
L1  Physical
```

A fault may originate at one layer while affecting higher layers.

For example:

```text
L7  Application unavailable
 ↑
L6  Communication unavailable
 ↑
L5  Session unavailable
 ↑
L4  Transport unavailable
 ↑
L3  Network unavailable
 ↑
L2  Link unavailable
 ↑
L1  Physical connection lost
```

The game representation should preserve the difference between:

```text
ROOT CAUSE

and

PROPAGATED SYMPTOM
```

---

# Operating-System World Integration

VNA may eventually provide different interactive representations for:

```text
Windows
Linux
macOS
```

These worlds should map operating-system-specific mechanisms back to common
VNA concepts where useful.

For example:

```text
Service
Log source
Storage
Boot state
Update state
Network state
Application
```

The common model should not pretend the operating systems are internally
identical.

It provides common integration points while preserving platform-specific
evidence.

---

# Oracle Integration

AI assistants used within VNA worlds can act as Oracles.

Oracles consume authorised evidence and help players reason about it.

They should distinguish between:

```text
Evidence
Hypothesis
Inference
Recommendation
Action
```

An Oracle should not silently promote a hypothesis into evidence.

Oracle output may be adjusted according to player experience.

For example:

```text
BEGINNER

"Layer 1 appears unhealthy. Inspect the physical connection."


INTERMEDIATE

"Which layer would you investigate next?"


EXPERT

[raw evidence]
```

The evidence remains the same.

---

# Mission

A future `Mission` represents work presented to a player.

A mission should retain a reference to its source.

Possible sources include:

```text
Synthetic tutorial
Procedural simulation
SysMedic snapshot
Charkive finding
VNA finding
Vendor scenario
Authorised live environment
```

A mission therefore needs provenance just as evidence does.

---

# MissionResult

A `MissionResult` represents what happened during gameplay.

It may include:

```text
mission ID
players
evidence reviewed
conclusion
confidence
recommendation
escalation
game actions
claimed real-world outcome
verified real-world outcome
```

The distinction between claimed and verified outcomes matters.

Gameplay must not manufacture operational certainty.

---

# Generalisation

Generalisation is the privacy and abstraction boundary between operational
reality and clients that do not require full detail.

Conceptually:

```text
REALITY MODEL
      |
      v
GENERALISATION
      |
      v
CLIENT MODEL
```

Example:

```text
REAL

Hostname:
EDDIE

Path:
/srv/private/customer/archive


GENERALISED

Asset:
SERVER-104

Location:
North Storage District

Collection:
Archive Tower
```

The generalised representation should preserve only information required for
the intended use.

---

# Generalisation Levels

A future implementation may support levels such as:

## Full fidelity

Authorised operational users can see real identities.

## Pseudonymised

Stable aliases replace sensitive identifiers.

## Generalised

Technical characteristics remain while unnecessary identity information is
removed.

## Abstract

Only information required for the investigation or training scenario remains.

The exact mechanism should be developed from actual security requirements.

---

# Reverse Mapping

Clients should not automatically receive the mapping from generalised
identities back to real infrastructure.

Conceptually:

```text
AUTHORISED VNA

SERVER-104
   =
EDDIE


GAME CLIENT

SERVER-104
```

This allows a player or external specialist to investigate the shape of a
problem without necessarily learning unnecessary infrastructure details.

---

# Read-Only First

The initial VNA ecosystem should make this path easy:

```text
REAL SYSTEM
      |
      v
OBSERVATION
      |
      v
FINDING
      |
      v
REPORT / VISUALISATION / GAME
```

before making this path possible:

```text
GAME
  |
  v
REAL SYSTEM CHANGE
```

Read-only integration provides enormous value without immediately creating a
high-risk remote-administration architecture.

---

# Future Action Boundary

When real operations are eventually required:

```text
PLAYER / CLIENT
      |
      v
REQUESTED ACTION
      |
      v
VNA
      |
      v
POLICY
      |
      v
AUTHORISATION
      |
      v
SPECIALIST TOOL
      |
      v
EXECUTION
      |
      v
VERIFICATION
      |
      v
AUDIT
```

The specialist tool remains responsible for its own safety requirements.

For example:

```text
Charkive
```

remains responsible for Charkive filesystem safeguards.

VNA should not bypass those safeguards simply because an action originated
elsewhere.

---

# Verification

An action being requested does not mean it happened.

A command returning does not necessarily mean the intended result occurred.

Where real operations exist, integration should distinguish:

```text
requested
authorised
started
completed
verified
failed
rolled back
unknown
```

The visual world should preferably reflect verified state rather than optimistic
assumptions.

---

# Audit

Cross-project operations should eventually create enough audit information to
answer:

```text
What happened?

Why?

Which finding caused it?

Who requested it?

Who authorised it?

Which system performed it?

What changed?

Was the result verified?

What evidence exists?
```

Playable interfaces should make infrastructure work more understandable, not
less accountable.

---

# MCP and Agent Integration

Future VNA integrations may expose selected capabilities through an agent or
tool protocol.

The architectural rule remains the same regardless of transport:

```text
AGENT
   |
   v
DEFINED CAPABILITY
   |
   v
VNA / SPECIALIST SERVICE
```

Do not expose arbitrary shell access merely because an agent can request it.

Prefer explicit capabilities such as:

```text
get_assets
get_findings
get_snapshot
get_storage_health
get_world_model
create_report
```

Write capabilities should be introduced separately and conservatively.

The names above are illustrative rather than a committed API.

---

# Versioning

Shared integration formats must be versioned.

Examples:

```text
vna.asset/0.1
vna.finding/0.1
vna.snapshot/0.1
```

Consumers should not assume future schema revisions are automatically
compatible.

During early development, changing the model is expected.

Versioning simply makes those changes explicit.

---

# Extensibility

The common model should support project-specific information without requiring
VNA to understand every specialist detail.

For example, a Charkive finding may contain Charkive-specific evidence.

A FileCity object may contain rendering-specific metadata.

A SysMedic snapshot may contain collector-specific provenance.

Common concepts provide the join points.

Specialist systems keep specialist knowledge.

---

# Integration Test Philosophy

Each integration should be testable independently.

Useful integration tests include:

```text
SysMedic snapshot
       |
       v
VNA importer
       |
       v
Asset + findings


Charkive finding
       |
       v
VNA
       |
       v
FileCity representation


VNA finding
       |
       v
Generalisation
       |
       v
Mission
       |
       v
Mission result
```

Synthetic fixtures should exist for testing.

They must be clearly marked as synthetic.

---

# First Integration Slice

Do not attempt to integrate the entire ecosystem at once.

The first useful end-to-end path should be:

```text
REAL MACHINE
     |
     v
SYSMEDIC
     |
     v
ENCRYPTED SNAPSHOT
     |
     v
SYSMEDIC IMPORT
     |
     v
STRUCTURED FINDING
     |
     v
VNA
     |
     v
HUMAN-READABLE REPORT
```

No VR is necessary.

No multiplayer is necessary.

No live control is necessary.

If this works, VNA has successfully incorporated trustworthy evidence from a
real disconnected machine.

---

# Second Integration Slice

Connect Charkive.

```text
REAL STORAGE
     |
     v
CHARKIVE
     |
     v
STRUCTURED FINDING
     |
     v
VNA
     |
     v
FILECITY
     |
     v
VISIBLE CONDITION
```

The visual condition must remain traceable to the original Charkive evidence.

---

# Third Integration Slice

Add generalisation.

```text
REAL FINDING
     |
     v
VNA
     |
     v
GENERALISED FINDING
     |
     v
CLIENT
```

Prove that a client can perform a useful investigation without receiving
unnecessary sensitive details.

---

# Fourth Integration Slice

Generate one read-only mission.

```text
REAL OR SNAPSHOT FINDING
          |
          v
         VNA
          |
          v
    GENERALISATION
          |
          v
       MISSION
          |
          v
       PLAYER
          |
          v
   MISSION RESULT
          |
          v
       REPORT
```

The mission performs no real remediation.

This proves the core idea safely.

---

# Integration Ownership

A simple ownership rule should be maintained.

```text
VNA
Reality model
Infrastructure relationships
Generalisation
Cross-project orchestration


SysMedic
Machine evidence collection
Rescue environment
Offline snapshots
Field diagnostics


Charkive
Storage health
Hygiene
Preservation
Data Archaeology
Temporal Anomalies
Reversible quarantine


FileCity
Storage geography
Spatial representation
Storage movement


T4
Audience-specific explanation


Binary4Babies
Foundational education


BoreDOOM!
Domestic chore gameplay
Spatial gameplay experimentation
Human/Borrower scale
Reusable gameplay ideas
```

No project should casually absorb another project's responsibilities.

---

# North Star

The ecosystem succeeds when this becomes possible:

```text
REALITY
   |
   v
TRUSTWORTHY EVIDENCE
   |
   v
STRUCTURED UNDERSTANDING
   |
   v
SAFE GENERALISATION
   |
   v
EXPLANATION / VISUALISATION / GAMEPLAY
   |
   v
HUMAN INVESTIGATION
   |
   v
USEFUL REPORT
```

and, only where explicitly authorised:

```text
USEFUL REPORT
   |
   v
PROPOSED ACTION
   |
   v
AUTHORISATION
   |
   v
SPECIALIST TOOL
   |
   v
REAL CHANGE
   |
   v
VERIFICATION
   |
   v
AUDIT
```

The individual projects remain useful on their own.

Together, they allow real machines, real storage, captured evidence, educational
systems, spatial worlds, and gameplay to describe different views of the same
underlying reality.

> **Reality first. Metaphor second. Evidence always.**
