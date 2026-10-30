# Filekive Ecosystem Integration Contract

## Purpose

The Filekive ecosystem consists of independent applications which may cooperate without becoming tightly coupled.

The current conceptual responsibilities are:

```text
Virtual Network Admin
    Infrastructure truth, telemetry and operational authority

FileCity
    Storage geography, spatial representation and data movement

Charkive
    Storage health, preservation, findings and controlled response

BoreDOOM!
    Standalone gameplay with optional ecosystem integrations
```

The applications should remain useful independently.

In particular:

> BoreDOOM! must remain a fun standalone game without VNA, FileCity or Charkive.

Integration happens through small, versioned, machine-readable contracts.

---

# Design Principles

## 1. Reality First

The real system is authoritative.

```text
REAL SYSTEM
     ↓
OBSERVATION
     ↓
STRUCTURED STATE
     ↓
VISUALISATION / GAMEPLAY
```

A renderer must never become the source of truth for infrastructure or storage state.

---

## 2. Producers Own Meaning

The component that understands a condition defines its meaning.

Examples:

```text
VNA
owns infrastructure state

Charkive
owns Charkive findings and Charkive operations

FileCity
owns spatial representation

BoreDOOM!
owns gameplay representation
```

FileCity should not reproduce Charkive's detection logic.

BoreDOOM! should not reproduce VNA's authorisation logic.

---

## 3. Consumers Own Presentation

The same event may be presented differently.

For example:

```text
storage.capacity.warning
```

might appear as:

```text
CLI:
WARNING: storage capacity threshold reached

FileCity:
A distressed building

Retro game:
Pixel-art fire engine

3D world:
Emergency response at a storage facility
```

These are presentations of the same underlying information.

---

## 4. Stable IDs, Replaceable Renderers

Every significant object should have a stable machine-readable identifier.

A building, sprite, 3D mesh or vehicle is replaceable.

Its relationship with the underlying object should survive renderer changes.

---

## 5. Version Every Contract

Each interchange object carries a schema identifier.

For example:

```json
{
  "schema": "filekive.finding/0.1"
}
```

Consumers must not assume that every future version has exactly the same structure.

---

## 6. Unknown Fields Are Not Errors

Consumers should normally ignore fields they do not understand.

This permits producers to add information without immediately breaking older clients.

---

## 7. Metaphor Never Replaces Evidence

If a FileCity building is on fire because of a real Charkive finding, the user must be able to reach the actual finding.

```text
🔥
 ↓
FileCity object
 ↓
Finding
 ↓
Evidence
 ↓
Real storage
```

No sirens without evidence.

---

# Contract 1: Asset

An asset represents an identifiable real or logical resource.

Typical producers:

```text
VNA
FileCity
Storage adapters
```

Possible assets include:

```text
machine
server
storage device
storage cluster
network device
service
cloud account
```

## Schema

```json
{
  "schema": "filekive.asset/0.1",
  "id": "asset:eddie",
  "kind": "machine",
  "name": "eddie",
  "status": "healthy",
  "source": {
    "system": "vna",
    "id": "eddie"
  },
  "attributes": {}
}
```

## Required Fields

```text
schema
id
kind
name
```

## Principle

The `id` identifies the logical object.

The display name may change without creating a new asset identity.

---

# Contract 2: Storage Location

A storage location represents somewhere data can reside.

A storage location is deliberately broader than an rclone remote.

Possible examples:

```text
local filesystem
disk
USB device
NAS
SMB share
GlusterFS
Google Drive
OneDrive
Dropbox
S3
archive storage
```

## Schema

```json
{
  "schema": "filekive.storage_location/0.1",
  "id": "storage:eddie:data",
  "name": "Eddie Data",
  "kind": "filesystem",
  "provider": "local",
  "capacity_bytes": 1000000000000,
  "used_bytes": 640000000000,
  "asset_id": "asset:eddie",
  "status": "healthy",
  "source": {
    "system": "vna",
    "id": "eddie:data"
  },
  "attributes": {}
}
```

## Required Fields

