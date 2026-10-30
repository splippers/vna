# VNA Event Schema v0.1

> **A Finding says something is true. An Event says something happened.**

## Purpose

An **Event** is the common VNA ecosystem representation of something that happened at a particular point in time.

Events provide the historical narrative connecting:

```text
Assets
Observations
Findings
Snapshots
Actions
Results
```

Examples include:

```text
A scan started
A scan completed
A Finding was created
A Finding was acknowledged
A Finding was resolved
A Finding was suppressed
A Finding became active again
A file was quarantined
A file was restored
A transfer started
A transfer completed
A service stopped
A service started
A snapshot was captured
A proposed action was authorised
A real operation failed
Verification completed
```

The Event contract exists so independently useful VNA ecosystem components can describe things that happened without requiring every consumer to understand the producer's internal implementation.

---

# Core Principle

> **State tells us what is true. Events tell us how we got there.**

For example:

```text
FINDING

state = active

        |
        v

EVENT

vna.finding.acknowledged

        |
        v

FINDING

state = acknowledged

        |
        v

EVENT

vna.finding.resolved

        |
        v

FINDING

state = resolved
```

The Finding represents current disposition.

The Events preserve the history.

---

# Ecosystem Position

The Event contract sits alongside, rather than underneath, Observations and Findings.

Conceptually:

```text
REALITY / SIMULATION
        |
        +----------------+
        |                |
        v                v
   OBSERVATION         EVENT
        |                |
        v                |
     FINDING             |
        |                |
        +-------+--------+
                |
                v
             HISTORY
```

An Event may reference:

```text
Asset
Observation
Finding
Snapshot
Proposed Action
Verified Result
```

An Event does not automatically replace any of those objects.

---

# Canonical File Location

Save the canonical contract at:

```text
vna/docs/contracts/EVENT.md
```

Alongside:

```text
vna/
└── docs/
    └── contracts/
        ├── ASSET.md
        ├── OBSERVATION.md
        ├── FINDING.md
        ├── SNAPSHOT.md
        ├── EVENT.md
        ├── STORAGE_LOCATION.md
        ├── RELATIONSHIP.md
        ├── PROPOSED_ACTION.md
        └── VERIFIED_RESULT.md
```

VNA owns the shared Event contract.

Specialist systems own the behaviour that causes their Events.

---

# Schema Identifier

Every Event MUST declare:

```json
{
  "schema": "vna.event/0.1"
}
```

The canonical identifier is:

```text
vna.event/0.1
```

Consumers MUST NOT assume future versions are structurally identical.

Consumers SHOULD ignore unknown fields unless those fields are explicitly required by the schema version they implement.

---

# Canonical Example

```json
{
  "schema": "vna.event/0.1",
  "id": "event:finding-resolved-001",
  "producer": {
    "id": "charkive",
    "type": "application",
    "version": "2026.10"
  },
  "source": {
    "kind": "live",
    "id": "charkive:scan:5678"
  },
  "occurred_at": "2026-10-30T07:10:00Z",
  "type": "vna.finding.resolved",
  "subject": {
    "type": "finding",
    "id": "finding:charkive-001"
  },
  "summary": "The storage condition is no longer present.",
  "data": {
    "previous_state": "active",
    "new_state": "resolved"
  },
  "production_changed": false,
  "relationships": [],
  "attributes": {}
}
```

This example says:

> A Finding transitioned from active to resolved.

It does not claim why unless that information is explicitly supplied.

---

# Required Fields

Event v0.1 requires:

```text
schema
id
producer
source
occurred_at
type
subject
summary
production_changed
```

Optional fields include:

```text
actor
data
relationships
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

## Required Value

```text
vna.event/0.1
```

## Purpose

Identifies the shared Event contract and its version.

---

# Field: `id`

## Type

```text
string
```

## Required

Yes.

## Purpose

Provides the unique identity of this Event.

Example:

```json
{
  "id": "event:finding-resolved-001"
}
```

Consumers MUST treat Event IDs as opaque.

They MUST NOT infer behaviour or technical meaning from the internal structure of an ID.

---

# Event Identity

An Event represents one occurrence.

Events are therefore normally immutable.

Prefer:

```text
Event A

07:00
scan started


Event B

07:03
scan completed
```

rather than repeatedly modifying:

```text
Event A

state = started

then

