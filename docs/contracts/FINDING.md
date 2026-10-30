# VNA Finding Schema v0.1

> **One shared definition of "we found something" across the VNA ecosystem.**

## Purpose

A **Finding** is the common VNA ecosystem representation of a noteworthy condition discovered by a producer.

It provides a shared language through which independently useful projects can describe things they have discovered without forcing every consumer to understand the producer's internal implementation.

Potential producers include:

```text
VNA
SysMedic
Charkive
FileCity
Vendor integrations
Future diagnostic tools
```

Potential consumers include:

```text
VNA
T4
FileCity
BoreDOOM!
Reports
Oracles
Training environments
Future clients
```

Not every project needs to produce Findings.

Not every project needs to understand every Finding category.

The contract exists so that when one component says:

> **I found something.**

the rest of the ecosystem has a common way to understand what that means.

---

# Core Principle

A Finding describes reality, captured reality, historical evidence, or explicitly synthetic reality.

It does not grant authority to change anything.

The fundamental flow is:

```text
REALITY / SIMULATION
        |
        v
   OBSERVATIONS
        |
        v
      FINDING
        |
        +-------------------+
        |                   |
        v                   v
    EXPLANATION       RECOMMENDATION
        |                   |
        v                   v
       T4             HUMAN DECISION
        |
        v
VISUALISATION / REPORT
```

If action is eventually appropriate:

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
```

A Finding itself does **not** authorise execution.

---

# Ecosystem Ownership

The Finding contract belongs to the VNA common model.

The canonical namespace is:

```text
vna.finding
```

Individual producers retain ownership of their specialist analysis.

For example:

```text
VNA
owns the common Finding contract

Charkive
owns Charkive detection and classification logic

SysMedic
owns SysMedic diagnostic evidence

FileCity
owns FileCity geography

T4
owns audience-specific explanation

BoreDOOM!
owns gameplay representation
```

A Charkive Finding expressed through this contract is still produced by Charkive.

A FileCity representation of that Finding does not become a FileCity Finding merely because FileCity displays it.

---

# Canonical File Location

The canonical contract should live at:

```text
vna/docs/contracts/FINDING.md
```

Related contracts should eventually live alongside it:

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

VNA is the umbrella.

The common contracts live here.

---

# Schema Identifier

Every Finding MUST declare:

```json
{
  "schema": "vna.finding/0.1"
}
```

The canonical identifier is:

```text
vna.finding/0.1
```

Consumers MUST NOT assume future versions are structurally identical.

Consumers SHOULD ignore unknown fields unless those fields are explicitly required by a schema version they implement.

---

# Canonical Example

```json
{
  "schema": "vna.finding/0.1",
  "id": "finding:0192f7b2-example",
  "producer": {
    "id": "charkive",
    "type": "application",
    "version": "2026.10"
  },
  "source": {
    "kind": "live",
    "id": "charkive:scan:0192f7b2"
  },
  "observed_at": "2026-10-30T07:00:00Z",
  "category": "storage.disposable_candidate",
  "severity": "notice",
  "state": "active",
  "confidence": 0.92,
  "subject": {
    "type": "storage_path",
    "asset_id": "asset:eddie",
    "storage_location_id": "storage:eddie:data",
    "path": "Projects/example/cache.tmp"
  },
  "summary": "Likely disposable temporary data",
  "observations": [
    {
      "type": "filesystem.metadata",
      "observed_at": "2026-10-30T07:00:00Z",
      "data": {
        "path": "Projects/example/cache.tmp",
        "size_bytes": 1879048192
      }
    }
  ],
  "evidence": [
    {
      "type": "charkive.rule_match",
      "rule": "temporary-file-pattern",
      "description": "Path matched a configured disposable-data rule."
    }
  ],
  "recommendations": [
    {
      "type": "charkive.review_for_quarantine",
      "summary": "Review this candidate before considering reversible quarantine."
    }
  ],
  "production_changed": false,
  "attributes": {}
}
```

This example is illustrative.

The contract should evolve from real implementations rather than attempting to predict every future producer.

---

# Required Fields

Finding v0.1 requires:

```text
schema
id
producer
source
observed_at
category
severity
state
subject
summary
production_changed
```

Optional fields include:

```text
confidence
observations
evidence
recommendations
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

## v0.1 Value

```text
vna.finding/0.1
```

## Purpose

Identifies the contract and version.

Example:

```json
{
  "schema": "vna.finding/0.1"
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

Provides the stable identity of the logical Finding.

Example:

```json
{
  "id": "finding:0192f7b2-example"
}
```

The identifier SHOULD be globally unique within the relevant VNA estate.

Consumers MUST treat the identifier as opaque.

They MUST NOT infer operational meaning from its internal format.

A producer republishing the same logical Finding SHOULD retain its identity where practical.

A genuinely distinct Finding receives a new identifier.

Detailed correlation, revision, deduplication, and recurrence rules are deliberately deferred beyond v0.1.

---

# Finding Identity

The Finding ID identifies the logical condition record rather than a particular JSON serialisation.

For example:

```text
Finding:
finding:123

