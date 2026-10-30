# Virtual Network Admin Roadmap

## Vision

Virtual Network Admin (VNA) is the reality and telemetry layer of a broader playable digital twin.

The long-term goal is to model real infrastructure, observe its actual state, identify problems and opportunities, and expose those conditions as structured events that can be understood both by conventional interfaces and by interactive or game-like clients.

The central idea is simple:

> **Real-world issues resolved through gameplay.**

Gameplay should not merely award points for completing conventional administrative tasks. The game itself can become an interface to useful work.

A player's actions should ultimately be capable of investigating, resolving, verifying, and reporting on real infrastructure issues.

```text
Reality
   ↓
Telemetry
   ↓
Virtual Network Admin
   ↓
Structured State + Events
   ↓
Simulation / Visualisation
   ↓
Gameplay
   ↓
Controlled Real-World Action
   ↓
Verification
   ↓
System Report
```

The system report is fundamental. The game metaphor must never obscure what is actually happening.

---

# Role in the Wider Ecosystem

VNA can become the authoritative reality layer beneath several related projects.

```text
                 PLAYABLE DIGITAL TWIN
                         │
                Virtual Network Admin
                 reality / telemetry
                         │
          ┌──────────────┴──────────────┐
          │                             │
      FileCity                      Charkive
   map / topology                health / response
          │                             │
          └──────────────┬──────────────┘
                         │
                      BoreDOOM!
              embodied / multiplayer UI
```

The responsibilities remain deliberately separated.

## Virtual Network Admin

Understands real infrastructure:

- Machines
- Servers
- Networks
- Storage
- Services
- Metrics
- Health
- Alerts
- Operational state

## FileCity

Turns storage topology and activity into a navigable spatial model.

It answers:

> **Where is everything and what is happening to it?**

## Charkive

Provides storage hygiene, preservation, quarantine and emergency response.

Within the FileCity metaphor, Charkive becomes the city's Police, Fire, Sanitation and SANitorium services.

It answers:

> **What requires attention, and what can safely be done about it?**

## BoreDOOM!

Provides the embodied and eventually multiplayer interface.

Players can explore real or generated environments and interact with systems represented within them.

It answers:

> **What if administering infrastructure felt like inhabiting it?**

---

# Core Design Principle

## Reality first, metaphor second

The game world is never authoritative.

```text
REAL SYSTEMS
     ↓
TELEMETRY
     ↓
VNA / FILECITY / CHARKIVE
     ↓
STRUCTURED WORLD MODEL
     ↓
RENDERERS / GAME CLIENTS
```

The visual metaphor represents state owned elsewhere.

Likewise, actions should travel through controlled application interfaces rather than allowing a renderer or game client unrestricted access to infrastructure.

```text
PLAYER
   ↓
GAME ACTION
   ↓
PROPOSED REAL ACTION
   ↓
VALIDATION / AUTHORISATION
   ↓
EXECUTION
   ↓
VERIFICATION
   ↓
SYSTEM REPORT
```

This maintains the preservation-first and safety-first philosophy already present in FileCity and Charkive.

---

# Non-Negotiable Principle: Never Hide Reality Behind the Metaphor

A burning building must never merely mean:

> FIRE!

It must be possible to discover what the representation actually means.

For example:

```text
Asset: eddie
Status: Warning

Filesystem:
<real filesystem>

Condition:
Storage capacity warning

Evidence:
<real telemetry>

Source:
<real system report>

Recommended Action:
<actual proposed operation>

Risk:
<actual operational risk>
```

The exact visual language can be playful.

The underlying information must remain operationally meaningful.

This is what separates the project from conventional gamification.

---

# Phase 1: Asset Inventory

## Goal

Create a common representation of the infrastructure VNA knows about.

Initial concepts should include:

```text
Machine
Storage
Service
Network
Account
Status
Metric
Alert
```

The first version does not need a 3D world, gameplay, simulation or multiplayer.

The initial win is simply:

> **VNA knows what exists.**

An asset should have a stable identity that other systems can reference.

For example:

```text
eddie
prodesk-01
prodesk-02
nas
router
storage-array
service-x
```

The exact implementation can evolve independently of the external identity.

---

# Phase 2: Machine-Readable System Reports

## Goal

Make infrastructure state available through a common structured report.

Conceptually:

```json
{
  "asset": "eddie",
  "status": "warning",
  "storage": {},
  "services": {},
  "network": {},
  "alerts": []
}
```

