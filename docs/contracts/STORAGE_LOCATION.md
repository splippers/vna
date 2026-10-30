# VNA Storage Location Schema v0.1

> **An Asset is the thing. A Storage Location is somewhere the data lives.**

## Purpose

A **Storage Location** is the common VNA ecosystem representation of a logical place where data can reside.

Examples include:

```text
local filesystem
mounted filesystem
disk volume
USB filesystem
NAS share
SMB share
NFS export
GlusterFS volume
cloud account
rclone remote
object-storage bucket
archive location
```

The Storage Location contract exists because:

```text
STORAGE DEVICE
```

and:

```text
PLACE WHERE DATA LIVES
```

are not necessarily the same thing.

For example:

```text
NAS appliance
    |
    v
ASSET

NAS filesystem export
    |
    v
STORAGE LOCATION
```

or:

```text
USB SSD
    |
    v
ASSET

Filesystem mounted from that SSD
    |
    v
STORAGE LOCATION
```

or:

```text
Cloud provider account
    |
    v
ASSET / ACCOUNT CONTEXT

Remote storage exposed through rclone
    |
    v
STORAGE LOCATION
```

This distinction gives Charkive, FileCity, SysMedic, and VNA a common way to talk about storage without assuming that all storage is local, physical, cloud-based, or mounted in the same way.

---

# Core Principle

> **Model where data logically lives, not merely the hardware underneath it.**

A physical storage device may expose:

```text
one filesystem
many filesystems
no currently mounted filesystem
```

A logical storage location may be backed by:

```text
one physical device
many physical devices
a network appliance
a cluster
a cloud provider
something the consumer cannot see
```

Storage Location therefore describes the usable logical storage boundary.

It does not attempt to reproduce the complete hardware implementation beneath it.

---

# Ecosystem Position

The initial model becomes:

```text
                         ASSET
                           |
                           v
                    STORAGE LOCATION
                           |
                +----------+----------+
                |                     |
                v                     v
          OBSERVATIONS             FINDINGS
                |                     |
                +----------+----------+
                           |
                           v
                       FILECITY
```

For example:

```text
ASSET

asset:eddie
    |
    v

STORAGE LOCATION

storage:eddie:data
    |
    v

CHARKIVE FINDING

Projects/cache
    |
    v

FILECITY

Eddie District
Projects Tower
Cache Floor
```

---

# Canonical File Location

Save this contract at:

```text
vna/docs/contracts/STORAGE_LOCATION.md
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

Every Storage Location MUST declare:

```json
{
  "schema": "vna.storage_location/0.1"
}
```

The canonical identifier is:

```text
vna.storage_location/0.1
```

Consumers MUST NOT assume future versions are structurally identical.

Consumers SHOULD ignore unknown fields unless those fields are explicitly required by a schema version they implement.

---

# Canonical Example

```json
{
  "schema": "vna.storage_location/0.1",
  "id": "storage:eddie:data",
  "name": "Eddie Data",
  "kind": "filesystem",
  "source": {
    "kind": "live",
    "producer": "vna"
  },
  "status": "active",
  "asset_id": "asset:eddie",
  "access": {
    "mode": "mounted",
    "reference": "/srv/data"
  },
  "filesystem": {
    "type": "ext4"
  },
  "attributes": {}
}
```

This says:

> VNA knows about a logical filesystem called Eddie Data associated with the Asset `asset:eddie`.

It does NOT say:

```text
the filesystem is healthy
the filesystem has free space
the filesystem is currently reachable
the underlying disk is healthy
the mount succeeded
the user is authorised to alter it
```

Those require Observations, Findings, Relationships, or operational policy.

---

# Required Fields

Storage Location v0.1 requires:

```text
schema
id
name
kind
source
status
```

Optional fields include:

```text
asset_id
access
filesystem
provider
aliases
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
vna.storage_location/0.1
```

## Purpose

Identifies the common Storage Location contract and its version.

---

# Field: `id`

## Type

```text
string
```

## Required

Yes.

## Purpose

Provides the stable identity of the logical Storage Location.

Example:

```json
{
  "id": "storage:eddie:data"
}
```

Consumers MUST treat the ID as opaque.

They MUST NOT infer:

```text
host
path
provider
filesystem
authority
location
```

from its internal structure.

---

# Stable Storage Identity

The Storage Location ID SHOULD survive ordinary changes in how the location is accessed.

For example:

```text
Yesterday

Mount:
 /mnt/data

Storage Location:
 storage:123
```

later:

```text
Today

Mount:
 /srv/data

Storage Location:
 storage:123
