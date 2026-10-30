# VNA Verified Result Schema v0.1

> **Execution is not success. Success is not verified until reality agrees.**

## Purpose

A **Verified Result** is the common VNA ecosystem representation of the outcome of an attempted operation after the responsible provider has evaluated what actually happened.

It closes the loop begun by a Proposed Action.

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

A Verified Result answers:

```text
Was the proposal accepted?

Was anything actually executed?

Did execution change reality?

Did the expected outcome occur?

Was that outcome verified?

Was the operation fully successful, partially successful, rejected, cancelled, or failed?

What evidence supports that conclusion?
```

The contract exists specifically to prevent this dangerous simplification:

```text
COMMAND RETURNED SUCCESS
        =
MISSION ACCOMPLISHED
```

The correct model is:

```text
COMMAND RETURNED
      |
      v
RE-OBSERVE REALITY
      |
      v
VERIFY EXPECTED OUTCOME
      |
      v
ONLY THEN
      |
      v
REPORT SUCCESS
```

---

# Core Principle

> **The provider verifies reality, not merely the execution mechanism.**

For example:

```text
Requested:
Restart service

Execution:
systemctl restart returned success

Verification:
Service state = running

Result:
SUCCEEDED + VERIFIED
```

But:

```text
Requested:
Restart service

Execution:
systemctl restart returned success

Verification:
Service state = stopped

Result:
FAILED VERIFICATION
```

The execution mechanism reporting success is evidence.

It is not the final truth.

---

# Ecosystem Position

The complete operational chain is:

```text
OBSERVATION
     |
     v
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
     |
     v
NEW OBSERVATIONS
     |
     v
FINDING RE-EVALUATION
```

The Verified Result records the outcome.

It does not by itself replace subsequent observation of the affected system where that observation is required to determine whether a Finding is resolved.

---

# Canonical File Location

Save this contract at:

```text
vna/docs/contracts/VERIFIED_RESULT.md
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

Every Verified Result MUST declare:

```json
{
  "schema": "vna.verified_result/0.1"
}
```

The canonical identifier is:

```text
vna.verified_result/0.1
```

Consumers MUST NOT assume future versions are structurally identical.

Consumers SHOULD ignore unknown fields unless those fields are explicitly required by the schema version they implement.

---

# Canonical Example

```json
{
  "schema": "vna.verified_result/0.1",
  "id": "result:charkive-quarantine-001",
  "proposed_action_id": "action:charkive-quarantine-001",
  "provider": {
    "id": "charkive",
    "type": "application"
  },
  "source": {
    "kind": "live"
  },
  "status": "succeeded",
  "execution": {
    "attempted": true,
    "completed": true
  },
  "verification": {
    "required": true,
    "performed": true,
    "verified": true
  },
  "started_at": "2026-10-30T07:25:10Z",
  "completed_at": "2026-10-30T07:25:14Z",
  "verified_at": "2026-10-30T07:25:15Z",
  "targets": [
    {
      "type": "finding",
      "id": "finding:charkive-001",
      "status": "succeeded"
    }
  ],
  "outcomes": [
    {
      "type": "charkive.quarantined",
      "summary": "Reviewed candidate moved to the SANitorium."
    }
  ],
  "evidence": [
    {
      "type": "charkive.destination_verified",
      "description": "The quarantined object was verified at the expected SANitorium destination."
    }
  ],
  "reversible": true,
  "production_changed": true,
  "summary": "Candidate quarantined and destination verified.",
  "attributes": {}
}
```

This means:

> The responsible provider attempted the requested operation, completed it, and verified the expected outcome.

---

# Required Fields

Verified Result v0.1 requires:

```text
schema
id
proposed_action_id
provider
source
status
execution
verification
completed_at
production_changed
summary
```

Optional fields include:

```text
started_at
verified_at
targets
outcomes
evidence
reversible
rollback
failures
warnings
observations
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
vna.verified_result/0.1
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

Provides the stable identity of this result record.

Example:

```json
{
  "id": "result:charkive-quarantine-001"
}
```

The ID identifies the outcome record.

It is not the Proposed Action ID.

---

# Result Identity Is Not Proposal Identity

For example:

```text
Proposed Action:
action:001

        |
        v

Verified Result:
result:001
```

The proposal records:

```text
what was requested
```

The Verified Result records:

```text
what happened
```

Keeping these identities separate preserves audit history.

---

# Field: `proposed_action_id`

## Type

```text
string
```

## Required

Yes.

## Purpose

Identifies the Proposed Action whose outcome this result describes.

Example:

```json
{
  "proposed_action_id": "action:charkive-quarantine-001"
}
```

A consumer should be able to navigate:

```text
Verified Result
      |
      v
Proposed Action
      |
      v
Finding / reason / requester
```

This provides the beginning of the audit chain.

---

# One Proposal, One Final Result

For v0.1, a Proposed Action SHOULD normally produce one final Verified Result.

Intermediate activity belongs in Events.

Conceptually:

```text
PROPOSED ACTION
      |
      +── Event: validation started
      |
      +── Event: authorised
      |
      +── Event: execution started
      |
      +── Event: execution completed
      |
      +── Event: verification completed
      |
      `── VERIFIED RESULT
```

This keeps the Result focused on the final known outcome.

---

# Retry Produces a New Proposal

If an operation fails and the user retries materially later, the preferred pattern is:

```text
Action 1
   |
   v
Result 1
FAILED

Action 2
   |
   v
Result 2
SUCCEEDED
```

Do not rewrite Result 1 because Result 2 later succeeded.

Historical failure remains useful evidence.

---

# Field: `provider`

## Type

```text
object
```

## Required

Yes.

## Purpose

Identifies the authoritative provider responsible for producing the Result.

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
version
instance
operation_id
```

---

# Provider Owns the Result Semantics

If:

```text
type:
charkive.quarantine
```

then Charkive determines:

```text
what execution means
what success means
what verification means
what reversibility means
```

VNA provides the common result vocabulary.

It does not replace provider-specific safety semantics.

---

# Provider Operation ID

A provider MAY include its internal operation identifier.

Example:

```json
{
  "provider": {
    "id": "charkive",
    "type": "application",
    "operation_id": "charkive-op-987"
  }
}
```

