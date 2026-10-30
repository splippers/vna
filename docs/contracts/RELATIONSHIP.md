# VNA Relationship Schema v0.1

> **A thing becomes part of a system when we know how it relates to other things.**

## Purpose

A **Relationship** is the common VNA ecosystem representation of a meaningful connection between two identifiable objects.

Relationships turn collections of Assets, Storage Locations, Findings, Snapshots, and other records into a connected model.

Without Relationships:

```text
ASSET
ASSET
STORAGE LOCATION
FINDING
ASSET
```

With Relationships:

```text
ASSET: Eddie
   |
   +── has_storage ─────> STORAGE: Eddie Data
   |
   +── connected_to ────> ASSET: Switch
   |
   `── depends_on ──────> ASSET: Router
```

Relationships allow VNA to express:

```text
topology
dependency
hosting
storage backing
physical attachment
logical attachment
data provenance
historical association
source-device history
quarantine movement
root-cause relationships
```

without embedding an entire graph inside every Asset or Storage Location.

---

# Core Principle

> **Objects own identity. Relationships describe how those identities are connected.**

An Asset should not need to contain:

```text
everything it connects to
everything it hosts
everything it depends upon
everything stored on it
everything mounted from it
```

Likewise, a Storage Location should not contain the entire infrastructure topology supporting it.

Instead:

```text
OBJECT A
   |
   v
RELATIONSHIP
   |
   v
OBJECT B
```

The objects remain independently identifiable.

The relationship can change without rewriting either object's identity.

---

# Ecosystem Position

The common model now becomes:

```text
              ASSET
                |
                +--------------------+
                |                    |
                v                    v
       STORAGE LOCATION        OBSERVATION
                |                    |
                |                    v
                |                 FINDING
                |                    |
                +---------+----------+
                          |
                          v
                   RELATIONSHIPS
```

Relationships can connect different contract types.

For example:

```text
ASSET
  |
  | has_storage
  v
STORAGE LOCATION
```

or:

```text
FINDING
  |
  | caused_by
  v
FINDING
```

or:

```text
SNAPSHOT
  |
  | captured_from
  v
ASSET
```

or:

```text
ASSET
  |
  | source_of
  v
STORAGE COLLECTION
```

where supported by the relevant contracts.

---

# Canonical File Location

Save this contract at:

```text
vna/docs/contracts/RELATIONSHIP.md
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

Every Relationship MUST declare:

```json
{
  "schema": "vna.relationship/0.1"
}
```

The canonical identifier is:

```text
vna.relationship/0.1
```

Consumers MUST NOT assume future versions are structurally identical.

Consumers SHOULD ignore unknown fields unless those fields are explicitly required by the schema version they implement.

---

# Canonical Example

```json
{
  "schema": "vna.relationship/0.1",
  "id": "relationship:eddie-data",
  "type": "has_storage",
  "source": {
    "type": "asset",
    "id": "asset:eddie"
  },
  "target": {
    "type": "storage_location",
    "id": "storage:eddie:data"
  },
  "provenance": {
    "kind": "live",
    "producer": "vna"
  },
  "state": "active",
  "observed_at": "2026-10-30T07:00:00Z",
  "confidence": 1.0,
  "attributes": {}
}
```

This means:

```text
asset:eddie
    |
    | has_storage
    v
storage:eddie:data
```

It does not mean that the storage is:

```text
healthy
mounted
writable
available
authorised for modification
```

Those concerns belong elsewhere.

---

# Required Fields

Relationship v0.1 requires:

```text
schema
id
type
source
target
provenance
state
observed_at
```

Optional fields include:

```text
confidence
evidence
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
vna.relationship/0.1
```

## Purpose

Identifies the common Relationship contract and its version.

---

# Field: `id`

## Type

```text
string
```

## Required

Yes.

## Purpose

Provides the stable identity of this logical Relationship.

Example:

```json
{
  "id": "relationship:eddie-data"
}
```

Consumers MUST treat the Relationship ID as opaque.

They MUST NOT derive source, target, authority, or behaviour from the string itself.

---

# Relationship Identity

A Relationship represents a logical assertion connecting a source and target.

For example:

```text
asset:eddie
has_storage
storage:eddie:data
```

may remain the same logical Relationship even when:

```text
mount point changes
capacity changes
display names change
FileCity geography changes
```

The Relationship exists because the underlying association remains meaningful.

---

# Relationship Direction Matters

Relationships are directional unless explicitly defined otherwise.

For example:

```text
asset:web
depends_on
asset:database
```

does not mean:

```text
asset:database
depends_on
asset:web
```

Likewise:

```text
storage:source
quarantined_to
storage:sanitorium
```

has a meaningful direction.

Consumers MUST preserve source and target orientation.

---

# Symmetric Relationships

Some Relationship types may be conceptually symmetric.