state = completed
```

If something else happens, record another Event.

---

# Event Immutability

Once emitted, an Event SHOULD normally remain unchanged.

The Event records:

> **This happened at this time.**

Changing historical Events would change the recorded history of the system.

Where a producer discovers that an Event was erroneous, a future correction or supersession mechanism MAY record that fact separately.

Event v0.1 does not define a complete correction lifecycle.

---

# Field: `producer`

## Type

```text
object
```

## Required

Yes.

## Purpose

Identifies the system responsible for producing the Event record.

Example:

```json
{
  "producer": {
    "id": "charkive",
    "type": "application",
    "version": "2026.10"
  }
}
```

## Required Producer Fields

```text
id
type
```

## Optional Producer Fields

```text
version
instance
```

Potential producers include:

```text
vna
sysmedic
charkive
filecity
t4t
vendor:<vendor>
future:<producer>
```

The producer identifies who emitted the VNA Event.

It does not necessarily identify who or what caused the underlying occurrence.

That belongs to `actor`.

---

# Producer Versus Actor

This distinction is important.

Consider:

```text
User:
Jonathan

uses

FileCity

to request

a Charkive operation.
```

The eventual Event might have:

```text
producer:
charkive

actor:
user
```

Charkive records the Event.

The user caused the action to be requested.

Those are different roles.

---

# Field: `source`

## Type

```text
object
```

## Required

Yes.

## Purpose

Describes the kind of reality or scenario in which the Event occurred.

Event uses the same high-level source kinds as Observation and Finding.

## Allowed v0.1 Source Kinds

```text
live
snapshot
historical
synthetic
vendor
user
```

---

# Source Kind: `live`

The Event represents something that occurred in a real current environment.

Example:

```json
{
  "source": {
    "kind": "live",
    "id": "charkive:operation:1234"
  }
}
```

---

# Source Kind: `snapshot`

The Event was reconstructed or obtained from captured real-world evidence.

Example:

```json
{
  "source": {
    "kind": "snapshot",
    "id": "sysmedic:snapshot:SM-20261030-0733"
  }
}
```

Consumers MUST distinguish:

```text
The snapshot contains evidence that an event occurred.
```

from:

```text
The event is happening now.
```

---

# Source Kind: `historical`

The Event derives from retained historical evidence.

Possible examples include:

```text
historical system logs
old Charkive scans
archived audit records
Data Archaeology
previous VNA state
```

---

# Source Kind: `synthetic`

The Event belongs to a simulation or training world.

Example:

```json
{
  "source": {
    "kind": "synthetic",
    "id": "tutorial:unplugged-cable"
  }
}
```

Synthetic Events MUST remain distinguishable from Events representing real infrastructure.

---

# Source Kind: `vendor`

The Event originates from a vendor-supplied simulation, diagnostic system, equipment model, or training scenario.

---

# Source Kind: `user`

The Event directly represents something reported or recorded manually by a user.

This is different from a machine-generated Event whose actor happens to be a user.

---

# Field: `occurred_at`

## Type

```text
string
```

## Required

Yes.

## Format

UTC ISO 8601 timestamp.

Example:

```json
{
  "occurred_at": "2026-10-30T07:10:00Z"
}
```

`occurred_at` answers:

> **When did this Event happen?**

It does not necessarily equal:

```text
when VNA received it
when it was displayed
when it was imported
when it was written to a database
```

---

# Unknown Event Time

Sometimes historical or snapshot evidence establishes that something happened but does not establish an exact occurrence time.

Event v0.1 requires `occurred_at`.

Therefore producers MUST NOT invent an exact timestamp merely to satisfy the schema.

If only an observation/import time is known and the actual event time is unknown, the producer SHOULD prefer an Observation or Finding until a richer temporal Event model is defined.

This is particularly important for:

```text
SysMedic snapshots
Charkive Data Archaeology
Temporal Anomalies
historical logs
```

Unknown time must remain unknown.

---

# Field: `type`

## Type

```text
string
```

## Required

Yes.

## Purpose

Identifies what happened.

Event types SHOULD use dotted namespaces.

Examples:

```text
vna.finding.created
vna.finding.acknowledged
vna.finding.resolved
vna.finding.suppressed
vna.finding.reactivated

vna.snapshot.captured
vna.snapshot.imported

charkive.scan.started
charkive.scan.completed
charkive.quarantine.started
charkive.quarantine.completed
charkive.restore.completed

filecity.transfer.started
filecity.transfer.completed
filecity.transfer.failed