This can help correlate:

```text
VNA proposal
provider logs
provider operation
VNA result
```

The provider operation ID is not a replacement for the VNA Result ID.

---

# Field: `source`

## Type

```text
object
```

## Required

Yes.

## Purpose

Preserves the operational reality context.

Allowed v0.1 kinds include:

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

# Live Result

Example:

```json
{
  "source": {
    "kind": "live"
  }
}
```

This means the result concerns a live operation or live target context.

---

# Synthetic Result

Example:

```json
{
  "source": {
    "kind": "synthetic",
    "id": "tutorial:unplugged-cable"
  }
}
```

This means the change was verified only inside the synthetic environment.

It MUST NOT be presented as a production change.

---

# Snapshot Result

Snapshots are normally investigated read-only.

A Result with:

```text
source.kind = snapshot
```

would ordinarily represent a symbolic or snapshot-local operation.

It does not prove anything changed on the original live machine.

---

# Generalised Result

A game client may receive a generalised version of a Result.

The authorised VNA layer may know the true target.

The less-trusted client sees only pseudonymous identity.

The technical outcome must remain truthful after generalisation.

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
succeeded
partially_succeeded
failed
rejected
cancelled
no_op
unknown
```

---

# Status: `succeeded`

The requested operation completed sufficiently and the required verification established the expected result.

Where:

```text
verification.required = true
```

`succeeded` SHOULD normally require:

```text
verification.verified = true
```

---

# Status: `partially_succeeded`

Some requested outcomes succeeded and others did not.

Typical example:

```text
10 files requested for quarantine

8 verified successfully

2 failed
```

Do not report full success merely because the majority succeeded.

---

# Status: `failed`

The operation was accepted or attempted, but the intended result was not achieved.

Possible causes include:

```text
execution error
verification failure
provider error
target error
partial work which does not justify partial success
```

---

# Status: `rejected`

The provider refused to execute the Proposed Action.

Possible reasons include:

```text
unauthorised
precondition failed
target changed
unsupported operation
proposal expired
safety policy denied
target not found
request malformed
```

A rejected result normally means:

```text
execution.attempted = false
production_changed = false
```

Rejection may be the correct safe outcome.

---

# Status: `cancelled`

The operation was cancelled before completing.

Whether production changed depends on how far execution progressed.

Therefore this is possible:

```json
{
  "status": "cancelled",
  "production_changed": true
}
```

if partial work occurred before cancellation.

Consumers must inspect the rest of the Result.

---

# Status: `no_op`

The provider determined that no change was necessary.

Example:

```text
Proposal:
Restart stopped service

Validation:
Service already running

Result:
no_op
```

This is not failure.

It is often the safest and most desirable result.

---

# Status: `unknown`

The final result cannot currently be established.

This may occur if:

```text
execution state became ambiguous
verification could not complete
provider connection was lost
target became unavailable
```

Unknown is preferable to pretending success or failure.

---

# Field: `execution`

## Type

```text
object
```

## Required

Yes.

## Purpose

Describes whether execution was attempted and whether the provider believes execution completed.

Required fields:

```text
attempted
completed
```

Example:

```json
{
  "execution": {
    "attempted": true,
    "completed": true
  }
}
```

---

# `execution.attempted`

## Type

```text
boolean
```

Meaning:

```text
true
```

The provider began the real or synthetic execution phase.

```text
false
```

The action was resolved without execution.

Examples include:

```text
rejected
no-op
cancelled before start
```

---

# `execution.completed`

## Type

```text
boolean
```

Meaning:

```text
true
```

The provider's execution phase reached its own completion boundary.

This does NOT by itself mean the desired real-world result occurred.

That is the verification layer's job.

---

# Execution Completed Does Not Mean Success

This is valid:

```json
{
  "status": "failed",
  "execution": {
    "attempted": true,
    "completed": true
  },
  "verification": {
    "required": true,
    "performed": true,
    "verified": false
  }
}
```

Meaning:

> The command or operation completed, but the expected outcome was not verified.

That distinction is central to VNA.

---

# Execution Provider Status

The provider MAY record its own execution status.

Example:

```json
{
  "execution": {
    "attempted": true,
    "completed": true,
    "provider_status": "exit_code_0"
  }
}
```

This is useful evidence.

It does not override verification.

---

# Field: `verification`

## Type

```text
object
```

## Required

Yes.

## Purpose

Describes whether verification was expected, attempted, and successful.

Required fields:

```text
required
performed
verified
```

Example:

```json
{
  "verification": {
    "required": true,
    "performed": true,
    "verified": true
  }
}
```

---

# `verification.required`

## Type

```text
boolean
```

Meaning:

> The operation required verification before it could legitimately be treated as successful.

For consequential real-world VNA operations, this SHOULD normally be:

```text
true
```

---

# `verification.performed`

## Type

```text
boolean
```

Meaning:

> The provider actually performed its verification procedure.

---

# `verification.verified`

## Type

```text
boolean
```

Meaning:

> The verification procedure established the expected result.

---

# Verification Invariant

For operations requiring verification:

```text
status = succeeded
```

SHOULD imply:

```text
verification.required = true
verification.performed = true
verification.verified = true
```

A provider which cannot satisfy that invariant should use an appropriate alternative status.

---

# Verification Need Not Mean Absolute Certainty

Verification means:

> The provider performed the defined check and the expected state was observed.

It does not mean:

```text
the entire system is healthy
there are no hidden failures
the action had no side effects
future failure is impossible
```

Verification remains scoped to the operation's expected outcome.

---

# Independent Verification

Where practical, verification SHOULD inspect reality independently of the execution command's return value.

Prefer:

```text
restart command
       |
       v
separate service-state query
       |
       v
confirmed running
```

over:

```text
restart command exited 0
       |
       v
assume running
```

Likewise:

```text
copy operation
       |
       v