For example:

```text
connected_to
```

may sometimes describe a bidirectional physical or logical connection.

Even then, the canonical record still contains:

```text
source
target
```

The Relationship type definition determines whether inverse interpretation is permitted.

Do not assume every Relationship is symmetric merely because the English phrase sounds reciprocal.

---

# Field: `type`

## Type

```text
string
```

## Required

Yes.

## Purpose

Describes the meaning of the connection.

Initial common Relationship types may include:

```text
connected_to
depends_on
contains
hosted_by
attached_to
has_storage
backed_by
mounted_from
exported_by
source_of
derived_from
observed_by
originally_stored_at
quarantined_to
restorable_to
caused_by
possibly_related_to
propagates_to
```

The vocabulary should grow from real ecosystem requirements.

Do not create hundreds of Relationship types before implementations need them.

---

# Common Versus Producer-Specific Types

A Relationship type should be common only when its meaning is genuinely shared across producers.

Common examples:

```text
connected_to
depends_on
hosted_by
attached_to
has_storage
backed_by
contains
derived_from
```

Specialist Relationships SHOULD use namespaces.

Examples:

```text
charkive.quarantined_to
charkive.restorable_to
charkive.source_of
sysmedic.captured_from
filecity.represented_by
vendor.example.member_of
```

If a producer-specific Relationship later proves broadly useful, it may be promoted into the common vocabulary in a future schema revision.

---

# Field: `source`

## Type

```text
object
```

## Required

Yes.

## Purpose

Identifies the object from which the Relationship originates.

Every source MUST contain:

```text
type
id
```

Example:

```json
{
  "source": {
    "type": "asset",
    "id": "asset:eddie"
  }
}
```

---

# Field: `target`

## Type

```text
object
```

## Required

Yes.

## Purpose

Identifies the object to which the Relationship points.

Every target MUST contain:

```text
type
id
```

Example:

```json
{
  "target": {
    "type": "storage_location",
    "id": "storage:eddie:data"
  }
}
```

---

# Initial Object Reference Types

Initial common reference types may include:

```text
asset
storage_location
observation
finding
snapshot
event
```

Future contracts may introduce other types.

Consumers SHOULD preserve unknown reference types even when they cannot interpret them.

---

# Referential Integrity

A Relationship SHOULD refer to identifiable objects.

Where the referenced object is unavailable to a consumer, the Relationship may still be preserved.

For example:

```text
Relationship:
asset:A depends_on asset:B

Consumer knows:
asset:A

Consumer does not know:
asset:B
```

The Relationship should not necessarily be discarded.

The target may become resolvable later.

---

# No Embedded Objects

Relationship v0.1 should normally reference objects by ID rather than embedding entire object records.

Prefer:

```json
{
  "source": {
    "type": "asset",
    "id": "asset:eddie"
  }
}
```

over embedding:

```text
the complete Asset
all its observations
all its findings
all its storage
```

This keeps contracts composable and avoids stale duplicated state.

---

# Field: `provenance`

## Type

```text
object
```

## Required

Yes.

## Purpose

Describes where the assertion that this Relationship exists came from.

This is distinct from the Relationship's source object.

Example:

```json
{
  "provenance": {
    "kind": "live",
    "producer": "vna"
  }
}
```

The Relationship source may be:

```text
asset:eddie
```

while the producer asserting the Relationship is:

```text
vna
```

These are different concepts.

---

# Provenance Kinds

Relationship v0.1 uses the common VNA provenance/source vocabulary:

```text
live
snapshot
historical
synthetic
vendor
user
generalised
```

---

# Provenance Kind: `live`

The Relationship was derived from current real-world evidence or configuration.

Example:

```json
{
  "provenance": {
    "kind": "live",
    "producer": "vna"
  }
}
```

---

# Provenance Kind: `snapshot`

The Relationship was inferred from captured evidence.

Example:

```json
{
  "provenance": {
    "kind": "snapshot",
    "producer": "sysmedic",
    "id": "sysmedic:snapshot:SM-20261030-0733"
  }
}
```

It does not necessarily describe the current state of the original machine.

---

# Provenance Kind: `historical`

The Relationship is known from historical evidence.

Potential examples include:

```text
old mount relationships
retired infrastructure
Charkive archaeological relationships
previous FileCity geography
```

---

# Provenance Kind: `synthetic`

The Relationship belongs to a simulation, tutorial, test fixture, or procedural environment.

Synthetic Relationships MUST remain distinguishable from real infrastructure.

---

# Provenance Kind: `vendor`

The Relationship originates from vendor-supplied models or scenarios.

---

# Provenance Kind: `user`

The Relationship was explicitly asserted by a user.

Example:

```text
"This old Kodak camera produced these photographs."
```

This can be highly valuable evidence.

It remains distinguishable from machine-discovered topology.