State:
active
```

might later become:

```text
Finding:
finding:123

State:
acknowledged
```

and later:

```text
Finding:
finding:123

State:
resolved
```

The same logical Finding can therefore move through its lifecycle without requiring a new ID for every state.

Whether a recurring condition reactivates the existing Finding or creates a new Finding is producer policy in v0.1.

---

# Field: `producer`

## Type

```text
object
```

## Required

Yes.

## Purpose

Identifies the system responsible for producing the Finding.

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

Possible producer IDs include:

```text
vna
sysmedic
charkive
filecity
vendor:<vendor>
future:<producer>
```

Possible producer types may include:

```text
application
collector
adapter
vendor
user
simulation
```

The producer owns the analysis that generated the Finding.

A consumer MUST preserve the producer identity.

---

# Producer-Specific Semantics

A producer may use the common Finding structure while retaining specialist evidence.

For example:

```text
Charkive
produces storage-health and archaeology Findings

SysMedic
produces machine-diagnostic Findings

VNA
produces infrastructure Findings
```

The common contract describes how those Findings are exchanged.

It does not require VNA to duplicate the specialist detection logic.

---

# Field: `source`

## Type

```text
object
```

## Required

Yes.

## Purpose

Describes the kind of reality or scenario from which the Finding originated.

This distinction is fundamental because VNA may eventually operate across:

```text
live systems
captured systems
historical data
synthetic training environments
vendor simulations
user observations
```

## Required Source Fields

```text
kind
```

## Optional Source Fields

```text
id
captured_at
description
```

---

# Source Kind: `live`

The Finding derives from observation of a current real system.

Example:

```json
{
  "source": {
    "kind": "live",
    "id": "charkive:scan:1234"
  }
}
```

---

# Source Kind: `snapshot`

The Finding derives from captured real-world state.

Example:

```json
{
  "source": {
    "kind": "snapshot",
    "id": "sysmedic:snapshot:1234",
    "captured_at": "2026-10-30T06:30:00Z"
  }
}
```

A snapshot Finding describes captured state.

It MUST NOT imply that the condition still exists on the original machine.

---

# Source Kind: `historical`

The Finding derives from retained historical evidence.

Potential uses include:

```text
Charkive Data Archaeology
historical telemetry
previous VNA observations
trend analysis
activity clustering
Temporal Anomaly analysis
```

---

# Source Kind: `synthetic`

The Finding was deliberately generated by a simulation or training environment.

Example:

```json
{
  "source": {
    "kind": "synthetic",
    "id": "tutorial:unplugged-cable"
  }
}
```

Synthetic Findings MUST remain distinguishable from real-system Findings.

A tutorial cable being unplugged must never masquerade as a production cable incident.

---

# Source Kind: `vendor`

The Finding originates from a vendor-supplied simulation, diagnostic package, training environment, or equipment model.

Example:

```json
{
  "source": {
    "kind": "vendor",
    "id": "vendor:example:scenario:42"
  }
}
```

Vendor provenance SHOULD be preserved where available.

---

# Source Kind: `user`

The Finding derives primarily from a human report, annotation, or classification.

Example:

```json
{
  "source": {
    "kind": "user",
    "id": "user:local"
  }
}
```

A user assertion remains distinguishable from direct machine observation.

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

This represents when the relevant condition was observed or inferred from the relevant evidence.

For historical or snapshot Findings, this does not necessarily equal:

```text
time imported
time analysed
time displayed
current time
```

---

# Field: `category`

## Type

```text
string
```

## Required

Yes.

## Purpose

Provides a machine-readable classification of what kind of condition was found.

Categories SHOULD use dotted namespaces.

Examples:

```text
network.physical.link_down
network.response.degraded
storage.capacity.warning
storage.disposable_candidate
storage.temporal_anomaly
system.service.unavailable
system.update.failure
hardware.temperature.warning
hardware.disk.health
```

Categories should be introduced because a real producer understands the condition.

Do not create categories merely because a visualisation might look entertaining.

---

# Category Ownership

Common categories SHOULD use general technical namespaces:

```text
network.*
storage.*
system.*
hardware.*
application.*
security.*
```

Producer-specific categories SHOULD use the producer namespace where the meaning is not genuinely common.

For example:

```text
charkive.archaeology.activity_cluster
charkive.archaeology.temporal_anomaly
sysmedic.windows.update_stalled
```

This preserves specialist semantics without pretending every producer understands them.

---

# Category Is Not Severity

Category answers:

> **What kind of thing is this?**

Severity answers:

> **How much attention does this deserve?**

These are independent.

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

---

# Severity: `info`

Useful information which does not itself indicate a problem.

Examples might include:

```text
interesting historical cluster
protected data discovered
successful state observation
```

---

# Severity: `notice`

Something worth bringing to the user's attention.

It is not necessarily urgent.

---

# Severity: `warning`

A condition deserving meaningful attention, investigation, or review.

---

# Severity: `critical`

A condition the producer considers highly important or urgent.

Critical severity must not imply automatic permission to act.

---

# Severity Is Not Confidence

Severity and confidence are independent.

Example:

```json
{
  "severity": "notice",
  "confidence": 0.99
}
```

may mean:

> The producer is highly confident about a relatively low-importance condition.

Conversely:

```json
{
  "severity": "critical",
  "confidence": 0.61
}
```

may mean:

> The possible condition is highly important, but the exact diagnosis remains uncertain.

Consumers MUST NOT derive severity from confidence or confidence from severity.

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
acknowledged
resolved
suppressed
```

