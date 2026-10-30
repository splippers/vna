
# VNA Asset Schema v0.1

> **Give the thing a stable identity before describing what happens to it.**

## Purpose

An **Asset** is the common VNA ecosystem representation of an identifiable real, captured, synthetic, generalised, or vendor-defined thing.

Assets give Observations and Findings something stable to refer to.

Conceptually:

```text
                        ASSET
                          |
             +------------+------------+
             |                         |
             v                         v
       OBSERVATIONS                 FINDINGS
             |                         |
             +------------+------------+
                          |
                          v
                     RELATIONSHIPS
```

An Asset might represent:

```text
computer
server
workstation
laptop
virtual machine
network device
storage device
service
application
cloud resource
vendor equipment
synthetic training equipment
```

The Asset contract provides identity.

It does not attempt to contain every piece of telemetry, every relationship, every Finding, or every property the ecosystem may ever discover.

---

# Core Principle

> **Identity is not the same thing as name, address, state, or location.**

A machine may:

```text
change hostname
change IP address
move network
gain another storage device
lose a service
be renamed
be generalised for a game
```

while remaining the same logical Asset.

The Asset ID exists so the ecosystem can continue talking about the same thing.

---

# Ecosystem Position

The basic VNA model becomes:

```text
ASSET
  |
  +------> OBSERVATION
  |           |
  |           v
  |        FINDING
  |
  +------> RELATIONSHIP
  |
  +------> STORAGE LOCATION
  |
  `------> SNAPSHOT REFERENCES
```

An Asset is primarily identity and relatively stable descriptive information.

Changing operational state belongs in Observations and Findings.

---

# Canonical File Location

Save this contract at:

```text
vna/docs/contracts/ASSET.md
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

VNA owns the common Asset contract.

Specialist systems may create or reference VNA Assets without becoming the authority for every property of those Assets.

---

# Schema Identifier

Every Asset MUST declare:

```json
{
  "schema": "vna.asset/0.1"
}
```

The canonical identifier is:

```text
vna.asset/0.1
```

Consumers MUST NOT assume future versions are structurally identical.

Consumers SHOULD ignore unknown fields unless those fields are explicitly required by a schema version they implement.

---

# Canonical Example

```json
{
  "schema": "vna.asset/0.1",
  "id": "asset:eddie",
  "kind": "machine",
  "name": "EDDIE",
  "source": {
    "kind": "live",
    "producer": "vna"
  },
  "aliases": [
    {
      "type": "hostname",
      "value": "eddie"
    }
  ],
  "platform": {
    "family": "linux"
  },
  "status": "active",
  "attributes": {}
}
```

This example says:

> There is a VNA Asset representing a machine currently known as EDDIE.

It does not claim:

```text
EDDIE is healthy
EDDIE is reachable
EDDIE is online
EDDIE has a particular IP address
EDDIE has no active Findings
```

Those require observations or other evidence.

---

# Required Fields

Asset v0.1 requires:

```text
schema
id
kind
name
source
status
```

Optional fields include:

```text
aliases
platform
description
labels
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
vna.asset/0.1
```

## Purpose

Identifies the shared Asset contract and its version.

---

# Field: `id`

## Type

```text
string
```

## Required

Yes.

## Purpose

Provides the stable identity of the logical Asset.

Example:

```json
{
  "id": "asset:eddie"
}
```

Consumers MUST treat the Asset ID as opaque.

They MUST NOT infer:

```text
hostname
IP address
physical location
platform
ownership
privilege
```

from the internal structure of the identifier.

---

# Stable Identity

An Asset ID SHOULD survive ordinary changes to the thing it identifies.

For example:

```text
Yesterday

hostname:
eddie

Asset:
asset:123


Today

hostname:
eddie-new

Asset:
asset:123
```

Changing the hostname does not necessarily create a new Asset.

Likewise:

```text
IP address changed
        ≠
new Asset

storage added
        ≠
new Asset

operating system updated
        ≠
new Asset

machine moved
        ≠
new Asset
```

Asset identity should represent continuity of the logical thing rather than one particular configuration snapshot.

---

# Asset IDs Must Not Depend on Display Names

Avoid requiring identifiers such as:

```text
asset:eddie
```

to remain forever coupled to the hostname `eddie`.