---

# Provenance Kind: `generalised`

The Relationship is a privacy-preserving derivative of another Relationship.

The authorised VNA layer retains any reverse mapping.

---

# Field: `observed_at`

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
  "observed_at": "2026-10-30T07:00:00Z"
}
```

## Purpose

Records when the Relationship was observed, asserted, or established from the relevant evidence.

For snapshot and historical Relationships, this may differ from:

```text
import time
analysis time
current time
display time
```

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
active
inactive
historical
unknown
```

---

# State: `active`

The producer currently considers the Relationship applicable.

Example:

```text
asset:web
depends_on
asset:database
```

and that dependency is currently part of the live estate model.

---

# State: `inactive`

The Relationship remains known but is not currently considered active.

Example:

```text
a mount relationship temporarily disabled
```

or:

```text
a service dependency removed from current configuration but retained briefly for context
```

---

# State: `historical`

The Relationship once applied or is relevant to historical evidence but is not considered currently active.

This is particularly useful for:

```text
retired infrastructure
Charkive archaeology
snapshot history
migration records
old FileCity layouts
```

---

# State: `unknown`

The current applicability of the Relationship cannot be established confidently.

Unknown is preferable to inventing certainty.

---

# Relationship State Is Not Endpoint State

An active Relationship does not mean its source and target are themselves operationally healthy or active.

For example:

```text
Relationship:
asset:web depends_on asset:database

Relationship state:
active
```

while:

```text
asset:database
```

may have:

```text
critical Finding
```

The Relationship describes topology.

Findings describe conditions.

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

## Purpose

Records how confident the producer is that the Relationship exists or is correctly characterised.

Example:

```json
{
  "confidence": 0.76
}
```

The field SHOULD be omitted when the producer cannot calculate a meaningful confidence value.

---

# Confidence Matters for Inferred Relationships

Some Relationships are directly configured.

Example:

```text
mount source
```

may be highly certain.

Others may be inferred.

Example:

```text
possibly_related_to
```

may be much less certain.

The Relationship can express that distinction explicitly.

---

# Field: `evidence`

## Type

```text
array of objects
```

## Required

No.

## Purpose

Provides evidence supporting the Relationship.

Example:

```json
{
  "evidence": [
    {
      "type": "mount_table",
      "description": "The live mount table identifies the filesystem as mounted from this source."
    }
  ]
}
```

Each evidence item MUST contain:

```text
type
```

Producer-specific evidence SHOULD use namespaces where appropriate.

---

# Relationship Evidence Versus Relationship Type

Relationship type says:

```text
what relationship is asserted
```

Evidence says:

```text
why we think that relationship exists
```

Example:

```text
Relationship:
asset:web depends_on asset:database

Evidence:
application configuration references database endpoint
```

These concepts must remain separate.

---

# Field: `attributes`

## Type

```text
object
```

## Required

No.

## Purpose

Provides an extension area for producer-specific Relationship metadata.

Example:

```json
{
  "attributes": {
    "charkive": {
      "reason": "user_confirmed_source_device"
    }
  }
}
```

Common consumers SHOULD NOT depend on producer-specific attributes for core Relationship semantics.

---

# Minimal Valid Relationship

```json
{
  "schema": "vna.relationship/0.1",
  "id": "relationship:001",
  "type": "connected_to",
  "source": {
    "type": "asset",
    "id": "asset:a"
  },
  "target": {
    "type": "asset",
    "id": "asset:b"
  },
  "provenance": {
    "kind": "live",
    "producer": "vna"
  },
  "state": "active",
  "observed_at": "2026-10-30T07:00:00Z"
}
```

---

# Relationship Type: `has_storage`

Describes a simple association between an Asset and a Storage Location.

Example:

```text
asset:eddie
   |
   | has_storage
   v
storage:eddie:data
```

JSON:

```json
{
  "schema": "vna.relationship/0.1",
  "id": "relationship:eddie-has-data",
  "type": "has_storage",
  "source": {
    "type": "asset",
    "id": "asset:eddie"
  },
  "target": {
    "type": "storage_location",
    "id": "storage:eddie:data"
  },
  "provenance": {
    "kind": "live",
    "producer": "vna"
  },
  "state": "active",
  "observed_at": "2026-10-30T07:00:00Z"
}
```

This relationship should be used where the association is meaningful and reasonably direct.

Complex distributed storage should prefer more specific topology.

---

# Relationship Type: `connected_to`

Represents a physical or logical connection between Assets.

Example:

```text
asset:server
    |
    | connected_to
    v
asset:switch
```

The Relationship alone does not necessarily specify:

```text
interface
speed
protocol
directionality
health
```

Those may require attributes, related Assets, Observations, or future specialised contracts.

---

# Physical Connections

A physical network connection may eventually be represented through:

```text
Asset A
   |
   v
Interface A

        connected_to

Interface B
   |
   v
Asset B
```

If interfaces are modelled as distinct objects.

Asset v0.1 does not currently require interface objects to be Assets.

Therefore early implementations may use Asset-to-Asset `connected_to` Relationships.

The model can evolve later.

---

# Relationship Type: `depends_on`

Represents a dependency from the source to the target.

Example:

```text
asset:web
    |
    | depends_on
    v
asset:database
```

Meaning:

> The source depends upon the target for some meaningful function.

The inverse is NOT automatically true.

---

# Relationship Type: `hosted_by`

Represents a logical component hosted by another Asset.

Example:

```text
asset:vm-42
    |
    | hosted_by
    v
asset:hypervisor-1
```

or:

```text
asset:application
    |
    | hosted_by
    v
asset:server
```

---

# Relationship Type: `attached_to`

Represents a physical or strongly associated attachment.

Example:

```text
asset:usb-disk
    |
    | attached_to
    v
asset:workstation
```

Attachment is not necessarily permanent.

Its lifecycle may change independently of either Asset.

---

# Relationship Type: `backed_by`

Represents an implementation relationship where one logical object is backed by another.

Example:

```text
storage:archive
    |
    | backed_by
    v
asset:nas-1
```

or, for distributed storage:

```text
storage:cluster
    |
    +── backed_by ──> asset:node-1
    |
    +── backed_by ──> asset:node-2
    |
    `── backed_by ──> asset:node-3
```

This is preferable to pretending the Storage Location belongs to only one Asset.

---

# Relationship Type: `mounted_from`

Represents a mount relationship.

Example:

```text
storage:local-mount
    |
    | mounted_from
    v
storage:remote-share
```

This can distinguish:

```text
the mounted view
```

from:

```text
the storage being mounted
```

where that distinction matters.

---

# Relationship Type: `exported_by`

Represents a share or exported Storage Location being provided by an Asset or service.

Example:

```text
storage:nas-archive
    |
    | exported_by
    v
asset:nas-1
```

---

# Relationship Type: `contains`

Represents a structural containment relationship.

Use carefully.

For example:

```text
asset:server-rack
    |
    | contains
    v
asset:server
```

or:

```text
snapshot
    |
    | contains
    v
observation
```

where the relevant contracts permit that structure.

Avoid using `contains` where a more meaningful relationship exists.

---

# Relationship Type: `derived_from`

Represents a derived object or representation.

Example:

```text
generalised Asset
    |
    | derived_from
    v
real Asset
```

or:

```text
generalised Finding
    |
    | derived_from
    v
real Finding
```

This is useful for privacy-preserving VNA client models.

---

# Relationship Type: `observed_by`

Represents an explicit relationship between something observed and the collector or system performing the observation.

Example:

```text
asset:server
    |
    | observed_by
    v
asset:monitoring-system
```

Use only when the monitoring relationship is itself relevant to the model.

The Observation contract already contains its producer, so this Relationship is not required merely to record provenance.

---

# Relationship Type: `source_of`

Represents a known source relationship.

This becomes particularly useful in Charkive Data Archaeology.

Example:

```text
asset:historical:kodak-camera
    |
    | source_of
    v
storage collection:photos-kodak
```

or an appropriate future collection object.

This Relationship may originate from:

```text
user confirmation
metadata
historical evidence
device-specific evidence
```

Provenance matters.

---

# Charkive Source-Device Example

```json
{
  "schema": "vna.relationship/0.1",
  "id": "relationship:kodak-source-photos",
  "type": "charkive.source_of",
  "source": {
    "type": "asset",
    "id": "asset:historical:kodak-camera"
  },
  "target": {
    "type": "storage_location",
    "id": "storage:archive:photos"
  },
  "provenance": {
    "kind": "user",
    "producer": "charkive"
  },
  "state": "historical",
  "observed_at": "2026-10-30T07:00:00Z",
  "confidence": 1.0,
  "evidence": [
    {
      "type": "user_confirmation",
      "description": "User identified this camera as the source of the photograph collection."
    }
  ]
}
```

This does not change any photograph metadata.

It records knowledge about the relationship.

---

# Relationship Type: `originally_stored_at`

Represents historical storage provenance.

Example:

```text
quarantined object
    |
    | originally_stored_at
    v
storage:eddie:data
```

Where more path detail is required, that may live in attributes or a future Path Reference contract.

---

# Relationship Type: `charkive.quarantined_to`

Represents movement into a Charkive SANitorium.

Example:

```text
Original location
      |
      v
Charkive quarantine
      |
      v
SANitorium
```

The Relationship does not itself prove that the operation succeeded.

The successful movement should be established by a Verified Result.

The Relationship may be created or activated after verification.

---

# Relationship Type: `charkive.restorable_to`

Represents a known restoration relationship.

Example:

```text
SANitorium object
    |
    | restorable_to
    v