destination existence / integrity check
```

is stronger than:

```text
copy command said done
```

---

# Field: `started_at`

## Type

```text
string
```

## Required

No.

## Format

UTC ISO 8601 timestamp.

Records when execution began.

For rejected or pre-execution no-op Results it may be omitted.

---

# Field: `completed_at`

## Type

```text
string
```

## Required

Yes.

## Format

UTC ISO 8601 timestamp.

Records when the provider reached the Result's final completion state.

For a rejected operation this is the time rejection was finalised.

For an executed operation it is the time execution/result processing completed.

---

# Field: `verified_at`

## Type

```text
string
```

## Required

No.

## Format

UTC ISO 8601 timestamp.

Records when verification completed successfully or unsuccessfully.

If verification was performed, this field SHOULD be supplied.

---

# Ordering of Times

For a normal successful operation:

```text
started_at
    <=
completed_at
    <=
verified_at
```

However, certain provider workflows may overlap execution and verification.

Consumers SHOULD use the semantic meaning of each field rather than assuming all providers have identical timing internals.

---

# Field: `targets`

## Type

```text
array of objects
```

## Required

No.

## Purpose

Records per-target outcomes.

This becomes especially useful for batch operations.

Example:

```json
{
  "targets": [
    {
      "type": "finding",
      "id": "finding:001",
      "status": "succeeded"
    },
    {
      "type": "finding",
      "id": "finding:002",
      "status": "failed"
    }
  ]
}
```

---

# Target Outcome Status

Initial per-target statuses may include:

```text
succeeded
failed
rejected
cancelled
no_op
unknown
```

A batch Result may then report:

```text
status:
partially_succeeded
```

while preserving exactly which targets succeeded.

---

# Per-Target Evidence

A target entry MAY include provider-specific verification detail.

Example:

```json
{
  "type": "storage_path",
  "storage_location_id": "storage:eddie:data",
  "path": "Projects/cache/example.tmp",
  "status": "succeeded",
  "evidence": [
    {
      "type": "charkive.destination_verified",
      "description": "Object exists at the expected SANitorium destination."
    }
  ]
}
```

---

# Field: `outcomes`

## Type

```text
array of objects
```

## Required

No.

## Purpose

Describes meaningful outcomes produced by the operation.

Every outcome MUST contain:

```text
type
```

It SHOULD contain a human-readable:

```text
summary
```

Example:

```json
{
  "outcomes": [
    {
      "type": "charkive.quarantined",
      "summary": "The reviewed candidate was moved into the SANitorium."
    }
  ]
}
```

---

# Outcome vs Verification

Outcome says:

```text
what state the provider says resulted
```

Verification says:

```text
whether that result was checked successfully
```

Do not conflate them.

---

# Provider-Specific Outcomes

Provider-specific outcomes SHOULD be namespaced.

Examples:

```text
charkive.quarantined
charkive.restored
vna.service_restarted
filecity.copy_completed
filecity.move_completed
sysmedic.test_completed
```

Common outcomes may emerge later if several providers genuinely share identical semantics.

---

# Field: `evidence`

## Type

```text
array of objects
```

## Required

No.

## Purpose

Records evidence supporting the Verified Result.

Every evidence item MUST contain:

```text
type
```

Example:

```json
{
  "evidence": [
    {
      "type": "system.service_state",
      "description": "Post-operation service query reported the service as running."
    }
  ]
}
```

---

# Verification Evidence Is Critical

For meaningful real-world changes, the Result should be able to answer:

> **How do you know it worked?**

Examples:

```text
service state re-read
destination checksum checked
quarantine destination verified
source absence verified
mount state re-read
network reachability retested
configuration re-read
hardware test result captured
```

The answer should not merely be:

> The command didn't complain.

---

# Field: `observations`

## Type

```text
array of strings or observation references
```

## Required

No.

## Purpose

References Observations created during verification.

Example:

```json
{
  "observations": [
    "observation:service-running-after-restart"
  ]
}
```

This creates a clean chain:

```text
PROPOSED ACTION
      |
      v
VERIFIED RESULT
      |
      v
POST-ACTION OBSERVATION
```

The Observation can subsequently support re-evaluation of the original Finding.

---

# Post-Action Observations

A particularly robust workflow is:

```text
ACTION
   |
   v
EXECUTION
   |
   v
POST-ACTION OBSERVATION
   |
   v
VERIFICATION
   |
   v
RESULT
```

For example:

```text
Restart service
      |
      v
Observe service state
      |
      v
state = running
      |
      v
Result verified
```

---

# Field: `reversible`

## Type

```text
boolean
```

## Required

No.

## Purpose

Records whether the provider considers the achieved result reversible through a known supported process.

Example:

```json
{
  "reversible": true
}
```

This field describes the achieved state, not merely the requester's expectation.

---

# Reversible Expected vs Reversible Achieved

Proposed Action:

```json
{
  "safety": {
    "reversible_expected": true
  }
}
```

Verified Result:

```json
{
  "reversible": true
}
```

These are different statements.

The proposal says:

> We require reversibility.

The Result says:

> The provider believes the achieved operation is reversible.

---

# Reversible Does Not Mean Automatic

A result may be reversible without authorising an automatic rollback.

For example:

```text
Charkive quarantine
```

may be reversible through:

```text
Charkive restore
```

That restoration is still a separate Proposed Action.

---

# Field: `rollback`

## Type

```text
object
```

## Required

No.

## Purpose

Describes a rollback or restoration capability discovered or created by the operation.

Example:

```json
{
  "rollback": {
    "available": true,
    "action_type": "charkive.restore"
  }
}
```

This does not invoke rollback.

It identifies a supported route for a future Proposed Action.

---

# Rollback Is a New Action

This is important:

```text
ACTION A
quarantine

        |
        v

RESULT
reversible = true

        |
        v