Human-readable IDs may be convenient during development, but consumers MUST still treat them as opaque.

A future identifier might instead resemble:

```text
asset:0192f7b2-8ab4-7d21
```

The contract does not mandate the ID-generation method.

---

# Identity Authority

A producer creating an Asset SHOULD have enough evidence to establish that it is referring to a distinct logical thing.

The ecosystem must avoid casually creating duplicate Assets for:

```text
hostname
IP address
MAC address
snapshot name
display name
```

when those values may merely describe the same underlying Asset.

A complete cross-source identity-resolution system is outside Asset v0.1.

For v0.1, producers should preserve known identities and avoid speculative merging.

---

# Do Not Merge Assets Aggressively

Suppose two sources report:

```text
hostname:
EDDIE
```

That alone may not prove they refer to the same machine.

Likewise:

```text
same IP address
```

does not prove persistent identity.

Address reuse, DHCP, virtualisation, snapshots, and renamed systems make aggressive identity assumptions dangerous.

When identity cannot be established confidently, separate Assets are safer than silently merging unrelated systems.

---

# Field: `kind`

## Type

```text
string
```

## Required

Yes.

## Purpose

Describes the broad class of thing represented by the Asset.

Initial common values may include:

```text
machine
virtual_machine
network_device
storage_device
service
application
cloud_resource
equipment
unknown
```

The vocabulary should remain intentionally small.

Add new common kinds only when real integrations need them.

---

# Kind: `machine`

Represents a physical computing system.

Examples include:

```text
desktop
laptop
server
workstation
single-board computer
```

More specific information can live in attributes where required.

---

# Kind: `virtual_machine`

Represents a virtualised computer instance.

The host relationship belongs in the Relationship contract rather than being embedded deeply into the Asset.

For example:

```text
asset:vm-42
    |
    | hosted_by
    v
asset:hypervisor-1
```

---

# Kind: `network_device`

Represents equipment whose primary role is networking.

Possible examples include:

```text
switch
router
access point
firewall
gateway
```

The common `kind` remains:

```text
network_device
```

More specific classifications may live in attributes until a shared vocabulary becomes necessary.

---

# Kind: `storage_device`

Represents a physical or logical device primarily associated with storage.

Possible examples include:

```text
HDD
SSD
USB disk
storage array
NAS appliance
```

A Storage Location is related to an Asset but is not identical to the Asset.

For example:

```text
Asset:
NAS appliance

Storage Location:
Shared filesystem exported by that NAS
```

That distinction will be defined in `STORAGE_LOCATION.md`.

---

# Kind: `service`

Represents a logical service that deserves independent identity within the VNA estate.

Examples might include:

```text
web service
database service
directory service
DNS service
```

Not every operating-system process needs to become an Asset.

Create service Assets where stable cross-observation identity is useful.

---

# Kind: `application`

Represents an application as a meaningful estate object.

This may be useful where Findings or relationships concern the application independently of the machine currently hosting it.

---

# Kind: `cloud_resource`

Represents a provider-hosted resource which does not fit cleanly into the more specific common kinds.

More detailed cloud-resource modelling may evolve later.

---

# Kind: `equipment`

Represents equipment useful to VNA training, vendor worlds, or infrastructure modelling which does not fit another current common kind.

This may be especially useful for vendor-supplied physical equipment.

---

# Kind: `unknown`

Used when an Asset is known to exist but its class cannot yet be established confidently.

Unknown is better than guessing.

---

# Producer-Specific Asset Kinds

Where a producer needs a specialist kind that is not universally defined, it SHOULD namespace it.

Examples:

```text
sysmedic.recovery_target
charkive.source_device
vendor.example.special_appliance
```

Common consumers may preserve these without understanding every specialist semantic.

---

# Field: `name`

## Type

```text
string
```

## Required

Yes.

## Purpose

Provides the current preferred human-readable name for the Asset.

Example:

```json
{
  "name": "EDDIE"
}
```

The name is presentation information.

It is not identity.

Changing `name` SHOULD NOT automatically change `id`.

---

# Names Can Change

Example:

```text
Asset ID:
asset:123

Name:
OLD-SERVER
```

later becomes:

```text
Asset ID:
asset:123

Name:
ARCHIVE-SERVER
```

The Asset remains:

```text
asset:123
```

The new name replaces the preferred display value.