original Storage Location
```

This Relationship describes restoration context.

It does not authorise restoration.

---

# SANitorium Provenance Model

Eventually:

```text
storage:eddie:data
        |
        | originally_stored_at
        v
quarantined object
        |
        | charkive.quarantined_to
        v
storage:eddie:sanitorium
```

and:

```text
quarantined object
        |
        | charkive.restorable_to
        v
storage:eddie:data
```

This gives FileCity enough structure to visualise the SANitorium journey while Charkive retains operational authority.

---

# Relationship Type: `caused_by`

Represents a causal assertion.

Example:

```text
Finding:
Application unavailable
    |
    | caused_by
    v
Finding:
Physical link unavailable
```

This is a strong Relationship.

It should only be used when the producer has sufficient evidence to assert causation.

---

# Causation Must Be Earned

Do not turn:

```text
happened at the same time
```

into:

```text
caused by
```

merely because causal arrows make visualisations satisfying.

Where causation is uncertain, use:

```text
possibly_related_to
```

instead.

---

# Relationship Type: `possibly_related_to`

Represents an association where the relationship may be useful but causation or exact dependency is not established.

Example:

```text
Finding:
Packet loss increased
    |
    | possibly_related_to
    v
Finding:
Wi-Fi signal degraded
```

This allows VNA and Oracles to reason about possible relationships without pretending certainty.

---

# Relationship Type: `propagates_to`

Represents a condition whose effects propagate into another condition.

This is particularly useful for the OSI model.

Example:

```text
L1 Finding
Physical link unavailable
    |
    | propagates_to
    v
L2 Finding
Data-link unavailable
```

Then:

```text
L2
 |
 | propagates_to
 v
L3
```

and so on.

This allows VNA gameplay to distinguish:

```text
ROOT CAUSE
```

from:

```text
PROPAGATED SYMPTOMS
```

---

# OSI Example

```text
L1 FINDING
Physical link unavailable
      |
      | propagates_to
      v
L2 FINDING
Data-link unavailable
      |
      | propagates_to
      v
L3 FINDING
Network unavailable
      |
      v
L4 FINDING
Transport unavailable
      |
      v
L5 FINDING
Session unavailable
      |
      v
L6 FINDING
Presentation unavailable
      |
      v
L7 FINDING
Application unavailable
```

The user experiences the symptom at Layer 7.

The graph records that the root cause belongs below it.

---

# Root Cause Representation

A root-cause Finding does not require a special root-cause boolean in v0.1.

It can be inferred from the relationship graph where appropriate.

For example:

```text
Finding A
caused_by
Finding B
```

means B is causal relative to A.

A single Finding may be root cause for one chain and downstream of another.

Avoid oversimplifying the graph into one universal `root_cause = true` flag.

---

# Relationship Chains

Relationships can form chains.

Example:

```text
Application
 depends_on
Database service
 hosted_by
Database server
 connected_to
Network switch
```

This allows graph traversal.

However, consumers should avoid assuming that every theoretical transitive relationship is semantically valid.

For example:

```text
A depends_on B
B depends_on C
```

may suggest:

```text
A indirectly depends_on C
```

but the contract does not automatically generate that Relationship.

Derived reasoning belongs to the consumer or graph-analysis layer.

---

# No Automatic Transitivity

Relationship v0.1 does not define relationships as transitively closed.

Do not automatically persist:

```text
A depends_on C
```

merely because:

```text
A depends_on B
B depends_on C
```

unless a producer explicitly determines that relationship is useful and valid.

This avoids graph inflation and accidental overstatement.

---

# Inverse Relationships

Some types have obvious linguistic inverses.

For example:

```text
hosted_by
```

could be read inversely as:

```text
hosts
```

and:

```text
has_storage
```

could be read inversely as:

```text
belongs_to_asset
```

Relationship v0.1 stores one canonical direction.

Consumers MAY render friendly inverse language.

They SHOULD NOT create duplicate persisted Relationships merely for presentation unless required by their implementation.

---

# Relationship Type Registry

A common implementation SHOULD maintain a small registry describing known Relationship types.

Conceptually:

```text
type
source types
target types
directionality
symmetric?
description
```

For example:

```text
has_storage

source:
asset

target:
storage_location

symmetric:
false
```

This registry may initially live in code or documentation.

It does not need to become another contract immediately.

---

# Invalid Relationship Shapes

A producer should avoid semantically nonsensical relationships.

For example:

```text
Observation
has_storage
Finding
```

should not validate semantically merely because source and target IDs are strings.

Structural validation and semantic validation are different layers.

---

# Structural Validation

Structural validation asks:

```text
Does the Relationship conform to vna.relationship/0.1?
```

Semantic validation asks:

```text
Does this Relationship type make sense between these object types?
```

Both matter.

The first implementation may begin with structural validation and a small type registry for known relationships.

---

# Duplicate Relationships

Two producers may independently discover the same logical relationship.

For example:

```text
VNA:
asset:eddie has_storage storage:eddie:data