```

The mount point changed.

The logical storage did not necessarily change.

Therefore:

> **Mount path is not Storage Location identity.**

---

# Storage Location Identity Is Not a Path

Avoid treating:

```text
/mnt/data
```

as the permanent identity of a Storage Location.

Paths may change because of:

```text
remounting
operating-system changes
administrator preference
drive-letter changes
containerisation
network remapping
```

Likewise, Windows:

```text
D:\
```

is not sufficient persistent identity for a Storage Location.

Use a stable `id`.

Represent the current access path separately.

---

# Storage Location Identity Is Not a Cloud Remote Name

An rclone remote may be:

```text
jon@example.com:
```

today and renamed later.

That should not necessarily create a different logical Storage Location.

The remote name is an access alias or provider reference.

The Storage Location ID remains the stable ecosystem identity.

---

# Field: `name`

## Type

```text
string
```

## Required

Yes.

## Purpose

Provides the preferred human-readable name.

Example:

```json
{
  "name": "Eddie Data"
}
```

The name is presentation information.

It is not identity.

Changing the name SHOULD NOT automatically change `id`.

---

# Field: `kind`

## Type

```text
string
```

## Required

Yes.

## Purpose

Identifies the broad type of logical storage represented.

Initial common values may include:

```text
filesystem
share
cloud
object_store
archive
volume
unknown
```

This vocabulary should remain intentionally small.

---

# Kind: `filesystem`

Represents a filesystem accessible as a logical hierarchy of paths.

Examples may include:

```text
local ext4 filesystem
NTFS filesystem
APFS filesystem
mounted GlusterFS filesystem
filesystem on removable media
```

The backing implementation may vary.

---

# Kind: `share`

Represents network-accessible shared storage.

Examples might include:

```text
SMB share
NFS export
other network file share
```

A share may also expose filesystem-like paths.

`kind` describes the logical storage boundary rather than the transport protocol alone.

---

# Kind: `cloud`

Represents logical cloud storage exposed through a provider or abstraction such as rclone.

Examples might include:

```text
Google Drive
OneDrive
Dropbox
other remote file-style provider
```

Provider detail can be stored separately.

---

# Kind: `object_store`

Represents storage whose primary abstraction is objects rather than a conventional filesystem hierarchy.

Examples might include:

```text
S3-style bucket
compatible object storage
```

FileCity may later choose how such storage maps into its geography.

---

# Kind: `archive`

Represents storage whose role is explicitly archival or historical.

This does not imply that its contents are immutable.

It describes the logical role of the location.

---

# Kind: `volume`

Represents a logical storage volume where a more specific common kind is not appropriate.

Use this cautiously.

Prefer a more meaningful kind where one genuinely exists.

---

# Kind: `unknown`

Represents a known storage boundary whose type cannot yet be established confidently.

Unknown is better than fabrication.

---

# Producer-Specific Kinds

Producer-specific kinds SHOULD be namespaced.

Examples:

```text
charkive.sanitorium
sysmedic.exchange
sysmedic.vault
filecity.remote
vendor.example.archive
```

Use producer-specific kinds only where the meaning is not genuinely common.

---

# Field: `source`

## Type

```text
object
```

## Required

Yes.

## Purpose

Describes the origin of the Storage Location record.

## Allowed v0.1 Source Kinds

```text
live
snapshot
historical
synthetic
vendor
user
generalised
```

These align with the wider VNA contracts.

---

# Source Kind: `live`

The Storage Location was discovered or defined against current real infrastructure.

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

The Storage Location is known from captured evidence.

Example:

```json
{
  "source": {
    "kind": "snapshot",
    "producer": "sysmedic",
    "id": "sysmedic:snapshot:SM-20261030-0733"
  }
}
```

A snapshot Storage Location does not imply the original storage is still mounted, connected, or unchanged.

---

# Source Kind: `historical`

The Storage Location is known mainly through historical evidence.

This may be useful for:

```text
old disks
retired filesystems
Charkive archaeology
historical FileCity worlds
```

---

# Source Kind: `synthetic`

The Storage Location belongs to a simulation, tutorial, test fixture, or generated environment.

Synthetic storage MUST remain distinguishable from real storage.

---

# Source Kind: `vendor`

The Storage Location comes from a vendor-provided model or scenario.

---

# Source Kind: `user`

The Storage Location was deliberately defined by a user.

This can be useful where automatic discovery cannot establish the logical storage boundary reliably.

---

# Source Kind: `generalised`

The Storage Location is a privacy-preserving representation derived from another Storage Location.

Example:

```text
REAL

storage:customer-finance-archive


GENERALISED

storage:archive-17
```

The reverse mapping remains within the authorised VNA layer.

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

The Storage Location is currently considered part of the relevant estate or world.

This does NOT mean:

```text
mounted
reachable
healthy
writable
online
```

Those are dynamic conditions.

---

# Status: `inactive`

The Storage Location remains known but is not currently considered active within the operational estate.

---

# Status: `retired`

The Storage Location is retained for historical reference but is no longer expected to participate in the active estate.

Historical Findings and Observations may still refer to it.

---

# Status: `unknown`

Current estate status cannot be established confidently.

---

# Storage Status Is Not Availability

This distinction matters.

```text
status:
active
```

means:

> This Storage Location belongs to the current estate.

It does not mean:

> I can access it right now.

Access availability belongs in an Observation.

For example:

```text
Observation:
storage.access.available

data:
false
```

---

# Storage Status Is Not Health

Avoid:

```text
healthy
warning
critical
full
offline
```

as Storage Location lifecycle states.

These belong in Observations and Findings.

For example:

```text
Storage Location:
active

Observation:
storage.capacity.used = 96%