Historical names may remain as aliases where useful.

---

# Field: `description`

## Type

```text
string
```

## Required

No.

## Purpose

Provides a short human-readable description where one is genuinely useful.

Example:

```json
{
  "description": "Home Ubuntu development and storage machine."
}
```

Descriptions SHOULD NOT become a dumping ground for telemetry or operational state.

Prefer structured Observations for changing technical information.

---

# Field: `aliases`

## Type

```text
array of objects
```

## Required

No.

## Purpose

Records alternative identifiers or names associated with the Asset.

Example:

```json
{
  "aliases": [
    {
      "type": "hostname",
      "value": "eddie"
    },
    {
      "type": "dns_name",
      "value": "eddie.example.local"
    }
  ]
}
```

Every alias MUST contain:

```text
type
value
```

---

# Common Alias Types

Possible initial alias types include:

```text
hostname
dns_name
serial_number
inventory_id
vendor_id
user_label
```

Network addresses SHOULD generally be observations or relationships rather than treated as eternal identity.

---

# MAC Addresses

A MAC address may be useful identity evidence.

However, it is not universally permanent.

Therefore the Asset contract should not require MAC address as identity.

A producer MAY retain a MAC address as:

```text
alias
attribute
observation
```

depending on context.

Do not assume:

```text
same MAC
=
same Asset forever
```

without appropriate evidence.

---

# IP Addresses

IP addresses SHOULD NOT be treated as stable Asset identity.

Prefer an Observation describing current addressing:

```text
Asset:
asset:123

Observation:
network.address

Value:
192.0.2.10
```

rather than defining:

```text
Asset ID:
asset:192.0.2.10
```

Dynamic addressing makes the latter fragile.

---

# Field: `source`

## Type

```text
object
```

## Required

Yes.

## Purpose

Describes the origin of the Asset record.

The Asset source is conceptually related to, but not identical with, Finding and Observation source.

## Required Source Fields

```text
kind
```

## Optional Source Fields

```text
producer
id
captured_at
```

---

# Allowed v0.1 Source Kinds

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

# Source Kind: `live`

Represents an Asset discovered or maintained from current real infrastructure.

Example:

```json
{
  "source": {
    "kind": "live",
    "producer": "vna"
  }
}
```

---

# Source Kind: `snapshot`

Represents an Asset inferred or created from captured real-world evidence.

Example:

```json
{
  "source": {
    "kind": "snapshot",
    "producer": "sysmedic",
    "id": "sysmedic:snapshot:SM-20261030-0733",
    "captured_at": "2026-10-30T06:45:00Z"
  }
}
```

A snapshot Asset does not imply current connectivity to the original system.

---

# Source Kind: `historical`

Represents an Asset known primarily through historical evidence.

Possible examples include:

```text
old machine record
historical storage device
archaeological source device
retired infrastructure
```

---

# Source Kind: `synthetic`

Represents an artificial Asset created for:

```text
tutorial
simulation
testing
training
procedural world
```

Example:

```json
{
  "source": {
    "kind": "synthetic",
    "producer": "vna",
    "id": "tutorial:unplugged-cable"
  }
}
```

Synthetic Assets MUST remain distinguishable from real Assets.

---

# Source Kind: `vendor`

Represents an Asset supplied through a vendor model, training package, or equipment definition.

This does not necessarily mean the Asset corresponds to a live physical device.

Vendor scenarios may be synthetic while still using vendor-defined Assets.

Further provenance belongs in attributes or future contracts.

---

# Source Kind: `user`

Represents an Asset explicitly created or identified by a user.

Example:

```text
"This old Kodak camera."
```

This may become important for Charkive source-device archaeology.

---

# Source Kind: `generalised`

Represents an Asset deliberately derived from another Asset with identifying detail removed or replaced.

Example:

```json
{
  "source": {
    "kind": "generalised",
    "producer": "vna",
    "id": "asset:eddie"
  }
}
```

The client-facing Asset might then be:

```text
SERVER-104
```

rather than:

```text
EDDIE
```

---

# Field: `platform`

## Type

```text
object
```

## Required

No.

## Purpose

Provides relatively stable platform classification where known.

A minimal example:

```json
{
  "platform": {
    "family": "linux"
  }
}
```

Possible family values might include:

```text
windows
linux
macos
network
embedded
unknown
```