ACTION B
restore
```

Do not mutate Action A backwards in time.

History should remain comprehensible.

---

# Field: `failures`

## Type

```text
array of objects
```

## Required

No.

## Purpose

Records failures encountered during execution or verification.

Every failure MUST contain:

```text
type
```

It SHOULD contain:

```text
stage
summary
```

Example:

```json
{
  "failures": [
    {
      "type": "verification_failed",
      "stage": "verification",
      "summary": "Service remained stopped after restart command completed."
    }
  ]
}
```

---

# Failure Stages

Suggested initial values include:

```text
validation
authorisation
execution
verification
rollback
unknown
```

The Verified Result primarily records final outcome, but preserving failure stage helps investigation.

---

# Rejected Actions

A rejected action might produce:

```json
{
  "status": "rejected",
  "execution": {
    "attempted": false,
    "completed": false
  },
  "verification": {
    "required": false,
    "performed": false,
    "verified": false
  },
  "production_changed": false,
  "failures": [
    {
      "type": "precondition_failed",
      "stage": "validation",
      "summary": "The motivating Finding was no longer active."
    }
  ]
}
```

Nothing happened.

That may be the correct result.

---

# Field: `warnings`

## Type

```text
array of objects
```

## Required

No.

## Purpose

Records noteworthy conditions which did not prevent the Result from reaching its final status.

Example:

```json
{
  "warnings": [
    {
      "type": "verification_delayed",
      "summary": "Service required several seconds before reporting healthy state."
    }
  ]
}
```

Warnings should not be used to bury genuine failures.

---

# Field: `summary`

## Type

```text
string
```

## Required

Yes.

## Purpose

Provides a concise human-readable description of the final outcome.

Example:

```json
{
  "summary": "Candidate quarantined and destination verified."
}
```

The structured fields remain authoritative for machine behaviour.

---

# Field: `production_changed`

## Type

```text
boolean
```

## Required

Yes.

## Purpose

Records whether the operation changed real production state.

Example:

```json
{
  "production_changed": true
}
```

A rejected or preview-only result normally reports:

```text
false
```

A successful real operation normally reports:

```text
true
```

unless the operation was inherently read-only or no-op.

---

# Production Changed Does Not Mean Success

This is valid:

```json
{
  "status": "failed",
  "production_changed": true
}
```

Example:

```text
operation changed something

but

