# VNA Proposed Action Schema v0.1

> **A request to change reality is not permission to change reality.**

## Purpose

A **Proposed Action** is the common VNA ecosystem representation of a request for something to happen to a real, captured, synthetic, or otherwise modelled target.

It is the explicit boundary between:

```text
UNDERSTANDING REALITY
```

and:

```text
REQUESTING A CHANGE TO REALITY
```

The ecosystem deliberately separates:

```text
OBSERVATION
"What did we see?"

FINDING
"What noteworthy condition exists?"

RECOMMENDATION
"What might be worth considering?"

PROPOSED ACTION
"What change is being requested?"

AUTHORISATION
"Is this change permitted?"

EXECUTION
"What was actually attempted?"

VERIFICATION
"What actually happened?"
```

A Proposed Action is therefore a **request**.

It is never proof that:

```text
the action is valid
the action is safe
the requester is authorised
the target still exists
the target is still in the expected state
the action has started
the action has completed
the action has succeeded
```

---

# Core Principle

> **Proposal is not authorisation.**

The complete safe flow is:

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
PREVIEW / DRY RUN
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

Different providers may require different steps.

The Proposed Action contract does not bypass any of them.

---

# Ecosystem Position

The common model now supports:

```text
ASSET
  |
STORAGE LOCATION
  |
OBSERVATION
  |
FINDING
  |
RECOMMENDATION
  |
PROPOSED ACTION
  |
AUTHORITATIVE PROVIDER
  |
VERIFIED RESULT
```

The important boundary is:

```text
READ SIDE

Asset
Observation
Finding
Relationship
Snapshot

        |
        v

ACTION BOUNDARY

Proposed Action

        |
        v

WRITE SIDE

Validation
Authorisation
Execution
Verification
```

The existence of a Proposed Action does not mean the write side has accepted it.

---

# Canonical File Location

Save this contract at:

```text
vna/docs/contracts/PROPOSED_ACTION.md
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

Every Proposed Action MUST declare:

```json
{
  "schema": "vna.proposed_action/0.1"
}
```

The canonical identifier is:

```text
vna.proposed_action/0.1
```

Consumers MUST NOT assume future versions are structurally identical.

Consumers SHOULD ignore unknown fields unless those fields are explicitly required by a schema version they implement.

---

# Canonical Example

```json
{
  "schema": "vna.proposed_action/0.1",
  "id": "action:charkive-quarantine-001",
  "type": "charkive.quarantine",
  "provider": {
    "id": "charkive",
    "type": "application"
  },
  "requester": {
    "type": "user",
    "id": "user:local"
  },
  "requested_at": "2026-10-30T07:20:00Z",
  "source": {
    "kind": "live"
  },
  "targets": [
    {
      "type": "finding",
      "id": "finding:charkive-001"
    }
  ],
  "reason": "Reviewed disposable-data candidate selected for reversible quarantine.",
  "mode": "dry_run",
  "state": "proposed",
  "safety": {
    "reversible_expected": true,
    "verification_required": true,
    "confirmation_required": true
  },
  "preconditions": [
    {
      "type": "finding_state",
      "finding_id": "finding:charkive-001",
      "expected": "active"
    }
  ],
  "parameters": {},
  "production_changed": false,
  "attributes": {}
}
```

This means:

> A user has requested that Charkive evaluate a dry-run quarantine operation against a Finding.

It does NOT mean:

> Charkive has accepted, authorised, or executed the quarantine.

---

# Required Fields

Proposed Action v0.1 requires:

```text
schema
id
type
provider
requester
requested_at
source
targets
mode
state
production_changed
```

Optional fields include:

```text
reason
finding_ids
recommendation_ids
safety
preconditions
parameters
expires_at
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
vna.proposed_action/0.1
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

Provides the stable identity of this Proposed Action.

Example:

```json
{
  "id": "action:charkive-quarantine-001"
}
```

Consumers MUST treat the ID as opaque.

The ID represents this particular request.

A later retry, changed target, changed parameter set, or materially different request SHOULD normally receive a new ID.

---

# Proposed Action Identity Is Not Operation Identity

A Proposed Action identifies the request.

The authoritative provider may later create:

```text
execution record
provider operation ID
verified result
events
```

Those are related objects.

They are not the Proposed Action itself.

Conceptually:

```text
action:proposal-001
       |
       v
provider-operation:987
       |
       v
result:001
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

Identifies the requested operation.

Common operation types should exist only where semantics are genuinely shared.

Specialist operations SHOULD use provider namespaces.

Examples:

```text
charkive.quarantine
charkive.restore
charkive.retry_preflight
vna.service_restart
vna.asset_reinspect
filecity.copy
filecity.move
sysmedic.run_test
```

The type describes the requested capability.

It does not prove that the provider supports it.

---

# Action Type Ownership

The provider responsible for executing an operation owns its operational semantics.

For example:

```text
charkive.quarantine
```

is defined by Charkive.

VNA may transport the proposal.

FileCity may present it.

BoreDOOM! may allow a player to request it.

T4 may explain it.

Only Charkive decides what constitutes a valid Charkive quarantine operation.

---

# Field: `provider`

## Type

```text
object
```

## Required

Yes.

## Purpose

Identifies the authoritative system expected to evaluate the Proposed Action.

Example:

```json
{
  "provider": {
    "id": "charkive",
    "type": "application"
  }
}
```

## Required Provider Fields

```text
id
type
```

## Optional Provider Fields

```text
instance
version
```

---

# Provider Is the Safety Authority for Its Operation

The provider receives the request.

The provider decides whether it is:

```text
understood
supported
currently applicable
safe
authorised
properly confirmed
valid for the target
```

For example:

```text
FileCity
    |
    | proposes
    v
charkive.quarantine
    |
    v
CHARKIVE
```

FileCity does not become authorised to reproduce Charkive's filesystem operations internally.

---

# Field: `requester`

## Type

```text
object
```

## Required

Yes.

## Purpose

Identifies who or what requested the Proposed Action.

Initial requester types may include:

```text
user
application
game
automation
oracle
policy
```

Example:

```json
{
  "requester": {
    "type": "user",
    "id": "user:local"
  }
}
```

---

# Requester Is Not Authoriser

This distinction is essential.

```text
REQUESTER

