# VNA Observation Schema v0.1

> **Record what happened before deciding what it means.**

## Purpose

An **Observation** is the common VNA ecosystem representation of something directly measured, recorded, reported, or captured about a subject.

Observations are the factual foundation beneath Findings.

The distinction is fundamental:

```text
OBSERVATION

"The network interface reports no carrier."

        ↓

FINDING

"Physical network connectivity is unavailable."

        ↓

HYPOTHESIS

"The Ethernet cable may be unplugged."

        ↓

RECOMMENDATION

"Inspect the physical connection."
```

These statements are related.

They are not interchangeable.

The Observation contract exists to preserve the closest available representation of what the source actually told us before interpretation is layered on top.

---

# Core Principle

> **Observation before interpretation.**

An Observation answers:

```text
WHAT was observed?

WHERE was it observed?

WHEN was it observed?

WHO or WHAT observed it?

HOW was it observed?

WHAT value or state was recorded?
```

It should not unnecessarily answer:

```text
WHY did this happen?

WHAT is the root cause?

HOW serious is it?

WHAT should we do?

IS something definitely broken?
```

Those belong to Findings, hypotheses, recommendations, or other higher-level objects.

---

# Ecosystem Position

The intended information flow is:

```text
REALITY / SIMULATION
        |
        v
   RAW EVIDENCE
        |
        v
   OBSERVATION
        |
        v
      FINDING
        |
        +-------------------+
        |                   |
        v                   v
  EXPLANATION         RECOMMENDATION
        |
        v
       T4
```

Not every Observation needs to produce a Finding.

For example:

```text
OBSERVATION

Filesystem utilisation:
42%
```

may be perfectly normal and therefore produce no Finding.

Observations describe.

Findings interpret.

---

# Canonical File Location

Save the canonical contract at:

```text
vna/docs/contracts/OBSERVATION.md
```

Alongside:

```text
vna/
└── docs/
    └── contracts/
        ├── FINDING.md
        ├── OBSERVATION.md
        ├── EVENT.md
        ├── ASSET.md
        ├── STORAGE_LOCATION.md
        ├── RELATIONSHIP.md
        ├── SNAPSHOT.md
        ├── PROPOSED_ACTION.md
        └── VERIFIED_RESULT.md
```

VNA owns the shared contract.

Specialist systems own the mechanisms that produce their Observations.

---

# Schema Identifier

Every Observation MUST declare:

```json
{
  "schema": "vna.observation/0.1"
}
```

The canonical identifier is:

```text
vna.observation/0.1
```

Consumers MUST NOT assume future versions are structurally identical.

Consumers SHOULD ignore unknown fields unless those fields are required by the schema version they implement.

---

# Canonical Example

```json
{
  "schema": "vna.observation/0.1",
  "id": "observation:network-carrier-001",
  "producer": {
    "id": "vna",
    "type": "application"
  },
  "source": {
    "kind": "synthetic",
    "id": "tutorial:unplugged-cable"
  },
  "observed_at": "2026-10-30T07:00:00Z",
  "type": "network.interface.carrier",
  "subject": {
    "type": "network_interface",
    "asset_id": "asset:tutorial-office-b",
    "interface_id": "interface:ethernet-1"
  },
  "method": {
    "type": "interface_state"
  },
  "data": {
    "carrier": false
  },
  "production_changed": false,
  "attributes": {}
}
```

This states:

> The interface reported no carrier.

It does **not** state:

> The cable is definitely unplugged.

That distinction is exactly why Observation exists.

---

# Required Fields

Observation v0.1 requires:

```text
schema
id
producer
source
observed_at
type
subject
data
production_changed
```

Optional fields include:

```text
method
quality
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
vna.observation/0.1
```

## Purpose

Identifies the shared contract and its version.

---

# Field: `id`

## Type

```text
string
```

## Required

Yes.

## Purpose

Provides the identity of this Observation.

Example:

```json
{
  "id": "observation:network-carrier-001"
}
```

Consumers MUST treat Observation IDs as opaque.

They MUST NOT infer behaviour or technical meaning from the structure of an ID.

---

# Observation Identity

Observations often represent measurements at particular points in time.

Therefore repeated measurements will commonly receive separate IDs.

For example:

```text
09:00
observation:rtt-001
18 ms

09:05
observation:rtt-002
21 ms

09:10
observation:rtt-003
187 ms
```