This becomes the bridge between real infrastructure and every higher-level representation.

A graphical dashboard may display the report.

FileCity may turn part of it into buildings.

BoreDOOM! may turn part of it into an interactive object.

The underlying report remains the same.

## Quick Win

Start with one real machine.

Prove:

```text
Real machine
     ↓
VNA
     ↓
Structured status report
```

Then expand to multiple assets.

---

# Phase 3: Event Feed

## Goal

Convert changes in infrastructure state into structured events.

Potential future event types include:

```text
storage.capacity.warning
storage.growth.anomaly

service.unavailable
service.recovered

network.unreachable
network.recovered

replication.warning
replication.failed
replication.complete
```

The precise taxonomy should evolve as real integrations are built.

The important architectural distinction is:

> **Reality produces events. Clients interpret them.**

For example:

```text
VNA EVENT

storage.capacity.warning
        ↓
```

A traditional interface might display:

```text
WARNING: Storage capacity threshold reached.
```

FileCity might display:

```text
🚨 Storage District Alert
```

A game renderer might show:

```text
🔥 Smoke from a building
🚒 Emergency response
```

Those are different representations of the same underlying event.

---

# Phase 4: FileCity Integration

VNA should be able to supply infrastructure and storage topology to FileCity without requiring VNA itself to understand how a city should look.

Conceptually:

```text
VNA
 │
 │ infrastructure state
 ↓
FileCity World Model
 │
 ├── Web City View
 ├── Experimental 3D renderer
 ├── BeamNG proof of concept
 └── BoreDOOM! client
```

Physical infrastructure can appear as FileCity districts alongside cloud resources.

Examples might eventually include:

```text
Server
NAS
USB storage
Storage cluster
Cloud account
Remote filesystem
```

Local versus cloud should not define the overall architecture.

They are simply different locations within the same data estate.

---

# Phase 5: Charkive Integration

Charkive becomes an operational responder rather than the "local-storage counterpart" to FileCity.

VNA can expose infrastructure conditions.

Charkive can expose storage-health conditions and controlled responses where appropriate.

```text
REAL STORAGE CONDITION
        ↓
      VNA
        ↓
   CHARKIVE REPORT
        ↓
   FILECITY EVENT
        ↓
 VISUAL REPRESENTATION
```

Within the city metaphor, this allows Charkive to become municipal emergency services.

## Fire Department

Potential representation for urgent storage-health events.

Examples may eventually include:

- Severe capacity pressure
- Rapid unexpected storage growth
- Serious storage-health conditions

## Police

Potential representation for conditions requiring investigation.

## Sanitation

Represents Charkive's handling of likely disposable data.

```text
Candidate data
      ↓
Review
      ↓
Quarantine
      ↓
SANitorium
```

## SANitorium

The existing reversible-quarantine philosophy becomes a physical place within the visual metaphor.

Files are not represented as disappearing.

They have been moved somewhere recoverable.

---

# Phase 6: The Playable Digital Twin

Once real infrastructure can generate trustworthy structured state and events, a game client can interpret them as missions.

The important inversion is:

> **Do not invent a mission and attach work to it.**
>
> **Observe real work that needs doing and turn it into a mission.**

Conceptually:

```text
REAL PROBLEM
    ↓
VNA ALERT
    ↓
MISSION GENERATED
    ↓
PLAYER INVESTIGATES
    ↓
ACTION PROPOSED
    ↓
VALIDATED OPERATION
    ↓
REAL SYSTEM CHANGES
    ↓
VERIFY
    ↓
MISSION COMPLETE
```

This is the central long-term proposition.

---

# Gameplay as Useful Work

Most productivity gamification effectively does this:

```text
Do work
   ↓
Receive points
```

VNA should ultimately allow something fundamentally different:

```text
Observe world
    ↓
Discover problem
    ↓
Investigate
    ↓
Understand evidence
    ↓
Choose response
    ↓
Perform controlled action
    ↓
See real-world consequence
```

The player's actions should correspond to useful operational outcomes.

Gameplay is therefore not merely a reward layered on top of administration.

> **Gameplay becomes an interface to administration.**

---

# System Reports as Gameplay Objects

Reports should be accessible from inside the interactive world.

For example, a player might enter a virtual representation of a server room and interact with a server.

```text
EDDIE

STATUS
Warning

Storage
██████████████████░░

Services
Nominal

Network
Nominal

Alerts
1

[Inspect Alert]
```