"I would like this to happen."
```

is not necessarily:

```text
AUTHORISER

"This operation is permitted."
```

The same human or system may eventually hold both roles.

The schema does not assume that they do.

---

# Game Requester Example

A future VNA game client might propose:

```json
{
  "requester": {
    "type": "game",
    "id": "vna-game"
  }
}
```

This explicitly identifies that the request originated from gameplay.

The provider still performs normal validation and authorisation.

There is no special:

```text
but-it-was-fun-so-skip-the-safety-checks
```

mode.

---

# Oracle Requester Example

An Oracle may propose an action for review.

Example:

```json
{
  "requester": {
    "type": "oracle",
    "id": "oracle:primary"
  }
}
```

This does not grant authority.

AI recommendation and operational authorisation remain separate.

---

# Automation Requester Example

Automation may eventually create proposals.

Example:

```json
{
  "requester": {
    "type": "automation",
    "id": "automation:capacity-policy"
  }
}
```

Automated proposal generation does not imply automated execution.

---

# Field: `requested_at`

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
  "requested_at": "2026-10-30T07:20:00Z"
}
```

This records when the proposal was created.

It is not:

```text
authorised_at
executed_at
completed_at
verified_at
```

Those belong to future Events or Verified Results.

---

# Field: `source`

## Type

```text
object
```

## Required

Yes.

## Purpose

Describes the reality context against which the Proposed Action was created.

Initial values for `kind` include:

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

# Live Proposed Actions

A Proposed Action with:

```json
{
  "source": {
    "kind": "live"
  }
}
```

may potentially target current production reality.

That does not mean it is authorised.

Live actions require the strongest current-state validation.

---

# Snapshot Proposed Actions

A proposal may originate while investigating a Snapshot.

Example:

```json
{
  "source": {
    "kind": "snapshot",
    "id": "snapshot:SM-20261030-0733"
  }
}
```

This must NOT automatically become an operation against a live system.

The Snapshot describes captured state.

Reality may have changed.

---

# Snapshot-to-Live Safety Boundary

This is forbidden:

```text
SNAPSHOT FINDING
      |
      v
"Fix"
      |
      v
LIVE SYSTEM CHANGE
```

without fresh live validation.

The safe flow is:

```text
SNAPSHOT FINDING
      |
      v
PROPOSED LIVE ACTION
      |
      v
IDENTIFY CURRENT TARGET
      |
      v
RE-OBSERVE CURRENT STATE
      |
      v
VALIDATE PRECONDITIONS
      |
      v
AUTHORISE
      |
      v
EXECUTE
```

Snapshot evidence informs the proposal.

It does not prove current applicability.

---

# Synthetic Proposed Actions

A training environment may use:

```json
{
  "source": {
    "kind": "synthetic"
  }
}
```

The resulting action should remain inside the synthetic environment unless explicitly bridged through a separate authorised live workflow.

Completing a tutorial must never accidentally restart a real server.

---

# Generalised Proposed Actions

A less-trusted client may know only:

```text
SERVER-104
```

rather than:

```text
EDDIE
```

VNA may translate a proposal back through an authorised reverse mapping.

The game or external client does not need access to the real identity.

The operational layer must still validate the real target before execution.

---

# Field: `targets`

## Type

```text
array of objects
```

## Required

Yes.

## Purpose

Identifies the object or objects the Proposed Action concerns.

Every target MUST contain:

```text
type
id
```

Example:

```json
{
  "targets": [
    {
      "type": "finding",
      "id": "finding:charkive-001"
    }
  ]
}
```

---

# Initial Target Types

Potential common target types include:

```text
asset
storage_location
storage_path
finding
relationship
snapshot
service
application
```

Provider-specific target types may also exist.

---

# Prefer Stable Targets

Where possible, proposals should target stable ecosystem identities.

Prefer:

```text
asset:eddie
```

over:

```text
192.168.1.17
```

Prefer:

```text
storage:eddie:data
+
logical path
```

over:

```text
/srv/data
```

Prefer:

```text
finding:charkive-001
```

over duplicating the Finding's current text.

Stable identities reduce accidental action against the wrong object.

---

# Targeting a Finding

A Proposed Action may target a Finding.

Example:

```json
{
  "targets": [
    {
      "type": "finding",
      "id": "finding:charkive-001"
    }
  ]
}
```

The provider must resolve the Finding to its current underlying subject.

The Finding ID itself is not sufficient authority to operate.

---

# Targeting a Storage Path

A more explicit storage action may target:

```json
{
  "targets": [
    {
      "type": "storage_path",
      "storage_location_id": "storage:eddie:data",
      "path": "Projects/cache/example.tmp"
    }
  ]
}
```

The provider MUST validate the logical path before translating it into a host filesystem operation.

---

# Path Safety

A `storage_path` target is untrusted input until validated.

Providers MUST prevent:

```text
path traversal
escaping the Storage Location root
unexpected symlink traversal
provider-specific path confusion
encoding tricks
case-sensitivity surprises
```

where applicable.

The common contract does not replace provider-specific path safety.

---

# Multi-Target Actions

A Proposed Action may contain multiple targets where the provider supports a batch operation.

Example:

```json
{
  "targets": [
    {
      "type": "finding",
      "id": "finding:001"
    },
    {
      "type": "finding",
      "id": "finding:002"
    }
  ]
}
```

The provider MUST define whether:

```text
all targets succeed or none
partial success is permitted
each target is independently verified
```

The proposal itself does not assume transaction semantics.

---

# Field: `reason`

## Type

```text
string
```

## Required

No.

## Purpose

Provides a human-readable reason for the request.

Example:

```json
{
  "reason": "Reviewed candidate selected for reversible quarantine."
}
```

Reasons are useful for:

```text
audit
review
authorisation
operator context
reports
```

The reason does not override policy.

---

# Action Reason Is Not Evidence

This:

```text
"I think this file is rubbish."
```

does not make the Finding true.

Reasons describe requester intent.

Evidence belongs to Observations and Findings.

---

# Field: `finding_ids`

## Type

```text
array of strings
```

## Required

No.

## Purpose

Associates the proposal with Findings that motivated it.

Example:

```json
{
  "finding_ids": [
    "finding:storage-capacity-001"
  ]
}
```

This provides traceability:

```text
FINDING
   |
   v
PROPOSED ACTION
```

A proposal may exist without a Finding, for example when a user explicitly requests a maintenance operation.

---

# Field: `recommendation_ids`

## Type

```text
array of strings
```

## Required

No.

## Purpose

Associates the proposal with recommendation objects or identifiers where the implementation models recommendations independently.

Finding v0.1 currently permits recommendations embedded within Findings.

Until a dedicated Recommendation contract exists, producers MAY omit this field.

---

# Field: `mode`

## Type

```text
string
```

## Required

Yes.

## Allowed v0.1 Values

```text
preview
dry_run
execute
```

---

# Mode: `preview`

The requester wants to understand what the provider believes the operation would involve.

No real-world change should occur.

Example:

```text
Which files would be affected?

What service would restart?

What data would move?

What validation would apply?
```

---

# Mode: `dry_run`

The provider should perform whatever non-destructive validation and simulation it supports without committing the real operation.

This may involve more provider logic than a simple preview.

A dry run MUST NOT silently become execution.

---

# Mode: `execute`

The requester is proposing a real operation.

This still does not mean the operation may proceed.

The provider must validate:

```text
target
current state
authorisation
confirmation
policy
preconditions
safety controls
```

before execution.

---

# Execute Is Still Only a Proposal

This distinction is central.

```json
{
  "mode": "execute"
}
```

means:

> The requester is asking for real execution.

It does NOT mean:

> Execute immediately without further checks.

---

# Field: `state`

## Type

```text
string
```

## Required

Yes.

## Initial v0.1 Value

The canonical initial state is:

```text
proposed
```

A Proposed Action begins:

```json
{
  "state": "proposed"
}
```

Further action lifecycle should primarily be represented through Events and Verified Results rather than continuously expanding the proposal object into an execution record.

---

# Proposed Action Is Intentionally Boring

The Proposed Action should remain the frozen record of:

```text
what was requested
by whom
when
for which target
in which mode
with what parameters
```

Later events can say:

```text
validated
rejected
authorised
started
completed
failed
cancelled
```

The proposal remains the original request.

This improves auditability.

---

# Proposed Action Immutability

Once submitted to an authoritative provider, the Proposed Action SHOULD normally be treated as immutable.

If the requester changes:

```text
targets
parameters
reason
mode
preconditions
```

materially, create a new Proposed Action.

Do not silently rewrite the historical request after the decision has been made.

---

# Field: `safety`

## Type

```text
object
```

## Required

No.

## Purpose

Records safety expectations requested or known by the requester.

Example:

```json
{
  "safety": {
    "reversible_expected": true,
    "verification_required": true,
    "confirmation_required": true
  }
}
```

These are expectations.

The provider owns the authoritative safety policy.

---

# `reversible_expected`

## Type

```text
boolean
```

Example:

```json
{
  "reversible_expected": true
}
```

Meaning:

> The requester expects this operation to be reversible.

The provider MUST reject or escalate if it cannot honour that expectation where the expectation is material to safety.

---

# `verification_required`

## Type

```text
boolean
```

Example:

```json
{
  "verification_required": true
}
```

Meaning:

> The requester requires the outcome to be verified before success is reported.

For real VNA operations, this SHOULD normally be true.

---

# `confirmation_required`

## Type

```text
boolean
```

Meaning:

> The requester expects an explicit confirmation boundary before consequential execution.

The provider may require confirmation even if this field is false or absent.

The requester cannot weaken provider policy.

---

# Provider Safety Overrides Requester Convenience

If the requester says:

```json
{
  "confirmation_required": false
}
```

but Charkive policy requires typed acknowledgement, Charkive still requires typed acknowledgement.

Likewise, if VNA policy requires:

```text
current-state validation
```

the requester cannot disable it through parameters.

---

# Field: `preconditions`

## Type

```text
array of objects
```

## Required

No.

## Purpose

Describes conditions expected to remain true before execution.

Example:

```json
{
  "preconditions": [
    {
      "type": "finding_state",
      "finding_id": "finding:charkive-001",
      "expected": "active"
    }
  ]
}
```

Preconditions defend against stale decisions.

---

# Why Preconditions Matter

Suppose:

```text
07:00

Finding:
Service stopped
```

The player investigates for ten minutes.

At:

```text
07:10
```

they request:

```text
Restart service
```

But meanwhile the service recovered at:

```text
07:05
```

Without precondition validation, VNA might perform an unnecessary restart.

The safer path is:

```text
PROPOSAL
   |
   v
VALIDATE CURRENT STATE
   |
   v
SERVICE ALREADY RUNNING
   |
   v
REJECT / NO-OP
```

---

# Precondition Type: `finding_state`

Example:

```json
{
  "type": "finding_state",
  "finding_id": "finding:001",
  "expected": "active"
}
```

The provider should verify the Finding is still applicable.

---

# Precondition Type: `observation_matches`

Example:

```json
{
  "type": "observation_matches",
  "subject_id": "asset:server-1",
  "observation_type": "system.service.state",
  "expected": {
    "state": "stopped"
  }
}
```

The exact provider implementation may vary.

The principle is:

> Recheck the assumption before acting.

---

# Precondition Type: `relationship_exists`

Example:

```json
{
  "type": "relationship_exists",
  "relationship_id": "relationship:service-host"
}
```

This can help ensure that topology assumptions remain current.

---

# Precondition Type: `target_unchanged`

A provider may support a stronger target-state check.

For example:

```json
{
  "type": "target_unchanged",
  "fingerprint": "provider-defined-state-token"
}
```

Precise semantics are provider-specific.

---

# Preconditions Fail Closed