sysmedic.collection.started
sysmedic.collection.completed
sysmedic.snapshot.imported
```

---

# Event Type Ownership

Events describing common VNA contract lifecycle transitions SHOULD use the `vna.` namespace.

For example:

```text
vna.finding.created
vna.finding.resolved
vna.snapshot.imported
vna.action.proposed
vna.action.authorised
vna.action.rejected
vna.result.verified
```

Producer-specific operations SHOULD use the producer namespace.

For example:

```text
charkive.scan.completed
charkive.quarantine.completed
sysmedic.collection.completed
filecity.transfer.completed
```

This distinguishes:

```text
common ecosystem semantics
```

from:

```text
specialist implementation semantics
```

---

# Field: `subject`

## Type

```text
object
```

## Required

Yes.

## Purpose

Identifies the primary thing this Event concerns.

A subject MUST contain:

```text
type
id
```

Example:

```json
{
  "subject": {
    "type": "finding",
    "id": "finding:charkive-001"
  }
}
```

Possible subject types include:

```text
asset
observation
finding
snapshot
storage_location
storage_path
service
application
proposed_action
verified_result
transfer
producer_specific
```

---

# Subject Example: Finding

```json
{
  "subject": {
    "type": "finding",
    "id": "finding:charkive-001"
  }
}
```

---

# Subject Example: Snapshot

```json
{
  "subject": {
    "type": "snapshot",
    "id": "snapshot:sysmedic-001"
  }
}
```

---

# Subject Example: Asset

```json
{
  "subject": {
    "type": "asset",
    "id": "asset:eddie"
  }
}
```

---

# Subject Example: Proposed Action

```json
{
  "subject": {
    "type": "proposed_action",
    "id": "action:charkive-001"
  }
}
```

---

# Field: `summary`

## Type

```text
string
```

## Required

Yes.

## Purpose

Provides a concise human-readable description of what happened.

Example:

```json
{
  "summary": "Charkive scan completed."
}
```

or:

```json
{
  "summary": "The Finding was resolved after the condition was no longer observed."
}
```

The summary should remain descriptive.

It should not be the only source of machine-readable meaning.

---

# Field: `actor`

## Type

```text
object
```

## Required

No.

## Purpose

Identifies the entity that caused, initiated, authorised, or performed the occurrence where that information is known.

Example:

```json
{
  "actor": {
    "type": "user",
    "id": "user:local"
  }
}
```

Possible actor types include:

```text
user
system
application
agent
policy
vendor
unknown
```

---

# Actor Is Optional

Not every Event has a meaningful actor.

For example:

```text
Disk temperature crossed a threshold.
```

may result from environmental or physical conditions rather than a deliberate actor.

Do not invent an actor merely to populate the field.

---

# Multiple Actors

Some future operations may involve:

```text
requester
authoriser
executor
```

Event v0.1 deliberately avoids modelling those roles inside a single generic `actor` object.

Those roles belong primarily to Proposed Action, Verified Result, and specialised audit contracts.

If multiple identities are relevant, use relationships or producer-specific attributes until the shared model requires a richer actor structure.

---

# Field: `data`

## Type

```text
object
```

## Required

No.

## Purpose

Contains structured information specific to the Event type.

Example:

```json
{
  "data": {
    "previous_state": "active",
    "new_state": "resolved"
  }
}
```

Another Event might contain:

```json
{
  "data": {
    "files_scanned": 4312
  }
}
```

A transfer Event might contain:

```json
{
  "data": {
    "bytes_transferred": 1879048192
  }
}
```

Consumers MUST interpret `data` in conjunction with `type`.

---

# Data Must Describe the Event

The `data` object should contain information about what happened.

It SHOULD NOT become a dumping ground for unrelated current state.

For example:

```text
Event:
filecity.transfer.completed
```

may reasonably contain:

```text
bytes transferred
source
destination
verification status
```

It should not automatically contain an entire FileCity world model.

---

# Field: `relationships`

## Type

```text
array of objects
```

## Required

No.

## Purpose

Links the Event to other relevant ecosystem objects.

Each relationship MUST contain:

```text
type
target_id
```

Example:

```json
{
  "relationships": [
    {
      "type": "result_of",
      "target_id": "action:charkive-001"
    },
    {
      "type": "affects",
      "target_id": "finding:charkive-001"
    }
  ]
}
```

---

# Suggested Relationship Types

Initial useful relationship types may include:

```text
caused_by
result_of
affects
produced
references
supersedes
related_to
verified_by
triggered_by
```

The vocabulary should remain intentionally small.

Introduce new relationship types only when real implementations require them.

---

# Relationship: `result_of`

Example:

```json
{
  "type": "result_of",
  "target_id": "action:charkive-001"
}
```

Meaning:

> This Event occurred as a result of the referenced action.

---

# Relationship: `affects`

Example:

```json
{
  "type": "affects",
  "target_id": "finding:charkive-001"
}
```

Meaning:

> This Event affects the referenced object's state or interpretation.

---

# Relationship: `produced`

Example:

```json
{
  "type": "produced",
  "target_id": "finding:charkive-002"
}
```

Meaning:

> The occurrence represented by this Event produced the referenced object.

---

# Relationship: `verified_by`

Example:

```json
{
  "type": "verified_by",
  "target_id": "result:charkive-001"
}
```

Meaning:

> The outcome associated with this Event was verified by the referenced Verified Result.

---

# Field: `production_changed`

## Type

```text
boolean
```

## Required

Yes.

## Purpose

Explicitly records whether the occurrence represented by the Event changed a real production environment.

Examples:

```text
scan started
false