These are three Observations of the same metric.

They are not three revisions of one Observation.

This distinction allows observations to form useful historical series.

---

# Observation Immutability

Once recorded, an Observation SHOULD normally be treated as immutable.

If a system measures the subject again, it SHOULD generally produce another Observation rather than rewriting the old one.

Prefer:

```text
Observation A
09:00
RTT = 18 ms

Observation B
09:05
RTT = 187 ms
```

over changing Observation A from:

```text
18 ms
```

to:

```text
187 ms
```

Observations are evidence of what was seen at a particular time.

Historical evidence should not move underneath us.

---

# Corrections

If an Observation itself is discovered to be malformed or erroneous, the producer MAY supersede or invalidate it through a future Event or relationship mechanism.

Observation v0.1 does not define a complete correction lifecycle.

Consumers SHOULD avoid silently rewriting previously recorded Observations.

---

# Field: `producer`

## Type

```text
object
```

## Required

Yes.

## Purpose

Identifies the system responsible for producing the Observation.

Example:

```json
{
  "producer": {
    "id": "t4t",
    "type": "collector",
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
t4t
filecity
vendor:<vendor>
future:<producer>
```

The producer describes who created the VNA Observation object.

It does not necessarily identify the physical device or software component that originated the raw evidence.

That may be described by `method`, `source`, or producer-specific attributes.

---

# Field: `source`

## Type

```text
object
```

## Required

Yes.

## Purpose

Describes the kind of reality or scenario from which the Observation originates.

Observation and Finding use the same high-level source kinds.

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

The Observation was made against a current real system.

Example:

```json
{
  "source": {
    "kind": "live",
    "id": "marvin:scan:1234"
  }
}
```

---

# Source Kind: `snapshot`

The Observation derives from evidence previously captured from a real system.

Example:

```json
{
  "source": {
    "kind": "snapshot",
    "id": "sysmedic:snapshot:SM-20261030-0733",
    "captured_at": "2026-10-30T06:45:00Z"
  }
}
```

A snapshot-derived Observation MUST NOT imply current real-world state.

---

# Source Kind: `historical`

The Observation derives from retained historical evidence.

Possible uses include:

```text
old Charkive scans
archived telemetry
Data Archaeology
previous system states
historical logs
```

---

# Source Kind: `synthetic`

The Observation belongs to an explicitly artificial training or simulation environment.

Example:

```json
{
  "source": {
    "kind": "synthetic",
    "id": "tutorial:unplugged-cable"
  }
}
```

Synthetic evidence must never masquerade as a production observation.

---

# Source Kind: `vendor`

The Observation originates from a vendor-supplied simulation, equipment model, training scenario, or diagnostic source.

---

# Source Kind: `user`

The Observation records something reported directly by a human.

Example:

```json
{
  "source": {
    "kind": "user",
    "id": "user:local"
  }
}
```

Human-reported observations remain useful evidence.

They must remain distinguishable from machine measurements.

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

`observed_at` describes when the relevant observation applies.

For snapshot or historical evidence this may differ significantly from:

```text
import time
analysis time
display time
current time
```

---

# Field: `type`

## Type

```text
string
```

## Required

Yes.

## Purpose

Identifies what was observed.

Observation types SHOULD use dotted namespaces.

Common types might include:

```text
network.interface.carrier
network.icmp.rtt
network.icmp.packet_loss
storage.capacity.used
storage.capacity.available
filesystem.metadata
system.service.state
hardware.temperature
hardware.disk.smart
application.response
```

Producer-specific observation types SHOULD use the producer namespace where the semantics are not genuinely common.

Examples:

```text
charkive.rule_match
charkive.temporal_cluster
sysmedic.windows.event
sysmedic.hardware_test
```

---

# Observation Type Principle

Observation type answers:

> **What sort of thing was measured or recorded?**

It should not bake a conclusion into the type unnecessarily.

Prefer:

```text
network.interface.carrier
```

with:

```json
{
  "carrier": false
}
```

over:

```text
network.cable_unplugged
```

when the actual evidence establishes only the absence of carrier.

The latter is an interpretation.

---

# Field: `subject`

## Type

```text
object
```

## Required

Yes.

## Purpose

Identifies what was observed.

Observation subjects SHOULD use the same shared subject identities used by Findings where practical.