If an important precondition cannot be evaluated safely, the provider SHOULD normally reject or pause the operation rather than guessing.

Prefer:

```text
Cannot confirm target state.

Action not executed.
```

over:

```text
Probably fine.
```

---

# Field: `parameters`

## Type

```text
object
```

## Required

No.

## Purpose

Provides operation-specific inputs.

Example:

```json
{
  "parameters": {
    "destination": "storage:eddie:sanitorium"
  }
}
```

Parameter semantics belong to the action provider.

The common VNA contract does not attempt to define every provider operation.

---

# Parameters Are Untrusted Input

Providers MUST validate every parameter before use.

Parameters MUST NOT be interpolated directly into:

```text
shell commands
filesystem paths
SQL
remote commands
API calls
configuration files
```

without appropriate validation and escaping.

---

# Parameters Must Not Carry Credentials

Avoid putting:

```text
password
token
private key
cloud credential
recovery secret
```

inside Proposed Action parameters.

The authoritative provider should use an appropriate credential-management boundary.

---

# Field: `expires_at`

## Type

```text
string
```

## Required

No.

## Format

UTC ISO 8601 timestamp.

## Purpose

Allows a proposal to declare that it should not remain actionable indefinitely.

Example:

```json
{
  "expires_at": "2026-10-30T07:30:00Z"
}
```

A provider MUST NOT execute an expired proposal.

---

# Why Expiry Matters

A request made against live state becomes increasingly stale.

For example:

```text
07:00
Disk 96% full

07:05
Proposal created

09:00
Someone frees 400 GB

14:00
Old proposal executes
```

That is precisely the sort of action VNA should avoid.

Expiry complements precondition checking.

It does not replace it.

---

# Field: `production_changed`

## Type

```text
boolean
```

## Required

Yes.

## Initial Required Value

For a newly created Proposed Action:

```json
{
  "production_changed": false
}
```

Creating the proposal itself must not be represented as changing production.

If execution later changes production, that belongs to Events and Verified Results.

---

# Proposed Action Is Not Execution

This distinction should remain painfully obvious:

```text
PROPOSED ACTION CREATED

production_changed = false
```

Later:

```text
EXECUTION EVENT

operation attempted
```

Later:

```text
VERIFIED RESULT

production_changed = true
verified = true
```

The Proposal does not retroactively become the execution record.

---

# Field: `attributes`

## Type

```text
object
```

## Required

No.

## Purpose

Provides an extension area for requester/provider-specific metadata.

Example:

```json
{
  "attributes": {
    "charkive": {
      "risk_tier": "reviewed"
    }
  }
}
```

or:

```json
{
  "attributes": {
    "vna": {
      "mission_id": "mission:layer1-001"
    }
  }
}
```

Common consumers SHOULD NOT depend upon producer-specific attributes for core Proposed Action semantics.

---

# Minimal Valid Proposed Action

```json
{
  "schema": "vna.proposed_action/0.1",
  "id": "action:001",
  "type": "charkive.quarantine",
  "provider": {
    "id": "charkive",
    "type": "application"
  },
  "requester": {
    "type": "user",
    "id": "user:local"
  },
  "requested_at": "2026-10-30T07:20:00Z",
  "source": {
    "kind": "live"
  },
  "targets": [
    {
      "type": "finding",
      "id": "finding:001"
    }
  ],
  "mode": "dry_run",
  "state": "proposed",
  "production_changed": false
}
```

---

# Charkive Dry-Run Example

```json
{
  "schema": "vna.proposed_action/0.1",
  "id": "action:charkive-quarantine-preview-001",
  "type": "charkive.quarantine",
  "provider": {
    "id": "charkive",
    "type": "application"
  },
  "requester": {
    "type": "user",
    "id": "user:local"
  },
  "requested_at": "2026-10-30T07:20:00Z",
  "source": {
    "kind": "live"
  },
  "targets": [
    {
      "type": "finding",
      "id": "finding:charkive-001"
    }
  ],
  "reason": "Preview reversible quarantine of the reviewed disposable-data candidate.",
  "finding_ids": [
    "finding:charkive-001"
  ],
  "mode": "dry_run",
  "state": "proposed",
  "safety": {
    "reversible_expected": true,
    "verification_required": true,
    "confirmation_required": true
  },
  "preconditions": [
    {
      "type": "finding_state",
      "finding_id": "finding:charkive-001",
      "expected": "active"
    }
  ],
  "production_changed": false
}
```

Expected effect:

```text
Charkive validates the candidate.

Charkive previews what would happen.

No file moves.

No production change occurs.
```

---

# Charkive Execute Example

After review, a separate proposal may request real execution.

```json
{
  "schema": "vna.proposed_action/0.1",
  "id": "action:charkive-quarantine-execute-001",
  "type": "charkive.quarantine",
  "provider": {
    "id": "charkive",
    "type": "application"
  },
  "requester": {
    "type": "user",
    "id": "user:local"
  },
  "requested_at": "2026-10-30T07:25:00Z",
  "source": {
    "kind": "live"
  },
  "targets": [
    {
      "type": "finding",
      "id": "finding:charkive-001"
    }
  ],
  "reason": "Reviewed candidate approved for reversible quarantine.",
  "finding_ids": [
    "finding:charkive-001"
  ],
  "mode": "execute",
  "state": "proposed",
  "safety": {
    "reversible_expected": true,
    "verification_required": true,
    "confirmation_required": true
  },
  "preconditions": [
    {
      "type": "finding_state",
      "finding_id": "finding:charkive-001",
      "expected": "active"
    }
  ],
  "production_changed": false
}
```

Even now, Charkive may still reject the proposal.

---

# Rejection Example

Suppose the file has changed since the Finding.

The safe lifecycle is:

```text
PROPOSAL

Quarantine candidate

        |
        v

CHARKIVE VALIDATION

Target changed

        |
        v

REJECT

        |
        v

NO PRODUCTION CHANGE
```

The rejection belongs in an Event and/or Verified Result.

The original Proposed Action remains unchanged.

---

# SysMedic Example

A future authorised SysMedic workflow might propose a hardware test.

