# Finding v0.1 Schema

## Purpose

A **Finding** is a machine-readable statement that a system observed something noteworthy.

It represents an observation, classification, or condition.

It does **not** represent permission to change anything.

```text
OBSERVE
   ↓
FINDING
   ↓
EVIDENCE
   ↓
RECOMMENDATION
```

A Finding may later lead to a Proposed Action, but those are deliberately separate contracts.

The initial ecosystem producer is expected to be Charkive, but the schema is intentionally generic enough for other Filekive components to produce findings later.

---

# Schema Identifier

Every Finding MUST identify its contract version:

```json
{
  "schema": "filekive.finding/0.1"
}
```

The identifier is:

```text
filekive.finding/0.1
```

Consumers MUST NOT assume that future schema versions are structurally identical.

Consumers SHOULD ignore unknown fields unless those fields are explicitly required by the schema version they implement.

---

# Canonical Example

```json
{
  "schema": "filekive.finding/0.1",
  "id": "finding:0192f7b2-example",
  "producer": "charkive",
  "observed_at": "2026-10-30T07:00:00Z",
  "category": "storage.disposable_candidate",
  "severity": "notice",
  "confidence": 0.92,
  "subject": {
    "type": "storage_path",
    "storage_location_id": "storage:eddie:data",
    "path": "Projects/example/cache.tmp"
  },
  "classification": "candidate",
  "summary": "Likely disposable temporary data",
  "evidence": [
    {
      "type": "rule_match",
      "rule": "temporary-file-pattern",
      "description": "Path matched a configured disposable-data rule."
    }
  ],
  "recommended_action": {
    "type": "review_for_quarantine"
  },
  "production_changed": false,
  "attributes": {}
}
```

---

# Required Fields

A Finding v0.1 MUST contain:

```text
schema
id
producer
observed_at
category
severity
subject
summary
production_changed
```

The following fields are optional:

```text
confidence
classification
evidence
recommended_action
attributes
```

---

# Field: `schema`

## Type

```text
string
```

## Required

Yes.

## v0.1 Value

```text
filekive.finding/0.1
```

## Purpose

Identifies the contract and its version.

Example:

```json
{
  "schema": "filekive.finding/0.1"
}
```

---

# Field: `id`

## Type

```text
string
```

## Required

Yes.

## Purpose

Provides a stable identifier for the Finding.

Example:

```json
{
  "id": "finding:0192f7b2-example"
}
```

The identifier SHOULD be globally unique within the Filekive ecosystem.

Consumers MUST treat the ID as opaque.

They MUST NOT infer semantics from its internal structure.

---

# Finding Identity

A Finding ID identifies the logical Finding rather than merely a particular JSON serialisation of it.

If a producer republishes exactly the same Finding, its ID SHOULD remain stable.

A genuinely distinct observation MAY receive a new Finding ID.

More sophisticated lifecycle and revision semantics are deliberately deferred beyond v0.1.

---

# Field: `producer`

## Type

```text
string
```

## Required

Yes.

## Purpose

Identifies the system responsible for producing the Finding.

Example:

```json
{
  "producer": "charkive"
}
```

Possible producers may eventually include:

```text
charkive
vna
filecity
other future detectors
```

The producer owns the semantics of its detector and evidence.

A consumer MUST NOT present itself as the producer of a Finding merely because it displays it.

---

# Field: `observed_at`

## Type

```text
string
```

## Format

UTC ISO 8601 timestamp.

## Required

Yes.

Example:

```json
{
  "observed_at": "2026-10-30T07:00:00Z"
}
```

## Purpose

Records when the underlying condition was observed.

This is not necessarily:

```text
the time it was displayed
the time it was transmitted
the time it was ingested by FileCity
the time an action subsequently occurred
```

Those are separate events.

---

# Field: `category`

## Type

```text
string
```

## Required

Yes.

## Purpose

Provides a machine-readable classification of the kind of condition observed.

Categories SHOULD use dotted namespaces.

Example:

```json
{
  "category": "storage.disposable_candidate"
}
```

Initial Charkive-oriented categories may include:

```text
storage.disposable_candidate
storage.protected
storage.quarantined
storage.preflight_warning
storage.preflight_failure
```

Categories should only be introduced for conditions actually understood by a producer.

Do not create fictional categories merely because a future visualisation might look entertaining.

---

# Category Semantics

A category describes:

> **What kind of thing was observed?**

It does not describe:

```text
how serious it is
how confident the producer is
what should be done
whether anything has already been changed
```

Those concepts have separate fields.

---

# Field: `severity`

## Type

```text
string
```

## Required

Yes.

## Allowed v0.1 Values

```text
info
notice
warning
critical
```

## Meaning

### `info`

Useful information which does not itself indicate a problem.