```text
schema
id
name
kind
```

Capacity information is optional because not every provider will necessarily supply meaningful capacity values.

---

# Contract 3: Finding

A finding represents something observed by an analysis system.

Charkive is expected to be an important producer of findings.

A finding describes reality.

It does not automatically authorise an action.

## Schema

```json
{
  "schema": "filekive.finding/0.1",
  "id": "finding:example-001",
  "producer": "charkive",
  "category": "storage.disposable_candidate",
  "severity": "notice",
  "confidence": 0.92,
  "storage_location_id": "storage:eddie:data",
  "path": "cache/example.tmp",
  "classification": "candidate",
  "summary": "Likely disposable temporary data",
  "evidence": [
    {
      "type": "rule_match",
      "rule": "temporary-file-pattern"
    }
  ],
  "recommended_action": "review_for_quarantine",
  "production_changed": false
}
```

## Required Fields

```text
schema
id
producer
category
severity
```

---

# Severity

Initial vocabulary:

```text
info
notice
warning
critical
```

Severity describes importance.

It does not describe confidence.

---

# Confidence

Where applicable:

```text
0.0 → completely uncertain
1.0 → maximum confidence
```

A producer may omit confidence when confidence scoring is meaningless.

---

# Evidence

Evidence explains why the producer generated the finding.

Consumers should preserve access to evidence even when presenting simplified representations.

For example:

```text
FileCity
🔥 Cache Tower

Inspect
    ↓

Finding:
Likely disposable temporary data

Evidence:
Matched temporary-file rule
```

---

# Contract 4: Event

An event represents something that happened.

Unlike a finding, an event is normally temporal.

Examples:

```text
scan completed
finding generated
transfer started
transfer completed
quarantine completed
restore completed
service unavailable
service restored
```

## Schema

```json
{
  "schema": "filekive.event/0.1",
  "id": "event:example-001",
  "producer": "charkive",
  "type": "quarantine.completed",
  "timestamp": "2026-10-30T07:00:00Z",
  "subject": {
    "type": "finding",
    "id": "finding:example-001"
  },
  "severity": "info",
  "data": {}
}
```

## Required Fields

```text
schema
id
producer
type
timestamp
```

---

# Events vs Findings

Do not treat these as interchangeable.

A finding says:

```text
"This condition exists."
```

An event says:

```text
"This happened."
```

For example:

```text
FINDING

Likely disposable cache exists
```

then:

```text
EVENT

Quarantine completed
```

---

# Contract 5: World Object

World objects belong primarily to FileCity's representation layer.

They connect real objects to geography.

Examples:

```text
district
block
building
floor
facility
route
```

## Schema

```json
{
  "schema": "filekive.world_object/0.1",
  "id": "world:building:projects",
  "kind": "building",
  "name": "Projects",
  "parent_id": "world:district:eddie",
  "represents": [
    {
      "type": "storage_path",
      "storage_location_id": "storage:eddie:data",
      "path": "Projects"
    }
  ],
  "metrics": {
    "size_bytes": 420000000000
  },
  "state": {
    "health": "healthy",
    "activity": "normal"
  },
  "presentation": {}
}
```

## Important Boundary

Storage systems do not need to understand buildings.

Charkive might report:

```text
Storage:
eddie:data

Path:
Projects/cache
```

FileCity determines:

```text
District:
Eddie

Building:
Projects

Floor:
Cache
```

This keeps geography out of Charkive.

---

# Presentation Hints

A world object may eventually contain presentation hints.

These must remain hints.

For example:

```json
{
  "presentation": {
    "importance": 0.8
  }
}
```

Avoid putting renderer-specific details into the shared contract.

Bad:

```json
{
  "unreal_mesh": "SM_Skyscraper_837",
  "beamng_object": "tower04"
}
```

Those belong in renderer adapters.

---

# Contract 6: Proposed Action

A proposed action represents a request to change reality.

This contract requires a much stronger trust boundary than read-only contracts.

Consumers may propose actions.

The system responsible for the affected resource decides whether the action is permitted.

