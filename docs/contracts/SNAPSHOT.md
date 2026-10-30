# VNA Snapshot Schema v0.1

> **A Snapshot is evidence carried through time. It is not a claim about now.**

## Purpose

A **Snapshot** is the common VNA ecosystem representation of a captured set of evidence describing a system, Asset, Storage Location, environment, or collection at a particular point or period in time.

Snapshots allow the VNA ecosystem to reason about things which are not currently connected.

Examples include:

```text
SysMedic evidence collected from a running Windows machine
SysMedic evidence collected while booted into the rescue environment
offline machine inventories
captured storage information
historical Charkive scan material
exported VNA state
vendor training scenarios
synthetic test fixtures
deliberately archived diagnostic bundles
```

The defining principle is:

> **A Snapshot describes captured state. It does not automatically describe current state.**

A machine captured yesterday may have changed.

A disk may have been repaired.

A service may have restarted.

A cable may have been reconnected.

A file may have moved.

The Snapshot remains valuable evidence of what was captured.

It must not silently become a claim about what exists now.

---

# Core Principle

```text
REAL SYSTEM
     |
     v
COLLECTION
     |
     v
SNAPSHOT
     |
     +----------+------------+-------------+
     |          |            |             |
     v          v            v             v
   ASSETS    STORAGE     OBSERVATIONS   RELATIONSHIPS
                             |
                             v
                          FINDINGS
```

A Snapshot is primarily a provenance and containment boundary.

It tells consumers:

```text
These records belong to the same captured body of evidence.

This is when the evidence was captured.

This is where it came from.

This is how it was collected.

This is what was included.

This is what was omitted.

This is what failed.

This is how its integrity can be checked.
```

---

# Snapshot Is Not Analysis

A Snapshot may contain or lead to Findings.

The Snapshot itself should not be confused with those Findings.

```text
SNAPSHOT

Captured evidence from machine


OBSERVATION

Windows service was recorded as stopped


FINDING

Service appears unavailable


T4

"This service wasn't running when SysMedic collected the snapshot."
```

These are separate layers of meaning.

---

# Snapshot Is Not Report

A Snapshot is primarily machine-oriented evidence.

A report is primarily human-oriented communication.

Conceptually:

```text
EVIDENCE
   |
   v
SNAPSHOT
   |
   v
ANALYSIS
   |
   v
FINDINGS
   |
   +----------+
   |          |
   v          v
REPORT       VNA
```

The same Snapshot may support several reports without changing the underlying captured evidence.

---

# Snapshot Is Not Backup

A Snapshot may contain enough information to reconstruct some aspects of a system.

That does not automatically make it a backup.

VNA Snapshot v0.1 does not claim:

```text
complete machine recovery
complete filesystem recovery
application-consistent backup
bare-metal restoration
disaster recovery
```

unless a specialised producer explicitly provides those guarantees elsewhere.

The contract describes captured evidence.

---

# Canonical File Location

Save this contract at:

```text
vna/docs/contracts/SNAPSHOT.md
```

Alongside:

```text
vna/
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

---

# Schema Identifier

Every Snapshot MUST declare:

```json
{
  "schema": "vna.snapshot/0.1"
}
```

The canonical identifier is:

```text
vna.snapshot/0.1
```

Consumers MUST NOT assume future versions are structurally identical.

Consumers SHOULD ignore unknown fields unless those fields are explicitly required by a schema version they implement.

---

# Canonical Example

```json
{
  "schema": "vna.snapshot/0.1",
  "id": "snapshot:SM-20261030-0733",
  "name": "SysMedic collection SM-20261030-0733",
  "producer": {
    "id": "sysmedic",
    "type": "application",
    "version": "2026.10"
  },
  "source": {
    "kind": "live",
    "asset_id": "asset:captured-machine"
  },
  "captured_at": "2026-10-30T06:45:00Z",
  "created_at": "2026-10-30T06:47:12Z",
  "state": "complete",
  "scope": {
    "type": "machine"
  },
  "contents": {
    "assets": [],
    "storage_locations": [],
    "observations": [],
    "relationships": [],
    "findings": []
  },
  "collection": {
    "mode": "read_only",
    "consent": "explicit",
    "network_required": false
  },
  "integrity": {
    "status": "verified"
  },
  "production_changed": false,
  "attributes": {}
}
```

This example means:

> SysMedic created a captured evidence set describing a machine.

It does not mean:

> The machine still has this state now.

---

# Required Fields

Snapshot v0.1 requires:

```text
schema
id
name
producer
source
captured_at
created_at
state
scope
contents
production_changed
```

Optional fields include:

```text
collection
integrity
omissions
failures
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
vna.snapshot/0.1
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

Provides the stable identity of the Snapshot.

Example:

```json
{
  "id": "snapshot:SM-20261030-0733"
}
```

Consumers MUST treat the identifier as opaque.

The ID identifies the captured evidence set.

It does not identify the source Asset itself.

---

# Snapshot ID Is Not Asset ID

This distinction is important.

```text
Asset:
asset:eddie

Snapshot:
snapshot:eddie-20261030
```

The Asset may exist for years.

Many Snapshots may describe it.

```text
asset:eddie
   |
   +── snapshot:eddie-20260101
   |
   +── snapshot:eddie-20260510
   |
   `── snapshot:eddie-20261030