expected final state was not achieved
```

This is especially important for partially completed operations.

---

# Production Unchanged Does Not Mean Failure

This is also valid:

```json
{
  "status": "no_op",
  "production_changed": false
}
```

or:

```json
{
  "status": "rejected",
  "production_changed": false
}
```

A safe system often succeeds precisely by refusing to make an unnecessary change.

---

# Field: `attributes`

## Type

```text
object
```

## Required

No.

## Purpose

Provides an extension area for provider-specific metadata.

Example:

```json
{
  "attributes": {
    "charkive": {
      "sanitorium_record_id": "quarantine:987"
    }
  }
}
```

or:

```json
{
  "attributes": {
    "vna": {
      "mission_id": "mission:incident-42"
    }
  }
}
```

Shared consumers SHOULD NOT depend upon provider-specific attributes for core Result semantics.

---

# Minimal Successful Result

```json
{
  "schema": "vna.verified_result/0.1",
  "id": "result:001",
  "proposed_action_id": "action:001",
  "provider": {
    "id": "charkive",
    "type": "application"
  },
  "source": {
    "kind": "live"
  },
  "status": "succeeded",
  "execution": {
    "attempted": true,
    "completed": true
  },
  "verification": {
    "required": true,
    "performed": true,
    "verified": true
  },
  "completed_at": "2026-10-30T07:25:14Z",
  "verified_at": "2026-10-30T07:25:15Z",
  "production_changed": true,
  "summary": "Operation completed and expected outcome verified."
}
```

---

# Minimal Rejected Result

```json
{
  "schema": "vna.verified_result/0.1",
  "id": "result:002",
  "proposed_action_id": "action:002",
  "provider": {
    "id": "charkive",
    "type": "application"
  },
  "source": {
    "kind": "live"
  },
  "status": "rejected",
  "execution": {
    "attempted": false,
    "completed": false
  },
  "verification": {
    "required": false,
    "performed": false,
    "verified": false
  },
  "completed_at": "2026-10-30T07:25:14Z",
  "production_changed": false,
  "summary": "Operation rejected because the target no longer matched the expected state."
}
```

---

# Minimal No-Op Result

```json
{
  "schema": "vna.verified_result/0.1",
  "id": "result:003",
  "proposed_action_id": "action:003",
  "provider": {
    "id": "vna",
    "type": "application"
  },
  "source": {
    "kind": "live"
  },
  "status": "no_op",
  "execution": {
    "attempted": false,
    "completed": false
  },
  "verification": {
    "required": true,
    "performed": true,
    "verified": true
  },
  "completed_at": "2026-10-30T07:25:14Z",
  "verified_at": "2026-10-30T07:25:14Z",
  "production_changed": false,
  "summary": "Target already matched the desired state; no change was required."
}
```

---

# Charkive Quarantine Success Example

```json
{
  "schema": "vna.verified_result/0.1",
  "id": "result:charkive-quarantine-001",
  "proposed_action_id": "action:charkive-quarantine-execute-001",
  "provider": {
    "id": "charkive",
    "type": "application",
    "operation_id": "charkive-op-987"
  },
  "source": {
    "kind": "live"
  },
  "status": "succeeded",
  "execution": {
    "attempted": true,
    "completed": true
  },
  "verification": {
    "required": true,
    "performed": true,
    "verified": true
  },
  "started_at": "2026-10-30T07:25:10Z",
  "completed_at": "2026-10-30T07:25:14Z",
  "verified_at": "2026-10-30T07:25:15Z",
  "targets": [
    {
      "type": "finding",
      "id": "finding:charkive-001",
      "status": "succeeded"
    }
  ],
  "outcomes": [
    {
      "type": "charkive.quarantined",
      "summary": "Reviewed candidate moved to the SANitorium."
    }
  ],
  "evidence": [
    {
      "type": "charkive.destination_verified",
      "description": "Object verified at the expected SANitorium destination."
    },
    {
      "type": "charkive.source_absence_verified",
      "description": "Original source path no longer contained the quarantined object."
    }
  ],
  "reversible": true,
  "rollback": {
    "available": true,
    "action_type": "charkive.restore"
  },
  "production_changed": true,
  "summary": "Candidate quarantined successfully and verified.",
  "attributes": {
    "charkive": {
      "sanitorium_record_id": "quarantine:987"
    }
  }
}
```

---

# Charkive Quarantine Verification Failure Example

```json
{
  "schema": "vna.verified_result/0.1",
  "id": "result:charkive-quarantine-002",
  "proposed_action_id": "action:charkive-quarantine-execute-002",
  "provider": {
    "id": "charkive",
    "type": "application"
  },
  "source": {
    "kind": "live"
  },
  "status": "failed",
  "execution": {
    "attempted": true,
    "completed": true,
    "provider_status": "operation_returned_success"
  },
  "verification": {
    "required": true,
    "performed": true,
    "verified": false
  },
  "started_at": "2026-10-30T07:25:10Z",
  "completed_at": "2026-10-30T07:25:14Z",
  "verified_at": "2026-10-30T07:25:15Z",
  "failures": [
    {
      "type": "verification_failed",
      "stage": "verification",
      "summary": "Expected destination state could not be verified."
    }
  ],
  "production_changed": true,
  "summary": "Quarantine operation executed but expected result could not be verified."
}
```

The correct UI is:

```text
FAILED / REQUIRES ATTENTION
```

not:

```text
SUCCESS
```

---

# Charkive Restore Example

```json
{
  "schema": "vna.verified_result/0.1",
  "id": "result:charkive-restore-001",
  "proposed_action_id": "action:charkive-restore-001",
  "provider": {
    "id": "charkive",
    "type": "application"
  },
  "source": {
    "kind": "live"
  },
  "status": "succeeded",
  "execution": {
    "attempted": true,
    "completed": true
  },
  "verification": {
    "required": true,
    "performed": true,
    "verified": true
  },
  "completed_at": "2026-10-30T07:40:00Z",
  "verified_at": "2026-10-30T07:40:01Z",
  "outcomes": [
    {
      "type": "charkive.restored",
      "summary": "Object restored from the SANitorium to its verified destination."
    }
  ],
  "reversible": true,
  "production_changed": true,
  "summary": "Quarantined object restored successfully."
}
```

---

# Service Restart Success Example

```json
{
  "schema": "vna.verified_result/0.1",
  "id": "result:service-restart-001",
  "proposed_action_id": "action:service-restart-001",
  "provider": {
    "id": "vna",
    "type": "application"
  },
  "source": {
    "kind": "live"
  },
  "status": "succeeded",
  "execution": {
    "attempted": true,
    "completed": true
  },
  "verification": {
    "required": true,
    "performed": true,
    "verified": true
  },
  "started_at": "2026-10-30T07:25:00Z",
  "completed_at": "2026-10-30T07:25:02Z",
  "verified_at": "2026-10-30T07:25:04Z",
  "observations": [
    "observation:service-running-after-restart"
  ],
  "outcomes": [
    {
      "type": "vna.service_restarted",
      "summary": "Service restarted and subsequently reported running."
    }
  ],
  "production_changed": true,
  "summary": "Service restart completed and running state verified."
}
```

---

# Service Restart No-Op Example

Suppose the service recovered before the action executed.

```json
{
  "schema": "vna.verified_result/0.1",
  "id": "result:service-restart-noop-001",
  "proposed_action_id": "action:service-restart-002",
  "provider": {
    "id": "vna",
    "type": "application"
  },
  "source": {
    "kind": "live"
  },
  "status": "no_op",
  "execution": {
    "attempted": false,
    "completed": false
  },
  "verification": {
    "required": true,
    "performed": true,
    "verified": true
  },
  "completed_at": "2026-10-30T07:25:00Z",
  "verified_at": "2026-10-30T07:25:00Z",
  "observations": [
    "observation:service-already-running"
  ],
  "production_changed": false,
  "summary": "Service was already running; restart was unnecessary."
}
```

This is a good result.

VNA avoided needless production change.

---

# FileCity Copy Example

```json
{
  "schema": "vna.verified_result/0.1",
  "id": "result:filecity-copy-001",
  "proposed_action_id": "action:filecity-copy-001",
  "provider": {
    "id": "filecity",
    "type": "application"
  },
  "source": {
    "kind": "live"
  },
  "status": "succeeded",
  "execution": {
    "attempted": true,
    "completed": true
  },
  "verification": {
    "required": true,
    "performed": true,
    "verified": true
  },
  "completed_at": "2026-10-30T08:20:00Z",
  "verified_at": "2026-10-30T08:20:05Z",
  "outcomes": [
    {
      "type": "filecity.copy_completed",
      "summary": "Requested data copied to the destination Storage Location."
    }
  ],
  "production_changed": true,
  "summary": "Copy completed and destination verified."
}
```

Only after verification should FileCity show:

```text
DELIVERY COMPLETE
```

---

# Partial Batch Example

```json
{
  "schema": "vna.verified_result/0.1",
  "id": "result:batch-001",
  "proposed_action_id": "action:batch-001",
  "provider": {
    "id": "charkive",
    "type": "application"
  },
  "source": {
    "kind": "live"
  },
  "status": "partially_succeeded",
  "execution": {
    "attempted": true,
    "completed": true
  },
  "verification": {
    "required": true,
    "performed": true,
    "verified": false
  },
  "completed_at": "2026-10-30T08:20:00Z",
  "verified_at": "2026-10-30T08:20:05Z",
  "targets": [
    {
      "type": "finding",
      "id": "finding:001",
      "status": "succeeded"
    },
    {
      "type": "finding",
      "id": "finding:002",
      "status": "succeeded"
    },
    {
      "type": "finding",
      "id": "finding:003",
      "status": "failed"
    }
  ],
  "failures": [
    {
      "type": "target_failed",
      "stage": "execution",
      "summary": "One target could not be processed."
    }
  ],
  "production_changed": true,
  "summary": "Two targets succeeded and one failed."
}
```

The overall verification value is false because the entire requested batch outcome was not achieved.

Individual targets preserve their outcome.

---

# Synthetic VNA Tutorial Result

The unplugged-cable tutorial can exercise exactly the same lifecycle without touching production.

```json
{
  "schema": "vna.verified_result/0.1",
  "id": "result:tutorial-cable-001",
  "proposed_action_id": "action:tutorial-cable-001",
  "provider": {
    "id": "vna",
    "type": "simulation"
  },
  "source": {
    "kind": "synthetic",
    "id": "tutorial:unplugged-cable"
  },
  "status": "succeeded",
  "execution": {
    "attempted": true,
    "completed": true
  },
  "verification": {
    "required": true,
    "performed": true,
    "verified": true
  },
  "completed_at": "2026-10-30T07:30:00Z",
  "verified_at": "2026-10-30T07:30:00Z",
  "outcomes": [
    {
      "type": "vna.synthetic_connection_restored",
      "summary": "Synthetic physical connection restored."
    }
  ],
  "production_changed": false,
  "summary": "Tutorial Layer 1 connection restored and synthetic recovery verified."
}
```

The player succeeded.

Production did not change.

Both facts are true.

---

# Game World Consequences

A game client should react to Verified Results rather than optimistic execution.

For example:

```text
PROPOSED ACTION
      |
      v