finding acknowledged
false

snapshot imported into VNA
false

real Charkive quarantine completed
true

synthetic tutorial cable connected
false
```

The field provides a simple, prominent safety distinction.

---

# `production_changed` and Source Kind

A synthetic Event should ordinarily have:

```json
{
  "production_changed": false
}
```

even if the synthetic world changed.

The field concerns real production state.

For example:

```text
Synthetic tutorial:

Player plugs virtual cable back in.

Virtual world changed:
YES

Production changed:
NO
```

---

# Production Change Does Not Mean Success

This is valid:

```json
{
  "production_changed": true
}
```

for an operation which changed reality but did not achieve its intended result.

Therefore:

```text
production_changed
```

MUST NOT be interpreted as:

```text
succeeded
verified
resolved
safe
```

Verified Result exists for that purpose.

---

# Field: `attributes`

## Type

```text
object
```

## Required

No.

## Purpose

Provides an escape hatch for producer-specific metadata.

Example:

```json
{
  "attributes": {
    "charkive": {
      "scan_id": "charkive:scan:1234"
    }
  }
}
```

or:

```json
{
  "attributes": {
    "sysmedic": {
      "collection_id": "SM-20261030-0733"
    }
  }
}
```

Shared consumers SHOULD NOT rely on producer-specific attributes for core Event semantics.

---

# Minimal Valid Event

```json
{
  "schema": "vna.event/0.1",
  "id": "event:001",
  "producer": {
    "id": "charkive",
    "type": "application"
  },
  "source": {
    "kind": "live"
  },
  "occurred_at": "2026-10-30T07:00:00Z",
  "type": "charkive.scan.completed",
  "subject": {
    "type": "asset",
    "id": "asset:storage-host"
  },
  "summary": "Charkive scan completed.",
  "production_changed": false
}
```

---

# Finding Created Example

```json
{
  "schema": "vna.event/0.1",
  "id": "event:finding-created-001",
  "producer": {
    "id": "charkive",
    "type": "application"
  },
  "source": {
    "kind": "live",
    "id": "charkive:scan:1234"
  },
  "occurred_at": "2026-10-30T07:00:00Z",
  "type": "vna.finding.created",
  "subject": {
    "type": "finding",
    "id": "finding:charkive-001"
  },
  "summary": "A new storage Finding was created.",
  "production_changed": false
}
```

---

# Finding Acknowledged Example

```json
{
  "schema": "vna.event/0.1",
  "id": "event:finding-acknowledged-001",
  "producer": {
    "id": "vna",
    "type": "application"
  },
  "source": {
    "kind": "live"
  },
  "occurred_at": "2026-10-30T07:05:00Z",
  "type": "vna.finding.acknowledged",
  "subject": {
    "type": "finding",
    "id": "finding:charkive-001"
  },
  "actor": {
    "type": "user",
    "id": "user:local"
  },
  "summary": "The Finding was acknowledged.",
  "data": {
    "previous_state": "active",
    "new_state": "acknowledged"
  },
  "production_changed": false
}
```

Acknowledgement changed VNA Finding disposition.

It did not modify production storage.

---

# Finding Resolved Example

```json
{
  "schema": "vna.event/0.1",
  "id": "event:finding-resolved-001",
  "producer": {
    "id": "charkive",
    "type": "application"
  },
  "source": {
    "kind": "live",
    "id": "charkive:scan:5678"
  },
  "occurred_at": "2026-10-30T07:10:00Z",
  "type": "vna.finding.resolved",
  "subject": {
    "type": "finding",
    "id": "finding:charkive-001"
  },
  "summary": "The previously reported storage condition is no longer present.",
  "data": {
    "previous_state": "acknowledged",
    "new_state": "resolved"
  },
  "production_changed": false
}
```

This Event records resolution.

It does not claim a production change occurred at that moment.

A separate earlier operation may have changed reality.

---

# Finding Suppressed Example

```json
{
  "schema": "vna.event/0.1",
  "id": "event:finding-suppressed-001",
  "producer": {
    "id": "vna",
    "type": "application"
  },
  "source": {
    "kind": "live"
  },
  "occurred_at": "2026-10-30T07:12:00Z",
  "type": "vna.finding.suppressed",
  "subject": {
    "type": "finding",
    "id": "finding:network-known-condition"
  },
  "actor": {
    "type": "user",
    "id": "user:local"
  },
  "summary": "The Finding was deliberately suppressed.",
  "data": {
    "previous_state": "active",
    "new_state": "suppressed"
  },
  "production_changed": false
}
```

The underlying condition may still exist.

The Event records a change in disposition, not resolution.

---

# Finding Reactivated Example

```json
{
  "schema": "vna.event/0.1",
  "id": "event:finding-reactivated-001",
  "producer": {
    "id": "charkive",
    "type": "application"
  },
  "source": {
    "kind": "live",
    "id": "charkive:scan:9999"
  },
  "occurred_at": "2026-10-30T07:20:00Z",
  "type": "vna.finding.reactivated",
  "subject": {
    "type": "finding",
    "id": "finding:charkive-001"
  },
  "summary": "The previously resolved condition was observed again.",
  "data": {
    "previous_state": "resolved",
    "new_state": "active"
  },
  "production_changed": false
}
```

---

# Charkive Scan Example

```json
{
  "schema": "vna.event/0.1",
  "id": "event:charkive-scan-001",
  "producer": {
    "id": "charkive",
    "type": "application"
  },
  "source": {
    "kind": "live",
    "id": "charkive:scan:1234"
  },
  "occurred_at": "2026-10-30T07:00:00Z",
  "type": "charkive.scan.completed",
  "subject": {
    "type": "storage_location",
    "id": "storage:eddie:data"
  },
  "summary": "Charkive scan completed.",
  "data": {
    "scan_id": "charkive:scan:1234"
  },
  "production_changed": false
}
```

---

# Charkive Quarantine Example

```json
{
  "schema": "vna.event/0.1",
  "id": "event:charkive-quarantine-001",
  "producer": {
    "id": "charkive",
    "type": "application"
  },
  "source": {
    "kind": "live",
    "id": "charkive:operation:quarantine-1234"
  },
  "occurred_at": "2026-10-30T07:15:00Z",
  "type": "charkive.quarantine.completed",
  "subject": {
    "type": "storage_path",
    "id": "storage-path:example"
  },
  "actor": {
    "type": "user",
    "id": "user:local"
  },
  "summary": "Reviewed candidate data was moved into the SANitorium.",
  "relationships": [
    {
      "type": "result_of",
      "target_id": "action:charkive-quarantine-001"
    },
    {
      "type": "affects",
      "target_id": "finding:charkive-001"
    }
  ],
  "production_changed": true
}
```

This does not by itself prove the operation produced the intended verified outcome.

That belongs to Verified Result.

---

# Charkive Restore Example

```json
{
  "schema": "vna.event/0.1",
  "id": "event:charkive-restore-001",
  "producer": {
    "id": "charkive",
    "type": "application"
  },
  "source": {
    "kind": "live",
    "id": "charkive:operation:restore-1234"
  },
  "occurred_at": "2026-10-30T07:25:00Z",
  "type": "charkive.restore.completed",
  "subject": {
    "type": "storage_path",
    "id": "storage-path:example"
  },
  "actor": {
    "type": "user",
    "id": "user:local"
  },
  "summary": "Quarantined data was restored from the SANitorium.",
  "production_changed": true
}
```

---

# SysMedic Snapshot Captured Example

```json
{
  "schema": "vna.event/0.1",
  "id": "event:sysmedic-snapshot-captured-001",
  "producer": {
    "id": "sysmedic",
    "type": "application",
    "version": "2026.10"
  },
  "source": {
    "kind": "live",
    "id": "sysmedic:collection:SM-20261030-0733"
  },
  "occurred_at": "2026-10-30T06:45:00Z",
  "type": "vna.snapshot.captured",
  "subject": {
    "type": "snapshot",
    "id": "snapshot:sysmedic-SM-20261030-0733"
  },
  "summary": "SysMedic captured a portable system snapshot.",
  "production_changed": false,
  "attributes": {
    "sysmedic": {
      "collection_id": "SM-20261030-0733"
    }
  }
}
```

---

# SysMedic Snapshot Imported Example

```json
{
  "schema": "vna.event/0.1",
  "id": "event:sysmedic-snapshot-imported-001",
  "producer": {
    "id": "vna",
    "type": "application"
  },
  "source": {
    "kind": "snapshot",
    "id": "snapshot:sysmedic-SM-20261030-0733"
  },
  "occurred_at": "2026-10-30T07:00:00Z",
  "type": "vna.snapshot.imported",
  "subject": {
    "type": "snapshot",
    "id": "snapshot:sysmedic-SM-20261030-0733"
  },
  "summary": "A SysMedic snapshot was imported into VNA.",
  "production_changed": false
}
```

The imported snapshot represents captured evidence.

Importing it does not imply the original machine was modified.

---

# FileCity Transfer Started Example

```json
{
  "schema": "vna.event/0.1",
  "id": "event:filecity-transfer-started-001",
  "producer": {
    "id": "filecity",
    "type": "application"
  },
  "source": {
    "kind": "live",
    "id": "filecity:transfer:1234"
  },
  "occurred_at": "2026-10-30T07:30:00Z",
  "type": "filecity.transfer.started",
  "subject": {
    "type": "transfer",
    "id": "filecity:transfer:1234"
  },
  "summary": "FileCity started a storage transfer.",
  "data": {
    "source_storage_location_id": "storage:local:data",
    "destination_storage_location_id": "storage:cloud:archive"
  },
  "production_changed": true
}
```

---

# FileCity Transfer Completed Example

```json
{
  "schema": "vna.event/0.1",
  "id": "event:filecity-transfer-completed-001",
  "producer": {
    "id": "filecity",
    "type": "application"
  },
  "source": {
    "kind": "live",
    "id": "filecity:transfer:1234"
  },
  "occurred_at": "2026-10-30T07:35:00Z",
  "type": "filecity.transfer.completed",
  "subject": {
    "type": "transfer",
    "id": "filecity:transfer:1234"
  },
  "summary": "FileCity completed the storage transfer.",
  "data": {
    "bytes_transferred": 1879048192
  },
  "production_changed": true
}
```

Verification of the destination remains a separate concept.

---

# FileCity Transfer Failed Example

```json
{
  "schema": "vna.event/0.1",
  "id": "event:filecity-transfer-failed-001",
  "producer": {
    "id": "filecity",
    "type": "application"
  },
  "source": {
    "kind": "live",
    "id": "filecity:transfer:1234"
  },
  "occurred_at": "2026-10-30T07:32:00Z",
  "type": "filecity.transfer.failed",
  "subject": {
    "type": "transfer",
    "id": "filecity:transfer:1234"
  },
  "summary": "FileCity storage transfer failed.",
  "data": {
    "stage": "copy"
  },
  "production_changed": false
}
```

Do not infer from a failed Event that nothing whatsoever changed unless the authoritative producer has established that fact.

---

# VNA Synthetic Cable Event Example

```json
{
  "schema": "vna.event/0.1",
  "id": "event:tutorial-cable-disconnected-001",
  "producer": {
    "id": "vna",
    "type": "simulation"
  },
  "source": {
    "kind": "synthetic",
    "id": "tutorial:unplugged-cable"
  },
  "occurred_at": "2026-10-30T07:00:00Z",
  "type": "vna.synthetic.physical_connection.disconnected",
  "subject": {
    "type": "network_interface",
    "id": "interface:tutorial-office-b:ethernet-1"
  },
  "summary": "The simulated physical network connection was disconnected.",
  "production_changed": false
}
```

The synthetic world changed.

Production did not.

---

# VNA Synthetic Cable Restored Example

```json
{
  "schema": "vna.event/0.1",
  "id": "event:tutorial-cable-restored-001",
  "producer": {
    "id": "vna",
    "type": "simulation"
  },
  "source": {
    "kind": "synthetic",
    "id": "tutorial:unplugged-cable"
  },
  "occurred_at": "2026-10-30T07:05:00Z",
  "type": "vna.synthetic.physical_connection.connected",
  "subject": {
    "type": "network_interface",
    "id": "interface:tutorial-office-b:ethernet-1"
  },
  "actor": {
    "type": "user",
    "id": "player:trainee"
  },
  "summary": "The player restored the simulated physical network connection.",
  "production_changed": false
}
```

The subsequent recovery of Layer 2 through Layer 7 can produce new Observations, Finding state changes, and Events as appropriate.

---

# Event Versus Observation

Consider:

```text
07:00
Service stopped.