Charkive:
asset:eddie has_storage storage:eddie:data
```

These may represent:

```text
one relationship
supported by multiple evidence sources
```

rather than two genuinely different topology edges.

Relationship v0.1 does not define full deduplication.

VNA SHOULD avoid blindly multiplying identical graph edges where equivalence can be established confidently.

---

# Conflicting Relationships

Different evidence sources may disagree.

For example:

```text
Source A:
storage:archive backed_by asset:nas-1

Source B:
storage:archive backed_by asset:nas-2
```

Possible explanations include:

```text
both are correct
one is stale
storage is replicated
identity was mistaken
configuration changed
```

VNA MUST NOT silently discard one merely because another arrived later.

Conflicting relationships should remain inspectable until reconciled.

---

# Relationship History

Relationships change over time.

For example:

```text
2026-01

VM hosted_by Hypervisor A


2026-10

VM hosted_by Hypervisor B
```

The VM Asset may remain stable.

The hosting Relationship changes.

This is precisely why topology belongs outside the Asset record.

---

# Relationship Lifecycle Events

Future Events may include:

```text
vna.relationship.created
vna.relationship.activated
vna.relationship.deactivated
vna.relationship.historicised
vna.relationship.changed
```

These belong to `EVENT.md`.

The Relationship represents the current known assertion and lifecycle state.

Events record what happened.

---

# Snapshot Relationships

A SysMedic snapshot may establish temporary or historical relationships.

For example:

```text
captured machine
    |
    | has_storage
    v
captured volume
```

with provenance:

```text
snapshot
```

These Relationships refer to the captured state.

They MUST NOT imply that the same topology exists now.

---

# SysMedic Example

```json
{
  "schema": "vna.relationship/0.1",
  "id": "relationship:sysmedic-machine-volume",
  "type": "has_storage",
  "source": {
    "type": "asset",
    "id": "asset:sysmedic:SM-20261030-0733"
  },
  "target": {
    "type": "storage_location",
    "id": "storage:snapshot:SM-20261030-0733:volume-1"
  },
  "provenance": {
    "kind": "snapshot",
    "producer": "sysmedic",
    "id": "sysmedic:snapshot:SM-20261030-0733"
  },
  "state": "historical",
  "observed_at": "2026-10-30T06:45:00Z",
  "confidence": 1.0
}
```

This says:

> At snapshot time, the captured machine had this captured storage relationship.

It does not claim current live state.

---

# FileCity Representation Relationships

FileCity may need to associate VNA objects with world objects.

A producer-specific relationship may eventually express:

```text
storage:eddie:data
    |
    | filecity.represented_by
    v
world:district:eddie-data
```

The world object remains FileCity-owned.

The Storage Location remains VNA-owned identity.

The Relationship provides the bridge.

---

# FileCity Must Not Rewrite Topology

FileCity may visualise:

```text
connected_to
depends_on
has_storage
```

using:

```text
roads
bridges
districts
buildings
traffic
```

The visual representation is not permitted to invent operational relationships merely because a route would make the city look better.

No road without a relationship.

No siren without a Finding.

Reality first.

---

# Generalised Relationships

VNA may derive privacy-preserving Relationships for less-trusted clients.

Example:

```text
REAL

asset:customer-app
 depends_on
asset:customer-db
```

Generalised:

```text
asset:generalised:application-1
 depends_on
asset:generalised:database-1
```

The generalised graph preserves the dependency shape while removing sensitive identity.

---

# Generalisation May Reduce Detail

A real topology might contain:

```text
Server A
  |
  +-- interface eth0
  |
  +-- VLAN 42
  |
  +-- Switch port 17
  |
  `-- Switch B
```

A generalised client may receive only:

```text
Server A
 connected_to
Network B
```

This is acceptable if the intended task does not require the omitted detail.

Generalisation should preserve the technical meaning required by the consumer.

---

# Generalisation Must Not Invent Relationships

A simplified topology may remove detail.

It MUST NOT add dependencies or causal relationships that do not exist merely to make the model easier to understand.

Simplification is allowed.

Fabrication is not.

---

# T4 Consumption

T4 may explain Relationships differently according to audience.

Example Relationship:

```text
asset:web
depends_on
asset:database
```

T4Tots:

```text
"The website needs the database to work."
```

T4Teens:

```text
"The web service depends on the database service."
```

T4Techies:

```text
Source:
asset:web

Relationship:
depends_on

Target:
asset:database

Provenance:
live VNA topology
```

Same Relationship.

Different explanation.

---

# Oracle Consumption