Finding:
storage.capacity.warning
severity = critical
```

This keeps identity separate from condition.

---

# Field: `asset_id`

## Type

```text
string
```

## Required

No.

## Purpose

References the Asset most directly associated with the Storage Location where such a relationship is simple and useful.

Example:

```json
{
  "asset_id": "asset:eddie"
}
```

This is a convenience reference.

More complicated infrastructure relationships belong in `RELATIONSHIP.md`.

---

# Asset Association Is Optional

Not all Storage Locations map neatly to a single Asset.

For example:

```text
clustered filesystem
distributed object store
cloud account
shared storage array
```

A location backed by multiple Assets should use Relationships instead of forcing one misleading `asset_id`.

The optional convenience field exists for simple cases such as:

```text
machine
    |
    v
local filesystem
```

---

# Asset Is Not Storage Location

Example:

```text
ASSET

asset:usb-disk-1


STORAGE LOCATION

storage:usb-disk-1:photos
```

The Asset may describe the actual USB disk.

The Storage Location describes the filesystem or logical volume holding the photos.

The distinction matters because one physical Asset may contain several Storage Locations.

---

# One Asset, Many Storage Locations

Example:

```text
asset:eddie
   |
   +---- storage:eddie:root
   |
   +---- storage:eddie:data
   |
   `---- storage:eddie:archive
```

Each location can have:

```text
separate capacity
separate filesystem
separate paths
separate Findings
separate FileCity geography
```

while remaining associated with the same machine.

---

# Many Assets, One Storage Location

Distributed storage may reverse the pattern.

```text
asset:storage-node-1
        \
asset:storage-node-2 ----> storage:cluster:data
        /
asset:storage-node-3
```

This topology belongs in Relationships.

Do not duplicate or fake the Storage Location merely because several Assets back it.

---

# Field: `access`

## Type

```text
object
```

## Required

No.

## Purpose

Describes the current or configured method by which this system can address the Storage Location.

Example:

```json
{
  "access": {
    "mode": "mounted",
    "reference": "/srv/data"
  }
}
```

The access reference is not permanent identity.

---

# Access Mode: `mounted`

Represents storage currently or conventionally addressed through a mounted filesystem path.

Example:

```json
{
  "access": {
    "mode": "mounted",
    "reference": "/srv/data"
  }
}
```

---

# Access Mode: `drive_letter`

Represents a Windows drive-letter based access reference.

Example:

```json
{
  "access": {
    "mode": "drive_letter",
    "reference": "D:\\"
  }
}
```

The drive letter MUST NOT be treated as stable Storage Location identity.

---

# Access Mode: `unc`

Represents a UNC-style network path.

Example:

```json
{
  "access": {
    "mode": "unc",
    "reference": "\\\\server\\share"
  }
}
```

Credentials MUST NOT be embedded in the access reference.

---

# Access Mode: `rclone`

Represents an rclone-facing reference.

Example:

```json
{
  "access": {
    "mode": "rclone",
    "reference": "jon@example.com:"
  }
}
```

The rclone remote name is an access mechanism.

It is not the Storage Location ID.

---

# Access Mode: `provider`

Represents a provider-native logical reference where FileCity or another integration can address the location without a filesystem mount.

Example:

```json
{
  "access": {
    "mode": "provider",
    "reference": "provider-specific-reference"
  }
}
```

Sensitive credentials MUST NOT appear here.

---

# Access Mode: `snapshot`

Represents a Storage Location inside captured evidence rather than an independently accessible current storage system.

Example:

```json
{
  "access": {
    "mode": "snapshot",
    "reference": "raw/storage/volume-1"
  }
}
```

The reference is meaningful only inside the relevant snapshot/import context.

---

# Access Is Not Authorisation

This:

```json
{
  "access": {
    "mode": "mounted",
    "reference": "/srv/data"
  }
}
```

does not mean:

```text
the consumer may read it
the consumer may write it
the consumer may delete from it
the consumer may migrate from it
```

The access field identifies addressing.

It does not grant permission.

---

# Field: `filesystem`

## Type

```text
object
```

## Required

No.

## Purpose

Provides relatively stable filesystem information when the Storage Location is filesystem-like.

Example:

```json
{
  "filesystem": {
    "type": "ext4"
  }
}
```

Possible values might include:

```text
ext4
xfs
btrfs
ntfs
exfat
vfat
apfs
glusterfs
unknown
```

This vocabulary may expand as real integrations require it.

---

# Filesystem Details

Additional relatively stable filesystem detail MAY be supplied where useful.

Example:

```json
{
  "filesystem": {
    "type": "ext4",
    "label": "DATA",
    "uuid": "filesystem-uuid"
  }
}
```

Care should be taken with identifiers which may expose sensitive or platform-specific information.

---

# Filesystem Capacity Does Not Belong Here

Avoid storing dynamic capacity directly on the canonical Storage Location record.

For example, do not rely on:

```json
{
  "used_bytes": 900000000000
}
```

inside the identity object.

Capacity changes.

Prefer Observations:

```text
storage.capacity.total
storage.capacity.used
storage.capacity.available
```

This provides history and preserves the distinction between:

```text
WHAT the storage is
```

and:

```text
WHAT state it is currently in
```

---

# Capacity Observation Example

```json
{
  "schema": "vna.observation/0.1",
  "id": "observation:eddie-data-used-001",
  "producer": {
    "id": "vna",
    "type": "collector"
  },
  "source": {
    "kind": "live"
  },
  "observed_at": "2026-10-30T07:00:00Z",
  "type": "storage.capacity.used",
  "subject": {
    "type": "storage_location",
    "storage_location_id": "storage:eddie:data"
  },
  "data": {
    "value": 640000000000,
    "unit": "bytes"
  },
  "production_changed": false
}
```