07:05
VNA checks service and sees "stopped".
```

The following may be available:

```text
EVENT

Service stopped at 07:00.
```

and:

```text
OBSERVATION

Service state observed as stopped at 07:05.
```

These are different pieces of evidence.

Do not transform the 07:05 Observation into a 07:05 stop Event.

---

# Event Versus Finding

Event:

```text
Disk temperature crossed 80°C.
```

Finding:

```text
Disk temperature is critically high.
```

The Event records something happening.

The Finding represents a condition requiring attention.

Both can coexist.

---

# Event Versus Action

Event:

```text
Quarantine operation started.
```

Action:

```text
Request reversible quarantine of candidate data.
```

The Proposed Action describes what somebody wants to happen.

The Event records that something actually happened in the workflow.

---

# Event Versus Verified Result

Event:

```text
Copy command completed.
```

Verified Result:

```text
Destination content was independently verified against source.
```

A completion Event does not automatically prove correctness.

This is a fundamental ecosystem rule.

---

# Events and Audit

Events provide much of the chronological backbone needed for audit.

A future operation may produce:

```text
07:00
Finding created

07:05
Finding acknowledged

07:07
Action proposed

07:08
Action authorised

07:09
Execution started

07:11
Execution completed

07:12
Verification completed

07:13
Finding resolved
```

This timeline allows both humans and machines to understand how the system moved from one state to another.

---

# Events and FileCity

FileCity can use Events to visualise activity.

For example:

```text
filecity.transfer.started
        |
        v