GAME SHOWS:
"REQUEST SENT"
```

then:

```text
EXECUTION EVENT
      |
      v
GAME SHOWS:
"WORK IN PROGRESS"
```

then:

```text
VERIFIED RESULT
status = succeeded
verified = true
      |
      v
GAME SHOWS:
"MISSION COMPLETE"
```

If verification fails:

```text
GAME SHOWS:
"THAT DIDN'T WORK"
```

This is more honest and better gameplay.

---

# FileCity Examples

A FileCity move might be visualised as:

```text
Truck leaves source
      |
      v
Data movement occurs
      |
      v
Truck reaches destination
      |
      v
Verification
      |
      v
Goods appear in destination building
```

If verification fails:

```text
Delivery problem
```

rather than celebratory fireworks.

---

# Rocket Rule

For cloud transfers:

```text
Rocket launch
```

can represent execution beginning.

It must not represent verified completion.

Only when:

```text
destination state verified
```

should FileCity display successful arrival.

This preserves one of the ecosystem's fundamental rules:

> **Animation follows verified truth.**

---

# Finding Resolution

A Verified Result may trigger re-observation.

The motivating Finding should normally become resolved only when reality supports that transition.

Example:

```text
Finding:
Service unavailable
state = active

        |
        v

Restart Result:
succeeded + verified

        |
        v

Post-action Observation:
service = running

        |
        v

Finding re-evaluation

        |
        v

Finding:
state = resolved
```

The Result does not directly force the Finding to resolved.

---

# Relationship Updates

Verified operations may cause Relationships to change.

Example:

```text
Before:

storage object
originally_stored_at
storage:data
```

After verified quarantine:

```text
storage object
charkive.quarantined_to
storage:sanitorium
```

Relationship changes should reflect verified reality.

Do not update topology merely because an action was proposed.

---

# Events

The operational lifecycle should also emit Events.

Possible examples include:

```text
vna.action.execution_started
vna.action.execution_completed
vna.action.execution_failed
vna.action.verification_started
vna.action.verification_completed
vna.result.created
```

Events describe what happened along the way.

Verified Result summarises the final outcome.

---

# Result vs Event

Event:

```text
Service restart command completed.
```

Verified Result:

```text
Service restarted and subsequent service-state check reported running.
```

The Result has stronger outcome semantics.

---

# Result vs Observation

Observation:

```text
Service state = running.
```

Verified Result:

```text
The service-restart request achieved its expected result, supported by the post-action running-state observation.
```

The Observation is evidence.

The Result interprets that evidence specifically in the context of the requested operation.

---

# Result vs Finding

Finding:

```text
Service unavailable.
```

Verified Result:

```text
Restart succeeded and service running state verified.
```

The Finding may subsequently become resolved.

The Result and Finding remain independent records.

---

# Result vs Proposed Action

Proposed Action:

```text
What we wanted to happen.
```

Verified Result:

```text
What actually happened.
```

This is the central distinction.

---

# Result vs Snapshot

A Verified Result may later become historical evidence included in a Snapshot.

But the Result is not itself a Snapshot.

Snapshots package state/evidence.

Results describe operational outcomes.

---

# Result and T4

T4 can explain the same Result at different levels.

Example:

```text
T4Tots

"IT WORKED!

The service is running again."


T4Teens

"The restart completed successfully and we checked that the service is now running."


T4Techies

Action:
vna.service_restart

Execution:
completed

Verification:
service state = running

Result:
succeeded

Production changed:
true
```

The explanation changes.

The Result does not.

---

# Result and Oracles

An Oracle may interpret a failed Result.

For example:

```text
Result:

Restart command completed
Verification failed
Service remains stopped
```

Oracle:

> "The restart mechanism completed, but the service is still not running. The original issue remains unresolved."

The Oracle must not rephrase this as:

> "Restart successful."

---

# Result and Generalisation

A Result may need to be generalised before reaching a less-trusted client.

Operational Result:

```text
service:
customer-prod-database
```

Generalised Result:

```text
service:
SERVICE-104
```

The outcome remains:

```text
restart succeeded and verified
```

Generalisation changes identity disclosure.

It must not change operational meaning.

---

# Result and Professional Assessment

VNA may eventually use Verified Results as part of evidence that a player performed real technical work safely.

For example:

```text
diagnosis correct
appropriate action proposed
authorisation respected
operation verified
documentation complete
```

The Verified Result can contribute evidence.

It does not alone prove professional competency.

Assessment remains a separate process.

---

# Audit Chain

The complete chain should eventually be traceable:

```text
OBSERVATION
     |
     v
FINDING
     |
     v
RECOMMENDATION
     |
     v
PROPOSED ACTION
     |
     v
EVENTS
     |
     v
VERIFIED RESULT
     |
     v
POST-ACTION OBSERVATION
     |
     v
FINDING STATE CHANGE
```

This should let an authorised reviewer answer:

```text
What was wrong?

What evidence supported that?

What was requested?

Who requested it?

Was it authorised?

What executed?

What changed?

How was success checked?

What was the final outcome?

Did the original condition disappear?
```

---

# Verified Means Scoped Verification

A Result must not overclaim.

Good:

```text
"The service is running after restart."
```

Bad:

```text
"The application is completely fixed."
```

unless application-level verification also supports that claim.

A Layer 4 repair may not prove Layer 7 functionality.

The Result should remain within the scope actually verified.

---

# OSI Verification

The VNA OSI model creates a useful verification ladder.

Suppose a Layer 1 fault is repaired.

Verification might proceed:

```text
L1
Physical carrier restored

        |
        v

L2
Link operational

        |
        v

L3
Network reachable

        |
        v

L4
Transport succeeds

        |
        v