```

A new Snapshot does not imply a new Asset.

---

# Field: `name`

## Type

```text
string
```

## Required

Yes.

## Purpose

Provides a useful human-readable name.

Example:

```json
{
  "name": "SysMedic collection SM-20261030-0733"
}
```

The name is presentation metadata.

It is not Snapshot identity.

---

# Field: `producer`

## Type

```text
object
```

## Required

Yes.

## Purpose

Identifies the system responsible for producing the Snapshot.

Example:

```json
{
  "producer": {
    "id": "sysmedic",
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
collector
collector_version
```

---

# Snapshot Producer vs Collector

The system packaging the Snapshot and the component collecting evidence may not always be identical.

For example:

```text
SysMedic
    |
    v
Windows collector
    |
    v
Encrypted evidence
    |
    v
SysMedic import
    |
    v
VNA Snapshot
```

The producer might therefore be:

```text
sysmedic
```

while collection provenance records:

```text
sysmedic-win32
```

Preserving both can be useful.

---

# Field: `source`

## Type

```text
object
```

## Required

Yes.

## Purpose

Describes what was captured.

Example:

```json
{
  "source": {
    "kind": "live",
    "asset_id": "asset:eddie"
  }
}
```

Possible `kind` values include:

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

The Snapshot was captured from a real system or environment.

Example:

```json
{
  "source": {
    "kind": "live",
    "asset_id": "asset:eddie"
  }
}
```

This does not mean the Snapshot remains live.

It means the original source was real.

---

# Source Kind: `snapshot`

The Snapshot was derived from another Snapshot.

Example:

```json
{
  "source": {
    "kind": "snapshot",
    "snapshot_id": "snapshot:original"
  }
}
```

This may be useful for:

```text
redacted derivative
generalised derivative
subset
analysis package
training derivative
```

The relationship to the original Snapshot SHOULD remain traceable.

---

# Source Kind: `historical`

The Snapshot was assembled from historical evidence rather than captured directly from a currently operating system.

This may support:

```text
Charkive archaeology
historical VNA state
reconstructed old systems
```

---

# Source Kind: `synthetic`

The Snapshot represents an explicitly artificial system.

Examples:

```text
tutorial
test fixture
procedural scenario
training system
```

Synthetic Snapshots must never masquerade as captured production evidence.

---

# Source Kind: `vendor`

The Snapshot or scenario was supplied by a vendor.

This may be used for vendor training worlds or equipment simulations.

---

# Source Kind: `user`

The Snapshot was explicitly assembled or supplied by a user.

This may be useful for manually curated historical evidence.

---

# Field: `captured_at`

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
  "captured_at": "2026-10-30T06:45:00Z"
}
```

## Purpose

Records when the Snapshot's source state was captured.

This is one of the most important fields in the contract.

A consumer MUST NOT silently treat:

```text
captured_at
```

as:

```text
current state
```

---

# Field: `created_at`

## Type

```text
string
```

## Required

Yes.

## Purpose

Records when the Snapshot package or logical Snapshot record was created.

Example:

```json
{
  "created_at": "2026-10-30T06:47:12Z"
}
```

This may differ from `captured_at`.

For example:

```text
06:45
Evidence captured

06:47
Snapshot package created

09:10
Snapshot imported

12:00
Report generated
```

These are distinct times.

---

# Capture Window

Some collection processes take time.

A future extension may need:

```text
capture_started_at
capture_completed_at
```

Snapshot v0.1 uses `captured_at` as the primary temporal reference.

Where a collection window materially affects interpretation, producer-specific attributes MAY preserve additional timing information until a common need justifies promoting those fields.

---

# Field: `state`

## Type

```text
string
```

## Required

Yes.

## Allowed v0.1 Values

```text
complete
partial
invalid
unknown
```

---

# State: `complete`

The producer considers the Snapshot complete for its declared scope.

This does NOT necessarily mean:

```text
complete backup of machine
all possible evidence
every file
every log
every subsystem
```

It means:

> The producer completed the collection it intended to perform for this declared scope.

---

# State: `partial`

The collection was created but some intended evidence is missing, unavailable, or failed.

Example:

```text
Windows event logs collected

SMART unavailable

Network inventory collected

Registry collection failed
```

The Snapshot remains useful.

Its limitations must remain visible.

---

# State: `invalid`

The Snapshot cannot be relied upon as a valid captured evidence set.

Potential reasons might include:

```text
failed integrity verification
malformed contents
unsupported critical structure
corruption
```

An invalid Snapshot SHOULD NOT silently feed operational Findings.

---

# State: `unknown`

The Snapshot state cannot currently be established confidently.

Unknown is preferable to pretending completeness.

---

# Snapshot State Is Not Source Health

A:

```text
complete
```

Snapshot may describe a catastrophically broken machine.

A:

```text
partial
```

Snapshot may describe a perfectly healthy system.

Snapshot state describes the quality/completeness of the collection.

It does not describe the health of the source.

---

# Field: `scope`

## Type

```text
object
```

## Required

Yes.

## Purpose

Describes what the Snapshot intends to represent.

Every scope MUST contain:

```text
type
```

Initial scope types may include:

```text
machine
asset
storage_location
estate_subset
network
collection
synthetic_scenario
```

---

# Scope: `machine`

Represents a capture centred on one machine.

Example:

```json
{
  "scope": {
    "type": "machine",
    "asset_id": "asset:captured-machine"
  }
}
```

This is the natural initial SysMedic scope.

---

# Scope: `asset`

Represents another Asset-specific Snapshot.

Example:

```json
{
  "scope": {
    "type": "asset",
    "asset_id": "asset:switch-1"
  }
}
```

---

# Scope: `storage_location`

Represents captured state from a Storage Location.

Example:

```json
{
  "scope": {
    "type": "storage_location",
    "storage_location_id": "storage:archive"
  }
}
```

---

# Scope: `estate_subset`

Represents several related Assets or Storage Locations captured together.

The exact membership may be represented through contents and Relationships.

---

# Scope: `network`

Represents a network-oriented evidence capture.

This may eventually be useful for:

```text
MARVIN network state
VNA topology snapshots
training scenarios
```

---

# Scope: `collection`

Represents a logical collection of evidence, potentially historical or archaeological.

This may become useful for Charkive.

---

# Scope: `synthetic_scenario`

Represents an artificial training world.

Example:

```json
{
  "scope": {
    "type": "synthetic_scenario",
    "scenario_id": "tutorial:unplugged-cable"
  }
}
```

---

# Field: `contents`

## Type

```text
object
```

## Required

Yes.

## Purpose

Declares the VNA objects included in or associated with the Snapshot.

The initial structure may contain:

```text
assets
storage_locations
observations
relationships
findings
```

Example:

```json
{
  "contents": {
    "assets": [
      "asset:captured-machine"
    ],
    "storage_locations": [
      "storage:snapshot:volume-1"
    ],
    "observations": [
      "observation:service-state-001"
    ],
    "relationships": [
      "relationship:machine-volume"
    ],
    "findings": [
      "finding:update-failure-001"
    ]
  }
}
```

References SHOULD normally use IDs.

The Snapshot does not need to embed full objects inside its canonical record.

---

# Why Contents Use References

The Snapshot contract tells us:

```text
which logical VNA objects belong to this captured evidence set
```

The individual contracts tell us:

```text
what those objects mean
```

This avoids duplicating complete Asset, Observation, Finding, and Relationship objects into the Snapshot itself.

---

# Embedded Packaging

A physical Snapshot package MAY contain complete serialised object files.

For example:

```text
snapshot/
├── manifest.json
├── assets/
├── storage/
├── observations/
├── relationships/
├── findings/
└── evidence/
```

That is a packaging choice.

The logical Snapshot contract still refers to the objects by identity.

---

# Raw Evidence

Snapshots may contain evidence that has not yet been normalised into VNA contracts.

Examples:

```text
Windows event logs
registry exports
SMART output
system logs
configuration files
command output
hardware inventories
screenshots
diagnostic artefacts
```

Raw evidence SHOULD remain distinguishable from normalised VNA objects.

A useful conceptual package is:

```text
SNAPSHOT
│
├── raw/
│
├── observations/
│
├── findings/
├── relationships/
└── manifest
```

The exact filesystem layout is producer-defined in v0.1.

---

# Raw Evidence Is Not Finding

A log line is evidence.

An Observation may represent a structured fact extracted from it.

A Finding is an interpretation.

Example:

```text
RAW EVENT LOG
      |
      v
OBSERVATION
Windows update failure event recorded
      |
      v
FINDING
Windows update appears to have failed
```

The levels should not be silently collapsed.

---

# Field: `collection`

## Type

```text
object
```

## Required

No.

## Purpose

Records useful information about how the source evidence was collected.

Example:

```json
{
  "collection": {
    "mode": "read_only",
    "consent": "explicit",
    "network_required": false
  }
}
```

Potential fields include:

```text
mode
consent
network_required
collector
collector_version
configuration
```

---

# Collection Mode

Initial values might include:

```text
read_only
mixed
synthetic
unknown
```

---

# Collection Mode: `read_only`

The collector was intended to avoid changing the source system.

This is particularly important for SysMedic.

---

# Collection Mode: `mixed`

Collection involved some operation which may have altered source state.

This should be used cautiously and accompanied by appropriate evidence.

---

# Collection Mode: `synthetic`

No real system was collected.

The Snapshot was generated from simulation or training state.

---

# Consent

Where relevant, collection may record consent context.

Potential values include:

```text
explicit
preauthorised
not_applicable
unknown
```

This information is provenance.

It does not replace actual access-control policy.

---

# Network Requirement

Example:

```json
{
  "network_required": false
}
```

This may be particularly useful for SysMedic collections intended to work completely offline.

It describes the collection process.

It does not automatically mean the Snapshot itself is safe to transmit.

---

# Collector Identity

Example:

```json
{
  "collection": {
    "collector": "sysmedic-win32",
    "collector_version": "2026.10"
  }
}
```

This allows:

```text
Snapshot producer:
SysMedic

Collector:
SysMedic Windows helper
```

to remain distinct.

---

# Collection Configuration

A producer MAY preserve non-sensitive configuration relevant to interpretation.

Example:

```json
{
  "collection": {
    "configuration": {
      "event_log_window": "configured"
    }
  }
}
```

Sensitive values MUST NOT be embedded casually.

Credentials do not belong here.

---

# Field: `integrity`

## Type

```text
object
```

## Required

No.

## Purpose

Describes integrity information for the Snapshot or its contents.

Example:

```json
{
  "integrity": {
    "status": "verified"
  }
}
```

Initial status values may include:

```text
verified
unverified
failed
unknown
```

---

# Integrity: `verified`

The producer or importer successfully completed the integrity checks it supports.

This does NOT mean:

```text
the source machine was trustworthy
the evidence is factually correct
the machine was uncompromised
every interpretation is correct
```

It means only:

> The captured package passed the specified integrity verification.

---

# Integrity: `unverified`

The Snapshot has not yet been integrity-verified.

It may still be readable.

Consumers should treat it appropriately.

---

# Integrity: `failed`

The available integrity check failed.

The Snapshot SHOULD normally become:

```text
state:
invalid
```

or otherwise be prevented from silently producing trusted downstream Findings.

---

# Integrity: `unknown`

Integrity status cannot currently be established.

---

# Integrity Mechanism

A future implementation may record:

```text
hash algorithm
manifest digest
signature information
encryption envelope
```

Snapshot v0.1 deliberately does not prescribe one cryptographic mechanism.

Producers SHOULD use established cryptographic tools rather than inventing new cryptography.

---

# Encryption Is Not Integrity

Encryption and integrity are related but distinct concerns.

A Snapshot may be:

```text
encrypted
```

to protect confidentiality.

It must still support integrity verification appropriate to its format.

Likewise, an integrity-verified plaintext Snapshot is not confidential merely because its hashes match.

---

# Snapshot Contract vs Snapshot Container

This distinction is important.

```text
VNA SNAPSHOT CONTRACT

The logical metadata and provenance model.
```

versus:

```text
SNAPSHOT CONTAINER

The physical package carrying the files.
```

For SysMedic, the portable container may be encrypted.

The VNA Snapshot contract should describe the imported logical evidence independently of whether the package travelled as:

```text
encrypted age file
directory
archive
API transfer
future package format
```

---

# SysMedic Portable Snapshot

SysMedic currently provides the clearest initial use case.

Conceptually:

```text
RUNNING WINDOWS
      |
      v
SYSMEDIC COLLECTOR
      |
      v
ENCRYPTED PORTABLE PACKAGE
      |
      v
SYSMEDIC IMPORT
      |
      v
RAW EVIDENCE
      |
      v
VNA SNAPSHOT
      |
      v
OBSERVATIONS
      |
      v
FINDINGS
```

The portable encrypted package is a transport/container.

The VNA Snapshot is the common semantic representation after import.

---

# SysMedic Snapshot Example

```json
{
  "schema": "vna.snapshot/0.1",
  "id": "snapshot:SM-20261030-0733",
  "name": "SysMedic collection SM-20261030-0733",
  "producer": {
    "id": "sysmedic",
    "type": "application",
    "version": "2026.10"
  },
  "source": {
    "kind": "live",
    "asset_id": "asset:sysmedic:SM-20261030-0733"
  },
  "captured_at": "2026-10-30T06:45:00Z",
  "created_at": "2026-10-30T06:47:12Z",
  "state": "complete",
  "scope": {
    "type": "machine",
    "asset_id": "asset:sysmedic:SM-20261030-0733"
  },
  "contents": {
    "assets": [
      "asset:sysmedic:SM-20261030-0733"
    ],
    "storage_locations": [
      "storage:snapshot:SM-20261030-0733:volume-1"
    ],
    "observations": [],
    "relationships": [
      "relationship:sysmedic-machine-volume"
    ],
    "findings": []
  },
  "collection": {
    "mode": "read_only",
    "consent": "explicit",
    "network_required": false,
    "collector": "sysmedic-win32"
  },
  "integrity": {
    "status": "verified"
  },
  "production_changed": false,
  "attributes": {
    "sysmedic": {
      "collection_id": "SM-20261030-0733"
    }
  }
}
```

---

# Partial SysMedic Snapshot Example

```json
{
  "schema": "vna.snapshot/0.1",
  "id": "snapshot:SM-20261030-0740",
  "name": "SysMedic collection SM-20261030-0740",
  "producer": {
    "id": "sysmedic",
    "type": "application"
  },
  "source": {
    "kind": "live",
    "asset_id": "asset:captured-machine-2"
  },
  "captured_at": "2026-10-30T06:50:00Z",
  "created_at": "2026-10-30T06:54:00Z",
  "state": "partial",
  "scope": {
    "type": "machine",
    "asset_id": "asset:captured-machine-2"
  },
  "contents": {
    "assets": [
      "asset:captured-machine-2"
    ],
    "storage_locations": [],
    "observations": [],
    "relationships": [],
    "findings": []
  },
  "collection": {
    "mode": "read_only",
    "consent": "explicit",
    "network_required": false
  },
  "omissions": [
    {
      "type": "unavailable",
      "component": "smart",
      "description": "SMART information was not available for one storage device."
    }
  ],
  "failures": [
    {
      "type": "collection_failure",
      "component": "event_log",
      "description": "One requested event log could not be collected."
    }
  ],
  "production_changed": false
}
```

The Snapshot remains useful.

Its incompleteness is explicit.

---

# Field: `omissions`

## Type

```text
array of objects
```

## Required

No.

## Purpose

Records evidence that was deliberately or necessarily not collected.

Examples include:

```text
privacy exclusion
unsupported subsystem
operator choice
scope exclusion
size limit
unavailable evidence
```

Every omission MUST contain:

```text
type
```

It SHOULD contain:

```text
component
description
```

where applicable.

---

# Omission Is Not Failure

An omission may be deliberate.

Example:

```text
Security logs intentionally excluded from this snapshot.
```

A failure means the collector attempted something and did not complete it successfully.

Keeping these separate improves provenance.

---

# Field: `failures`

## Type

```text
array of objects
```

## Required

No.

## Purpose

Records collection or processing operations which failed.

Example:

```json
{
  "failures": [
    {
      "type": "collection_failure",
      "component": "event_log",
      "description": "Requested evidence could not be collected."
    }
  ]
}
```

A partial Snapshot SHOULD make relevant failures visible rather than silently pretending completeness.

---

# Failure Does Not Necessarily Invalidate Snapshot

A Snapshot can remain:

```text
partial
```

and useful.

For example:

```text
hardware inventory:
collected

storage information:
collected

network configuration:
collected

one event log:
failed
```

The Snapshot still contains valuable evidence.

---

# Field: `production_changed`

## Type

```text
boolean
```

## Required

Yes.

## Purpose

Records whether creating the Snapshot caused a meaningful change to the source environment.

For read-only SysMedic collection:

```json
{
  "production_changed": false
}
```

A synthetic Snapshot also normally uses:

```text
false
```

because no production reality exists to change.

---

# Snapshot Collection Should Prefer Read-Only

The preferred VNA evidence path is:

```text
COLLECT
   |
   v
PRESERVE
   |
   v
ANALYSE
```

rather than:

```text
COLLECT WHILE MODIFYING
```

Where modification is necessary, it must be declared explicitly.

This is particularly important for diagnostic and forensic-style evidence.

---

# Field: `relationships`

## Type

```text
array of strings or relationship references
```

## Required

No.

## Purpose

Allows the Snapshot itself to participate in VNA topology and provenance.

For example:

```text
snapshot
captured_from
asset
```

or:

```text
generalised snapshot
derived_from
original snapshot
```

The canonical topology still belongs to `RELATIONSHIP.md`.

---

# Snapshot Relationship: `captured_from`

A future relationship may express:

```text
snapshot:123
    |
    | captured_from
    v
asset:eddie
```

Where the source Asset is known.

For SysMedic, the source machine may not yet have a reconciled live VNA identity.

In that case, the Snapshot may initially reference its captured Asset identity.

---

# Snapshot Relationship: `derived_from`

Useful for redacted or transformed Snapshots.

```text
snapshot:generalised-123
    |
    | derived_from
    v
snapshot:original-123
```

This allows VNA to produce privacy-preserving evidence packages while retaining provenance internally.

---

# Snapshot Relationship: `contains`

The Snapshot may conceptually contain:

```text
Assets
Storage Locations
Observations
Findings
Relationships
```

This membership is already expressed through the `contents` field.

Explicit `contains` Relationships are therefore optional and SHOULD NOT be generated redundantly unless a graph-based consumer genuinely requires them.

---

# Snapshot Assets

A Snapshot may contain Asset records.

For example:

```text
Captured machine
Captured storage device
Captured network device
Historical source device
```

These Assets must preserve source provenance.

Example:

```json
{
  "source": {
    "kind": "snapshot",
    "producer": "sysmedic",
    "id": "snapshot:SM-20261030-0733"
  }
}
```

---

# Snapshot Storage Locations

A Snapshot may contain Storage Locations.

For example:

```text
captured NTFS volume
captured Linux filesystem
historical storage root
```

Their source remains:

```text
snapshot
```

or:

```text
historical
```

as appropriate.

---

# Snapshot Observations

Observations derived from captured evidence MUST preserve Snapshot provenance.

For example:

```json
{
  "source": {
    "kind": "snapshot",
    "id": "snapshot:SM-20261030-0733"
  }
}
```

This prevents a downstream client from accidentally reporting:

> The service is stopped now.

when the defensible statement is:

> The service was recorded as stopped when the Snapshot was captured.

---

# Snapshot Findings

Findings derived from a Snapshot MUST also preserve:

```text
source.kind = snapshot
```

Example:

```text
Finding:
Windows Update appears to have failed.

Source:
SysMedic Snapshot

Captured:
06:45
```

This temporal context is not optional fluff.

It is part of the Finding's meaning.

---

# Snapshot Relationships

Relationships captured in a Snapshot describe topology at capture time.

For example:

```text
captured machine
has_storage
captured volume
```

They must not silently become live topology.

The provenance remains:

```text
snapshot
```

---

# Reanalysis

Snapshots should support future reanalysis.

This is an important design goal.

For example:

```text
2026

Snapshot captured
    |
    v
Analysis v1
    |
    v
Finding A


2027

Improved analyser
    |
    v
same Snapshot
    |
    v
Finding A
Finding B
```

The captured evidence did not change.

The analysis improved.

This is one reason raw evidence and normalised Observations should remain preserved where practical.

---

# Findings Can Change Without Snapshot Changing

A Snapshot should normally be immutable once captured and verified.

Findings derived from it may evolve as:

```text
detectors improve
rules improve
T4 explanations improve
VNA reasoning improves
human annotations are added
```

The Snapshot is evidence.

The interpretations may evolve.

---

# Snapshot Immutability

Once a Snapshot has been captured, packaged, imported, and integrity-verified, its original evidence SHOULD be treated as immutable.

Do not rewrite raw captured evidence to reflect later understanding.

Prefer:

```text
ORIGINAL SNAPSHOT
        |
        v
NEW OBSERVATION / FINDING / ANNOTATION
```

over:

```text
MODIFY OLD EVIDENCE
```

---

# User Annotations

Human knowledge added later should not alter the original Snapshot.

For example:

```text
Snapshot evidence:

Camera timestamps say 2003.


Later user annotation:

"The camera clock was wrong around this period."
```

The user annotation becomes a separate Observation or relationship with user provenance.

The original evidence remains unchanged.

---

# Charkive Archaeology Snapshot

Charkive may eventually package a historical evidence set into a Snapshot.

Example:

```json
{
  "schema": "vna.snapshot/0.1",
  "id": "snapshot:charkive:archaeology-42",
  "name": "Historical photo collection analysis",
  "producer": {
    "id": "charkive",
    "type": "application"
  },
  "source": {
    "kind": "historical"
  },
  "captured_at": "2026-10-30T07:00:00Z",
  "created_at": "2026-10-30T07:01:00Z",
  "state": "complete",
  "scope": {
    "type": "collection"
  },
  "contents": {
    "assets": [
      "asset:historical:kodak-camera"
    ],
    "storage_locations": [
      "storage:archive:photos"
    ],
    "observations": [
      "observation:photo-time-001"
    ],
    "relationships": [
      "relationship:kodak-source-photos"
    ],
    "findings": [
      "finding:charkive-ta-001"
    ]
  },
  "production_changed": false
}
```

The Snapshot records the evidence set used for that historical analysis.

---

# VNA Synthetic Tutorial Snapshot

A synthetic scenario can use the same contract.

Example:

```json
{
  "schema": "vna.snapshot/0.1",
  "id": "snapshot:tutorial:unplugged-cable",
  "name": "Tutorial: Unplugged Cable",
  "producer": {
    "id": "vna",
    "type": "simulation"
  },
  "source": {
    "kind": "synthetic",
    "id": "tutorial:unplugged-cable"
  },
  "captured_at": "2026-10-30T07:00:00Z",
  "created_at": "2026-10-30T07:00:00Z",
  "state": "complete",
  "scope": {
    "type": "synthetic_scenario",
    "scenario_id": "tutorial:unplugged-cable"
  },
  "contents": {
    "assets": [
      "asset:tutorial-office-a",
      "asset:tutorial-office-b"
    ],
    "storage_locations": [],
    "observations": [
      "observation:tutorial-carrier-001"
    ],
    "relationships": [
      "relationship:tutorial-a-b"
    ],
    "findings": [
      "finding:vna-layer1-001",
      "finding:vna-layer7-001"
    ]
  },
  "collection": {
    "mode": "synthetic",
    "consent": "not_applicable",
    "network_required": false
  },
  "production_changed": false
}
```

The same VNA contracts now describe both:

```text
real captured systems
```

and:

```text
training worlds
```

without confusing the two.

---

# Snapshot-Based Gameplay

A Snapshot may become the source of a VNA mission.

```text
SNAPSHOT
   |
   v
GENERALISE
   |
   v
WORLD
   |
   v
PLAYER INVESTIGATES
   |
   v
MISSION RESULT
```

The source context MUST remain known.

A player should be able to see:

```text
World type:
Snapshot

Captured:
[time]

Live production access:
None
```

where appropriate.

---

# Snapshot Mission Safety

A Snapshot world is particularly valuable because the player can investigate freely without changing the original machine.

The environment may allow:

```text
opening logs
inspecting topology
testing hypotheses
examining storage
asking Oracles
producing diagnoses
```

while:

```text
production_changed = false
```

remains true.

---

# Snapshot vs Live World

VNA must make this distinction obvious.

```text
SNAPSHOT WORLD

Evidence describes captured state.

Actions are normally symbolic.

Original machine may have changed since capture.
```

versus:

```text
LIVE WORLD

Evidence may describe current infrastructure.

Actions may potentially cross an operational authority boundary.
```

A player must never accidentally mistake one for the other.

---

# Generalised Snapshot

A Snapshot may be transformed into a privacy-preserving derivative.

Example:

```text
ORIGINAL SNAPSHOT

EDDIE
/srv/private/archive
real hostnames
real paths

        |
        v

GENERALISED SNAPSHOT

SERVER-104
Archive District
pseudonymous identities
reduced detail
```

The derivative Snapshot SHOULD retain provenance to the original within an authorised layer.

---

# Generalisation Must Preserve Source Truth

Generalisation may:

```text
rename Assets
remove sensitive aliases
generalise paths
remove unnecessary evidence
reduce topology detail
```

It MUST NOT silently convert:

```text
snapshot
```

into:

```text
live
```

or:

```text
historical
```

into:

```text
synthetic
```

The nature of the evidence must remain explicit.

---

# Redaction and Omission

A generalised Snapshot may deliberately omit evidence.

Those omissions SHOULD be recorded.

Example:

```json
{
  "omissions": [
    {
      "type": "privacy_redaction",
      "component": "usernames",
      "description": "User-identifying information was removed before sharing."
    }
  ]
}
```

This allows a recipient to understand that absence of evidence may be deliberate.

---

# Evidence Chain

A healthy Snapshot provenance chain looks like:

```text
REAL SYSTEM
    |
    v
COLLECTOR
    |
    v
RAW EVIDENCE
    |
    v
SNAPSHOT
    |
    v
OBSERVATION
    |
    v
FINDING
    |
    v
EXPLANATION
```

At every stage the consumer should be able to move back towards the evidence where authorised.

---

# Snapshot and T4

T4 may explain Snapshot-derived Findings.

Example:

```text
T4Tots

"When SysMedic checked this computer, the update looked stuck."


T4Teens

"The SysMedic snapshot contains evidence that Windows Update had not completed successfully when the machine was collected."


T4Techies

Source:
snapshot:SM-20261030-0733

Captured:
2026-10-30T06:45:00Z

Finding:
system.update.failure

Evidence:
[references]
```

T4 must preserve the historical nature of the evidence in every explanation.

---

# Snapshot and FileCity

FileCity may render captured storage as historical or offline geography.

For example:

```text
LIVE STORAGE DISTRICT

bright / active


SNAPSHOT STORAGE DISTRICT

frozen / historical / clearly labelled
```

The exact rendering belongs to FileCity.

The Snapshot provenance supplies the distinction.

---

# Snapshot and Charkive

Charkive may analyse storage evidence from a Snapshot without requiring the original filesystem to remain connected.

This can support:

```text
Data Archaeology
Temporal Anomaly analysis
historical classification
storage investigation
```

Charkive MUST preserve Snapshot provenance in resulting Observations and Findings.

---

# Snapshot and SysMedic

SysMedic is expected to be the first major producer of real-world VNA Snapshots.

The intended boundary is:

```text
SYSMEDIC

Collect safely
Preserve raw evidence
Import encrypted package
Verify package
Normalise evidence

        |
        v

VNA SNAPSHOT

Common provenance
Asset references
Storage references
Observation references
Relationship references
Finding references
```

SysMedic remains responsible for its collection and safety mechanisms.

VNA owns the common Snapshot representation.

---

# Snapshot and Relationships

Relationships captured within a Snapshot describe the topology seen in the captured state.

Example:

```text
captured machine
    |
    | has_storage
    v
captured volume
```

These Relationships should carry:

```text
provenance.kind = snapshot
```

and reference the Snapshot where appropriate.

---

# Snapshot and Events

A Snapshot can contain evidence of historical Events.

It may also produce VNA Events during its own lifecycle.

Examples:

```text
snapshot.created
snapshot.imported
snapshot.integrity_verified
snapshot.analysis_completed
snapshot.generalised
snapshot.rejected
```

These belong in `EVENT.md`.

The Snapshot contract itself records the current Snapshot object.

---

# Snapshot and Proposed Actions

A Snapshot does not grant authority to act on the original system.

This is especially important.

A Finding derived from:

```text
snapshot
```

must not lead automatically to:

```text
live production action
```

because reality may have changed since capture.

Any proposed real-world action requires:

```text
current target identification
fresh validation
authorisation
current-state checks
execution
verification
```

Snapshot evidence may inform that process.

It does not replace it.

---

# Snapshot and Verified Results

A Verified Result may itself eventually be included in a later Snapshot or historical evidence set.

But:

```text
Snapshot
```

and:

```text
Verified Result
```

remain distinct concepts.

A Snapshot says:

> This is what was captured.

A Verified Result says:

> This requested operation was executed and its outcome was checked.

---

# Field: `attributes`

## Type

```text
object
```

## Required

No.

## Purpose

Provides an extension area for producer-specific Snapshot metadata.

Example:

```json
{
  "attributes": {
    "sysmedic": {
      "collection_id": "SM-20261030-0733",
      "import_path": "visit/raw"
    }
  }
}
```

or:

```json
{
  "attributes": {
    "charkive": {
      "scan_id": "charkive:scan:1234"
    }
  }
}
```

Shared consumers SHOULD NOT depend upon producer-specific attributes for core Snapshot semantics.

---

# Minimal Valid Snapshot

```json
{
  "schema": "vna.snapshot/0.1",
  "id": "snapshot:001",
  "name": "Example Snapshot",
  "producer": {
    "id": "sysmedic",
    "type": "application"
  },
  "source": {
    "kind": "live"
  },
  "captured_at": "2026-10-30T06:45:00Z",
  "created_at": "2026-10-30T06:47:00Z",
  "state": "complete",
  "scope": {
    "type": "machine"
  },
  "contents": {
    "assets": [],
    "storage_locations": [],
    "observations": [],
    "relationships": [],
    "findings": []
  },
  "production_changed": false
}
```

---

# Validation Rules

A valid Snapshot v0.1 MUST satisfy:

1. `schema` equals `vna.snapshot/0.1`.
2. `id` is a non-empty string.
3. `name` is a non-empty string.
4. `producer` is an object.
5. `producer.id` is a non-empty string.
6. `producer.type` is a non-empty string.
7. `source` is an object.
8. `source.kind` is one of the recognised v0.1 source kinds.
9. `captured_at` is a valid UTC ISO 8601 timestamp.
10. `created_at` is a valid UTC ISO 8601 timestamp.
11. `state` is one of the recognised v0.1 Snapshot states.
12. `scope` is an object.
13. `scope.type` is a non-empty string.
14. `contents` is an object.
15. `production_changed` is a boolean.
16. If supplied, `collection` is an object.
17. If supplied, `integrity` is an object.
18. If supplied, `omissions` is an array.
19. Every omission contains a non-empty `type`.
20. If supplied, `failures` is an array.
21. Every failure contains a non-empty `type`.
22. If supplied, `relationships` is an array.
23. If supplied, `attributes` is an object.

Consumers SHOULD reject structurally invalid Snapshots rather than silently repairing them.

---

# Semantic Validation

Structural validity does not prove that a Snapshot is trustworthy or complete.

Additional semantic checks may include:

```text
referenced object IDs exist
referenced objects carry compatible provenance
captured_at is plausible
integrity status matches package verification
partial collections declare relevant omissions/failures
synthetic snapshots do not claim live provenance
```

These checks may evolve with implementations.

---

# Compatibility Rules

## Producer

A v0.1 Snapshot producer:

- MUST provide all required fields
- MAY provide optional fields
- MAY add unknown fields
- MUST preserve source provenance
- MUST distinguish real and synthetic evidence
- SHOULD record omissions and failures honestly
- SHOULD preserve raw evidence where practical
- SHOULD make original captured evidence immutable after verification
- SHOULD use established cryptography where encryption is required
- MUST NOT use Snapshot creation as implicit remediation
- SHOULD emit `production_changed: false` for read-only collection

## Consumer

A v0.1 consumer:

- MUST validate fields it depends upon
- SHOULD ignore unknown fields
- SHOULD preserve unknown fields when forwarding Snapshots
- MUST preserve producer identity
- MUST preserve source kind
- MUST preserve captured time
- MUST NOT treat Snapshot evidence as automatically current
- MUST NOT treat `complete` as meaning complete backup
- MUST NOT convert Snapshot Findings into live actions without fresh validation
- MUST treat Snapshot content as untrusted input
- SHOULD surface partial/invalid state where relevant

---

# Security Boundary

Snapshots may contain highly sensitive information.

Examples include:

```text
hostnames
usernames
IP addresses
hardware identifiers
storage layouts
event logs
software inventories
filesystem paths
configuration
crash information
historical activity
customer data fragments
```

Snapshots therefore require stronger handling than many individual records.

---

# Snapshot Content Is Untrusted

A Snapshot may originate from:

```text
broken machine
compromised machine
unknown customer machine
vendor package
external user
synthetic generator
```

Every imported field and raw file must be treated as untrusted input.

Consumers MUST NOT execute content merely because it is inside a Snapshot.

---

# Never Execute Captured Content Automatically

A Snapshot may contain:

```text
scripts
executables
configuration
documents
archives
malicious files
```

Their presence as evidence does not authorise execution.

Import and analysis pipelines should preserve the evidence without casually running it.

---

# Credentials

Snapshots SHOULD avoid capturing secrets unless the specialised collection workflow explicitly requires and protects them.

Particular care is required around:

```text
passwords
tokens
private keys
session cookies
cloud credentials
recovery secrets
```

The common Snapshot contract does not provide a justification for collecting sensitive credentials.

---

# Confidentiality

Sensitive Snapshots SHOULD be protected appropriately during:

```text
collection
temporary storage
transport
import
analysis
archive
```

The VNA contract intentionally does not mandate one encryption format.

SysMedic may use its chosen established portable encryption mechanism.

Other producers may use mechanisms appropriate to their environment.

---

# Transport Independence

Snapshot v0.1 defines a logical evidence object, not a package transport.

Possible physical forms include:

```text
encrypted file
directory
archive
USB-carried bundle
HTTP transfer
database-backed object
vendor package
test fixture
```

The semantic Snapshot record should remain consistent.

---

# Snapshot Retention

Snapshot v0.1 does not mandate retention periods.

Different evidence may have different privacy, legal, technical, or operational requirements.

A system deleting a Snapshot must consider that:

```text
Observations
Findings
Reports
Relationships
Events
```

may reference it.

The ecosystem should avoid leaving unexplained dangling provenance where practical.

---

# Snapshot Deletion vs Asset Deletion

Deleting a Snapshot does not imply deleting the Asset it described.

Likewise:

```text
Asset retired
```

does not automatically imply:

```text
delete historical Snapshots
```

Identity and evidence retention are separate concerns.

---

# First Implementation Target

SysMedic is the obvious first real implementation.

Map the existing SysMedic imported collection into:

```text
vna.snapshot/0.1
```

using:

```text
collection ID
capture time
collector identity
source machine Asset
captured Storage Locations
collection failures
omissions
integrity result
```

No new SysMedic evidence collection is required merely to prove the contract.

Use what SysMedic already captures.

---

# Second Implementation Target

Generate VNA Observations from the imported SysMedic Snapshot.

```text
SNAPSHOT
   |
   v
RAW EVIDENCE
   |
   v
OBSERVATIONS
```

For example:

```text
service state
storage state
network configuration
operating-system evidence
```

All generated Observations preserve:

```text
source.kind = snapshot
```

and reference the Snapshot.

---

# Third Implementation Target

Generate Findings from those Observations.

```text
SNAPSHOT
   |
   v
OBSERVATIONS
   |
   v
FINDINGS
```

The Finding retains Snapshot provenance.

T4 can then explain:

```text
what was seen
when it was seen
how certain the interpretation is
```

without presenting old evidence as live state.

---

# Fourth Implementation Target

Create the first synthetic VNA tutorial Snapshot.

Use the unplugged-cable OSI scenario.

The same contract then supports:

```text
real SysMedic evidence
```

and:

```text
synthetic VNA training evidence
```

while `source.kind` keeps them unmistakably distinct.

---

# Fifth Implementation Target

Create a generalised derivative of a Snapshot.

```text
REAL SNAPSHOT
      |
      v
GENERALISATION
      |
      v
SAFE SHARED SNAPSHOT
```

Remove or replace unnecessary:

```text
hostnames
paths
usernames
identifiers
topology details
```

while preserving the evidence required for a useful investigation.

This proves the privacy boundary for future multiplayer and external-support scenarios.

---

# North Star

A Snapshot should let the ecosystem answer:

```text
WHAT body of evidence is this?

WHO created it?

WHAT was it captured from?

WHEN was the source state captured?

WHEN was the Snapshot package created?

WHAT was the intended scope?

WHAT Assets are represented?

WHAT Storage Locations are represented?

WHAT Observations belong to it?

WHAT Relationships belong to it?

WHAT Findings were derived from it?

HOW was the evidence collected?

WAS collection intended to be read-only?

WAS consent relevant and recorded?

WHAT was deliberately omitted?

WHAT failed to collect?

HAS integrity been verified?

DID collection change production?

IS this evidence live, captured, historical, synthetic, vendor-supplied, or user-supplied?
```

Most importantly, every consumer must remember:

> **A Snapshot can tell us what was true when we looked.**

> **It cannot promise that reality waited for us.**