```json
{
  "schema": "vna.proposed_action/0.1",
  "id": "action:sysmedic-test-001",
  "type": "sysmedic.run_hardware_test",
  "provider": {
    "id": "sysmedic",
    "type": "application"
  },
  "requester": {
    "type": "user",
    "id": "engineer:local"
  },
  "requested_at": "2026-10-30T07:20:00Z",
  "source": {
    "kind": "live"
  },
  "targets": [
    {
      "type": "asset",
      "id": "asset:repair-machine"
    }
  ],
  "mode": "execute",
  "state": "proposed",
  "parameters": {
    "test": "cpu_cooling"
  },
  "safety": {
    "verification_required": true,
    "confirmation_required": true
  },
  "production_changed": false
}
```

SysMedic still owns:

```text
whether the test exists
whether it is appropriate
what confirmation it requires
how it executes
how results are captured
```

---

# Service Restart Example

```json
{
  "schema": "vna.proposed_action/0.1",
  "id": "action:service-restart-001",
  "type": "vna.service_restart",
  "provider": {
    "id": "vna",
    "type": "application"
  },
  "requester": {
    "type": "user",
    "id": "user:operator"
  },
  "requested_at": "2026-10-30T07:20:00Z",
  "source": {
    "kind": "live"
  },
  "targets": [
    {
      "type": "service",
      "id": "service:web"
    }
  ],
  "reason": "Service remains stopped after investigation.",
  "mode": "execute",
  "state": "proposed",
  "preconditions": [
    {
      "type": "observation_matches",
      "subject_id": "service:web",
      "observation_type": "system.service.state",
      "expected": {
        "state": "stopped"
      }
    }
  ],
  "safety": {
    "verification_required": true,
    "confirmation_required": true
  },
  "production_changed": false
}
```

Before execution, VNA rechecks:

```text
Is the service still stopped?

Is this still the correct service?

Is the requester authorised?

Is restart permitted?

Does another operation already affect it?
```

---

# FileCity Copy Example

FileCity might eventually propose a storage copy.

```json
{
  "schema": "vna.proposed_action/0.1",
  "id": "action:filecity-copy-001",
  "type": "filecity.copy",
  "provider": {
    "id": "filecity",
    "type": "application"
  },
  "requester": {
    "type": "user",
    "id": "user:local"
  },
  "requested_at": "2026-10-30T07:20:00Z",
  "source": {
    "kind": "live"
  },
  "targets": [
    {
      "type": "storage_path",
      "storage_location_id": "storage:cloud:jon-drive",
      "path": "Projects/example"
    }
  ],
  "mode": "preview",
  "state": "proposed",
  "parameters": {
    "destination_storage_location_id": "storage:eddie:data",
    "destination_path": "Projects/example"
  },
  "safety": {
    "verification_required": true
  },
  "production_changed": false
}
```

The preview may allow FileCity to show:

```text
what would move
where it would go
estimated scope where known
potential conflicts
```

without moving anything.

---

# BoreDOOM! / VNA Gameplay Example

A player investigates an incident.

They choose:

```text
REQUEST REPAIR
```

The game creates a proposal.

```json
{
  "schema": "vna.proposed_action/0.1",
  "id": "action:mission-001",
  "type": "vna.service_restart",
  "provider": {
    "id": "vna",
    "type": "application"
  },
  "requester": {
    "type": "game",
    "id": "vna-game"
  },
  "requested_at": "2026-10-30T07:20:00Z",
  "source": {
    "kind": "live"
  },
  "targets": [
    {
      "type": "service",
      "id": "service:generalised-42"
    }
  ],
  "mode": "execute",
  "state": "proposed",
  "safety": {
    "verification_required": true,
    "confirmation_required": true
  },
  "production_changed": false,
  "attributes": {
    "vna": {
      "mission_id": "mission:incident-42"
    }
  }
}
```

The game has requested the action.

It has not executed anything.

---

# Gameplay Is Not Authority

The player's virtual action may be:

```text
press button
pull lever
repair machine
connect cable
drive fire engine
quarantine data
```

The operational translation remains:

```text
GAMEPLAY
   |
   v
PROPOSED ACTION
   |
   v
AUTHORITY BOUNDARY
   |
   v
PROVIDER
```

A spectacular animation does not weaken the safety model.

---

# Synthetic Gameplay Actions

In a synthetic tutorial:

```json
{
  "source": {
    "kind": "synthetic"
  }
}
```

the provider may simply alter the synthetic world.

For example:

```text
Player plugs in virtual cable

        |
        v

Synthetic Proposed Action

        |
        v

Synthetic provider validates

        |
        v

Synthetic connection restored
```

This is intentionally separate from live authority.

---

# Promoting a Synthetic Action to Live Is Forbidden by Default

A synthetic proposal MUST NOT be silently transformed into:

```text
source.kind = live
```

If a user wants to perform a corresponding live operation, create a new live Proposed Action and perform full live validation.

---

# Oracle Recommendation vs Proposed Action

An Oracle may say:

> The service may need restarting.

That is reasoning or recommendation.

It becomes a Proposed Action only when an authorised workflow explicitly creates:

```text
vna.proposed_action/0.1
```

This separation prevents conversational AI from accidentally becoming the execution boundary.

---

# T4 and Proposed Actions

T4 can explain the action without changing its semantics.

Example:

```text
T4Tots

"Restart this service?"


T4Teens

"Request a restart of the service. This may briefly interrupt it."


T4Techies

Action:
vna.service_restart

Target:
service:web

Mode:
execute

Preconditions:
service state = stopped

Verification:
required
```

The explanation changes.

The proposal does not.

---

# Authorisation

Proposed Action v0.1 deliberately does not embed a universal authorisation decision.

Authorisation belongs to the responsible authority/provider.

Reasons include:

```text
different providers have different safety models
different actions carry different risks
different users have different permissions
credentials may live outside VNA
professional competency may matter
local confirmation may be required
```

VNA transports intent.

It does not flatten every security model into one boolean.

---

# Future Authorisation Evidence