Detailed operating-system versions SHOULD normally be observations if they are expected to change.

---

# Platform Detail

A producer MAY include additional platform information where useful.

Example:

```json
{
  "platform": {
    "family": "linux",
    "name": "Ubuntu"
  }
}
```

Avoid treating transient patch levels, kernel versions, or build numbers as permanent Asset identity.

Those are better represented as Observations.

---

# Asset Identity Survives OS Changes

If a machine is upgraded from one operating-system version to another, it may remain the same Asset.

Likewise, reinstalling an operating system does not necessarily prove either:

```text
same logical Asset
```

or:

```text
new logical Asset
```

Identity decisions may require contextual evidence.

Asset v0.1 deliberately does not attempt to solve every reincarnation problem.

---

# Field: `status`

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
retired
unknown
```

---

# Status: `active`

The Asset is currently considered part of the relevant estate or world.

This does NOT necessarily mean:

```text
powered on
reachable
healthy
online
```

Those are operational observations.

---

# Status: `inactive`

The Asset remains known but is not currently considered active within the operational estate.

This may be appropriate for temporarily unused systems.

---

# Status: `retired`

The Asset is retained for historical or referential purposes but is no longer expected to participate in the active estate.

Historical Observations and Findings may still reference it.

---

# Status: `unknown`

The Asset's current estate status cannot be established confidently.

Unknown is preferable to fabrication.

---

# Asset Status Is Not Health

This distinction is critical.

```text
Asset status:
active
```

does not mean:

```text
Asset health:
healthy
```

A currently active server may have critical Findings.

Likewise:

```text
Asset status:
retired
```

does not mean:

```text
Asset health:
failed
```

The Asset contract describes existence within the estate.

Observations and Findings describe operational condition.

---

# Do Not Put `healthy` in Asset Status

Avoid:

```text
healthy
warning
critical
offline
```

as Asset lifecycle statuses.

Those are dynamic operational states better represented through Observations and Findings.

Instead:

```text
Asset:
active

Finding:
hardware.disk.health
severity:
critical
```

This keeps identity separate from health.

---

# Field: `labels`

## Type

```text
array of strings
```

## Required

No.

## Purpose

Allows lightweight organisation without creating semantic relationships.

Example:

```json
{
  "labels": [
    "homelab",
    "development"
  ]
}
```

Labels are organisational hints.

They MUST NOT be treated as authority, security classification, or topology.

---

# Field: `attributes`

## Type

```text
object
```

## Required

No.

## Purpose

Provides an extension area for producer-specific relatively stable metadata.

Example:

```json
{
  "attributes": {
    "sysmedic": {
      "collection_id": "SM-20261030-0733"
    }
  }
}
```

or:

```json
{
  "attributes": {
    "charkive": {
      "source_device_type": "camera"
    }
  }
}
```

Common consumers SHOULD NOT rely upon producer-specific attributes for core Asset behaviour.

If multiple producers repeatedly require the same concept, it may be promoted into a future common Asset schema.

---

# What Does Not Belong in Asset?

Avoid turning Asset into a kitchen-sink machine record.

Data such as:

```text
current CPU utilisation
current temperature
current IP address
current disk capacity
current service state
current network connectivity
current error count
current health
current Findings
```

generally belongs in:

```text
Observations
Findings
Relationships
Storage Locations
```

rather than the core Asset record.

---

# Asset Versus Observation

Asset:

```text
This is machine asset:eddie.
```

Observation:

```text
asset:eddie currently reports kernel version X.
```

Asset:

```text
This is network device asset:switch-1.
```

Observation:

```text
asset:switch-1 currently reports temperature Y.
```

Identity is stable.

Observation is temporal.

---

# Asset Versus Finding

Asset:

```text
asset:server-42
```

Finding:

```text
Storage capacity critically constrained on asset:server-42.
```

The Asset exists independently of whether any Findings are active.

---

# Asset Versus Relationship

Asset:

```text
asset:web-server
```

Asset:

```text
asset:database-server
```

Relationship:

```text
asset:web-server
depends_on
asset:database-server
```

The relationship does not belong embedded permanently inside either Asset.

This keeps topology independently evolvable.

---

# Asset Versus Storage Location

Asset:

```text
asset:eddie
```

may have:

```text
storage:eddie:root
storage:eddie:data
storage:eddie:usb-archive
```

The machine is the Asset.

Those places where data resides are Storage Locations.

This distinction becomes important for FileCity.

---

# Asset Versus FileCity World Object

Asset:

```text
asset:eddie
```

FileCity might represent it as:

```text
Eddie District
```

or, after generalisation:

```text
North Computing District
```

The district is a representation.

The Asset is the underlying identity.

---

# Minimal Valid Asset

```json
{
  "schema": "vna.asset/0.1",
  "id": "asset:001",
  "kind": "machine",
  "name": "Example Machine",
  "source": {
    "kind": "live",
    "producer": "vna"
  },
  "status": "active"
}
```

---

# Live Linux Machine Example

```json
{
  "schema": "vna.asset/0.1",
  "id": "asset:eddie",
  "kind": "machine",
  "name": "EDDIE",
  "source": {
    "kind": "live",
    "producer": "vna"
  },
  "aliases": [
    {
      "type": "hostname",
      "value": "eddie"
    }
  ],
  "platform": {
    "family": "linux",
    "name": "Ubuntu"
  },
  "status": "active",
  "labels": [
    "homelab"
  ],
  "attributes": {}
}
```

---

# SysMedic Snapshot Asset Example

A SysMedic collection may identify a machine which is not currently connected to VNA.

```json
{
  "schema": "vna.asset/0.1",
  "id": "asset:sysmedic:SM-20261030-0733",
  "kind": "machine",
  "name": "Captured Windows Machine",
  "source": {
    "kind": "snapshot",
    "producer": "sysmedic",
    "id": "sysmedic:snapshot:SM-20261030-0733",
    "captured_at": "2026-10-30T06:45:00Z"
  },
  "platform": {
    "family": "windows"
  },
  "status": "unknown",
  "attributes": {
    "sysmedic": {
      "collection_id": "SM-20261030-0733"
    }
  }
}
```

The Asset can support:

```text
Observations
Findings
reports
VNA missions
```

without implying:

```text
the machine is currently online
VNA can connect to it
its captured state is still current
```

---

# Snapshot Asset Reconciliation

A SysMedic Asset may later be recognised as a machine already known to VNA.

For example:

```text
Snapshot Asset:
asset:sysmedic:123