Example:

```json
{
  "state": "active"
}
```

The state describes the current lifecycle disposition of the Finding.

---

# Lifecycle Overview

```text
                 ┌────────────┐
                 │   ACTIVE   │
                 └─────┬──────┘
                       │
           ┌───────────┼────────────┐
           │           │            │
           v           v            v
   ACKNOWLEDGED     RESOLVED     SUPPRESSED
           │           │            │
           └───────────┼────────────┘
                       │
                       │ condition returns /
                       │ disposition changes
                       v
                     ACTIVE
```

The lifecycle describes the Finding.

It does not automatically describe an action.

---

# State: `active`

The producer currently considers the Finding applicable.

```text
CONDITION OBSERVED
       |
       v
     ACTIVE
```

An active Finding may require:

- no action
- review
- investigation
- continued observation
- recommendation
- a proposed action

`active` does not imply urgency.

Urgency belongs to `severity`.

---

# State: `acknowledged`

The Finding has been explicitly acknowledged, but the underlying condition has not necessarily been resolved.

```text
ACTIVE
   |
   v
USER / OPERATOR REVIEWS
   |
   v
ACKNOWLEDGED
```

Acknowledgement means:

> **We know about this.**

It does not mean:

> **This has been fixed.**

An acknowledged Finding may remain visible.

Presentation is a consumer concern.

---

# State: `resolved`

The producer considers the condition no longer applicable.

```text
ACTIVE
   |
   v
REALITY CHANGES
   |
   v
REALITY RE-OBSERVED
   |
   v
CONDITION NO LONGER PRESENT
   |
   v
RESOLVED
```

A Finding MUST NOT be considered resolved merely because somebody attempted an operation.

The preferred principle is:

```text
ACTION ATTEMPTED
      |
      v
RESULT VERIFIED
      |
      v
SYSTEM RE-OBSERVED
      |
      v
CONDITION GONE
      |
      v
RESOLVED
```

---

# State: `suppressed`

The Finding remains known but should not currently demand normal attention.

```text
ACTIVE
   |
   v
DELIBERATE SUPPRESSION
   |
   v
SUPPRESSED
```

Suppression may be appropriate when a condition is:

- expected
- deliberately accepted
- temporarily irrelevant
- repeatedly reported but intentionally tolerated
- scheduled for later review

Suppression does not mean the condition disappeared.

---

# Suppressed Is Not Resolved

```text
RESOLVED

The condition is no longer considered present.
```

```text
SUPPRESSED

The condition may still exist, but normal attention has deliberately been reduced.
```

Consumers MUST preserve this distinction.

---

# Lifecycle Transitions

Finding v0.1 permits:

```text
active → acknowledged
active → resolved
active → suppressed

acknowledged → active
acknowledged → resolved
acknowledged → suppressed

suppressed → active
suppressed → resolved

resolved → active
```

Consumers MUST NOT assume every transition was initiated manually.

A producer may change state after re-observing reality.

---

# Reactivation

A resolved Finding may become active again if the condition returns.

```text
RESOLVED
   |
   v
CONDITION RETURNS
   |
   v
ACTIVE
```

Whether recurrence reactivates the existing Finding or creates a new Finding is producer policy in v0.1.

---

# State Is Not Severity

These are all valid:

```json
{
  "state": "active",
  "severity": "critical"
}
```

```json
{
  "state": "acknowledged",
  "severity": "critical"
}
```

```json
{
  "state": "suppressed",
  "severity": "warning"
}
```

```json
{
  "state": "resolved",
  "severity": "warning"
}
```

Lifecycle state does not rewrite the Finding's meaning.

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

Where meaningful:

```text
0.0 = minimum confidence
1.0 = maximum confidence
```

A producer which cannot calculate meaningful confidence SHOULD omit the field.

Missing confidence means:

> **No confidence value was supplied.**

It does not mean zero confidence.

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

Initial subject types may include:

```text
asset
storage_location
storage_path
service
network_interface
application
collection
```

The model should grow carefully from actual use.

---

# Subject: `asset`

Example:

```json
{
  "subject": {
    "type": "asset",
    "asset_id": "asset:eddie"
  }
}
```

---

# Subject: `storage_location`

Example:

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

# Subject: `storage_path`

Example:

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

The path SHOULD be relative to the referenced storage location where practical.

Consumers MUST NOT assume that every path uses Windows, Linux, or macOS absolute-path semantics.

---

# Subject: `service`

Example:

```json
{
  "subject": {
    "type": "service",
    "asset_id": "asset:server-42",
    "service_id": "service:web"
  }
}
```

---

# Subject: `network_interface`

Example:

```json
{
  "subject": {
    "type": "network_interface",
    "asset_id": "asset:server-42",
    "interface_id": "interface:eth0"
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

Provides a concise human-readable description of the Finding.

Example:

```json
{
  "summary": "Physical network connection unavailable"
}
```

The summary answers:

> **What did the producer find?**

It is not the sole authoritative representation.

Machine decisions should rely on structured fields and evidence.

---

# Field: `observations`

## Type

```text
array of objects
```

## Required

No.

## Purpose

Provides or references observations which contributed to the Finding.

Example:

```json
{
  "observations": [
    {
      "type": "network.interface.carrier",
      "observed_at": "2026-10-30T07:00:00Z",
      "data": {
        "carrier": false
      }
    }
  ]
}
```

Observations describe what was measured or reported.

Findings describe the producer's interpretation of those observations.

---

# Observation Versus Finding

Example:

```text
OBSERVATION

Network interface reports no carrier.
```

versus:

```text
FINDING

Physical network connection unavailable.
```

versus:

```text
HYPOTHESIS

Cable may be unplugged.
```

These MUST remain distinguishable.

A no-carrier observation does not automatically prove that the cable itself is physically unplugged.

---

# Field: `evidence`

## Type

```text
array of objects
```

## Required

No.

## Purpose

Explains why the Finding exists.

Every evidence object MUST contain:

```text
type
```

Example:

```json
{
  "evidence": [
    {
      "type": "charkive.rule_match",
      "rule": "temporary-file-pattern",
      "description": "Path matched a configured disposable-data rule."
    }
  ]
}
```

---

# Evidence Namespaces

Common evidence types may use general namespaces:

```text
filesystem.metadata
network.interface
network.icmp
system.service_state
hardware.sensor
```

Producer-specific evidence SHOULD use the producer namespace:

```text
charkive.rule_match
charkive.temporal_cluster
sysmedic.event_log
sysmedic.hardware_test
vendor:<vendor>:diagnostic
```

This prevents specialist concepts from pretending to be universally defined.

---

# Evidence Design Principle

A user should eventually be able to ask:

> **Why do you believe this?**

and receive a meaningful answer.

Prefer:

```json
{
  "type": "network.interface",
  "description": "Interface reports no physical carrier."
}
```

over:

```json
{
  "type": "magic"
}
```

Explainability becomes increasingly important as Findings influence real decisions.

---

# Evidence May Contain Uncertainty

Evidence does not need to support one absolute conclusion.

For example:

```text
Recorded timestamp:
2003

Neighbouring activity:
Different period

Known source device:
Camera with unreliable clock
```

may support:

```text
Temporal Anomaly
```

without proving an exact alternative date.

The Finding should preserve that uncertainty.

---

# Field: `recommendations`

## Type

```text
array of objects
```

## Required

No.

## Purpose

Describes possible next steps the producer recommends considering.

Example:

```json
{
  "recommendations": [
    {
      "type": "inspect_physical_connection",
      "summary": "Inspect the physical network connection."
    }
  ]
}
```

Every recommendation MUST contain:

```text
type
```

It SHOULD contain:

```text
summary
```

where a meaningful human-readable description is available.

---

# Recommendations Are Not Actions

This:

```json
{
  "type": "review_for_quarantine"
}
```

does not mean:

```text
QUARANTINE IT
```

Likewise:

```json
{
  "type": "inspect_physical_connection"
}
```

does not mean:

```text
CHANGE NETWORK CONFIGURATION
```

Recommendations inform decisions.

They do not carry execution authority.

---

# Producer-Specific Recommendations

Producer-specific recommendations SHOULD use namespaces.

Examples:

```text
charkive.review_for_quarantine
charkive.review_preservation
sysmedic.run_hardware_test
vna.inspect_physical_connection
```

Generic recommendations may eventually be standardised when several producers share the same semantics.

---

# Field: `relationships`

## Type

```text
array of objects
```

## Required

No.

## Purpose

Links the Finding to related objects.

Examples might include:

```text
related Finding
parent Finding
root-cause Finding
propagated symptom
related asset
related snapshot
related event
```

Example:

```json
{
  "relationships": [
    {
      "type": "caused_by",
      "target_id": "finding:layer1-link-down"
    }
  ]
}
```

Relationship vocabulary should remain small in v0.1.

---

# Root Cause and Propagated Symptoms

Findings may represent either root causes or consequences.

For example:

```text
L1 FINDING

Physical link unavailable
      |
      +----> causes
      |
      v
L2 FINDING

Data-link connectivity unavailable
      |
      v
L3 FINDING

Network connectivity unavailable
      |
      v
L4 FINDING

Transport connection unavailable
      |
      v
L7 FINDING