Vehicle departs

filecity.transfer.completed
        |
        v
Vehicle arrives

verification complete
        |
        v
Delivery marked successful
```

The animation is presentation.

The Event remains the authoritative record of what happened.

---

# Events and Charkive City Services

Charkive Events may eventually drive playful visual representations.

For example:

```text
charkive.quarantine.started
        |
        v
Sanitation vehicle dispatched

charkive.quarantine.completed
        |
        v
Vehicle reaches SANitorium
```

A visual client MUST NOT invent a completed Event because its animation finished.

The data drives the animation.

The animation does not drive the data.

---

# Events and T4

T4 may explain Events according to audience.

For example:

```text
EVENT

filecity.transfer.failed
```

T4Tots:

```text
THE DELIVERY DIDN'T MAKE IT!
```

T4Teens:

```text
The storage transfer failed before completion.
```

T4Techies:

```text
Event:
filecity.transfer.failed

Stage:
copy

[technical evidence]
```

The explanation changes.

The Event does not.

---

# Events and VNA Gameplay

VNA can use Events to build mission history.

For example:

```text
MISSION STARTED
      |
      v
PLAYER INSPECTED L7
      |
      v
PLAYER DESCENDED TO L1
      |
      v
PHYSICAL FAULT IDENTIFIED
      |
      v