Live VNA Asset:
asset:eddie
```

If sufficient identity evidence establishes that these represent the same underlying machine, VNA may reconcile them.

Asset v0.1 does not define the complete reconciliation algorithm.

Important principle:

> **Do not merge because the names look similar.**

Identity reconciliation should preserve provenance and be auditable.

---

# Synthetic Tutorial Asset Example

```json
{
  "schema": "vna.asset/0.1",
  "id": "asset:tutorial-office-b",
  "kind": "machine",
  "name": "Office B Computer",
  "source": {
    "kind": "synthetic",
    "producer": "vna",
    "id": "tutorial:unplugged-cable"
  },
  "platform": {
    "family": "unknown"
  },
  "status": "active"
}
```

Every Observation and Finding related to the tutorial can reference:

```text
asset:tutorial-office-b
```

without any risk of pretending it is production infrastructure.

---

# Vendor Equipment Example

```json
{
  "schema": "vna.asset/0.1",
  "id": "asset:vendor:example-switch-model",
  "kind": "equipment",
  "name": "Example Training Switch",
  "source": {
    "kind": "vendor",
    "producer": "vendor:example",
    "id": "vendor:example:equipment:switch"
  },
  "status": "active",
  "attributes": {
    "vendor": {
      "manufacturer": "Example",
      "model": "Training Switch"
    }
  }
}
```

A vendor-defined equipment Asset may represent a training model rather than a unique physical device.

A later version may need to distinguish:

```text
equipment model
```

from:

```text
equipment instance
```

if real integrations require it.

Do not over-design that distinction in v0.1.

---

# Charkive Source-Device Example

Charkive Data Archaeology may identify a historical source device.

For example:

```json
{
  "schema": "vna.asset/0.1",
  "id": "asset:historical:kodak-camera",
  "kind": "equipment",
  "name": "Old Kodak Camera",
  "source": {
    "kind": "user",
    "producer": "charkive"
  },
  "status": "retired",
  "attributes": {
    "charkive": {
      "source_device_type": "digital_camera",
      "known_clock_issue": true
    }
  }
}
```

This Asset can then participate in relationships with historical collections.

For example:

```text
asset:historical:kodak-camera
    |
    | source_of
    v