The Storage Location remains:

```text
storage:eddie:data
```

while the measurement changes over time.

---

# Field: `provider`

## Type

```text
object
```

## Required

No.

## Purpose

Provides provider context for remote or specialised storage.

Example:

```json
{
  "provider": {
    "type": "google_drive"
  }
}
```

Possible provider types may include:

```text
google_drive
onedrive
dropbox
s3
s3_compatible
local
unknown
```

Do not treat this initial vocabulary as exhaustive.

---

# Provider Identity

A provider name describes technology.

It does not identify the user's account.

For example:

```text
provider:
google_drive
```

does not tell us:

```text
which account
who owns it
which credentials are used
```

Those are separate identity and authorisation concerns.

Credentials MUST NEVER be stored in the Storage Location contract.

---

# Field: `aliases`

## Type

```text
array of objects
```

## Required

No.

## Purpose

Records alternate human or system references associated with the Storage Location.

Example:

```json
{
  "aliases": [
    {
      "type": "mount_path",
      "value": "/srv/data"
    },
    {
      "type": "filesystem_label",
      "value": "DATA"
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

# Alias Types

Potential aliases include:

```text
mount_path
drive_letter
unc_path
rclone_remote
filesystem_label
provider_name
user_label
historical_name
```

Aliases are useful references.

They are not stable identity.

---

# Historical Aliases

A Storage Location that moves or is renamed may retain previous references.

Example:

```json
{
  "aliases": [
    {
      "type": "historical_name",
      "value": "Old Archive Share"
    }
  ]
}
```

This can become especially useful for:

```text
historical snapshots
Charkive archaeology
old reports
SysMedic evidence
```

---

# Field: `labels`

## Type

```text
array of strings
```

## Required

No.

## Purpose

Provides lightweight organisational tagging.

Example:

```json
{
  "labels": [
    "archive",
    "photos"
  ]
}
```

Labels MUST NOT be treated as authority, security policy, or topology.

---

# Field: `attributes`

## Type

```text
object
```

## Required

No.

## Purpose

Provides a controlled escape hatch for producer-specific relatively stable metadata.

Example:

```json
{
  "attributes": {
    "filecity": {
      "remote_type": "rclone"
    }
  }
}
```

or:

```json
{
  "attributes": {
    "sysmedic": {
      "captured_volume_id": "volume-1"
    }
  }
}
```

or:

```json
{
  "attributes": {
    "charkive": {
      "configured_root": true
    }
  }
}
```

Common consumers SHOULD NOT depend upon producer-specific attributes for core Storage Location semantics.

---

# Minimal Valid Storage Location

```json
{
  "schema": "vna.storage_location/0.1",
  "id": "storage:001",
  "name": "Example Storage",
  "kind": "filesystem",
  "source": {
    "kind": "live",
    "producer": "vna"
  },
  "status": "active"
}
```

---

# Local Filesystem Example

```json
{
  "schema": "vna.storage_location/0.1",
  "id": "storage:eddie:data",
  "name": "Eddie Data",
  "kind": "filesystem",
  "source": {
    "kind": "live",
    "producer": "vna"
  },
  "status": "active",
  "asset_id": "asset:eddie",
  "access": {
    "mode": "mounted",
    "reference": "/srv/data"
  },
  "filesystem": {
    "type": "ext4",
    "label": "DATA"
  },
  "labels": [
    "homelab"
  ],
  "attributes": {}
}
```

---

# Windows Volume Example

```json
{
  "schema": "vna.storage_location/0.1",
  "id": "storage:windows-machine:data",
  "name": "Data Volume",
  "kind": "filesystem",
  "source": {
    "kind": "live",
    "producer": "vna"
  },
  "status": "active",
  "asset_id": "asset:windows-machine",
  "access": {
    "mode": "drive_letter",
    "reference": "D:\\"
  },
  "filesystem": {
    "type": "ntfs",
    "label": "DATA"
  }
}
```

The drive letter may later change.

The Storage Location ID need not.

---

# Removable Disk Example

Physical disk:

```text
asset:usb-disk-1
```

Filesystem:

```json
{
  "schema": "vna.storage_location/0.1",
  "id": "storage:usb-disk-1:photos",
  "name": "Photo Archive",
  "kind": "filesystem",
  "source": {
    "kind": "live",
    "producer": "vna"
  },
  "status": "active",
  "asset_id": "asset:usb-disk-1",
  "access": {
    "mode": "mounted",
    "reference": "/media/user/PHOTOS"
  },
  "filesystem": {
    "type": "exfat",
    "label": "PHOTOS"
  }
}
```

If the removable disk is disconnected, the Storage Location may remain:

```text
status:
active
```

while an Observation reports:

```text
storage.access.available = false
```

if the storage still belongs to the estate but is temporarily absent.

---

# NAS Example

NAS appliance:

```text
asset:nas-1
```

Share:

```json
{
  "schema": "vna.storage_location/0.1",
  "id": "storage:nas-1:archive",
  "name": "Archive Share",
  "kind": "share",
  "source": {
    "kind": "live",
    "producer": "vna"
  },
  "status": "active",
  "asset_id": "asset:nas-1",
  "access": {
    "mode": "unc",
    "reference": "\\\\nas-1\\archive"
  },
  "filesystem": {
    "type": "unknown"
  }
}
```

The NAS appliance is not the share.

The share is not the underlying physical disk.

Those are three potentially distinct concepts.

---

# GlusterFS Example

A mounted GlusterFS export can be represented as one logical Storage Location even though several Assets may participate underneath it.

```json
{
  "schema": "vna.storage_location/0.1",
  "id": "storage:gluster:archive",
  "name": "Gluster Archive",
  "kind": "filesystem",
  "source": {
    "kind": "live",
    "producer": "charkive"
  },
  "status": "active",
  "access": {
    "mode": "mounted",
    "reference": "/mnt/archive"
  },
  "filesystem": {
    "type": "glusterfs"
  },
  "attributes": {
    "charkive": {
      "gluster_volume": "archive"
    }
  }
}
```

Relationships can later identify the participating storage nodes.

---

# rclone Cloud Example

```json
{
  "schema": "vna.storage_location/0.1",
  "id": "storage:cloud:jon-drive",
  "name": "Jon Drive",
  "kind": "cloud",
  "source": {
    "kind": "live",
    "producer": "filecity"
  },
  "status": "active",
  "access": {
    "mode": "rclone",
    "reference": "jon@example.com:"
  },
  "provider": {
    "type": "google_drive"
  },
  "attributes": {
    "filecity": {
      "account_scope": "linked"
    }
  }
}
```

The rclone remote name is not the canonical identity.

Credentials do not appear in the contract.

---

# Object Storage Example

```json
{
  "schema": "vna.storage_location/0.1",
  "id": "storage:object:archive-bucket",
  "name": "Archive Bucket",
  "kind": "object_store",
  "source": {
    "kind": "live",
    "producer": "filecity"
  },
  "status": "active",
  "provider": {
    "type": "s3_compatible"
  },
  "access": {
    "mode": "provider",
    "reference": "archive-bucket"
  }
}
```

A consumer MUST NOT assume conventional filesystem behaviour merely because FileCity chooses to render the object hierarchy as buildings.

---

# SysMedic Snapshot Storage Example

SysMedic may capture storage evidence from an offline machine.

```json
{
  "schema": "vna.storage_location/0.1",
  "id": "storage:snapshot:SM-20261030-0733:volume-1",
  "name": "Captured Windows Volume",
  "kind": "filesystem",
  "source": {
    "kind": "snapshot",
    "producer": "sysmedic",
    "id": "sysmedic:snapshot:SM-20261030-0733",
    "captured_at": "2026-10-30T06:45:00Z"
  },
  "status": "unknown",
  "asset_id": "asset:sysmedic:SM-20261030-0733",
  "access": {
    "mode": "snapshot",
    "reference": "raw/storage/volume-1"
  },
  "filesystem": {
    "type": "ntfs"
  },
  "attributes": {
    "sysmedic": {
      "collection_id": "SM-20261030-0733"
    }
  }
}
```

This Storage Location exists in the captured evidence.

It does not imply the original disk is currently attached or reachable.

---

# Charkive Root Example

A Charkive analysis root can refer directly to a VNA Storage Location.

```text
Charkive config root:
 /srv/data

        |
        v