Oracles may reason over Relationship graphs.

For example:

```text
Finding:
Application unavailable

Application
 depends_on
Database

Database
 hosted_by
Server

Server
 has Finding:
Storage critical
```

An Oracle may form a hypothesis that the infrastructure chain deserves investigation.

It MUST distinguish:

```text
relationship evidence
```

from:

```text
causal conclusion
```

unless causation is explicitly established.

---

# VNA Gameplay

Relationships turn abstract infrastructure into navigable geography.

Examples:

```text
connected_to
    |
    v
route between buildings

depends_on
    |
    v
logical dependency corridor

has_storage
    |
    v
entrance into FileCity

hosted_by
    |
    v
one system contained within another environment
```

Gameplay may render those relationships metaphorically.

The underlying relationship remains inspectable.

---

# Gameplay Must Preserve Direction

If:

```text
Application
depends_on
Database
```

a game should not accidentally teach:

```text
Database
depends_on
Application
```

simply because a two-way corridor is visually convenient.

The world can permit movement in both directions while preserving the logical relationship direction in its explanation.

---

# Borrower-Scale or Other Renderers

Future clients may represent Relationships in radically different ways.

For example:

```text
wire
road
bridge
railway
lift
door
tunnel
portal
dependency beam
```

Renderer choice belongs to the client.

Relationship semantics belong to VNA.

---

# Relationship and Proposed Actions

Relationships are informational.

They do not authorise changes.

For example:

```text
asset:usb-disk
attached_to
asset:workstation
```

does not authorise:

```text
detach disk
reformat disk
mount disk
copy files
```

Likewise:

```text
storage:item
charkive.restorable_to
storage:original
```

does not authorise restoration.

Authority belongs to Proposed Action and execution policy.

---

# Relationship and Verified Results

A real operation may cause Relationships to change.

For example:

```text
Before:

Object
originally_stored_at
storage:A


After verified quarantine:

Object
charkive.quarantined_to
storage:SANitorium
```

The Relationship update should follow verified reality.

Do not create the new Relationship merely because an operation was requested.

---

# Relationship and Events

A Relationship describes a current or historical assertion.

An Event records that something happened.

For example:

```text
Relationship:

VM hosted_by Hypervisor B
```

Event:

```text
VM migrated from Hypervisor A to Hypervisor B
```

Both may be valuable.

They should not be collapsed into one contract.

---

# Relationship and Observation

An Observation may provide evidence for a Relationship.

Example:

```text
Observation:

Mount table reports:
/srv/data mounted from server:/archive

        |
        v

Relationship:

storage:local-view
mounted_from
storage:remote-archive
```

The Observation is what was seen.

The Relationship is the topology inferred from it.

---

# Relationship and Finding

A Finding may itself participate in Relationships.

Example:

```text
Finding A
caused_by
Finding B
```

or:

```text
Finding A
possibly_related_to
Finding B
```

This allows the ecosystem to reason about incident structure without embedding entire finding graphs into each Finding object.

---

# Confidence and Causation

A causal Relationship with weak evidence should either:

```text
use an appropriately lower confidence
```

or, preferably where causation itself is uncertain:

```text
use possibly_related_to
```

instead of `caused_by`.

Do not use confidence as an excuse to make an otherwise misleading categorical assertion.

---

# Privacy

Relationships can expose sensitive topology even when object names themselves are pseudonymised.

For example:

```text
public service
depends_on
internal database
```

may reveal architecture.

Therefore generalisation and access control must consider:

```text
nodes
AND
edges
```

Protecting Asset names alone is not sufficient.

---

# Security Boundary

Relationship records are data.

They do not confer authority.

All fields MUST be treated as untrusted input at integration boundaries.

This includes:

```text
id
type
source.id
target.id
evidence
attributes
provenance
```

Consumers MUST NOT interpolate Relationship content directly into:

```text
shell commands
SQL
filesystem operations
network configuration
privileged APIs
game scripting
```

without appropriate validation and authorisation.

---

# Transport Independence

Relationship v0.1 defines an object.

It does not define how Relationships are transported.

Possible transports include:

```text
JSON
HTTP API
snapshot package
database
graph store
message queue
test fixture
```

The semantics should remain stable.

---

# Validation Rules

A valid Relationship v0.1 MUST satisfy:

1. `schema` equals `vna.relationship/0.1`.
2. `id` is a non-empty string.
3. `type` is a non-empty string.
4. `source` is an object.
5. `source.type` is a non-empty string.
6. `source.id` is a non-empty string.
7. `target` is an object.
8. `target.type` is a non-empty string.
9. `target.id` is a non-empty string.
10. `provenance` is an object.
11. `provenance.kind` is one of the recognised v0.1 provenance kinds.
12. `provenance.producer` is a non-empty string.
13. `state` is one of the recognised v0.1 state values.
14. `observed_at` is a valid UTC ISO 8601 timestamp.
15. If supplied, `confidence` is between `0.0` and `1.0`.
16. If supplied, `evidence` is an array.
17. Every evidence item contains a non-empty `type`.
18. If supplied, `attributes` is an object.