SIMULATED CONNECTION RESTORED
      |
      v
FINDING RESOLVED
      |
      v
MISSION COMPLETED
```

Not every tiny player movement needs to become a VNA Event.

Use Events for meaningful state transitions and auditable gameplay milestones.

---

# Events and BoreDOOM!

BoreDOOM! may use the same general principle for optional integrations.

Domestic BoreDOOM! remains independent from VNA contracts where appropriate.

If integrated with VNA or another ecosystem source, gameplay Events must remain distinguishable from real operational Events.

For example:

```text
BOREDOOM EVENT

Player completed virtual sanitation mission.
```

does not mean:

```text
CHARKIVE EVENT

Real filesystem quarantine completed.
```

Those are separate occurrences.

---

# Event Ordering

Consumers SHOULD primarily use:

```text
occurred_at
```

for chronological presentation.

However, distributed systems may produce Events with identical timestamps, clock skew, delayed delivery, or import latency.

Event v0.1 therefore does not promise a perfect global total ordering.

Consumers MUST NOT assume array order or arrival order is authoritative chronology.

A future sequencing mechanism may be added if real implementations require it.

---

# Idempotency

Consumers may receive the same Event more than once.

The Event `id` SHOULD be used to recognise duplicates.

A consumer SHOULD avoid applying irreversible side effects repeatedly merely because an Event was delivered twice.

This is particularly important for:

```text
message queues
retries
offline imports
snapshot replay
synchronisation
```

---

# Events Do Not Command Consumers

An Event says:

> **This happened.**

It does not say:

> **Do this.**

For example:

```text
vna.finding.created
```

may cause FileCity to refresh a visual indicator.

But arbitrary Event content MUST NOT automatically trigger privileged operations.

Events are historical/state-transition messages, not executable commands.

---

# Replay

Historical Events may eventually be replayed for:

```text
timeline views
training
simulation
incident review
FileCity historical visualisation
VNA world reconstruction
```

Replay MUST remain distinguishable from live execution.

Replaying:

```text
charkive.quarantine.completed
```

for visualisation must not quarantine the file again.

The Event describes the past.

It is not an instruction to reproduce it.

---

# Privacy

Events may contain sensitive information.

Examples include:

```text
user identities
asset identities
storage paths
machine names
action details
snapshot IDs
timestamps
operational history
```

Consumers should expose only information appropriate to the current user and context.

VNA generalisation may pseudonymise or redact Event subjects and relationships before delivery to less-trusted clients.

---

# Generalisation

A real Event might contain:

```text
Asset:
EDDIE

Finding:
finding:charkive-001
```

A generalised client might receive:

```text
Asset:
SERVER-104