Selecting the alert should expose the underlying evidence rather than merely explaining it through fictional world-building.

This provides progressively deeper layers:

```text
WORLD
  ↓
OBJECT
  ↓
STATUS
  ↓
ALERT
  ↓
REPORT
  ↓
RAW / AUTHORITATIVE EVIDENCE
```

A casual player may only need the upper layers.

An administrator can drill down to the real information.

---

# Multiplayer Vision

Different players may eventually perform different operational roles.

Possible roles include:

## Network Engineer

Focused on:

- Connectivity
- Routers
- Switches
- Network health

## Storage Engineer

Focused on:

- Capacity
- Storage topology
- Migration
- Replication
- FileCity

## Charkive Emergency Response

Focused on:

- Storage-health alerts
- Candidate-data investigation
- Quarantine
- Recovery

## Archivist

Focused on:

- Data classification
- Preservation
- Migration
- Long-term storage

Roles should not become arbitrary RPG classes.

They should correspond to actual categories of work understood by the underlying systems.

---

# BoreDOOM! Integration

BoreDOOM! provides an opportunity to make VNA spatial and embodied.

The first integration should be deliberately tiny.

## Quick Win: One Real Object

Represent exactly one VNA-managed machine inside the game world.

For example:

```text
Virtual Server Rack
        ↓
Interact
        ↓
VNA Request
        ↓
Real System Report
        ↓
Displayed In-World
```

This proves the critical loop:

> **The game world can expose reality.**

Nothing more sophisticated is required for the first integration.

---

# Later BoreDOOM! Integration

Once the basic reporting loop works:

- Infrastructure events can become missions
- FileCity can become an explorable environment
- Charkive alerts can require player response
- Players can investigate incidents cooperatively
- Completed actions can produce real system reports
- Successful remediation can change the game world automatically

The environment thereby becomes a live digital twin rather than a static level.

---

# Human and Miniature-Scale Interaction

BoreDOOM!'s scanned-room concept creates an unusual opportunity.

A real room can be reconstructed at real scale.

Some players may experience it at approximately human scale, while others experience the same space at much smaller scale.

At miniature scale:

```text
Desk → landscape
Cable → route / bridge
Server rack → enormous structure
Storage device → building / facility
```

This can allow infrastructure itself to become an explorable world.

The concept should remain independent of a specific renderer or engine until prototypes establish which implementation works best.

---

# FileCity as an Explorable Data Estate

VNA's infrastructure model and FileCity's storage model can eventually produce a combined spatial environment.

Conceptually:

```text
FILECITY

Downtown
  Active storage

Industrial District
  Servers / compute / storage infrastructure

Archive District
  Long-term storage

Cloud District
  Cloud-hosted data

SANitorium
  Quarantined data

Emergency Services
  Charkive
```

The user's infrastructure therefore becomes geography.

---

# Data Movement as Transport

Infrastructure events involving data movement can become visible movement through the world.

The FileCity visual language may eventually include:

```text
Ground → Cloud
🚀 Rocket

Cloud → Ground
🪂 Parachute

Ground → Ground
🚚 Vehicle / freight

Cloud → Cloud
✈️ Air transport
```

These representations are deliberately metaphorical.

Their source state is not.

Every movement should ultimately correspond to real reported activity where such integration exists.

---

# Traffic

Transfers between storage locations can become traffic.

The intention is not simply decoration.

A successful implementation may make activity patterns spatially understandable:

```text
Storage A
   │
   │ data flow
   ↓
Storage B
```

becomes:

```text
District A
   │
   │ transport route
   ↓
District B
```

The abstraction should emphasize important activity rather than attempt to create one visual object for every individual file operation.

---

# Generalisation

Large infrastructure and storage estates cannot be represented literally.

The world model therefore needs generalisation.

Instead of:

```text
1 file = 1 object
1 folder = 1 building
```

prefer hierarchical aggregation:

```text
Estate
  ↓
District
  ↓
Block
  ↓
Building
  ↓
Floor / sector
  ↓
Detailed information on demand
```

The representation becomes more detailed as the user investigates.

The goal is comprehensibility, not a one-to-one reconstruction of filesystem hierarchy.

---

# Renderer Independence

No game engine should become foundational to VNA.

VNA provides state.

Other systems render it.

```text
VNA
 ↓
WORLD / EVENT MODEL
 ↓
 ├── Conventional Web UI
 ├── FileCity Web Renderer
 ├── BeamNG Experiment
 ├── BoreDOOM!
 └── Future Clients
```