Structural validation alone does not prove that a Relationship is semantically meaningful.

Known types SHOULD additionally be checked against their permitted source and target types.

---

# Compatibility Rules

## Producer

A v0.1 producer:

- MUST provide all required fields
- MAY provide optional fields
- MAY add unknown fields
- SHOULD use stable Relationship IDs where practical
- MUST preserve source and target direction
- MUST distinguish real and synthetic provenance
- SHOULD provide evidence for inferred relationships
- SHOULD avoid asserting causation without sufficient evidence
- SHOULD namespace specialist Relationship types
- SHOULD preserve historical relationships where they remain referenced

## Consumer

A v0.1 consumer:

- MUST validate fields it depends upon
- SHOULD ignore unknown fields
- SHOULD preserve unknown fields when forwarding records
- MUST preserve direction
- MUST preserve provenance
- MUST NOT infer symmetry unless the Relationship type defines it
- MUST NOT infer transitivity automatically
- MUST NOT execute actions based solely on a Relationship
- MUST NOT treat `possibly_related_to` as `caused_by`
- MUST treat all Relationship content as untrusted input

---

# v0.1 Deliberate Omissions

Relationship v0.1 intentionally does not fully define:

```text
graph query language
relationship registry format
inverse relationship persistence
automatic transitivity
deduplication
graph reconciliation
edge version numbers
effective time ranges
ownership
RBAC
relationship weighting
routing
network interface model
port model
cable model
VLAN model
cloud topology model
FileCity world-object contract
collection contract
quarantine-item contract
```

These should be added only when real implementation work proves they are needed.

The purpose of v0.1 is much smaller:

> **Give the ecosystem a stable way to say how two known things are related.**

---

# First Implementation Target

Join Asset and Storage Location.

```text
asset:eddie
    |
    | has_storage
    v
storage:eddie:data
```

Implement:

```json
{
  "schema": "vna.relationship/0.1",
  "id": "relationship:eddie-data",
  "type": "has_storage",
  "source": {
    "type": "asset",
    "id": "asset:eddie"
  },
  "target": {
    "type": "storage_location",
    "id": "storage:eddie:data"
  },
  "provenance": {
    "kind": "live",
    "producer": "vna"
  },
  "state": "active",
  "observed_at": "2026-10-30T07:00:00Z"
}
```

This proves the graph model with the simplest useful relationship.

---

# Second Implementation Target

Charkive SANitorium provenance.

Represent:

```text
original Storage Location
        |
        v
quarantined object
        |
        v
SANitorium Storage Location
```

using explicit Relationships.

This gives FileCity and VNA enough information to understand the journey without either reproducing Charkive's quarantine logic.

---

# Third Implementation Target

SysMedic snapshot topology.

```text
Captured Machine
      |
      | has_storage
      v
Captured Filesystem
```

and, where evidence supports it:

```text
Captured disk
      |
      | attached_to
      v
Captured machine
```

The provenance remains:

```text
snapshot
```

---

# Fourth Implementation Target

VNA OSI tutorial topology.

```text
Office A
    |
    | connected_to
    v
Office B
```

Then introduce the synthetic Layer 1 fault.

The topology remains.

The connectivity Finding changes.

This proves an important distinction:

```text
RELATIONSHIP

These things are connected in the model.


FINDING

That connection is currently not working.
```

Those are not the same concept.

---

# Fifth Implementation Target

Root cause and propagated symptoms.

Create:

```text
L1 Finding
    |
    | propagates_to
    v
L2 Finding
    |
    v
L3 Finding
    |
    v
...
    |
    v
L7 Finding
```

and:

```text
L7 Finding
    |
    | caused_by
    v
L1 Finding
```

The OSI Tower can now explain root cause versus symptom using common VNA contracts rather than game-specific scripting.

---

# North Star

A Relationship should let the ecosystem answer:

```text
WHAT two things are related?

WHAT kind of relationship is it?

WHICH direction does it point?

WHO asserted the relationship?

WHERE did that knowledge come from?

WHEN was it observed?

IS it still considered active?

HOW confident are we?

WHAT evidence supports the assertion?

IS this a direct relationship, dependency, causal link, storage relationship, historical association, or possible correlation?
```

It should not pretend to answer:

```text
Are the objects healthy?

Are they reachable?

Are we authorised to change them?

Did an operation succeed?

Is correlation causation?

How should the relationship look in a game?
```

Those belong elsewhere.

The guiding rule is:

> **Identity tells us what the things are.**

> **Relationships tell us how the things fit together.**