Future Events or Verified Results may record:

```text
authorised
rejected
authoriser
policy
credential
capability
confirmation
```

where appropriate.

Do not add these prematurely to the proposal itself.

The proposal describes the request, not the decision.

---

# Competency and Professional Authority

Future VNA professional gameplay may associate real operational capabilities with verified competency.

For example:

```text
Player qualification
        |
        v
Capability
        |
        v
Permitted proposed actions
```

Even then:

```text
qualified to request
```

does not necessarily mean:

```text
every request is automatically authorised
```

Current state, target policy, risk, and provider rules still matter.

---

# Capability Principle

A user may be generally authorised to request:

```text
service restart
```

while a particular service may prohibit:

```text
restart during backup
restart during deployment
restart without approval
```

Capability and current operational permission are separate.

---

# Dry Run Before Execution

Providers SHOULD support dry-run or preview modes where practical.

A useful workflow is:

```text
PROPOSE PREVIEW
      |
      v
SHOW EXPECTED EFFECT
      |
      v
REVIEW
      |
      v
PROPOSE EXECUTION
```

This is especially appropriate for:

```text
Charkive quarantine
FileCity movement
configuration changes
batch operations
```

---

# Dry Run Is Not Proof

A successful dry run says:

> The provider currently believes the operation could proceed.

It does not guarantee that a later execution will succeed.

Reality may change between:

```text
dry run
```

and:

```text
execution
```

Therefore execution still requires validation.

---

# No-Op Is a Valid Outcome

A provider may determine that the desired state already exists.

Example:

```text
Proposal:
Restart because service is stopped

Validation:
Service already running
```

The provider may return a verified no-op result.

This is preferable to performing unnecessary work merely because the user pressed a button.

---

# Rejection Is a Successful Safety Outcome

A rejected Proposed Action is not necessarily a system failure.

Examples:

```text
target changed
precondition failed
request expired
requester unauthorised
operation unsupported
safety policy denied
current state makes operation unnecessary
```

The safety system working correctly may mean nothing happened.

That should be reported clearly.

---

# Cancellation

A proposal may be cancelled before execution where the provider supports cancellation.

Cancellation should be represented through Event/Result lifecycle rather than rewriting the original Proposed Action.

The proposal remains evidence that the request existed.

---

# Retries

A retry SHOULD normally create a new Proposed Action.

This preserves:

```text
what was originally requested
why it failed
what changed
what was requested next
```

Avoid silently recycling the same action ID through multiple materially different attempts.

---

# Idempotency

Some provider operations may support idempotent request handling.

A future implementation may introduce an idempotency key.

Proposed Action v0.1 does not require one.

Providers should nevertheless avoid executing the same logical request twice merely because transport retries occurred.

This is an implementation safety requirement even before the common contract formalises it.

---

# Concurrency

Reality may change while an action is being reviewed.

For example:

```text
Operator A:
proposes quarantine

Operator B:
moves file

Operator A:
executes stale proposal
```

Preconditions and fresh provider validation are the first v0.1 defence.

More sophisticated concurrency controls can evolve later.

---

# Action Against Relationships

Some future operations may target Relationships.

Example:

```text
disconnect
detach
remove dependency
```

The provider must interpret the target safely.

The existence of:

```text
relationship:attached-to
```

does not itself prove how the real-world relationship can be modified.

---

# Action Against Findings

Findings are often convenient action anchors because they contain:

```text
subject
evidence
producer
current state
recommendation
```

But the provider must re-resolve the actual target.

Never translate:

```text
Finding ID
```

directly into:

```text
shell command
```

without current validation.

---

# Action Against Generalised Targets

A game client may know:

```text
SERVER-104
```

while VNA knows:

```text
EDDIE
```

The authorised VNA layer may map the generalised target back to the real object.

The client MUST NOT be given the reverse mapping unless its authorisation requires it.

---

# Audit Chain

Every meaningful real operation should eventually be traceable:

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
VALIDATION EVENT
   |
   v
AUTHORISATION EVENT
   |
   v
EXECUTION EVENT
   |
   v
VERIFIED RESULT
```

The chain should make it possible to answer:

```text
Why did this happen?

Who asked for it?

What target did they mean?

What did the provider validate?

Was it authorised?

What executed?

Did reality change?

Was the outcome verified?
```

---

# Proposed Action vs Event

Proposed Action:

```text
"Please restart this service."
```

Event:

```text
"Restart began."
```

or:

```text
"Request rejected."
```

or:

```text
"Restart completed."
```

The request and what happened are separate records.

---

# Proposed Action vs Verified Result

Proposed Action:

```text
INTENT
```

Verified Result:

```text
OUTCOME
```

Example:

```text
Proposal:
Quarantine candidate

Result:
Succeeded and destination verified
```

or:

```text
Proposal:
Quarantine candidate

Result:
Rejected because target changed
```

The Result closes the operational loop.

---

# Proposed Action vs Recommendation

Recommendation:

```text
It may be worth restarting the service.
```

Proposed Action:

```text
Request service restart.
```

The move from recommendation to proposal is a deliberate decision boundary.

---

# Proposed Action vs Finding

Finding:

```text
Service unavailable.
```

Proposed Action:

```text
Restart service.
```

Another perfectly valid proposal might instead be:

```text
Inspect service logs.
```

A Finding does not dictate one inevitable action.

---

# Proposed Action vs Observation

Observation:

```text
Service state = stopped.
```

Proposed Action:

```text
Restart service.
```

The measured state does not grant execution authority.

---

# Proposed Action vs Relationship

Relationship:

```text
Application depends_on Database.
```

Proposed Action:

```text
Restart Database.
```

The dependency may inform the decision and its risk.

It does not authorise it.

---

# Proposed Action vs Snapshot

Snapshot:

```text
Service was stopped yesterday.
```

Proposed Action:

```text
Restart service today.
```

These are separated by both:

```text
time
```

and:

```text
authority
```

Fresh validation is mandatory before any live operation.

---

# Proposed Action and Events

Suggested future Event types include:

```text
vna.action.proposed
vna.action.validation_started
vna.action.validation_failed
vna.action.validated
vna.action.authorisation_requested
vna.action.authorised
vna.action.rejected
vna.action.cancelled
vna.action.execution_started
vna.action.execution_completed
vna.action.execution_failed
vna.action.verification_started
vna.action.verification_completed
```

These belong to `EVENT.md`.

The Proposed Action remains the immutable request.

---

# Proposed Action and Findings Lifecycle

A successful operation does not automatically resolve the motivating Finding.

Preferred sequence:

```text
Finding:
active

        |
        v