Possible subject types include:

```text
asset
network_interface
storage_location
storage_path
service
application
collection
```

---

# Subject Example: Asset

```json
{
  "subject": {
    "type": "asset",
    "asset_id": "asset:eddie"
  }
}
```

---

# Subject Example: Network Interface

```json
{
  "subject": {
    "type": "network_interface",
    "asset_id": "asset:eddie",
    "interface_id": "interface:eth0"
  }
}
```

---

# Subject Example: Storage Location

```json
{
  "subject": {
    "type": "storage_location",
    "asset_id": "asset:eddie",
    "storage_location_id": "storage:eddie:data"
  }
}
```

---

# Subject Example: Storage Path

```json
{
  "subject": {
    "type": "storage_path",
    "asset_id": "asset:eddie",
    "storage_location_id": "storage:eddie:data",
    "path": "Projects/cache"
  }
}
```

---

# Field: `method`

## Type

```text
object
```

## Required

No.

## Purpose

Describes how the Observation was obtained.

Example:

```json
{
  "method": {
    "type": "icmp_echo",
    "count": 4
  }
}
```

Possible method types include:

```text
icmp_echo
interface_state
snmp
filesystem_scan
log_parse
hardware_test
user_report
synthetic_state
```

Producer-specific methods SHOULD use appropriate namespaces.

---

# Why Method Matters

Two Observations may appear to describe a similar condition while having different evidential strengths.

For example:

```text
Method:
ICMP probe

Result:
No reply
```

and:

```text
Method:
Local interface state

Result:
No carrier
```

are not equivalent evidence.

Recording method helps downstream systems avoid pretending they are.

---

# Field: `data`

## Type

```text
object
```

## Required

Yes.

## Purpose

Contains the value, state, or structured evidence that was observed.

The shape depends on the Observation `type`.

Example:

```json
{
  "type": "network.icmp.rtt",
  "data": {
    "value": 187,
    "unit": "ms"
  }
}
```

Another Observation might contain:

```json
{
  "type": "network.interface.carrier",
  "data": {
    "carrier": false
  }
}
```

The `data` object SHOULD contain observations rather than interpretations wherever practical.

---

# Units

Measurements SHOULD include explicit units.

Prefer:

```json
{
  "value": 187,
  "unit": "ms"
}
```

over:

```json
{
  "value": 187
}
```

unless the Observation type defines a dimensionless or inherently unambiguous value.

Potential units might include:

```text
ms
percent
bytes
degrees_celsius
rpm
volts
watts
seconds
count
```

A future units contract may standardise this further if real interoperability requires it.

---

# Unknown and Missing Values

An unavailable measurement should not be silently represented as zero.

For example:

```text
No RTT measurement available
```

is not equivalent to:

```text
RTT = 0 ms
```

Where a measurement attempt failed, the Observation may record the failure explicitly.

Example:

```json
{
  "type": "network.icmp.rtt",
  "data": {
    "status": "unavailable"
  }
}
```

or a producer may emit a related Observation describing the failed probe.

The precise pattern should remain consistent within a producer.

---

# Field: `quality`

## Type

```text
object
```

## Required

No.

## Purpose

Describes limitations or quality information associated with the Observation.

Potential fields include:

```text
confidence
complete
estimated
reason
```

Example:

```json
{
  "quality": {
    "complete": false,
    "reason": "Only partial log evidence was available."
  }
}
```

Quality describes the Observation itself.

It should not be confused with Finding confidence.

---

# Observation Quality vs Finding Confidence

These are related but distinct concepts.

```text
OBSERVATION QUALITY

How trustworthy or complete is this measurement?
```

versus:

```text
FINDING CONFIDENCE

How confident is the producer in its interpretation?
```

For example:

```text
High-quality Observation

Interface reports no carrier.
```

may support:

```text
High-confidence Finding

Physical link unavailable.
```

but only:

```text
Moderate-confidence Hypothesis

Cable unplugged.
```

The schema should preserve those distinctions.

---

# Field: `production_changed`

## Type

```text
boolean
```

## Required

Yes.

## Purpose

Records whether obtaining the Observation itself changed the real system.

Most observations should say:

```json
{
  "production_changed": false
}
```

Examples include:

```text
reading interface state
reading telemetry
reading filesystem metadata
parsing existing logs
analysing a snapshot
```