VNA Storage Location:
 storage:eddie:data
```

Findings then use:

```json
{
  "subject": {
    "type": "storage_path",
    "storage_location_id": "storage:eddie:data",
    "path": "Projects/cache"
  }
}
```

The path is relative to the Storage Location.

This avoids baking `/srv/data` into every cross-system Finding.

---

# The SANitorium

The Charkive SANitorium may eventually be represented as its own logical Storage Location.

For example:

```json
{
  "schema": "vna.storage_location/0.1",
  "id": "storage:eddie:sanitorium",
  "name": "Charkive SANitorium",
  "kind": "charkive.sanitorium",
  "source": {
    "kind": "live",
    "producer": "charkive"
  },
  "status": "active",
  "asset_id": "asset:eddie",
  "attributes": {
    "charkive": {
      "purpose": "reversible_quarantine"
    }
  }
}
```

A quarantined item can then participate in relationships such as:

```text
originally_stored_at
quarantined_to
restorable_to
```

These belong in `RELATIONSHIP.md`.

---

# FileCity Consumption

FileCity is expected to be a major consumer of Storage Locations.

The basic mapping is:

```text
STORAGE LOCATION
       |
       v
FILECITY DISTRICT
       |
       v
PATH / COLLECTION
       |
       v
BUILDING / FLOOR
```

For example:

```text
storage:eddie:data
       |
       v
EDDIE DATA DISTRICT
```

or:

```text
storage:cloud:jon-drive
       |
       v
CLOUD DISTRICT
```

The FileCity world object is a representation.

The Storage Location remains the logical storage identity.

---

# FileCity Must Not Become the Storage Identity Authority

FileCity may assign:

```text
district IDs
building IDs
floor IDs
world coordinates
visual style
```

Those belong to FileCity.

It should retain a reference back to:

```text
vna.storage_location/0.1
```

rather than replacing the Storage Location identity with a building identity.

---

# Charkive Consumption

Charkive can associate scan roots with Storage Locations.

For example:

```text
Charkive scans:
 /srv/data