## Schema

```json
{
  "schema": "filekive.proposed_action/0.1",
  "id": "action:example-001",
  "requester": "filecity",
  "provider": "charkive",
  "action": "quarantine",
  "target": {
    "finding_id": "finding:example-001"
  },
  "dry_run": true,
  "requested_by": {
    "type": "user",
    "id": "user:local"
  }
}
```

## Critical Rule

Receiving a proposed action does **not** authorise its execution.

The provider must independently evaluate:

```text
Is the action known?

Does the target exist?

Is it still in the expected state?

Is the user authorised?

Does this require confirmation?

Is dry-run required?

Is the operation reversible?

Are safety checks satisfied?
```

Only the provider can decide whether execution proceeds.

---

# Game Clients and Actions

Game clients are untrusted interfaces.

Conceptually:

```text
BoreDOOM!
    ↓
"I want to quarantine this."
    ↓
Proposed Action
    ↓
Charkive
    ↓
Validation
    ↓
Authorisation
    ↓
Execution
```

Never:

```text
BoreDOOM!
    ↓
filesystem command
```

Likewise:

```text
FileCity
    ↓
VNA privileged command
```

must not occur directly.

---

# Contract 7: Verified Result

A verified result records the outcome of an attempted real-world action.

## Schema

```json
{
  "schema": "filekive.verified_result/0.1",
  "id": "result:example-001",
  "action_id": "action:example-001",
  "provider": "charkive",
  "status": "succeeded",
  "executed": true,
  "verified": true,
  "reversible": true,
  "timestamp": "2026-10-30T07:05:00Z",
  "result": {
    "destination": "SANitorium/example"
  },
  "evidence": []
}
```

## Suggested Status Vocabulary

```text
proposed
rejected
cancelled
failed
succeeded
```

Execution and verification remain separate concepts.

For example:

```json
{
  "status": "failed",
  "executed": true,
  "verified": false
}
```

can represent an operation that ran but did not produce the expected verified result.

---

# End-to-End Lifecycle

The complete operational contract becomes:

```text
REALITY
   ↓
Asset / Storage Location
   ↓
Finding
   ↓
Event
   ↓
World Object
   ↓
User investigates
   ↓
Proposed Action
   ↓
Provider validates
   ↓
Provider executes
   ↓
Provider verifies
   ↓
Verified Result
   ↓
New events / findings
   ↓
World updates
```

---

# First Integration Slice

The first implementation should be deliberately tiny.

Do not attempt to implement every contract at once.

## Goal

Prove this:

```text
REAL FILESYSTEM
      ↓
CHARKIVE SCAN
      ↓
STRUCTURED FINDING
      ↓
FILECITY
      ↓
BUILDING CHANGES STATE
      ↓
USER CLICKS BUILDING
      ↓
REAL FINDING + EVIDENCE
```

No real operation needs to occur.

This first slice can be entirely read-only.

---

# Slice 1: Charkive Finding

Charkive exports one genuine finding using:

```text
filekive.finding/0.1
```

For example:

```json
{
  "schema": "filekive.finding/0.1",
  "id": "finding:001",
  "producer": "charkive",
  "category": "storage.disposable_candidate",
  "severity": "notice",
  "storage_location_id": "storage:local:test",
  "path": "Projects/cache",
  "summary": "Likely disposable data found",
  "production_changed": false
}
```

Success:

> The finding exists independently of Charkive's CLI or HTML representation.

---

# Slice 2: FileCity Ingests the Finding

FileCity matches:

```text
storage_location_id
+
path
```

to an existing world object.

For example:

```text
storage:local:test
Projects/cache

        ↓

Projects Tower
Cache Floor
```

Success:

> FileCity knows which part of the city the finding relates to.

---

# Slice 3: Visual State

FileCity changes the representation.

Initially, keep this simple.

For example:

```text
normal building
      ↓
warning marker
```

Do not build fire engines yet.

Success:

> Real Charkive state causes a visible FileCity change.

---

# Slice 4: Inspection

Selecting the affected world object reveals:

```text
Finding
Category
Severity
Summary
Evidence
Storage location
Path
Producer
```

Success:

> The user can travel from metaphor back to reality.

This proves one of the ecosystem's fundamental principles.

---

# Slice 5: Live Update

Once the static integration works:

```text
Charkive scans again
      ↓
Finding changes
      ↓
FileCity receives update
      ↓
World changes
```

Success:

> The city responds to real storage state.

At this point FileCity begins becoming a genuine digital twin rather than a generated illustration.

---

# Slice 6: First Event

Add:

```text
filekive.event/0.1
```

Start with one meaningful Charkive event.

For example:

```text
scan.completed
```

or:

```text
quarantine.completed
```

Success:

> FileCity can distinguish persistent state from something that happened.

---

# Slice 7: First Safe Action

Only after read-only integration is trustworthy should another application request a Charkive operation.

Initially use dry-run.

```text
FileCity
    ↓
Proposed Action

quarantine
dry_run = true
    ↓
Charkive
    ↓
Validation
    ↓
Preview result
```

Success:

> One application can safely request an operation without acquiring Charkive's underlying privileges or bypassing its safeguards.

---

# Slice 8: First Verified Real Action

Perform one deliberately low-risk authorised operation through the complete lifecycle.

```text
Propose
   ↓
Validate
   ↓
Authorise
   ↓
Execute
   ↓
Verify
   ↓
Report
```

Success:

> A real filesystem change can be initiated through the integration boundary and independently verified by the responsible provider.

---

# Slice 9: First Game Representation

Only now does BoreDOOM! need to become involved.

BoreDOOM! receives a simplified representation of a genuine condition.

For example:

```text
REAL CHARKIVE FINDING
        ↓
FILECITY LOCATION
        ↓
BOREDOOM! OBJECTIVE
```

The first implementation can remain completely read-only.

Success:

> A real-world condition creates meaningful gameplay without giving the game privileged infrastructure access.

---

# Slice 10: First Real-Work Gameplay Loop

Eventually:

```text
REAL CONDITION
      ↓
FINDING
      ↓
WORLD REPRESENTATION
      ↓
GAME OBJECTIVE
      ↓
PLAYER INVESTIGATION
      ↓
PROPOSED ACTION
      ↓
AUTHORITATIVE PROVIDER
      ↓
VALIDATION
      ↓
EXECUTION
      ↓
VERIFICATION
      ↓
VERIFIED RESULT
      ↓
WORLD CHANGES
      ↓
MISSION COMPLETE
```

This is the first complete expression of the ecosystem vision.

---

# BoreDOOM! Independence

BoreDOOM!'s primary game loop must not depend upon these contracts.

Its standalone gameplay can remain:

```text
Real physical room
      ↓
Scan / model
      ↓
Virtual dolls' house
      ↓
Explore
      ↓
Build
      ↓
Tidy / organise real space
      ↓
Rescan / confirm
      ↓
Virtual reward
```

Filekive integration is an optional additional source of meaningful world state and objectives.

This distinction is fundamental.

A player does not need to understand:

```text
servers
storage
VNA
Charkive
FileCity
network administration
```

to enjoy BoreDOOM!.

---

# Professional Capability Boundary

Any future VNA-connected professional gameplay should introduce an explicit capability boundary.

```text
BOREDOOM! CORE
     │
     │ optional integration
     ↓
PROFESSIONAL CAPABILITY LAYER
     ↓
VNA / CHARKIVE / OTHER PROVIDERS
```

The professional layer may use training, assessment, authorisation and credentials.

Those mechanisms must not become prerequisites for ordinary BoreDOOM! gameplay.

The dolls' house comes first.

The data centre is optional.

---

# Future Renderers

These contracts deliberately do not define rendering technology.

Consumers might eventually include:

```text
Web
Retro 2D
2.5D
BeamNG
Unreal
Unity
VR
AR
BoreDOOM!
Other future clients
```

A Charkive finding should mean the same thing whether represented by:

```text
!
```

in a terminal,

```text
🚒
```