This reinforces the ecosystem's observation-first philosophy.

---

# Why `production_changed` Matters

Not every diagnostic method is perfectly passive.

If an observation required a probe that changed system state in a meaningful way, that fact should not be hidden.

The common contract therefore asks explicitly:

> **Did observing this alter reality?**

For the majority of VNA's early integration work, the answer should be:

```text
false
```

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
      "scan_id": "charkive:scan:1234"
    }
  }
}
```

Shared consumers SHOULD NOT rely on producer-specific attributes for common Observation semantics.

---

# Minimal Valid Observation

```json
{
  "schema": "vna.observation/0.1",
  "id": "observation:001",
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
    "asset_id": "asset:device-42"
  },
  "data": {
    "value": 18,
    "unit": "ms"
  },
  "production_changed": false
}
```

---

# MARVIN / T4T Example

```json
{
  "schema": "vna.observation/0.1",
  "id": "observation:marvin-rtt-001",
  "producer": {
    "id": "t4t",
    "type": "collector"
  },
  "source": {
    "kind": "live",
    "id": "marvin:scan:1234"
  },
  "observed_at": "2026-10-30T07:00:00Z",
  "type": "network.icmp.rtt",
  "subject": {
    "type": "asset",
    "asset_id": "asset:network-device-42"
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

This Observation might contribute to a Finding such as:

```text
Network response appears degraded.
```

T4 can then explain the Finding differently for:

```text
T4Tots
T4Teens
T4Techies
```

The Observation remains unchanged.

---

# Packet Loss Example

```json
{
  "schema": "vna.observation/0.1",
  "id": "observation:marvin-packet-loss-001",
  "producer": {
    "id": "t4t",
    "type": "collector"
  },
  "source": {
    "kind": "live",
    "id": "marvin:scan:1234"
  },
  "observed_at": "2026-10-30T07:00:00Z",
  "type": "network.icmp.packet_loss",
  "subject": {
    "type": "asset",
    "asset_id": "asset:network-device-42"
  },
  "method": {
    "type": "icmp_echo",
    "count": 4
  },
  "data": {
    "value": 25,
    "unit": "percent"
  },
  "production_changed": false
}
```

---

# SysMedic Example

A SysMedic snapshot may contain evidence that a service was stopped when the snapshot was captured.

```json
{
  "schema": "vna.observation/0.1",
  "id": "observation:sysmedic-service-001",
  "producer": {
    "id": "sysmedic",
    "type": "application",
    "version": "2026.10"
  },
  "source": {
    "kind": "snapshot",
    "id": "sysmedic:snapshot:SM-20261030-0733",
    "captured_at": "2026-10-30T06:45:00Z"
  },
  "observed_at": "2026-10-30T06:45:00Z",
  "type": "system.service.state",
  "subject": {
    "type": "service",
    "asset_id": "asset:captured-machine",
    "service_id": "service:example"
  },
  "method": {
    "type": "sysmedic.snapshot_collection"
  },
  "data": {
    "state": "stopped"
  },
  "production_changed": false,
  "attributes": {
    "sysmedic": {
      "collection_id": "SM-20261030-0733"
    }
  }
}
```

This says:

> The captured evidence recorded the service as stopped.

It does not say:

> The service is still stopped now.

---

# Charkive File Metadata Example

```json
{
  "schema": "vna.observation/0.1",
  "id": "observation:charkive-file-001",
  "producer": {
    "id": "charkive",
    "type": "application"
  },
  "source": {
    "kind": "live",
    "id": "charkive:scan:1234"
  },
  "observed_at": "2026-10-30T07:00:00Z",
  "type": "filesystem.metadata",
  "subject": {
    "type": "storage_path",
    "storage_location_id": "storage:eddie:data",
    "path": "Projects/render/cache/frame-0001.tmp"
  },
  "method": {
    "type": "filesystem_scan"
  },
  "data": {
    "size_bytes": 1879048192,
    "modified_at": "2026-10-20T14:30:00Z"
  },
  "production_changed": false,
  "attributes": {
    "charkive": {
      "scan_id": "charkive:scan:1234"
    }
  }
}
```

Charkive may combine this Observation with configuration rules and other evidence to produce:

```text
Finding:
Likely disposable render-cache data.
```

The file metadata itself does not contain that conclusion.

---

# Charkive Temporal Example

Temporal Anomaly detection is an especially important reason to maintain clean Observation semantics.

Consider:

```json
{
  "schema": "vna.observation/0.1",
  "id": "observation:photo-time-001",
  "producer": {
    "id": "charkive",
    "type": "application"
  },
  "source": {
    "kind": "historical",
    "id": "charkive:archaeology:collection-42"
  },
  "observed_at": "2026-10-30T07:00:00Z",
  "type": "media.recorded_time",
  "subject": {
    "type": "storage_path",
    "storage_location_id": "storage:archive",
    "path": "Photos/Kodak/example.jpg"
  },
  "method": {
    "type": "metadata_read"
  },
  "data": {
    "recorded_time": "2003-01-01T00:00:00Z",
    "metadata_source": "exif"
  },
  "production_changed": false
}
```

The Observation says:

> The EXIF metadata recorded this timestamp.

It does not say:

> The photograph was definitely taken at this time.

That distinction allows Charkive to later produce a Finding such as:

```text
TEMPORAL ANOMALY

Recorded time appears inconsistent with surrounding evidence.
```

without corrupting or rewriting the original Observation.

---

# User-Confirmed Temporal Observation

Human knowledge may also become Observation data when explicitly identified as such.

Example:

```json
{
  "schema": "vna.observation/0.1",
  "id": "observation:user-camera-clock-001",
  "producer": {
    "id": "charkive",
    "type": "application"
  },
  "source": {
    "kind": "user",
    "id": "user:local"
  },
  "observed_at": "2026-10-30T07:00:00Z",
  "type": "user.device_clock_history",
  "subject": {
    "type": "collection",
    "storage_location_id": "storage:archive",
    "path": "Photos/Kodak"
  },
  "method": {
    "type": "user_report"
  },
  "data": {
    "statement": "The camera clock was known to be unreliable."
  },
  "production_changed": false
}
```

This is legitimate useful evidence.

But the schema clearly identifies:

```text
source.kind = user
```

It is not silently transformed into machine-measured fact.

---

# VNA Synthetic OSI Example

The unplugged-cable tutorial can use the same Observation format.

```json
{
  "schema": "vna.observation/0.1",
  "id": "observation:tutorial-carrier-001",
  "producer": {
    "id": "vna",
    "type": "simulation"
  },
  "source": {
    "kind": "synthetic",
    "id": "tutorial:unplugged-cable"
  },
  "observed_at": "2026-10-30T07:00:00Z",
  "type": "network.interface.carrier",
  "subject": {
    "type": "network_interface",
    "asset_id": "asset:tutorial-office-b",
    "interface_id": "interface:ethernet-1"
  },
  "method": {
    "type": "synthetic_state"
  },
  "data": {
    "carrier": false
  },
  "production_changed": false
}
```

VNA may then create:

```text
Finding:
Physical network connection unavailable.
```

And the tutorial may know separately that its synthetic root cause is:

```text
Cable unplugged.
```

The Observation contract remains honest about what is being represented.

---

# Multiple Observations Supporting One Finding

A Finding may combine multiple Observations.

For example:

```text
Observation A

ICMP RTT:
187 ms


Observation B

Packet loss:
25%


Observation C

Interface carrier:
true

        |
        v

Finding

Network response is degraded.
```

This is one of the main reasons Findings and Observations are separate contracts.

---

# One Observation Supporting Multiple Findings

The same Observation may contribute to more than one Finding.

For example:

```text
Observation

Filesystem utilisation:
96%
```

could potentially support:

```text
Finding A

Storage capacity is critically constrained.
```

and perhaps:

```text
Finding B

Application performance may be at risk.
```

if another producer has evidence supporting that interpretation.

The Observation remains stable and reusable.

---

# Observations Need Not Become Findings

Not every fact deserves attention.

This is entirely valid:

```text
Observation

CPU temperature:
42°C

Finding:
NONE
```

The absence of a Finding does not make the Observation worthless.

It may become important later as historical context.

---

# Historical Value

Observations can form time series.

```text
09:00  42°C
09:05  43°C
09:10  45°C
09:15  61°C
09:20  78°C
```

A later Finding might interpret:

> Temperature is rising unusually quickly.

The historical Observation series provides the evidence.

This pattern applies equally to:

```text
storage growth
network response
packet loss
service state
health telemetry
file activity
```

---

# Observation Versus Event

An Observation says:

```text
The service state is stopped.
```

An Event says:

```text
The service stopped at 07:00.
```

The difference matters.

An Observation describes a state seen at a time.

An Event describes something that happened.

Sometimes both may be available.

Sometimes only one is known.

Do not fabricate an Event merely because an Observation exists.

Seeing a stopped service at 07:05 does not prove it stopped at 07:05.

---

# Observation Versus Finding

Observation:

```text
Filesystem used:
96%
```

Finding:

```text
Storage capacity critically constrained.
```

Observation:

```text
Interface carrier:
false
```

Finding:

```text
Physical network connection unavailable.
```

Observation:

```text
EXIF timestamp:
2003-01-01
```

Finding:

```text
Recorded timestamp appears inconsistent with surrounding evidence.
```

---

# Observation Versus Hypothesis

Observation:

```text
Interface reports no carrier.
```

Hypothesis:

```text
Cable may be unplugged.
```

Observation:

```text
Photos report 2003 timestamps.
```

Hypothesis:

```text
Camera clock may have been wrong.
```

Hypotheses should not be smuggled into Observation data as if they were measured facts.

---

# Observation Versus Recommendation

Observation:

```text
Packet loss:
25%
```

Recommendation:

```text
Investigate network connectivity.
```

The Observation contract carries facts.

The recommendation belongs to a Finding or future specialised contract.

---

# T4 Consumption

T4 may explain observations directly where no higher-level Finding is needed.

For example:

```text
Observation

RTT = 187 ms
```

T4Tots:

```text
HOW FAST DID IT ANSWER?

A bit slowly!
```

T4Teens:

```text
NETWORK RESPONSE TIME

The device took 187 milliseconds to reply.
```

T4Techies:

```text
ICMP RTT:
187 ms
```

The Observation remains unchanged.

Where a Finding exists, T4 may prefer to explain the Finding while retaining access to its supporting Observations.

---

# FileCity Consumption

FileCity may use Observations for visual metrics.

For example:

```text
Observation

Storage utilisation:
82%
```

might affect building scale, status, or a dashboard.

FileCity does not need to reinterpret raw evidence as a Finding unless a producer has actually created one.

This prevents visualisation logic from accidentally becoming diagnostic logic.

---

# VNA Gameplay Consumption

VNA gameplay may expose Observations as clues.

For example, at Layer 1:

```text
Interface status panel

Carrier:
NO
```

At T4Tots depth:

```text
NO PHYSICAL CONNECTION
```

At T4Techies depth:

```text
carrier = false
```

The player can use Observations to form a diagnosis.

That means a tutorial does not always need to hand the player a Finding immediately.

Sometimes the point of gameplay is for the player to infer it.

---

# Oracle Consumption

An Oracle may reason over Observations.

The Oracle MUST distinguish:

```text
what the Observation says
```

from:

```text
what the Oracle thinks it means
```

For example:

```text
Observation:

Interface reports no carrier.


Oracle:

"This suggests a Layer 1 connectivity issue. An unplugged or damaged cable is one possible cause."
```

This is acceptable.

The Oracle must not rewrite the Observation as:

```text
Cable definitely unplugged.
```

unless other evidence genuinely establishes that fact.

---

# Privacy

Observations may contain sensitive information.

Examples include:

```text
IP addresses
MAC addresses
hostnames
file paths
usernames
service names
device information
hardware serials
historical timestamps
user statements
```

Consumers should expose only information appropriate to the user and context.

VNA generalisation may transform or redact Observation subjects and data before they reach less-trusted clients.

---

# Generalisation

A real Observation might contain:

```text
asset:
EDDIE

interface:
enp2s0
```

A generalised client might receive:

```text
asset:
SERVER-104

interface:
NETWORK-1
```

The technical meaning may remain:

```text
carrier = false
```

Generalisation must not silently alter the measured state.

---

# Security Boundary

Observation content is untrusted input at integration boundaries.

Fields such as:

```text
path
asset IDs
service IDs
user statements
metadata
attributes
```

MUST NOT be interpolated directly into:

```text
shell commands
SQL
filesystem operations
HTML
privileged APIs
game-engine scripting
```

without appropriate validation and escaping.

An Observation describes reality.

It does not request an operation.

---

# Transport Independence

Observation v0.1 defines an object, not a transport.

It may travel through:

```text
JSON file
HTTP API
snapshot container
local IPC
event stream
message queue
database
test fixture
```

Its meaning should remain consistent across transports.

---

# Validation Rules

A valid Observation v0.1 MUST satisfy:

1. `schema` equals `vna.observation/0.1`.
2. `id` is a non-empty string.
3. `producer` is an object.
4. `producer.id` is a non-empty string.
5. `producer.type` is a non-empty string.
6. `source` is an object.
7. `source.kind` is one of the recognised v0.1 source kinds.
8. `observed_at` is a valid UTC ISO 8601 timestamp.
9. `type` is a non-empty string.
10. `subject` is an object.
11. `subject.type` is a non-empty string.
12. `data` is an object.
13. `production_changed` is a boolean.
14. If supplied, `method` is an object.
15. If supplied, `method.type` is a non-empty string.
16. If supplied, `quality` is an object.
17. If supplied, `attributes` is an object.

Consumers SHOULD reject structurally invalid Observations rather than silently repairing them.

---

# Compatibility Rules

## Producer

A v0.1 producer:

- MUST provide all required fields
- MAY provide optional fields
- MAY add unknown fields
- SHOULD treat historical Observations as immutable
- SHOULD create new Observations for new measurements
- SHOULD preserve provenance
- MUST distinguish real and synthetic sources
- SHOULD emit `production_changed: false` for passive collection

## Consumer

A v0.1 consumer:

- MUST validate fields it depends upon
- SHOULD ignore unknown fields
- SHOULD preserve unknown fields when forwarding Observations
- MUST preserve source kind
- MUST preserve producer identity
- MUST NOT silently convert observations into conclusions
- MUST NOT interpret missing measurements as zero
- MUST treat all Observation content as untrusted input

---

# v0.1 Deliberate Omissions

Observation v0.1 intentionally does not fully define:

```text
correction lifecycle
supersession
retention
aggregation
sampling policy
unit conversion
time-series storage
deduplication
cross-source reconciliation
trust scores
sensor calibration
authentication
authorisation
transport
renderer behaviour
```

These should be added only when implementation experience demonstrates the need.

---

# First Implementation Target

T4T/MARVIN is the ideal first implementation.

Take one existing ICMP measurement:

```text
MARVIN
   |
   v
PING
   |
   v
RTT
```

and emit:

```text
vna.observation/0.1
```

For example:

```json
{
  "schema": "vna.observation/0.1",
  "id": "observation:test-rtt-001",
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
    "asset_id": "asset:test-device"
  },
  "method": {
    "type": "icmp_echo"
  },
  "data": {
    "value": 18,
    "unit": "ms"
  },
  "production_changed": false
}
```

Then:

```text
Observation
     |
     v
Finding
     |
     ├── T4Tots
     ├── T4Teens
     └── T4Techies
```

One real measurement now travels through the entire explanation architecture without changing meaning.

---

# Second Implementation Target

Charkive:

```text
FILESYSTEM
    |
    v
METADATA READ
    |
    v
OBSERVATION
    |
    v
RULE / ANALYSIS
    |
    v
FINDING
```

This proves that the same Observation contract works outside network telemetry.

---

# Third Implementation Target

SysMedic:

```text
MACHINE
   |
   v
COLLECT
   |
   v
SNAPSHOT
   |
   v
IMPORT
   |
   v
OBSERVATIONS
   |
   v
FINDINGS
```

Snapshot provenance remains attached.

---

# Fourth Implementation Target

VNA gameplay:

```text
SYNTHETIC SYSTEM
      |
      v
OBSERVATIONS
      |
      v
PLAYER INSPECTION
      |
      v
DIAGNOSIS
```

The player can now troubleshoot using the exact same conceptual distinction used by the real diagnostic tools.

---

# North Star

An Observation should let the ecosystem answer:

```text
WHAT was observed?

WHAT was it observed about?

WHO produced the Observation?

WHAT kind of source did it come from?

WHEN did it apply?

HOW was it observed?

WHAT value or state was recorded?

HOW complete or reliable was the observation?

DID obtaining it change reality?
```

It should **not** pretend to answer questions the evidence cannot support.

The guiding rule is simple:

> **Record what you saw.**

> **Then decide what it means.**