L7
Application works
```

A Layer 1 Result may truthfully say:

```text
Physical connectivity restored and verified.
```

It should not automatically claim:

```text
Application fixed.
```

until higher-layer verification occurs.

---

# Verification Depth

Providers may eventually expose the depth of verification performed.

For example:

```text
operation-level

target-state

service-level

application-level

end-to-end
```

Verified Result v0.1 does not mandate a universal vocabulary yet.

Producer-specific evidence can record this until implementation experience establishes a common need.

---

# Re-Verification

A successfully verified result describes a point in time.

It does not guarantee permanent future state.

Example:

```text
07:25
Service restart verified.

09:00
Service fails again.
```

The earlier Result remains correct historically.

A new Finding may become active.

Do not rewrite the old Result as failed merely because the problem later returned.

---

# Result Immutability

Once finalised, a Verified Result SHOULD normally be treated as immutable.

Later evidence should create:

```text
new Observations
new Findings
new Events
new Results
```

rather than rewriting history.

---

# Correction

If a Result record itself is later discovered to be malformed or factually incorrect due to a system defect, a future correction/supersession mechanism may be required.

v0.1 does not define that mechanism fully.

Do not silently edit audit history where correction matters.

---

# Idempotent Execution

Where a provider supports idempotent operations, duplicate transport delivery should not create duplicate real-world changes.

Verified Result v0.1 does not yet define a universal idempotency key.

Providers SHOULD nevertheless protect against accidental duplicate execution.

---

# Uncertain Execution

One of the most important cases is:

```text
provider lost connection during execution
```

If the provider cannot establish whether the real operation occurred, report:

```text
status = unknown
```

and:

```text
verification.verified = false
```

Do not guess.

---

# Example Unknown Result

```json
{
  "schema": "vna.verified_result/0.1",
  "id": "result:unknown-001",
  "proposed_action_id": "action:unknown-001",
  "provider": {
    "id": "vna",
    "type": "application"
  },
  "source": {
    "kind": "live"
  },
  "status": "unknown",
  "execution": {
    "attempted": true,
    "completed": false
  },
  "verification": {
    "required": true,
    "performed": false,
    "verified": false
  },
  "completed_at": "2026-10-30T07:25:14Z",
  "production_changed": true,
  "failures": [
    {
      "type": "execution_state_unknown",
      "stage": "execution",
      "summary": "Connection to the provider was lost while the operation was in progress."
    }
  ],
  "summary": "The final state of the operation could not be established."
}
```

If it is impossible even to know whether production changed, a simple boolean becomes insufficient.

For that case, see the next rule.

---

# Unknown Production Change

Some operations may fail so ambiguously that VNA cannot honestly say whether production changed.

To support this, `production_changed` MAY use:

```text
true
false
null
```

in v0.1.

Interpretation:

```text
true
Production definitely changed.

false
Production definitely did not change.

null
Whether production changed cannot be established.
```

Therefore the field type is:

```text
boolean or null
```

This is a deliberate exception to the earlier contracts, where observation/proposal creation itself normally knows whether it changed production.

For a Verified Result, ambiguity is a real operational state.

---

# Updated Field: `production_changed`

## Type

```text
boolean | null
```

## Required

Yes.

Examples:

```json
{
  "production_changed": true
}
```

```json
{
  "production_changed": false
}
```

```json
{
  "production_changed": null
}
```

Never coerce unknown into false.

---

# Safety Principle

> **Unknown is not false.**

This applies particularly after interrupted or partially observed execution.

If VNA does not know whether something changed, the system should say exactly that.

---

# Security Boundary

Verified Results are data.

They do not themselves grant further authority.

A Result saying:

```text
reversible = true
```

does not authorise automatic rollback.

A Result saying:

```text
rollback.available = true
```

means only that a rollback capability exists.

A new Proposed Action is still required.

---

# Untrusted Provider Output

Even provider-generated Results should be treated as untrusted input at system boundaries.

Fields such as:

```text
summary
evidence
outcomes
warnings
failures
attributes
```

MUST be escaped or validated before use in:

```text
HTML
SQL
shell output
game scripting
reports
logs
```

---

# Credentials

Verified Results MUST NOT expose credentials merely because an operation used them.

Do not include:

```text
passwords
tokens
private keys
API secrets
cloud credentials
recovery secrets
```

in Results.

---

# Sensitive Evidence

Verification evidence may itself be sensitive.

Examples:

```text
filesystem paths
hostnames
usernames
service names
customer filenames
network topology
provider identifiers
```

Consumers must respect the same privacy and generalisation boundaries used elsewhere in VNA.

---

# Transport Independence

Verified Result v0.1 defines an outcome object.

It does not mandate transport.

Possible transports include:

```text
HTTP
local IPC
message queue
database
event stream
CLI
game bridge
agent interface
```

The Result semantics remain the same.

---

# Validation Rules

A valid Verified Result v0.1 MUST satisfy:

1. `schema` equals `vna.verified_result/0.1`.
2. `id` is a non-empty string.
3. `proposed_action_id` is a non-empty string.
4. `provider` is an object.
5. `provider.id` is a non-empty string.
6. `provider.type` is a non-empty string.
7. `source` is an object.
8. `source.kind` is one of the recognised source kinds.
9. `status` is one of the recognised v0.1 statuses.
10. `execution` is an object.
11. `execution.attempted` is a boolean.
12. `execution.completed` is a boolean.
13. `verification` is an object.
14. `verification.required` is a boolean.
15. `verification.performed` is a boolean.
16. `verification.verified` is a boolean.
17. `completed_at` is a valid UTC ISO 8601 timestamp.
18. `production_changed` is `true`, `false`, or `null`.
19. `summary` is a non-empty string.
20. If supplied, `started_at` is a valid UTC ISO 8601 timestamp.
21. If supplied, `verified_at` is a valid UTC ISO 8601 timestamp.
22. If supplied, `targets` is an array.
23. Every target contains a valid target identity and a recognised target result status.
24. If supplied, `outcomes` is an array.
25. Every outcome contains a non-empty `type`.
26. If supplied, `evidence` is an array.
27. Every evidence item contains a non-empty `type`.
28. If supplied, `reversible` is a boolean.
29. If supplied, `rollback` is an object.
30. If supplied, `failures` is an array.
31. Every failure contains a non-empty `type`.
32. If supplied, `warnings` is an array.
33. Every warning contains a non-empty `type`.
34. If supplied, `observations` is an array.
35. If supplied, `attributes` is an object.

---

# Semantic Validation

Structural validity does not establish that the Result makes sense.

Consumers SHOULD additionally verify invariants such as:

```text
status = succeeded
AND
verification.required = true