### `notice`

Something worth bringing to the user's attention.

### `warning`

A condition deserving meaningful attention or investigation.

### `critical`

A condition the producer considers highly important or urgent.

---

# Severity Is Not Confidence

These are independent dimensions.

For example:

```json
{
  "severity": "notice",
  "confidence": 0.99
}
```

may mean:

> Charkive is extremely confident about a condition which is not particularly urgent.

Conversely:

```json
{
  "severity": "critical",
  "confidence": 0.61
}
```

may describe something potentially very important whose exact diagnosis remains uncertain.

Consumers MUST NOT derive one from the other.

---

# Field: `confidence`

## Type

```text
number
```

## Required

No.

## Range

```text
0.0 <= confidence <= 1.0
```

Example:

```json
{
  "confidence": 0.92
}
```

## Meaning

Where the producer supports confidence scoring:

```text
0.0 = minimum confidence
1.0 = maximum confidence
```

The absence of this field means:

> **No confidence value was supplied.**

It MUST NOT automatically be interpreted as:

```text
0.0
0.5
1.0
unknown quality
```

A producer which cannot calculate meaningful confidence SHOULD omit the field.

---

# Field: `subject`

## Type

```text
object
```

## Required

Yes.

## Purpose

Identifies what the Finding concerns.

For the initial Charkive/FileCity integration, the primary subject type is:

```text
storage_path
```

Example:

```json
{
  "subject": {
    "type": "storage_path",
    "storage_location_id": "storage:eddie:data",
    "path": "Projects/example/cache.tmp"
  }
}
```

---

# Subject: `storage_path`

A storage-path subject contains:

```text
type
storage_location_id
path
```

Example:

```json
{
  "type": "storage_path",
  "storage_location_id": "storage:eddie:data",
  "path": "Projects/cache"
}
```

`storage_location_id` identifies the storage location using the Filekive Storage Location contract.

`path` identifies the target relative to that storage location.

The path SHOULD NOT be assumed to use a particular operating system's absolute path syntax.

This allows FileCity to resolve:

```text
storage location
+
relative path
```

into its own geography.

---

# Future Subject Types

Future schema versions may support subjects such as:

```text
asset
service
network
account
world_object
storage_location
```

Consumers implementing v0.1 SHOULD NOT invent semantics for unknown subject types.

---

# Field: `classification`

## Type

```text
string
```

## Required

No.

## Purpose

Provides the producer's human/domain-oriented classification of the subject.

Example:

```json
{
  "classification": "candidate"
}
```

For Charkive, possible values may be derived from its actual classification vocabulary.

`classification` differs from `category`.

For example:

```text
category:
storage.disposable_candidate

classification:
candidate
```

`category` is intended for cross-system machine interpretation.

`classification` preserves useful producer/domain terminology.

---

# Field: `summary`

## Type

```text
string
```

## Required

Yes.

## Purpose

Provides a short human-readable explanation of the Finding.

Example:

```json
{
  "summary": "Likely disposable temporary data"
}
```

The summary should answer:

> **What did the producer find?**

It SHOULD NOT be the only representation of important evidence.

Clients may display the summary prominently, but structured fields remain authoritative for machine behaviour.

---

# Field: `evidence`

## Type

```text
array of objects
```

## Required

No.

Default conceptual value:

```json
[]
```

## Purpose

Explains why the Finding exists.

Example:

```json
{
  "evidence": [
    {
      "type": "rule_match",
      "rule": "temporary-file-pattern",
      "description": "Path matched a configured disposable-data rule."
    }
  ]
}
```

Evidence is intentionally extensible.

Every evidence object MUST contain:

```text
type
```

Additional fields are defined by that evidence type.

---

# Evidence Type: `rule_match`

Example:

```json
{
  "type": "rule_match",
  "rule": "temporary-file-pattern",
  "description": "Path matched a configured disposable-data rule."
}
```

Possible fields:

```text
type
rule
description
```

A producer MAY add further evidence fields.

Consumers SHOULD preserve unknown evidence fields.

---

# Evidence Design Principle

The user should ultimately be able to ask:

> **Why did you flag this?**

and receive a meaningful answer.

Therefore prefer:

```json
{
  "type": "rule_match",
  "rule": "cache-directory",
  "description": "The containing directory matched a configured cache rule."
}
```

over an unexplained:

```json
{
  "type": "magic"
}
```

Explainability matters especially when Findings later lead to real-world actions.

---

# Field: `recommended_action`

## Type

```text
object
```

## Required

No.

Example:

```json
{
  "recommended_action": {
    "type": "review_for_quarantine"
  }
}
```

## Purpose

Describes what the producer recommends considering next.

It does **not** authorise that action.

This distinction is critical.