Application unavailable
```

The ecosystem should preserve the distinction between:

```text
ROOT CAUSE
```

and:

```text
PROPAGATED SYMPTOM
```

This becomes particularly important in VNA gameplay.

---

# Relationship Type: `caused_by`

Example:

```json
{
  "type": "caused_by",
  "target_id": "finding:layer1-link-down"
}
```

This should only be used when the producer has sufficient evidence to assert a causal relationship.

---

# Relationship Type: `possibly_related_to`

Example:

```json
{
  "type": "possibly_related_to",
  "target_id": "finding:packet-loss"
}
```

This is appropriate when correlation exists but causation is not established.

---

# Relationship Type: `propagates_to`

Example:

```json
{
  "type": "propagates_to",
  "target_id": "finding:application-unavailable"
}
```

This may be useful where a lower-level condition has known consequences elsewhere.

---

# Relationship Honesty

Do not turn:

```text
These happened together.
```

into:

```text
This caused that.
```

simply because stronger relationships are convenient for a game or visualisation.

Evidence discipline applies to relationships too.

---

# Field: `production_changed`

## Type

```text
boolean
```

## Required

Yes.

## Purpose

Explicitly records whether creation of the Finding itself changed the real-world system.

Most Findings should say:

```json
{
  "production_changed": false
}
```

Observation should ordinarily be non-destructive.

The field reinforces:

```text
FINDING
≠
ACTION
```

A Finding resulting from a scan, telemetry query, snapshot analysis, simulation, or diagnostic read should normally have:

```text
production_changed = false
```

---

# Why `production_changed` Exists

The ecosystem deliberately separates:

```text
OBSERVE

from