in a retro platform game,

or a fully animated emergency response in a future 3D environment.

---

# Retro-First Principle

Early graphical integrations should favour simplicity.

A useful progression might be:

```text
Structured JSON
      ↓
CLI / Web
      ↓
Retro 2D
      ↓
2.5D
      ↓
3D
      ↓
VR / AR
```

The sophistication of the interface may improve.

The meaning of the underlying contracts should remain stable.

This lets functionality arrive before graphical perfection.

---

# Trust Boundaries

The ecosystem has three broad trust levels.

## Level 1: Observation

```text
Assets
Storage locations
Findings
Events
World objects
```

Primarily read-only.

Low risk.

---

## Level 2: Proposal

```text
Proposed actions
```

A client can ask for something to happen.

It cannot assume that it will happen.

---

## Level 3: Execution

Owned by the authoritative provider.

Examples:

```text
Charkive executes Charkive operations.

VNA executes authorised infrastructure operations.

FileCity executes FileCity-controlled migration operations.
```

Game engines and renderers should not inherit these privileges merely by being connected.

---

# Audit Principle

Every real-world action should eventually be traceable through:

```text
Who requested it?
What was requested?
What object was targeted?
Which provider approved it?
What executed?
What happened?
Was it verified?
```

A playful interface must never make real operations less accountable.

---

# Failure Principle

Failure is real state.

Do not visually turn:

```text
command returned
```

into:

```text
mission accomplished
```

until the authoritative provider reports the expected outcome.

For example:

```text
Rocket launches
      ↓
Transfer occurs
      ↓
Destination receives data
      ↓
Verification occurs
      ↓
ONLY THEN
      ↓
Successful delivery
```

If verification fails, the world should show failure or uncertainty rather than success.

---

# Repository Ownership

## Virtual Network Admin

Owns:

```text
Infrastructure assets
Infrastructure telemetry
Infrastructure operational state
Infrastructure authorisation
VNA operations
VNA verification
```

Does not own:

```text
FileCity geography
Charkive classification
BoreDOOM! gameplay
```

---

## FileCity

Owns:

```text
Storage geography
World model
Districts
Buildings
Spatial aggregation
Storage movement visualisation
FileCity migration behaviour
```

Does not own:

```text
VNA infrastructure authority
Charkive detection rules
BoreDOOM! core gameplay
```

---

## Charkive

Owns:

```text
Storage scanning
Classification
Findings
Evidence
Quarantine
SANitorium
Restoration
Charkive safety policy
Charkive verification
```

Does not own:

```text
FileCity geography
VNA infrastructure state
BoreDOOM! gameplay
```

---

## BoreDOOM!

Owns:

```text
Gameplay
Player experience
Virtual dolls' house
Room exploration
Building
Domestic improvement loop
Multiplayer
Game representation
```

Optionally consumes:

```text
FileCity world state
Charkive findings
VNA professional integrations
```

BoreDOOM! remains playable when every optional integration is absent.

---

# The Ecosystem in One Diagram

```text
                        REAL WORLD
                            │
          ┌─────────────────┴─────────────────┐
          │                                   │
    INFRASTRUCTURE                         STORAGE
          │                                   │
          ↓                                   ↓
         VNA                              CHARKIVE
          │                                   │
          │ assets / state          findings / actions
          │                                   │
          └───────────────┬───────────────────┘
                          ↓
                       FILECITY
                    data geography
                          │
                          │ optional
                          ↓
                      BOREDOOM!
                        gameplay

                          +

                      BOREDOOM!
                  standalone home loop
                          │
                          ↓
                  REAL HOME IMPROVEMENT
```

---

# North Star

The ecosystem should make real systems easier to understand and useful work more satisfying without confusing simulation with reality.

```text
OBSERVE REALITY
      ↓
UNDERSTAND IT
      ↓
REPRESENT IT
      ↓
INTERACT WITH IT
      ↓
ACT SAFELY
      ↓
VERIFY THE RESULT
```

And wherever possible:

> **Make useful work feel like play, without making play dependent on work.**