```text
FINDING
   ↓
RECOMMENDATION
   ↓
HUMAN / POLICY DECISION
   ↓
PROPOSED ACTION
   ↓
AUTHORISATION
   ↓
EXECUTION
```

A recommended action MUST NOT be interpreted as permission to modify reality.

---

# Recommended Action v0.1

Every recommended-action object MUST contain:

```text
type
```

Example action types may include:

```text
review
review_for_quarantine
investigate
none
```

Producer-specific recommendations may also exist.

The executable operation, if one exists, belongs to the separate Proposed Action contract.

---

# Field: `production_changed`

## Type

```text
boolean
```

## Required

Yes.

Example:

```json
{
  "production_changed": false
}
```

## Purpose

Explicitly states whether creation of the Finding itself caused a real-world production change.

For normal observation:

```json
{
  "production_changed": false
}
```

A Charkive scan which merely identifies a candidate therefore produces a Finding with:

```text
production_changed = false
```

This field exists to reinforce the boundary between:

```text
OBSERVATION
```

and:

```text
ACTION
```

For v0.1, Finding producers SHOULD normally produce:

```json
{
  "production_changed": false
}
```

Real modifications should ordinarily be represented separately through actions, results, and events.

---

# Field: `attributes`

## Type

```text
object
```

## Required

No.

Example:

```json
{
  "attributes": {}
}
```

## Purpose

Provides an escape hatch for useful producer-specific metadata that does not yet justify promotion into the common Finding contract.

Example:

```json
{
  "attributes": {
    "charkive": {
      "scan_id": "scan:0192f7b2"
    }
  }
}
```

Shared consumers SHOULD NOT rely upon producer-specific attributes for core Finding semantics.

If multiple producers repeatedly need the same attribute, it should be considered for promotion into a future common schema version.

---

# Minimal Valid Finding

A minimal Finding v0.1 could be:

```json
{
  "schema": "filekive.finding/0.1",
  "id": "finding:001",
  "producer": "charkive",
  "observed_at": "2026-10-30T07:00:00Z",
  "category": "storage.disposable_candidate",
  "severity": "notice",
  "subject": {
    "type": "storage_path",
    "storage_location_id": "storage:test",
    "path": "cache/example.tmp"
  },
  "summary": "Likely disposable temporary data",
  "production_changed": false
}
```

---

# Full Charkive Example

```json
{
  "schema": "filekive.finding/0.1",
  "id": "finding:0192f7b2-001",
  "producer": "charkive",
  "observed_at": "2026-10-30T07:00:00Z",
  "category": "storage.disposable_candidate",
  "severity": "notice",
  "confidence": 0.97,
  "subject": {
    "type": "storage_path",
    "storage_location_id": "storage:eddie:data",
    "path": "Projects/render/cache/frame-0001.tmp"
  },
  "classification": "candidate",
  "summary": "Likely disposable render-cache data",
  "evidence": [
    {
      "type": "rule_match",
      "rule": "render-cache",
      "description": "The path matched a configured render-cache rule."
    },
    {
      "type": "file_metadata",
      "size_bytes": 1879048192
    }
  ],
  "recommended_action": {
    "type": "review_for_quarantine"
  },
  "production_changed": false,
  "attributes": {
    "charkive": {
      "scan_id": "scan:0192f7b2"
    }
  }
}
```

---

# FileCity Consumption

FileCity should be able to consume the Finding without understanding Charkive internals.

It receives:

```text
storage_location_id:
storage:eddie:data

path:
Projects/render/cache/frame-0001.tmp
```

FileCity can resolve that against its own world:

```text
EDDIE DISTRICT
      ↓
PROJECTS BUILDING
      ↓
RENDER FLOOR
      ↓
CACHE AREA
```

The Finding remains unchanged.

FileCity owns the geography.

Charkive owns the Finding.

---

# Rendering

A renderer may interpret severity visually.

For example:

```text
info      → informational marker
notice    → attention marker
warning   → warning state
critical  → critical state
```

A future game might render these as:

```text
notice    → municipal inspection
warning   → police / maintenance response
critical  → emergency response
```

These are presentation decisions.

The Finding itself contains no:

```text
fire_engine
police_car
sprite
3D mesh
particle effect
BeamNG object
Unreal asset
```

Renderer-specific data does not belong in Finding v0.1.

---

# Read-Only First Integration

The first Charkive → FileCity integration should use Findings as read-only information.

```text
REAL FILESYSTEM
      ↓
CHARKIVE SCAN
      ↓
FINDING v0.1
      ↓
FILECITY
      ↓
WORLD OBJECT CHANGES STATE
      ↓
USER INSPECTS
      ↓
FINDING + EVIDENCE
```

No filesystem change is required.

This is the first target implementation for Finding v0.1.

---