Proposed Action

        |
        v

Execution

        |
        v

Verified Result

        |
        v

Re-observation

        |
        v

Condition gone?

YES
 |
 v
Finding:
resolved
```

The Finding reflects reality.

It does not merely reflect the action workflow.

---

# Operational Failure

Suppose:

```text
Proposal:
restart service

Execution:
command returned success

Verification:
service remains stopped
```

The operation is NOT successfully verified.

The Finding should remain active.

The world should not show:

```text
MISSION COMPLETE
```

merely because the command exited cleanly.

---

# Partial Success

Batch operations may produce mixed outcomes.

For example:

```text
10 files proposed for quarantine

8 verified in SANitorium

2 failed
```

The Verified Result must be capable of representing that outcome.

The Proposed Action remains the original request for all 10.

Do not rewrite it to pretend only eight were requested.

---

# Reversibility

Where an operation is intended to be reversible, the resulting Verified Result should eventually describe enough state to support that claim.

For Charkive quarantine:

```text
original location
quarantine location
verification
restoration context
```

The Proposed Action may state:

```text
reversible_expected = true
```

but the Verified Result establishes what was actually achieved.

---

# User Experience

Clients should make the proposal boundary obvious.

Good:

```text
RECOMMENDED

Review for quarantine

[ Preview ]
```

then:

```text
PREVIEW

These 12 objects would move to the SANitorium.

No changes have been made.

[ Request quarantine ]
```

then:

```text
CONFIRMATION REQUIRED
```

Bad:

```text
🔥 FIX IT
```

followed immediately by unreviewed production changes.

---

# Gameplay User Experience

Gameplay may remain playful while the authority boundary stays explicit.

Example:

```text
PLAYER

loads sanitation truck

        |
        v

GAME

"Ready to request collection?"

        |
        v

PROPOSED ACTION

        |
        v

CHARKIVE VALIDATES

        |
        v

AUTHORISATION

        |
        v

REAL OPERATION

        |
        v

VERIFICATION

        |
        v

SANITATION TRUCK ACTUALLY DEPARTS
```

The metaphor waits for verified reality before declaring success.

---

# Preview Can Be Spectacular

Nothing prevents the game from making a dry run visually enjoyable.

For example:

```text
ghost vehicle
planned route
highlighted buildings
estimated affected objects
```

The world should clearly communicate:

> **PREVIEW: nothing has changed yet.**

That distinction should survive every renderer.

---

# Security Boundary

Proposed Actions are untrusted requests.

That remains true even when the requester is:

```text
trusted UI
VNA
FileCity
BoreDOOM!
Oracle
automation
local administrator
```

Every provider MUST validate:

```text
type
targets
parameters
preconditions
requester authority
current state
```

before consequential execution.

---

# Never Execute User-Controlled Strings Directly

Fields such as:

```text
type
target IDs
paths
reason
parameters
attributes
```

MUST NOT be interpolated directly into:

```text
shell commands
PowerShell
SQL
filesystem commands
rclone commands
service commands
remote APIs
configuration files
```

without provider-specific validation and escaping.

---

# Command Injection Is a Provider Concern

The common VNA contract can define safe semantics.

It cannot make unsafe provider implementation safe automatically.

A provider must translate:

```text
structured action
```

into:

```text
known validated operation
```

not:

```text
arbitrary string to execute
```

---

# No Generic Shell Action

The shared VNA contract SHOULD NOT define:

```text
vna.run_shell_command
```

as a normal general-purpose action.

Prefer explicit capabilities:

```text
vna.service_restart
charkive.quarantine
charkive.restore
filecity.copy
sysmedic.run_hardware_test
```

Explicit actions create enforceable safety boundaries.

---

# Credentials

Proposed Actions MUST NOT carry:

```text
passwords
private keys
OAuth tokens
API secrets
BitLocker recovery keys
SNMP community strings
cloud credentials
```

Providers should access credentials through appropriate authorised mechanisms.

---

# Privacy

Proposals may reveal sensitive operational intent.

Examples:

```text
target identity
filesystem path
service name
storage destination
maintenance reason
infrastructure topology
```

Generalisation and access control may therefore be required before exposing Proposed Actions to less-trusted clients.

---

# Generalised Proposals

A client may propose against pseudonymous targets.

Example:

```text
Application:
SERVICE-12
```

VNA may internally resolve:

```text
SERVICE-12
        |
        v