Resolved Storage Location:
 storage:eddie:data
```

Its Observations and Findings can then reference stable VNA identities rather than host-specific absolute paths.

This makes Charkive output more portable across:

```text
CLI
web UI
VNA
FileCity
T4
historical reports
```

---

# SysMedic Consumption

SysMedic can create Storage Location records while importing a snapshot.

For example:

```text
Captured disk
   |
   v
Captured filesystem
   |
   v
Storage Location
   |
   v
Observations / Findings
```

The source remains:

```text
snapshot
```

so downstream consumers cannot confuse captured historical state with live accessible storage.

---

# T4 Consumption

T4 may explain Findings or Observations associated with a Storage Location.

For example:

```text
T4Tots

"THIS STORAGE CUPBOARD IS NEARLY FULL!"


T4Teens

"This storage location has very little free space left."


T4Techies

Storage:
storage:eddie:data

Used:
[measurement]

Available:
[measurement]
```

T4 explains.

It does not own storage identity.

---

# VNA Gameplay Consumption

A Storage Location may become somewhere the player can visit.

For example:

```text
storage:eddie:data
   |
   v
FileCity district
   |
   v
VNA world
```

The player may see:

```text
roads
buildings
traffic
warnings
Charkive services
```

while the underlying reference remains:

```text
storage:eddie:data
```

The metaphor can evolve.

The identity remains stable.

---

# Generalised Storage Example

Operational Storage Location:

```json
{
  "schema": "vna.storage_location/0.1",
  "id": "storage:customer-finance-archive",
  "name": "Customer Finance Archive",
  "kind": "share",
  "source": {
    "kind": "live",
    "producer": "vna"
  },
  "status": "active"
}
```

Generalised client representation:

```json
{
  "schema": "vna.storage_location/0.1",
  "id": "storage:generalised:archive-17",
  "name": "Archive 17",
  "kind": "share",
  "source": {
    "kind": "generalised",
    "producer": "vna",
    "id": "storage:customer-finance-archive"
  },
  "status": "active"
}
```

An unauthorised game client receives only:

```text
Archive 17
```

The authorised VNA layer retains the mapping.

---

# Storage Paths

A Storage Location defines the logical root beneath which relative paths may be referenced.

Example:

```text
Storage Location:
storage:eddie:data

Access:
 /srv/data
```

A Finding refers to:

```text
Projects/cache/example.tmp
```

not necessarily:

```text
/srv/data/Projects/cache/example.tmp
```

This allows the external mount point to change without invalidating the logical Finding subject.

---

# Path Normalisation

Producing systems SHOULD emit paths in a consistent logical format relative to the Storage Location.

The recommended cross-platform convention for v0.1 is:

```text
forward slash separators
no leading slash
`.` for the logical root where required
```

Examples:

```text
Projects/cache/example.tmp
```

```text
Photos/2003/Kodak
```

```text
.
```

for the root itself.

---

# Path Normalisation Examples

Host path:

```text
/srv/data/Projects/cache/example.tmp
```

Storage Location:

```text
storage:eddie:data
```

Logical path:

```text
Projects/cache/example.tmp
```

Windows host path:

```text
D:\Projects\cache\example.tmp
```

Storage Location:

```text
storage:windows:data
```

Logical path:

```text
Projects/cache/example.tmp
```

The common logical path permits consumers to reason about storage without caring how the host mounted it.

---

# Path Safety

A logical storage path MUST NOT be treated automatically as a safe host filesystem path.

Consumers MUST validate any translation between:

```text
logical path
```

and:

```text
host filesystem path
```

before performing operations.

For example:

```text
../
```

and equivalent traversal must never allow an externally supplied logical path to escape the Storage Location root.

This becomes particularly important once Proposed Actions exist.

---

# Symbolic Paths

Cloud and object-storage providers may not behave exactly like conventional filesystems.

The `path` vocabulary can still provide logical hierarchy where the producer can define it meaningfully.

Consumers MUST NOT assume that path semantics imply:

```text
POSIX filesystem
Windows filesystem
atomic rename
permissions
hard links
symlinks
case sensitivity
```

unless the relevant provider contract explicitly establishes those semantics.

---

# Case Sensitivity

Storage Location v0.1 does not impose universal case-sensitivity rules.

These vary by implementation.

Consumers MUST NOT assume:

```text
Foo
```

and:

```text
foo
```

are either definitely equal or definitely different across all Storage Locations.

Producer/provider-specific semantics may be added later if cross-platform operations require them.

---

# Storage Location Relationships

Relationships may eventually express:

```text
hosted_by
backed_by
mounted_on
exported_by
mirrors
contains
replicates
depends_on
```

The Relationship contract should model this rather than expanding Storage Location into nested infrastructure topology.

---

# Storage Movement

Movement between Storage Locations should reference their stable IDs.

For example:

```text
Source:
storage:cloud:jon-drive

Destination:
storage:eddie:data
```

FileCity can then visualise:

```text
cloud
    |
    v
rocket / transport metaphor
    |
    v