CHANGE
```

A game client or report consuming a Finding should be able to establish that the act of discovering the condition did not itself modify the production system.

If analysis did involve changes, that fact must be explicit.

---

# Field: `attributes`

## Type

```text
object
```

## Required

No.

## Purpose

Allows producer-specific metadata to travel with the Finding without polluting the shared contract.

Example:

```json
{
  "attributes": {
    "charkive": {
      "scan_id": "charkive:scan:1234",
      "classification": "candidate"
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

Shared consumers SHOULD NOT depend on producer-specific attributes for core Finding behaviour.

If several producers repeatedly need the same concept, it may be promoted into a future common schema version.

---

# Minimal Valid Finding

```json
{
  "schema": "vna.finding/0.1",
  "id": "finding:001",
  "producer": {
    "id": "charkive",
    "type": "application"
  },
  "source": {
    "kind": "live"
  },
  "observed_at": "2026-10-30T07:00:00Z",
  "category": "storage.disposable_candidate",
  "severity": "notice",
  "state": "active",
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

# Charkive Example

```json
{
  "schema": "vna.finding/0.1",
  "id": "finding:charkive-001",
  "producer": {
    "id": "charkive",
    "type": "application"
  },
  "source": {
    "kind": "live",
    "id": "charkive:scan:1234"
  },
  "observed_at": "2026-10-30T07:00:00Z",
  "category": "storage.disposable_candidate",
  "severity": "notice",
  "state": "active",
  "confidence": 0.97,
  "subject": {
    "type": "storage_path",
    "asset_id": "asset:eddie",
    "storage_location_id": "storage:eddie:data",
    "path": "Projects/render/cache/frame-0001.tmp"
  },
  "summary": "Likely disposable render-cache data",
  "evidence": [
    {
      "type": "charkive.rule_match",
      "rule": "render-cache",
      "description": "Path matched a configured render-cache rule."
    },
    {
      "type": "filesystem.metadata",
      "size_bytes": 1879048192
    }
  ],
  "recommendations": [
    {
      "type": "charkive.review_for_quarantine",
      "summary": "Review the candidate before reversible quarantine."
    }
  ],
  "production_changed": false,
  "attributes": {
    "charkive": {
      "scan_id": "charkive:scan:1234",
      "classification": "candidate"
    }
  }
}
```

---

# Charkive Temporal Anomaly Example

```json
{
  "schema": "vna.finding/0.1",
  "id": "finding:charkive-ta-001",
  "producer": {
    "id": "charkive",
    "type": "application"
  },
  "source": {
    "kind": "historical",
    "id": "charkive:archaeology:collection-42"
  },
  "observed_at": "2026-10-30T07:00:00Z",
  "category": "charkive.archaeology.temporal_anomaly",
  "severity": "info",
  "state": "active",
  "confidence": 0.81,
  "subject": {
    "type": "collection",
    "storage_location_id": "storage:archive",
    "path": "Photos/Kodak"
  },
  "summary": "Recorded timestamps appear inconsistent with surrounding historical evidence.",
  "evidence": [
    {
      "type": "charkive.recorded_time",
      "description": "Photographs contain timestamps placing them in 2003."
    },
    {
      "type": "charkive.activity_cluster",
      "description": "Surrounding activity suggests the collection may belong to a different period."
    },
    {
      "type": "charkive.source_device",
      "description": "The collection is associated with a camera known to have had unreliable clock settings."
    }
  ],
  "recommendations": [
    {
      "type": "charkive.review_temporal_context",
      "summary": "Review the collection's historical context without changing original metadata."
    }
  ],
  "production_changed": false
}
```

The Finding preserves uncertainty.

It does not silently rewrite the recorded timestamp.

---

# SysMedic Snapshot Example

```json
{
  "schema": "vna.finding/0.1",
  "id": "finding:sysmedic-001",
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
  "category": "system.update.failure",
  "severity": "warning",
  "state": "active",
  "subject": {
    "type": "asset",
    "asset_id": "asset:captured-windows-machine"
  },
  "summary": "Evidence indicates that a Windows update did not complete successfully.",
  "evidence": [
    {
      "type": "sysmedic.event_log",
      "description": "Relevant update failure evidence was captured from the source system."
    }
  ],
  "recommendations": [
    {
      "type": "sysmedic.review_update_state",
      "summary": "Review the captured Windows Update evidence."
    }
  ],
  "production_changed": false,
  "attributes": {
    "sysmedic": {
      "collection_id": "SM-20261030-0733"
    }
  }
}
```

The source is explicitly:

```text
snapshot
```

Therefore a consumer MUST NOT present the Finding as proof that the condition still exists on the machine now.

---

# T4 Network Example

```json
{
  "schema": "vna.finding/0.1",
  "id": "finding:t4-network-001",
  "producer": {
    "id": "t4t",
    "type": "application"
  },
  "source": {
    "kind": "live",
    "id": "marvin:network-scan:1234"
  },
  "observed_at": "2026-10-30T07:00:00Z",
  "category": "network.response.degraded",
  "severity": "warning",
  "state": "active",
  "confidence": 0.98,
  "subject": {
    "type": "asset",
    "asset_id": "asset:network-device-42"
  },
  "summary": "Network response is slow and some probes were lost.",
  "observations": [
    {
      "type": "network.icmp.rtt",
      "observed_at": "2026-10-30T07:00:00Z",
      "data": {
        "value": 187,
        "unit": "ms"
      }
    },
    {
      "type": "network.icmp.packet_loss",
      "observed_at": "2026-10-30T07:00:00Z",
      "data": {
        "value": 25,
        "unit": "percent"
      }
    }
  ],
  "production_changed": false
}
```

T4 can then explain this same Finding as:

```text
T4Tots
T4Teens
T4Techies
```

without altering the Finding itself.

---

# VNA OSI Example

```json
{
  "schema": "vna.finding/0.1",
  "id": "finding:vna-layer1-001",
  "producer": {
    "id": "vna",
    "type": "application"
  },
  "source": {
    "kind": "synthetic",
    "id": "tutorial:unplugged-cable"
  },
  "observed_at": "2026-10-30T07:00:00Z",
  "category": "network.physical.link_down",
  "severity": "warning",
  "state": "active",
  "confidence": 1.0,
  "subject": {
    "type": "network_interface",
    "asset_id": "asset:tutorial-office-b",
    "interface_id": "interface:ethernet-1"
  },
  "summary": "Physical network connection unavailable.",
  "observations": [
    {
      "type": "network.interface.carrier",
      "observed_at": "2026-10-30T07:00:00Z",
      "data": {
        "carrier": false
      }
    }
  ],
  "recommendations": [
    {
      "type": "vna.inspect_physical_connection",
      "summary": "Inspect the Layer 1 physical connection."
    }
  ],
  "production_changed": false
}
```

The tutorial source is explicitly synthetic.

No consumer should present it as a real infrastructure incident.

---

# Propagated OSI Finding Example

A Layer 7 application symptom might reference the Layer 1 Finding.

```json
{
  "schema": "vna.finding/0.1",
  "id": "finding:vna-layer7-001",
  "producer": {
    "id": "vna",
    "type": "application"
  },
  "source": {
    "kind": "synthetic",
    "id": "tutorial:unplugged-cable"
  },
  "observed_at": "2026-10-30T07:00:00Z",
  "category": "application.unavailable",
  "severity": "warning",
  "state": "active",
  "subject": {
    "type": "application",
    "asset_id": "asset:tutorial-office-b",
    "application_id": "application:tutorial-service"
  },
  "summary": "Application communication is unavailable.",
  "relationships": [
    {
      "type": "caused_by",
      "target_id": "finding:vna-layer1-001"
    }
  ],
  "production_changed": false
}
```

This allows VNA to show:

```text
L7 symptom
   |
   v
L1 root cause
```

without pretending every failed layer is independently broken.

---

# FileCity Consumption

FileCity consumes Findings without becoming their producer.

For example:

```text
VNA Finding
    |
    v
Subject:
storage:eddie:data
Projects/cache
    |
    v
FILECITY
    |
    v
Eddie District
Projects Tower
Cache Floor
```

FileCity owns:

```text
district
building
floor
route
visual state
```

The Finding owns:

```text
condition
severity
state
evidence
provenance
```

FileCity MUST retain a reference to the original Finding.

---

# FileCity Visualisation Rule

A FileCity state change should remain traceable:

```text
VISUAL WARNING
      |
      v
WORLD OBJECT
      |
      v
FINDING
      |
      v
EVIDENCE
      |
      v
SOURCE
```

This implements the ecosystem rule:

> **Never hide reality behind the metaphor.**

---

# T4 Consumption

T4 consumes Findings and produces audience-appropriate explanations.

Conceptually:

```text
                   FINDING
                      |
                      v
                     T4
                      |
          ┌───────────┼───────────┐
          |           |           |
          v           v           v
       T4Tots      T4Teens    T4Techies
```

T4 MUST NOT:

```text
change category
change severity
change state
invent observations
invent certainty
erase provenance
```

merely to simplify language.

---

# BoreDOOM! and Gameplay Consumption

Game systems may convert Findings into:

```text
mission
objective
incident
visual state
story element
```

The game representation is not authoritative.

```text
FINDING
   |
   v
GENERALISATION
   |
   v
MISSION
   |
   v
PLAYER
```

Completing the mission does not automatically resolve the Finding.

Only the authoritative producer or VNA orchestration layer may determine that reality has changed sufficiently for the Finding to become resolved.

---

# Gameplay Is Not Resolution

This MUST NOT happen:

```text
PLAYER CLICKS "FIX"
      |
      v
FINDING = RESOLVED
```

Instead:

```text
PLAYER PROPOSES ACTION
      |
      v
ACTION AUTHORISED
      |
      v
ACTION EXECUTED
      |
      v
RESULT VERIFIED
      |
      v
REALITY RE-OBSERVED
      |
      v
FINDING RESOLVED
```

The game cannot wish reality healthy.

---

# Event Relationship

Finding and Event remain separate contracts.

A Finding says:

```text
This condition exists.
```

An Event says:

```text
Something happened.
```

Examples:

```text
finding created
finding acknowledged
finding suppressed
finding resolved
finding reactivated
action proposed
action executed
action failed
verification completed
```

Lifecycle transitions SHOULD eventually generate Events.

---

# Suggested Finding Lifecycle Events

Potential Event types include:

```text
vna.finding.created
vna.finding.acknowledged
vna.finding.suppressed
vna.finding.resolved
vna.finding.reactivated
```

These belong to the Event contract.

They do not require additional timestamp fields to be added to every Finding.

This maintains:

```text
Finding
= current condition and disposition

Event
= something happened at a point in time
```

---

# Finding Is Not Observation

An Observation says:

```text
Interface reports carrier=false.
```

A Finding says:

```text
Physical network connection unavailable.
```

An Observation may exist without producing a Finding.

A Finding may reference several Observations.

---

# Finding Is Not Recommendation

A Finding says:

```text
Storage appears critically constrained.
```

A Recommendation says:

```text
Review disposable-data candidates.
```

These are distinct.

---

# Finding Is Not Proposed Action

A Finding says:

```text
Possible disposable data exists.
```

A Proposed Action says:

```text
Request reversible quarantine of these reviewed candidates.
```

The Proposed Action requires separate validation and authorisation.

---

# Finding Is Not Verified Result

A Verified Result says:

```text
The requested operation executed and its expected outcome was independently verified.
```

A Finding may be re-evaluated after that result.

The Verified Result does not itself silently rewrite the Finding.

---

# Lifecycle and Events

The Finding object carries current state:

```text
active
acknowledged
resolved
suppressed
```

The Event stream records transitions.

Example:

```text
Finding
state = active

        ↓

Event
vna.finding.acknowledged

        ↓

Finding
state = acknowledged

        ↓

Event
vna.finding.resolved

        ↓

Finding
state = resolved
```

This allows consumers to reconstruct history without bloating the current Finding record.

---

# Historical Findings

Resolved Findings SHOULD remain available for appropriate historical inspection.

They may be useful for:

```text
trend analysis
training
audit
incident review
Data Archaeology
VNA world history
repeated-fault detection
```

Resolved does not mean forgotten.

---

# Temporal Findings

Findings involving time should preserve different kinds of temporal knowledge.

The wider ecosystem may need to distinguish:

```text
recorded time
observed time
inferred time
user-confirmed time
```

These detailed semantics may live inside producer-specific evidence until a common Temporal model is justified.

No producer should silently replace recorded evidence with inferred history.

---

# Privacy and Generalisation

Findings may contain sensitive data.

Examples include:

```text
hostnames
paths
user-created filenames
network identities
device identities
infrastructure relationships
vendor equipment
historical activity
```

Before Findings reach clients that do not need full fidelity, VNA may generalise them.

Conceptually:

```text
REAL FINDING

Host:
EDDIE

Path:
/srv/private/archive

        |
        v

GENERALISATION

Asset:
SERVER-104

Location:
Archive District
```

The generalised representation should preserve the technical shape of the condition while removing unnecessary identity information.

---

# Generalisation Does Not Change Meaning

Generalisation may change:

```text
identity
names
paths
topology precision
presentation
```

It MUST NOT silently change:

```text
category
severity
state
technical meaning
source kind
confidence
```

unless the generalised Finding explicitly records that transformation.

---

# Security Boundary

Findings are data.

They are not executable commands.

All Finding fields MUST be treated as untrusted input at application boundaries.

This includes:

```text
id
summary
path
evidence
attributes
producer values
recommendation text
relationship IDs
```

Consumers MUST NOT interpolate Finding content directly into:

```text
shell commands
SQL
filesystem operations
HTML
privileged APIs
game scripting
```

without appropriate validation, escaping, and authorisation.

---

# Transport Independence

Finding v0.1 defines an object.

It does not mandate a transport.

Possible transports include:

```text
JSON file
HTTP API
local IPC
event stream
message queue
snapshot package
test fixture
database record
```

The meaning should remain the same.

---

# Validation Rules

A valid Finding v0.1 MUST satisfy:

1. `schema` equals `vna.finding/0.1`.
2. `id` is a non-empty string.
3. `producer` is an object.
4. `producer.id` is a non-empty string.
5. `producer.type` is a non-empty string.
6. `source` is an object.
7. `source.kind` is one of the recognised v0.1 source kinds.
8. `observed_at` is a valid UTC ISO 8601 timestamp.
9. `category` is a non-empty string.
10. `severity` is one of the recognised v0.1 severity values.
11. `state` is one of the recognised v0.1 lifecycle states.
12. `subject` is an object.
13. `subject.type` is a non-empty string.
14. `summary` is a non-empty string.
15. `production_changed` is a boolean.
16. If supplied, `confidence` is between `0.0` and `1.0`.
17. If supplied, `observations` is an array.
18. If supplied, `evidence` is an array.
19. Every evidence item contains a non-empty `type`.
20. If supplied, `recommendations` is an array.
21. Every recommendation contains a non-empty `type`.
22. If supplied, `relationships` is an array.
23. Every relationship contains `type` and `target_id`.
24. If supplied, `attributes` is an object.

Consumers SHOULD reject structurally invalid Findings rather than silently repairing them.

---

# Compatibility Rules

## Producer

A v0.1 producer:

- MUST provide all required fields
- MAY provide optional fields
- MAY provide additional unknown fields
- SHOULD retain stable Finding IDs where appropriate
- SHOULD preserve evidence provenance
- SHOULD emit `production_changed: false` for read-only analysis
- MUST distinguish real and synthetic source kinds

## Consumer

A v0.1 consumer:

- MUST validate fields it depends upon
- MUST understand the v0.1 severity vocabulary
- MUST understand the v0.1 lifecycle vocabulary
- SHOULD ignore unknown fields
- SHOULD preserve unknown fields when forwarding Findings
- MUST NOT invent semantics for unknown categories
- MUST NOT execute recommendations automatically
- MUST NOT assume missing confidence means zero confidence
- MUST preserve producer identity
- MUST preserve source kind
- MUST treat all Finding content as untrusted input

---

# v0.1 Deliberate Omissions

Finding v0.1 intentionally does not fully define:

```text
revision numbers
optimistic concurrency
assignment
ownership
suppression expiry
acknowledgement comments
resolution reasons
automatic deduplication
correlation algorithms
multi-subject conditions
professional authority
credential requirements
action execution
renderer behaviour
game missions
transport protocol
authentication
authorisation
```

These should be added only when real implementation work demonstrates the need.

---

# First Implementation Target

The first implementation should remain deliberately small.

Choose one existing real producer.

T4T/MARVIN is a good candidate because it already has live observations.

For example:

```text
MARVIN
   |
   v
ICMP OBSERVATION
   |
   v
vna.finding/0.1
   |
   v
T4
   |
   ├── T4Tots
   ├── T4Teens
   └── T4Techies
```

Success means:

> Three radically different explanations consume the same Finding ID and evidence.

---

# Second Implementation Target

Charkive:

```text
REAL STORAGE
     |
     v
CHARKIVE
     |
     v
vna.finding/0.1
     |
     v
FILECITY
     |
     v
VISIBLE CONDITION
     |
     v
USER INSPECTS
     |
     v
ORIGINAL FINDING + EVIDENCE
```

No filesystem action is required.

---

# Third Implementation Target

SysMedic:

```text
RUNNING OR OFFLINE MACHINE
          |
          v
      SYSMEDIC
          |
          v
ENCRYPTED SNAPSHOT
          |
          v
       IMPORT
          |
          v
      ANALYSIS
          |
          v
vna.finding/0.1
          |
          v
        VNA
```

The Finding retains:

```text
source.kind = snapshot
```

so downstream consumers know they are investigating captured state.

---

# Fourth Implementation Target

VNA synthetic OSI tutorial:

```text
SYNTHETIC WORLD
      |
      v
L1 LINK FAILURE
      |
      v
ROOT-CAUSE FINDING
      |
      v
PROPAGATED FINDINGS
      |
      v
OSI TOWER
      |
      v
PLAYER INVESTIGATION
```

The same Finding contract now works for:

```text
real telemetry
real storage
captured machines
synthetic training
```

At that point the shared model has proved its value.

---

# North Star

A Finding should always make it possible to answer:

```text
WHAT did we find?

WHO found it?

WHAT kind of source produced it?

WHAT does it affect?

WHEN was it observed?

HOW serious is it?

IS it still active?

HAS somebody acknowledged it?

HAS it deliberately been suppressed?

HAS reality shown it to be resolved?

HOW confident are we?

WHAT observations support it?

WHAT evidence supports the interpretation?

WHAT other Findings relate to it?

WHAT do we recommend considering?

DID observing it change reality?
```

And every consumer should remember:

> **A Finding reports something about reality.**

> **It does not grant permission to change reality.**

That distinction is the foundation on which the rest of the VNA ecosystem can safely become much more ambitious.