real service identity
```

The provider still validates the real target.

The client does not gain access merely because it knows the pseudonym.

---

# Transport Independence

Proposed Action v0.1 defines intent.

It does not mandate transport.

Possible transports include:

```text
HTTP
local IPC
message queue
agent tool call
game bridge
desktop application
CLI
```

The authority model must remain consistent regardless of transport.

---

# Authentication Is Outside the Object

A Proposed Action may identify the requester.

That identity field does not itself authenticate the requester.

Transport and execution layers must establish trustworthy requester identity through appropriate mechanisms.

This distinction is critical.

Anyone can send JSON claiming:

```json
{
  "requester": {
    "type": "user",
    "id": "administrator"
  }
}
```

The JSON string is not proof.

---

# Authorisation Is Outside the Object

Likewise:

```json
{
  "mode": "execute"
}
```

is not proof of authorisation.

The provider or VNA authority layer must make the decision independently.

---

# Validation Rules

A valid Proposed Action v0.1 MUST satisfy:

1. `schema` equals `vna.proposed_action/0.1`.
2. `id` is a non-empty string.
3. `type` is a non-empty string.
4. `provider` is an object.
5. `provider.id` is a non-empty string.
6. `provider.type` is a non-empty string.
7. `requester` is an object.
8. `requester.type` is a non-empty string.
9. `requester.id` is a non-empty string.
10. `requested_at` is a valid UTC ISO 8601 timestamp.
11. `source` is an object.
12. `source.kind` is one of the recognised source kinds.
13. `targets` is a non-empty array.
14. Every target contains a non-empty `type`.
15. Every target contains an identifier appropriate to its type.
16. `mode` is one of `preview`, `dry_run`, or `execute`.
17. `state` equals `proposed` for a newly created proposal.
18. `production_changed` is a boolean and MUST initially be `false`.
19. If supplied, `reason` is a string.
20. If supplied, `finding_ids` is an array of strings.
21. If supplied, `recommendation_ids` is an array of strings.
22. If supplied, `safety` is an object.
23. If supplied, `preconditions` is an array.
24. Every precondition contains a non-empty `type`.
25. If supplied, `parameters` is an object.
26. If supplied, `expires_at` is a valid UTC ISO 8601 timestamp.
27. If supplied, `attributes` is an object.

Structural validity does not mean the action is safe, supported, or authorised.

---

# Semantic Validation

Before execution, the provider SHOULD establish at minimum:

```text
action type is supported

request has not expired

targets exist

target identities resolve safely

target state is current enough

preconditions pass

parameters are valid

requester identity is authenticated

requester is authorised

provider policy permits the operation

required confirmation exists

required safety controls are available

requested reversibility expectations can be satisfied

verification plan exists where required
```

Failure of an important validation should prevent execution.

---

# Compatibility Rules

## Requester

A v0.1 requester:

- MUST provide all required fields
- MAY provide optional fields
- MAY add unknown fields
- MUST NOT assume submission means acceptance
- MUST NOT assume `execute` means immediate execution
- SHOULD provide preconditions for state-sensitive operations
- SHOULD request verification for consequential operations
- SHOULD use stable ecosystem identities
- MUST NOT include credentials
- SHOULD create a new proposal for materially changed requests

## Provider

A v0.1 provider:

- MUST treat proposals as untrusted input
- MUST validate action type
- MUST validate targets
- MUST validate parameters
- MUST enforce its own authorisation policy
- MUST enforce its own safety requirements
- MUST reject expired proposals
- MUST evaluate applicable preconditions
- MUST NOT allow requester fields to weaken mandatory safety policy
- SHOULD support preview/dry-run where practical
- SHOULD verify consequential outcomes
- SHOULD produce Events describing lifecycle changes
- SHOULD produce a Verified Result

## Consumer

A v0.1 consumer:

- SHOULD preserve unknown fields
- MUST preserve requester identity as supplied
- MUST preserve provider identity
- MUST preserve source kind
- MUST preserve requested mode
- MUST NOT display a proposal as completed work
- MUST NOT infer execution success from proposal existence

---

# v0.1 Deliberate Omissions

Proposed Action v0.1 intentionally does not fully define:

```text
authentication protocol
authorisation protocol
RBAC
professional credential format
approval chains
multi-person approval
provider capability discovery
idempotency keys
transaction semantics
distributed locking
batch rollback
compensation actions
credential storage
action queueing
scheduling
recurring actions
full cancellation semantics
provider-specific parameter schemas
```

These should evolve from real implementations.

The purpose of v0.1 is narrower:

> **Represent a request to change something without confusing the request with permission, execution, or success.**

---

# First Implementation Target

Charkive dry-run quarantine.

```text
ACTIVE CHARKIVE FINDING
        |
        v
USER REVIEWS
        |
        v
PROPOSED ACTION

charkive.quarantine
mode = dry_run
        |
        v
CHARKIVE VALIDATES
        |
        v
CHARKIVE PREVIEWS
        |
        v
NO PRODUCTION CHANGE
```

This proves the action boundary safely.

---

# Second Implementation Target

Charkive real quarantine.

Only after dry-run behaviour works through the contract:

```text
DRY RUN
   |
   v
REVIEW
   |
   v
NEW PROPOSED ACTION
mode = execute
   |
   v
CHARKIVE VALIDATION
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

The SANitorium makes this a particularly good first operation because the action is deliberately reversible.

---

# Third Implementation Target

VNA synthetic service restart.

Use a synthetic environment first.

```text
SYNTHETIC FINDING
      |
      v
PROPOSED ACTION
      |
      v
SYNTHETIC PROVIDER
      |
      v
VERIFIED SYNTHETIC RESULT
```

This proves the gameplay action path without production risk.

---

# Fourth Implementation Target

FileCity movement preview.

```text
SOURCE STORAGE
      |
      v
PROPOSED COPY / MOVE
mode = preview
      |
      v
VISUAL ROUTE
      |
      v
NO REAL MOVEMENT
```

This lets FileCity demonstrate its transport metaphors against a genuine action contract before real migration is enabled.

---

# Fifth Implementation Target

One deliberately low-risk live VNA operation.

Only after:

```text
authentication
authorisation
preconditions
events
verification
audit
```

are working end to end.

Do not select the operation because it looks impressive.

Select it because its safety properties are easy to understand and verify.

---

# North Star

A Proposed Action should let the ecosystem answer:

```text
WHAT is being requested?

WHO or WHAT requested it?

WHEN was it requested?

WHICH provider is responsible?

WHAT targets does it concern?

WHAT evidence or Findings motivated it?

IS this preview, dry-run, or real execution?

WHAT reason was supplied?

WHAT parameters apply?

WHAT assumptions must still be true?

IS reversibility expected?

IS verification required?

IS confirmation required?

WHEN does the request expire?

HAS proposing it changed production?
```

And it should answer one question with absolute clarity:

```text
HAS THIS ACTION BEEN AUTHORISED AND SUCCESSFULLY EXECUTED?
```

with:

> **You cannot determine that from the Proposed Action.**

That is exactly the point.

> **Intent is not authority.**

> **Authority is not execution.**

> **Execution is not success.**

> **Success is not verified until reality agrees.**