SHOULD imply:

verification.performed = true
verification.verified = true
```

Likewise:

```text
status = rejected

SHOULD normally imply:

execution.attempted = false
production_changed = false
```

And:

```text
status = no_op

SHOULD normally imply:

production_changed = false
```

Exceptions should be explicit and explainable.

---

# Status Consistency

The provider SHOULD NOT emit contradictory combinations such as:

```text
status = succeeded

verification.required = true

verification.performed = false
```

or:

```text
status = rejected

execution.attempted = true
```

without compelling provider-specific semantics and clear evidence.

The common model should make operational truth easier to understand, not harder.

---

# Compatibility Rules

## Provider

A v0.1 provider:

- MUST link the Result to a Proposed Action
- MUST preserve provider identity
- MUST report final status honestly
- MUST distinguish execution from verification
- MUST NOT report full success when required verification failed
- SHOULD verify independently of command return values where practical
- MUST preserve partial failures in batch operations
- MUST use `unknown` rather than guessing ambiguous outcomes
- MUST use `production_changed: null` when change cannot be established
- SHOULD produce post-action Observations
- SHOULD expose rollback capability without automatically invoking it
- MUST NOT hide provider failures merely to produce a clean status

## Consumer

A v0.1 consumer:

- MUST validate fields it depends upon
- SHOULD ignore unknown fields
- SHOULD preserve unknown fields when forwarding Results
- MUST distinguish `succeeded` from `partially_succeeded`
- MUST distinguish `failed` from `rejected`
- MUST distinguish `no_op` from failure
- MUST not treat `unknown` as success
- MUST not treat `production_changed: null` as false
- MUST not assume Finding resolution merely because Result succeeded
- MUST not perform rollback without a new authorised action
- MUST preserve source kind
- MUST treat Result content as untrusted input

---

# v0.1 Deliberate Omissions

Verified Result v0.1 intentionally does not fully define:

```text
distributed transaction semantics
two-phase commit
provider compensation
multi-provider rollback
automatic retries
result supersession
formal correction objects
professional assessment
SLA measurement
billing
full action-event timeline
deep verification levels
idempotency keys
approval history
cryptographic signing
non-repudiation
```

These should evolve from real implementations.

The purpose of v0.1 is narrower:

> **Record what actually happened after an action was proposed, including whether execution occurred and whether the intended result was verified.**

---

# First Implementation Target

Charkive dry-run.

A dry-run proposal produces a Result such as:

```text
status:
succeeded

execution:
attempted = false

verification:
performed = true

production_changed:
false

summary:
Dry-run completed successfully.
```

This proves the Result contract without production risk.

---

# Second Implementation Target

Charkive real quarantine.

```text
PROPOSED ACTION
charkive.quarantine

        |
        v

EXECUTION

        |
        v

VERIFY DESTINATION

        |
        v

VERIFY EXPECTED SOURCE STATE

        |
        v

VERIFIED RESULT
```

Only after the Result says:

```text
status = succeeded
verification.verified = true
```

should downstream clients declare the operation complete.

---

# Third Implementation Target

Synthetic VNA service repair.

```text
SYNTHETIC PROPOSAL
      |
      v
SYNTHETIC EXECUTION
      |
      v
SYNTHETIC OBSERVATION
      |
      v
VERIFIED RESULT
```

This proves the full gameplay lifecycle safely.

---

# Fourth Implementation Target

FileCity copy or move.

```text
PLAYER REQUESTS MOVEMENT
        |
        v
PROPOSED ACTION
        |
        v
TRANSFER STARTS
        |
        v
TRANSFER COMPLETES
        |
        v
DESTINATION VERIFIED
        |
        v
VERIFIED RESULT
        |
        v
WORLD UPDATES
```

Only then should the transport metaphor show a successful delivery.

---

# Fifth Implementation Target

One deliberately low-risk live VNA operation.

The entire chain must already exist:

```text
Finding
Recommendation
Proposed Action
Validation
Authorisation
Events
Execution
Verification
Verified Result
Post-action Observation
Finding re-evaluation
Audit
```

Do not choose the operation because it is impressive.

Choose it because failure modes are easy to understand, contain, and verify.

---

# Contract Stack

At this point the VNA common language is:

```text
ASSET

What is the thing?


STORAGE LOCATION

Where does its data live?


OBSERVATION

What did we see?


FINDING

What noteworthy condition do we think exists?


RELATIONSHIP

How do the things fit together?


SNAPSHOT

What reality did we capture?


EVENT

What happened?


PROPOSED ACTION

What change is being requested?


VERIFIED RESULT

What actually happened, and did reality confirm it?
```

This is the core grammar of the VNA ecosystem.

---

# North Star

A Verified Result should let the ecosystem answer:

```text
WHAT proposal is this the result of?

WHO executed or evaluated it?

WHAT source context did it apply to?

WAS execution attempted?

DID execution complete?

WHAT was the final status?

WAS verification required?

WAS verification actually performed?

DID verification confirm the expected outcome?

WHEN did execution begin?

WHEN did it complete?

WHEN was the outcome verified?

WHICH targets succeeded?

WHICH targets failed?

WHAT outcomes were achieved?

WHAT evidence proves those outcomes?

DID production change?

IF WE DON'T KNOW WHETHER PRODUCTION CHANGED, IS THAT UNCERTAINTY EXPLICIT?

IS the result reversible?

IS rollback available?

WHAT failed?

WHAT warnings remain?

WHAT post-action Observations were created?
```

And the contract should make one principle impossible to ignore:

> **A command completing is not the same thing as the job being done.**

> **The job is done when the expected result is observed and verified.**

Or, in VNA terms:

```text
DON'T TRUST THE BUTTON.

LOOK AT REALITY.
```