# Finding Is Not Event

A Finding describes an observed condition.

```text
"Likely disposable data exists here."
```

An Event describes something that happened.

```text
"A scan completed."
```

or:

```text
"Data was quarantined."
```

These contracts are separate.

A Finding may cause or accompany Events, but the terms MUST NOT be treated as synonyms.

---

# Finding Is Not Action

A Finding may contain:

```json
{
  "recommended_action": {
    "type": "review_for_quarantine"
  }
}
```

That does not mean:

```text
QUARANTINE IT
```

The consumer may later submit a separate:

```text
filekive.proposed_action/*
```

The authoritative provider then decides whether that operation is:

```text
valid
permitted
safe
still applicable
authorised
```

Finding v0.1 itself cannot authorise execution.

---

# Finding Is Not World Object

A Finding might concern:

```text
storage:eddie:data
Projects/cache
```

FileCity might represent that as:

```text
Projects Tower
Floor 7
```

The Finding does not know or care.

This allows the same Finding to appear in:

```text
CLI
Charkive Web
FileCity
Retro game
BoreDOOM!
Future 3D client
```

without changing its semantics.

---

# Validation Rules

A Finding v0.1 is valid only when:

1. `schema` equals `filekive.finding/0.1`.
2. `id` is a non-empty string.
3. `producer` is a non-empty string.
4. `observed_at` is a valid timestamp.
5. `category` is a non-empty string.
6. `severity` is one of the defined v0.1 severity values.
7. `subject` is an object containing a recognised `type`.
8. `summary` is a non-empty string.
9. `production_changed` is boolean.
10. If supplied, `confidence` is between `0.0` and `1.0`.
11. If supplied, `evidence` is an array of evidence objects.
12. Every evidence object contains a non-empty `type`.
13. If supplied, `recommended_action` contains a non-empty `type`.
14. If supplied, `attributes` is an object.

A consumer SHOULD reject structurally invalid Findings at its ingestion boundary rather than attempting to silently repair them.

---

# Compatibility Rules

## Producer

A producer emitting v0.1 MUST satisfy the required v0.1 fields.

It MAY add additional fields.

## Consumer

A v0.1 consumer:

- MUST validate required fields it depends upon
- MUST understand the v0.1 severity vocabulary
- SHOULD ignore unknown top-level fields
- SHOULD preserve unknown fields when storing and forwarding Findings where practical
- MUST NOT assign semantics to unknown categories
- MUST NOT turn a recommended action directly into execution
- MUST NOT assume missing `confidence` means zero confidence

---

# Security Boundary

Finding v0.1 should be safe to transport through read-oriented components because it contains observations, not executable commands.

Nevertheless, all Finding content should be treated as untrusted input at system boundaries.

In particular:

```text
path
summary
evidence text
attributes
producer-supplied identifiers
```

must not be interpolated into:

```text
shell commands
SQL
HTML
filesystem operations
privileged API calls
```

without the appropriate validation or escaping.

A Finding which says:

```text
path = something
```

is describing a path.

It is not instructing the consumer to operate on that path.

---

# Privacy

Findings may expose sensitive information such as:

```text
filenames
directory structures
storage identifiers
machine relationships
user-created names
```

Consumers should therefore expose Findings only to users authorised to inspect the underlying data.

Future ecosystem transports may require redaction or summary mechanisms.

Those mechanisms are outside Finding v0.1.

---

# v0.1 Deliberate Omissions

Finding v0.1 intentionally does not fully define:

```text
finding resolution lifecycle
acknowledgement
assignment
suppression
expiry
deduplication
revision history
correlation
multi-subject findings
remediation execution
professional authority
game missions
renderer behaviour
transport protocol
```

Those concerns can be added when real implementations demonstrate the need.

The objective of v0.1 is much smaller:

> **Allow one system to say what it found, where it found it, how important it believes the condition is, why it believes that, and what it recommends considering next.**

---

# v0.1 First Implementation Target

The first implementation should prove:

```text
CHARKIVE
   ↓
Finding v0.1
   ↓
JSON
   ↓
FILECITY
   ↓
Validate
   ↓
Resolve subject
   ↓
Show affected building
   ↓
Display summary + evidence
```

Nothing else is required to validate the contract.

No action.

No quarantine.

No fire engine.

No VNA.

No BoreDOOM!.

Just:

> **Charkive found something real, and FileCity understood it.**

---

# North Star

A Finding should always make it possible to answer:

```text
WHAT did you find?

WHERE did you find it?

WHEN did you observe it?

WHO produced this conclusion?

HOW important is it?

HOW confident are you?

WHY do you believe it?

WHAT do you suggest I consider doing?

DID observing it change anything?
```

If Finding v0.1 answers those questions cleanly, it is doing its job.