ground district
```

while the underlying migration operation remains expressed in logical storage terms.

---

# Storage Location Is Not Migration Authority

Knowing:

```text
source storage ID
destination storage ID
```

does not imply that a client may copy, move, or delete anything.

A real operation still requires:

```text
Proposed Action
      |
      v
Validation
      |
      v
Authorisation
      |
      v
Responsible provider
      |
      v
Execution
      |
      v
Verification
```

---

# Historical Storage Locations

A retired Storage Location may remain relevant to:

```text
old snapshots
Charkive archaeology
migration history
audit
FileCity history
user memories
```

Therefore:

```text
retired
```

does not mean:

```text
delete its identity
```

Historical references need somewhere stable to point.

---

# Storage Location Events

Changes may eventually generate Events such as:

```text
vna.storage_location.created
vna.storage_location.renamed
vna.storage_location.activated
vna.storage_location.inactivated
vna.storage_location.retired
vna.storage_location.access_changed
```

These belong in `EVENT.md`.

The Storage Location object describes current identity.

Events describe what happened.

---

# Observations About Storage

Dynamic state belongs in Observations.

Examples include:

```text
storage.capacity.total
storage.capacity.used
storage.capacity.available
storage.access.available
storage.performance.read
storage.performance.write
storage.health.state
filesystem.read_only
filesystem.mount_state
```

Only add common Observation types when producers actually require interoperability.

---

# Findings About Storage

Interpretation belongs in Findings.

Examples include:

```text
storage.capacity.warning
storage.capacity.critical
storage.disposable_candidate
storage.integrity.warning
charkive.archaeology.activity_cluster
charkive.archaeology.temporal_anomaly
```

A Storage Location can accumulate Findings over time without its identity changing.

---

# Storage Location Versus Observation

Storage Location:

```text
storage:eddie:data
```

Observation:

```text
Used capacity:
640 GB
```

Later:

```text
Used capacity:
700 GB
```

The Storage Location remains the same.

The Observations change.

---

# Storage Location Versus Finding

Storage Location:

```text
storage:eddie:data
```

Finding:

```text
Storage capacity critically constrained.
```

Resolving the Finding does not delete the Storage Location.

---

# Storage Location Versus Asset

Asset:

```text
asset:usb-disk-1
```

Storage Location:

```text
storage:usb-disk-1:photos
```

The disk is the thing.

The filesystem containing the photos is the place data lives.

---

# Storage Location Versus FileCity District

Storage Location:

```text
storage:eddie:data
```

World Object:

```text
filecity:district:eddie-data
```

The district is the visual/spatial representation.

The Storage Location is the logical storage identity.

---

# Storage Location Versus Charkive SANitorium Item

Storage Location:

```text
storage:eddie:sanitorium
```

Item:

```text
2026-W43/Projects/cache/example.tmp
```

The SANitorium location is the logical storage boundary.

Individual quarantined objects remain paths/records within it.

---

# Storage Location Discovery

Possible producers may discover Storage Locations through:

```text
VNA inventory
SysMedic snapshot import
Charkive configuration
mounted filesystem discovery
FileCity rclone configuration
user declaration
vendor integration
```

Discovery method does not automatically determine ownership or authority.

---

# Duplicate Discovery

Different producers may discover the same logical Storage Location.

For example:

```text
VNA:
 /srv/data

Charkive:
 configured root /srv/data

FileCity:
 local mounted storage