collection:photos-kodak
```

The relationship belongs in `RELATIONSHIP.md`.

---

# Generalised Asset Example

Real operational Asset:

```json
{
  "schema": "vna.asset/0.1",
  "id": "asset:eddie",
  "kind": "machine",
  "name": "EDDIE",
  "source": {
    "kind": "live",
    "producer": "vna"
  },
  "status": "active"
}
```

Generalised client-facing Asset:

```json
{
  "schema": "vna.asset/0.1",
  "id": "asset:generalised:server-104",
  "kind": "machine",
  "name": "SERVER-104",
  "source": {
    "kind": "generalised",
    "producer": "vna",
    "id": "asset:eddie"
  },
  "status": "active"
}
```

The client need not receive the reverse mapping unless authorised.

---

# Generalisation Must Preserve Technical Meaning

Generalisation may change:

```text
name
identifier presented to client
precise location
aliases
sensitive labels
topology detail
```

It SHOULD preserve relevant technical meaning such as:

```text
machine
network_device
storage_device
service
```

unless the intended generalisation explicitly requires stronger abstraction.

The generalised Asset should remain useful for the client's intended task.

---

# Generalisation Is Not Renaming the Real Asset

The operational Asset remains:

```text
asset:eddie
```

The generalised representation might be:

```text
asset:generalised:server-104
```

Do not overwrite the real Asset's name with the pseudonym merely because a game client needs privacy.

Generalisation creates a derived representation.

It does not rewrite reality.

---

# Asset Relationships

Topology belongs in the Relationship contract.

Possible future relationships include:

```text
hosted_by
connected_to
depends_on
contains
attached_to
stored_on
mounted_from
source_of
managed_by
observed_by
```

The Asset contract should not duplicate this topology.

---

# Why Relationships Are Separate

Suppose:

```text
asset:web
depends_on
asset:database
```

Later the application moves to another database.

If the relationship is a separate object:

```text
Asset identities remain stable.

Relationship changes.
```

That is cleaner than rewriting large nested Asset structures.

---

# Asset and FileCity

FileCity may consume VNA Assets as infrastructure context.

Example:

```text
Asset:
asset:eddie

        |
        v

FileCity:
Eddie District
```

or:

```text
Asset:
asset:nas-1

        |
        v

FileCity:
Archive District
```

FileCity retains its own World Object identities.

The Asset ID provides the bridge back to VNA reality.

---

# Asset and Charkive

Charkive Findings may reference an Asset when the storage being analysed belongs to a known machine.

Example:

```text
Asset:
asset:eddie

Storage Location:
storage:eddie:data

Charkive Finding:
storage.disposable_candidate
```

Charkive does not need to become the authoritative inventory of `asset:eddie`.

It simply references the common identity.

---

# Asset and SysMedic

SysMedic can produce or contribute to an Asset identity from a snapshot.

Its collection may provide observations such as:

```text
hostname
platform
hardware inventory
serial information
storage devices
```

These observations may help VNA identify or reconcile the Asset.

SysMedic evidence should remain distinguishable from VNA's identity decision.

---

# Asset and T4

T4 can explain observations and Findings associated with an Asset.

For example:

```text
T4Tots

"This computer isn't answering."


T4Teens

"This device isn't responding to the network check."


T4Techies

Asset:
asset:eddie

Finding:
network.response.unavailable
```

The explanation changes.

The Asset identity does not.

---

# Asset and VNA Gameplay

VNA may turn an Asset into a navigable game-world destination.

For example:

```text
asset:eddie
    |
    v
OSI Tower
```

or:

```text
asset:switch-1
    |
    v