BeamNG can therefore be explored aggressively without turning it into an architectural dependency.

Likewise, BoreDOOM! should be free to choose whichever rendering/game technology ultimately proves appropriate.

The data model survives regardless.

---

# Safety Boundary

Real-world actions require a stronger trust boundary than visualisation.

The client should not directly execute arbitrary infrastructure commands.

Preferred conceptual flow:

```text
GAME CLIENT
     ↓
REQUESTED ACTION
     ↓
VNA
     ↓
POLICY / VALIDATION
     ↓
OPTIONAL HUMAN CONFIRMATION
     ↓
CONTROLLED EXECUTION
     ↓
VERIFY RESULT
     ↓
AUDIT
     ↓
REPORT BACK TO CLIENT
```

Game clients are therefore interfaces, not privileged infrastructure controllers.

This allows playful interaction without treating a game environment as inherently trusted.

---

# Verification

An operation is not complete merely because a command was issued.

The intended lifecycle is:

```text
OBSERVE
   ↓
PROPOSE
   ↓
AUTHORISE
   ↓
EXECUTE
   ↓
VERIFY
   ↓
REPORT
```

Only after verification should the simulated world show the incident as resolved.

This means the world itself becomes feedback:

```text
Problem detected
     ↓
Alarm appears

Action performed
     ↓
Verification pending

Verification succeeds
     ↓
Alarm clears
     ↓
World returns to healthy state
```

---

# Auditability

Real-world actions triggered through interactive clients should be attributable and reviewable.

The audit trail should eventually make it possible to answer questions such as:

```text
What happened?
What asset was affected?
What condition triggered the action?
What action was requested?
Was it authorised?
What actually executed?
What was the result?
Was the result verified?
```

Gameplay must never make operational history less transparent.

Ideally it makes infrastructure behaviour easier to understand.

---

# Short-Term Roadmap

## Slice 1: Know What Exists

Implement the initial asset inventory.

Success means:

> VNA can represent one or more real infrastructure assets consistently.

---

## Slice 2: Know What State They're In

Produce machine-readable system reports.

Success means:

> Another application can retrieve meaningful state without understanding the underlying monitoring mechanism.

---

## Slice 3: Know When Something Changes

Introduce structured events.

Success means:

> Consumers can react to a real change without continuously interpreting raw telemetry themselves.

---

## Slice 4: Expose One Asset to Another UI

Create the smallest possible external integration.

For example:

```text
VNA → FileCity

or

VNA → BoreDOOM!
```

Success means:

> One real VNA asset exists meaningfully in another project.

---

## Slice 5: One Real Alert

Take one trustworthy real-world condition and expose it as an event.

Success means:

```text
REAL CONDITION
      ↓
VNA EVENT
      ↓
EXTERNAL REPRESENTATION
```

---

## Slice 6: One Safe Action

Choose one deliberately low-risk action and implement the complete controlled loop.

```text
Observe
  ↓
Request
  ↓
Validate
  ↓
Execute
  ↓
Verify
  ↓
Report
```

Success means:

> An external interface can initiate one useful real action without bypassing VNA's safety boundary.

---

## Slice 7: First Gameplay Loop

Represent that real alert as a gameplay objective.

Success means:

```text
REAL ISSUE
   ↓
GAME OBJECTIVE
   ↓
PLAYER INVESTIGATION
   ↓
CONTROLLED ACTION
   ↓
REAL ISSUE RESOLVED
   ↓
VERIFIED
   ↓
GAME OBJECTIVE COMPLETE
```

This is the first truly significant proof of the overall vision.

---

# Long-Term Vision

Virtual Network Admin becomes the authoritative operational state engine for a playable digital twin of real infrastructure.

The environment can represent:

- Machines
- Storage
- Networks
- Services
- Cloud resources
- Data movement
- Health
- Incidents
- Maintenance
- Recovery

FileCity provides geography.

Charkive provides storage health and emergency response.

BoreDOOM! provides embodiment and multiplayer interaction.

VNA keeps the entire experience anchored to reality.

The ultimate milestone is:

> **Completing a mission in BoreDOOM! resolves, verifies and records an actual infrastructure task.**

At that point, the distinction between "playing the game" and "administering the system" becomes intentionally blurred.

The player has explored a world.

The administrator has solved a problem.

They are the same person performing the same action through a better interface.

## North Star

> **Gameplay isn't wasted life. It's real, fun work.**