```

VNA should avoid creating three logical locations if evidence establishes that all three references describe the same storage.

However:

> **Do not merge aggressively.**

Similar names and paths do not always establish identity.

A complete reconciliation algorithm is outside v0.1.

---

# Reconciliation Evidence

Future reconciliation may consider:

```text
filesystem UUID
volume identifier
provider account and remote ID
device relationship
mount source
snapshot provenance
user confirmation
```

Storage Location v0.1 does not mandate the algorithm.

Where uncertainty remains, preserve separate records rather than silently conflating unrelated storage.

---

# Privacy

Storage Location records may reveal sensitive information.

Examples include:

```text
mount paths
share names
cloud account labels
provider information
filesystem labels
infrastructure relationships
archive names
```

Generalisation may therefore be required before exposing locations to:

```text
game clients
external support
training environments
shared reports
less-trusted users
```

---

# Security Boundary

Storage Location records are data.

They do not confer access or authority.

Fields such as:

```text
access.reference
aliases
name
provider
filesystem label
attributes
```

MUST be treated as untrusted input at system boundaries.

Consumers MUST NOT interpolate them directly into:

```text
shell commands
mount commands
rclone commands
SQL
filesystem operations
privileged APIs
```

without appropriate validation and authorisation.

---

# Credentials

The Storage Location contract MUST NOT contain:

```text
passwords
API secrets
OAuth tokens
SNMP community strings
private encryption keys
cloud credentials
mount passwords
```

At most it may contain references meaningful to an authorised credential-management layer.

Credential design belongs elsewhere.

---

# Transport Independence

Storage Location v0.1 defines an object, not a transport.

It may be exchanged through:

```text
JSON
HTTP API
snapshot package
local IPC
database
message queue
test fixture
```

The semantics should remain consistent.

---

# Validation Rules

A valid Storage Location v0.1 MUST satisfy:

1. `schema` equals `vna.storage_location/0.1`.
2. `id` is a non-empty string.
3. `name` is a non-empty string.
4. `kind` is a non-empty string.
5. `source` is an object.
6. `source.kind` is one of the recognised v0.1 source kinds.
7. `status` is one of the recognised v0.1 status values.
8. If supplied, `asset_id` is a non-empty string.
9. If supplied, `access` is an object.
10. If supplied, `access.mode` is a non-empty string.
11. If supplied, `access.reference` is a string.
12. If supplied, `filesystem` is an object.
13. If supplied, `filesystem.type` is a non-empty string.
14. If supplied, `provider` is an object.
15. If supplied, `provider.type` is a non-empty string.
16. If supplied, `aliases` is an array.
17. Every alias contains a non-empty `type`.
18. Every alias contains a non-empty `value`.
19. If supplied, `labels` is an array of strings.
20. If supplied, `attributes` is an object.

Consumers SHOULD reject structurally invalid records rather than silently repairing them.

---

# Compatibility Rules

## Producer

A v0.1 producer:

- MUST provide all required fields
- MAY provide optional fields
- MAY add unknown fields
- SHOULD maintain stable logical identity
- MUST distinguish real and synthetic sources
- SHOULD avoid using mount paths as stable IDs
- SHOULD avoid using drive letters as stable IDs
- SHOULD avoid using rclone remote names as stable IDs
- MUST NOT include credentials
- SHOULD normalise logical paths consistently
- SHOULD preserve provenance

## Consumer

A v0.1 consumer:

- MUST validate fields it depends upon
- SHOULD ignore unknown fields
- SHOULD preserve unknown fields when forwarding records
- MUST preserve Storage Location identity
- MUST preserve source kind
- MUST NOT equate lifecycle status with availability
- MUST NOT equate lifecycle status with health
- MUST NOT treat access information as authorisation
- MUST NOT assume universal filesystem semantics
- MUST treat all record content as untrusted input

---

# v0.1 Deliberate Omissions

Storage Location v0.1 intentionally does not fully define:

```text
credentials
permissions
ACLs
mount options
filesystem feature matrices
provider API capabilities
case-sensitivity rules
path encoding rules
storage tiers
replication
RAID
cluster topology
performance characteristics
capacity
quotas
snapshot behaviour
backup state
retention
migration semantics
identity reconciliation
multi-tenant ownership
billing
cloud pricing
```

Those should be added only when actual integrations demonstrate the need.

The purpose of v0.1 is narrower:

> **Give every project a stable way to identify the logical place where data lives.**

---

# First Implementation Target

Charkive and FileCity provide the most useful first proof.

Create:

```text
ASSET

asset:eddie
```

Then:

```text
STORAGE LOCATION

storage:eddie:data
```

Then let Charkive emit:

```text
OBSERVATION

storage path metadata
```

and:

```text
FINDING

storage.disposable_candidate
```

with:

```json
{
  "subject": {
    "type": "storage_path",
    "storage_location_id": "storage:eddie:data",
    "path": "Projects/cache"
  }
}
```

FileCity consumes the same identity:

```text
storage:eddie:data
       |
       v
Eddie Data District
       |
       v
Projects Building
       |
       v
Cache area
```

This is the first important bridge between the operational model and FileCity geography.

---

# Second Implementation Target

SysMedic snapshot import.

```text
CAPTURED MACHINE
       |
       v
ASSET
       |
       v
CAPTURED STORAGE LOCATIONS
       |
       v
OBSERVATIONS
       |
       v
FINDINGS
```

The locations retain:

```text
source.kind = snapshot
```

so nobody mistakes captured evidence for a currently mounted production filesystem.

---

# Third Implementation Target

FileCity cloud storage.

A current rclone remote becomes:

```text
vna.storage_location/0.1
```

with:

```text
kind:
cloud
```

and an appropriate provider/access description.

The same FileCity world can now consume:

```text
local storage
mounted storage
network storage
cloud storage
```

through one common concept.

---

# Fourth Implementation Target

Charkive SANitorium.

Model the SANitorium as a Storage Location.

Now a quarantine lifecycle can eventually express:

```text
SOURCE STORAGE LOCATION
        |
        v
ORIGINAL PATH
        |
        v
CHARKIVE ACTION
        |
        v
SANITORIUM STORAGE LOCATION
        |
        v
QUARANTINED PATH
```

without Charkive and FileCity having to invent separate identities for the same logical places.

---

# North Star

A Storage Location should let the ecosystem answer:

```text
WHERE does this data logically live?

WHAT stable identity does that place have?

WHAT should humans currently call it?

WHAT broad kind of storage is it?

WHERE did we learn about it?

IS it real, captured, historical, synthetic, vendor-defined, user-defined, or generalised?

IS it still part of the active estate?

IS it associated with a particular Asset?

HOW can an authorised system currently address it?

WHAT filesystem or provider context do we know?

CAN a Finding reference a path within it without knowing the host mount point?

CAN FileCity turn it into geography without becoming the storage identity authority?
```

It should **not** pretend to answer:

```text
Is it healthy?

Is it mounted right now?

Is it full?

Is it fast?

Are we allowed to write to it?

Should anything be deleted?

How should it be rendered?
```

Those belong elsewhere.

The guiding rule is:

> **The Asset is the thing.**

> **The Storage Location is where the data lives.**