Network Building
```

The representation is gameplay.

The Asset remains the common identity beneath it.

---

# Asset and BoreDOOM!

BoreDOOM! may eventually consume generalised VNA Assets through optional integrations.

It MUST NOT need Asset records for its standalone domestic game loop.

The VNA integration boundary remains optional.

---

# Asset Lifecycle

Asset v0.1 deliberately keeps lifecycle simple:

```text
active
inactive
retired
unknown
```

It does not attempt to model:

```text
commissioning
maintenance
repair
degraded
failed
offline
powered_down
destroyed
lost
```

Most of those are operational conditions better represented through Observations, Findings, and Events.

---

# Asset Events

Changes to Asset identity or lifecycle may eventually produce Events.

Potential examples include:

```text
vna.asset.created
vna.asset.renamed
vna.asset.activated
vna.asset.inactivated
vna.asset.retired
vna.asset.reconciled
```

These belong to `EVENT.md`.

The Asset represents current identity.

Events represent what happened to it.

---

# Historical Assets

Retired Assets SHOULD remain addressable where historical evidence references them.

For example:

```text
2018 Observation
      |
      v
asset:old-laptop

2026 Asset status:
retired
```

The Asset remaining in the model allows historical Findings and snapshots to remain comprehensible.

Retirement is not deletion.

---

# Deletion

Asset v0.1 does not define hard deletion semantics.

In general, an Asset referenced by historical evidence SHOULD NOT simply disappear from the model.

Prefer:

```text
status:
retired
```

where historical continuity matters.

Physical data-retention policies remain separate concerns.

---

# Asset Trust

Different sources may provide different claims about the same Asset.

For example:

```text
SysMedic snapshot:
hostname = DESKTOP-123

Live VNA:
hostname = WORKSHOP-PC

User:
"That's the same old workshop laptop."
```

These are pieces of identity evidence.

VNA should not pretend that reconciliation is certain until it has sufficient basis.

A future identity-resolution model may explicitly score or record these claims.

Asset v0.1 does not invent that machinery prematurely.

---

# Asset Observation Example

Asset:

```json
{
  "schema": "vna.asset/0.1",
  "id": "asset:eddie",
  "kind": "machine",
  "name": "EDDIE",
  "source": {
    "kind": "live",
    "producer": "vna"
  },
  "status": "active"
}
```

Observation:

```json
{
  "schema": "vna.observation/0.1",
  "id": "observation:eddie-rtt-001",
  "producer": {
    "id": "t4t",
    "type": "collector"
  },
  "source": {
    "kind": "live"
  },
  "observed_at": "2026-10-30T07:00:00Z",
  "type": "network.icmp.rtt",
  "subject": {
    "type": "asset",
    "asset_id": "asset:eddie"
  },
  "data": {
    "value": 18,
    "unit": "ms"
  },
  "production_changed": false
}
```

Finding:

```json
{
  "schema": "vna.finding/0.1",
  "id": "finding:eddie-network-001",
  "producer": {
    "id": "t4t",
    "type": "application"
  },
  "source": {
    "kind": "live"
  },
  "observed_at": "2026-10-30T07:00:00Z",
  "category": "network.response.normal",
  "severity": "info",
  "state": "active",
  "subject": {
    "type": "asset",
    "asset_id": "asset:eddie"
  },
  "summary": "Network response is within the expected range.",
  "production_changed": false
}
```

The same Asset identity ties them together.

---

# Security Boundary

Asset records are data.

They do not confer authority.

An Asset record such as:

```json
{
  "id": "asset:eddie",
  "name": "EDDIE"
}
```

does NOT imply that a consumer may:

```text
connect to EDDIE
SSH to EDDIE
modify EDDIE
restart EDDIE
read EDDIE's files
change its services
```

Authority belongs to separate authentication, policy, and action layers.

---

# Untrusted Input

Asset fields MUST be treated as untrusted input at integration boundaries.

This includes:

```text
name
description
aliases
labels
attributes
source identifiers
```

Consumers MUST NOT interpolate Asset content directly into:

```text
shell commands
SQL
HTML
filesystem operations
privileged API calls
game scripting
```

without appropriate validation and escaping.

---

# Privacy

Assets may expose sensitive information such as:

```text
hostnames
serial numbers
inventory identifiers
device roles
user labels
platform information
infrastructure structure
```

Consumers should reveal only information appropriate to the context.

Generalisation exists specifically to reduce unnecessary disclosure.

---

# Transport Independence

Asset v0.1 defines an object.

It does not define how Assets move between systems.

Possible transports include:

```text
JSON files
HTTP APIs
snapshot packages
local IPC
message queues
databases
test fixtures
```

The Asset meaning should remain consistent regardless of transport.

---

# Validation Rules

A valid Asset v0.1 MUST satisfy:

1. `schema` equals `vna.asset/0.1`.
2. `id` is a non-empty string.
3. `kind` is a non-empty string.
4. `name` is a non-empty string.
5. `source` is an object.
6. `source.kind` is one of the recognised v0.1 source kinds.
7. `status` is one of the recognised v0.1 status values.
8. If supplied, `aliases` is an array.
9. Every alias contains a non-empty `type`.
10. Every alias contains a non-empty `value`.
11. If supplied, `platform` is an object.
12. If supplied, `description` is a string.
13. If supplied, `labels` is an array of strings.
14. If supplied, `attributes` is an object.

Consumers SHOULD reject structurally invalid Assets rather than silently repairing them.

---

# Compatibility Rules

## Producer

A v0.1 Asset producer:

- MUST provide all required fields
- MAY provide optional fields
- MAY add unknown fields
- SHOULD maintain stable Asset identity where practical
- MUST distinguish synthetic and real sources
- SHOULD avoid using transient addresses as Asset IDs
- SHOULD avoid aggressive identity merging
- SHOULD preserve provenance

## Consumer

A v0.1 consumer:

- MUST validate fields it depends upon
- SHOULD ignore unknown fields
- SHOULD preserve unknown fields when forwarding Assets
- MUST preserve Asset identity
- MUST preserve source kind
- MUST NOT equate Asset status with health
- MUST NOT treat Asset presence as operational authorisation
- MUST treat Asset content as untrusted input

---

# v0.1 Deliberate Omissions

Asset v0.1 intentionally does not fully define:

```text
identity reconciliation
asset revision numbers
ownership
RBAC
credentials
network addressing
location hierarchy
detailed hardware inventory
software inventory
asset health
asset telemetry
asset Findings
asset dependency topology
retention
deletion
multi-tenant identity
vendor model versus instance semantics
```

Those concerns can be defined when actual implementations require them.

The objective of Asset v0.1 is much smaller:

> **Give the ecosystem a stable way to say which thing it is talking about.**

---

# First Implementation Target

T4T/MARVIN can create Assets for discovered network devices.

For example:

```text
MARVIN
   |
   v