Finding:
FINDING-22
```

The event meaning should remain:

```text
Finding resolved.
```

Generalisation may change identity.

It MUST NOT silently change what happened.

---

# Security Boundary

Event content is untrusted input at integration boundaries.

Fields such as:

```text
id
summary
subject IDs
actor IDs
data
relationships
attributes
```

MUST NOT be interpolated directly into:

```text
shell commands
SQL
filesystem operations
HTML
privileged APIs
game scripting
```

without appropriate validation and escaping.

An Event is a record.

It is not executable authority.

---

# Transport Independence

Event v0.1 defines an object, not a transport.

Events may travel through:

```text
JSON files
HTTP APIs
event streams
message queues
local IPC
snapshot bundles
databases
test fixtures
```

The meaning should remain the same regardless of transport.

---

# Validation Rules

A valid Event v0.1 MUST satisfy:

1. `schema` equals `vna.event/0.1`.
2. `id` is a non-empty string.
3. `producer` is an object.
4. `producer.id` is a non-empty string.
5. `producer.type` is a non-empty string.
6. `source` is an object.
7. `source.kind` is one of the recognised v0.1 source kinds.
8. `occurred_at` is a valid UTC ISO 8601 timestamp.
9. `type` is a non-empty string.
10. `subject` is an object.
11. `subject.type` is a non-empty string.
12. `subject.id` is a non-empty string.
13. `summary` is a non-empty string.
14. `production_changed` is a boolean.
15. If supplied, `actor` is an object.
16. If supplied, `actor.type` is a non-empty string.
17. If supplied, `actor.id` is a non-empty string.
18. If supplied, `data` is an object.
19. If supplied, `relationships` is an array.
20. Every relationship contains a non-empty `type`.
21. Every relationship contains a non-empty `target_id`.
22. If supplied, `attributes` is an object.

Consumers SHOULD reject structurally invalid Events rather than silently repairing them.

---

# Compatibility Rules

## Producer

A v0.1 Event producer:

- MUST provide all required fields
- MAY provide optional fields
- MAY add unknown fields
- SHOULD treat emitted Events as immutable
- MUST distinguish real and synthetic sources
- SHOULD preserve occurrence time accurately
- MUST NOT invent exact occurrence times where they are unknown
- SHOULD use stable unique Event IDs
- SHOULD clearly identify production-changing Events

## Consumer

A v0.1 consumer:

- MUST validate fields it depends upon
- SHOULD ignore unknown fields
- SHOULD preserve unknown fields when forwarding Events
- MUST preserve producer identity
- MUST preserve source kind
- MUST NOT assume delivery order equals occurrence order
- SHOULD tolerate duplicate delivery using Event IDs
- MUST NOT treat Events as executable commands
- MUST NOT treat `production_changed` as proof of success
- MUST treat Event content as untrusted input

---

# v0.1 Deliberate Omissions

Event v0.1 intentionally does not fully define:

```text
global sequence numbers
distributed clock reconciliation
event correction
event retraction
causal vector clocks
retention policy
stream offsets
event signing
event transport
subscription mechanisms
acknowledgement protocols
authentication
authorisation
replay controls
multi-actor audit structures
```

These should be added only when real implementation work demonstrates the need.

The purpose of v0.1 is much smaller:

> **Provide a trustworthy common representation of something that happened.**

---

# First Implementation Target

The first Event implementation should be extremely simple.

T4T/MARVIN could emit:

```text
scan started
scan completed
```

or Charkive could emit:

```text
scan completed
finding created
finding resolved
```

A useful first lifecycle would be:

```text
CHARKIVE SCAN
      |
      v
EVENT

charkive.scan.completed
      |
      v
FINDING CREATED
      |
      v
EVENT

vna.finding.created
      |
      v
CONDITION LATER DISAPPEARS
      |
      v
EVENT

vna.finding.resolved
```

This proves:

```text
Observation
Finding
Event
```

are genuinely different concepts in running code.

---

# Second Implementation Target

SysMedic:

```text
COLLECTION STARTED
      |
      v
COLLECTION COMPLETED
      |
      v
SNAPSHOT CAPTURED
      |
      v
SNAPSHOT IMPORTED
```

The original machine need not be online when later Events occur.

---

# Third Implementation Target

FileCity:

```text
TRANSFER STARTED
      |
      v
TRANSFER ACTIVITY
      |
      v
TRANSFER COMPLETED
      |
      v
VERIFICATION
```

This gives FileCity the temporal information required to turn data movement into visible traffic without making animation authoritative.

---

# Fourth Implementation Target

VNA gameplay:

```text
SYNTHETIC FAULT CREATED
      |
      v
PLAYER INVESTIGATION
      |
      v
SIMULATED FIX
      |
      v
FINDING RESOLVED
      |
      v
MISSION REPORT
```

The same Event language now spans:

```text
real storage
captured systems
live telemetry
synthetic training
gameplay
```

while source provenance keeps those worlds distinct.

---

# North Star

An Event should always make it possible to answer:

```text
WHAT happened?

WHEN did it happen?

WHO recorded it?

WHAT kind of world did it happen in?

WHAT did it happen to?

WHO caused it, if known?

WHAT structured details describe it?

WHAT other objects does it relate to?

DID it change real production state?
```

And consumers should always remember:

> **An Event tells us that something happened.**

> **It does not tell us to make it happen again.**