DISCOVER DEVICE
   |
   v
vna.asset/0.1
   |
   v
OBSERVATIONS
   |
   v
FINDINGS
```

Initially:

```json
{
  "schema": "vna.asset/0.1",
  "id": "asset:test-device",
  "kind": "unknown",
  "name": "Test Device",
  "source": {
    "kind": "live",
    "producer": "t4t"
  },
  "status": "active"
}
```

Then MARVIN observations can reference:

```text
asset:test-device
```

rather than duplicating identity fields into every measurement.

---

# Second Implementation Target

SysMedic imports a snapshot.

The snapshot establishes:

```text
captured machine Asset
```

and then associates:

```text
hardware observations
operating-system observations
storage observations
network observations
Findings
```

with that stable Asset identity.

---

# Third Implementation Target

Charkive associates a known storage location with its underlying Asset where that relationship is available.

```text
ASSET
asset:eddie
    |
    v
STORAGE LOCATION
storage:eddie:data
    |
    v
CHARKIVE OBSERVATIONS
    |
    v
CHARKIVE FINDINGS
```

This gives FileCity the bridge between infrastructure and data geography.

---

# Fourth Implementation Target

VNA creates synthetic tutorial Assets.

```text
asset:tutorial-office-a
asset:tutorial-office-b
```

The same Asset model now supports:

```text
real devices
captured machines
synthetic machines
generalised machines
vendor equipment
```

At that point the contract has demonstrated its purpose.

---

# North Star

An Asset should let the ecosystem answer:

```text
WHAT thing are we talking about?

WHAT stable identity does it have?

WHAT kind of thing is it?

WHAT should humans currently call it?

WHERE did this Asset record come from?

IS it real, captured, historical, synthetic, vendor-defined, user-defined, or generalised?

IS it still part of the active estate?

WHAT other names has it been known by?

WHAT relatively stable platform information do we know?

CAN observations and findings refer to it without repeating its identity?
```

It should **not** pretend to answer:

```text
Is it healthy?

Is it online?

What is wrong with it?

What should we do to it?

Are we authorised to change it?
```

Those belong elsewhere.

The guiding rule is:

> **Know what the thing is before describing what happened to it.**
