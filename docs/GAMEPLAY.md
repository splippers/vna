# VNA Gameplay

> **You don't learn how computers work by looking at diagrams. You go inside them.**

## Purpose

This document defines the gameplay language of Virtual Network Admin (VNA).

VNA turns computing, networking, diagnostics, administration, maintenance, and
technical learning into explorable environments.

The underlying systems may be:

- handcrafted tutorials
- synthetic simulations
- vendor-supplied training scenarios
- captured real-world systems
- generalised representations of real infrastructure
- eventually, authorised live infrastructure

The gameplay may be spectacular.

The underlying evidence must remain honest.

VNA's central gameplay idea is:

> **Real systems. Real problems. Playable work.**

---

# Relationship With BoreDOOM!

BoreDOOM! and VNA are related but distinct.

BoreDOOM! is fundamentally a domestic chore game.

It explores how boring real-world jobs such as hoovering, tidying, sorting, and
maintenance can become enjoyable spatial gameplay.

VNA applies the same broader philosophy to IT:

```text
BoreDOOM!

Boring domestic work
        |
        v
Gameplay
        |
        v
Useful real-world outcome


VNA

Boring / difficult technical work
        |
        v
Gameplay
        |
        v
Understanding / diagnosis / report
```

BoreDOOM! may contribute gameplay ideas such as:

- missions
- spatial interaction
- human and miniature scale
- multiplayer
- progression
- tools
- vehicles
- procedural environments
- reporting

VNA owns the IT-specific world.

The OSI model, operating-system worlds, technical Oracles, SysMedic scenarios,
FileCity, Charkive incidents, vendor equipment, and infrastructure training
belong primarily to VNA.

---

# Core Gameplay Philosophy

VNA should not teach computing by placing conventional lessons inside a VR
environment.

It should turn the concepts themselves into places, journeys, objects,
relationships, and problems.

The guiding rule is:

> **Never teach through a diagram when the player could experience the
> diagram.**

If the concept is distance:

```text
Make the player travel.
```

If the concept is bandwidth:

```text
Give the route limited capacity.
```

If the concept is congestion:

```text
Create traffic.
```

If the concept is dependency:

```text
Allow failure below to affect the systems above.
```

If the concept is abstraction:

```text
Let expertise remove repetitive detail.
```

If the concept is maintenance:

```text
Give the player the tools.
```

---

# Start in the 1960s

The player should begin with almost none of the technological conveniences a
modern administrator takes for granted.

Their first office is a 1960s-inspired Layer 7 workplace.

It contains things such as:

```text
Desk
Filing cabinets
Paper logs
Wall diagrams
Rotary telephone
Teletype
Clipboard
Basic diagnostic tools
Elevator
```

There is no magical telemetry.

There is no teleportation.

There is no omniscient map showing exactly where the fault is.

There is no button marked:

```text
FIX COMPUTER
```

If something is wrong in another building, the player has to go there.

This is intentional.

---

# Why Start Primitive?

Technology is compressed distance.

Modern computing hides enormous quantities of movement, procedure, dependency,
and infrastructure.

The beginner should experience some of that inconvenience before being given
the abstractions that remove it.

The player begins with:

```text
YOU
 |
 v
CLIPBOARD
 |
 v
ELEVATOR
 |
 v
FEET
 |
 v
OTHER MACHINE
```

Progress gradually introduces:

```text
TOOLS
  |
  v
VEHICLES
  |
  v
TERMINALS
  |
  v
NETWORKS
  |
  v
REMOTE ACCESS
  |
  v
TELEMETRY
  |
  v
ORACLES
  |
  v
REMOTE PRESENCE
  |
  v
TELEPORTATION
```

The player understands the abstraction because they previously experienced the
thing it abstracts.

---

# The Layer 7 Office

The player's normal home is Layer 7.

This is where human-facing symptoms tend to become visible.

Typical reports might include:

```text
"The application isn't working."

"I can't reach the other computer."

"The website won't load."

"The file server has disappeared."

"The cloud isn't syncing."

"Everything is broken."
```

The user's symptom appears at Layer 7.

The root cause may be somewhere else entirely.

The administrator's job is to determine where.

---

# The OSI Tower

VNA turns the OSI model into architecture.

A networked system can be represented as a seven-storey structure.

```text
┌──────────────────────────────────────────┐
│ L7  APPLICATION                          │
│     Applications, services, user needs   │
│     Your office                          │
├──────────────────────────────────────────┤
│ L6  PRESENTATION                         │
│     Representation, encoding, crypto     │
├──────────────────────────────────────────┤
│ L5  SESSION                              │
│     Conversations and state              │
├──────────────────────────────────────────┤
│ L4  TRANSPORT                            │
│     Connections, TCP, UDP, ports         │
├──────────────────────────────────────────┤
│ L3  NETWORK                              │
│     Addressing and routing               │
├──────────────────────────────────────────┤
│ L2  DATA LINK                            │
│     Local links, switching, frames       │
├──────────────────────────────────────────┤
│ L1  PHYSICAL                             │
│     Physical communication               │
└────────────────────┬─────────────────────┘
                     │
                     v
               PHYSICAL WORLD
```

The tower is both a teaching environment and a diagnostic environment.

The player should learn the OSI model because they repeatedly use it to solve
problems.

They should not need to memorise it merely because the game asks them a quiz
question.

---

# The Elevator

Movement between OSI layers initially happens through an elevator.

The elevator is deliberately important.

A novice may begin a diagnostic journey at Layer 7 and descend through the
stack:

```text
L7
 |
 v
L6
 |
 v
L5
 |
 v
L4
 |
 v
L3
 |
 v
L2
 |
 v
L1
```

Each floor provides evidence.

The player gradually learns which evidence eliminates which classes of fault.

At first, they may stop everywhere.

Eventually, they recognise patterns and travel more intelligently.

---

# Root Cause and Propagated Symptoms

A failure at a lower layer may cause visible problems throughout the layers
above it.

VNA should model this distinction clearly.

For example:

```text
L7  APPLICATION UNAVAILABLE
 ↑
L6  COMMUNICATION UNAVAILABLE
 ↑
L5  SESSION CANNOT ESTABLISH
 ↑
L4  TRANSPORT CANNOT CONNECT
 ↑
L3  NETWORK UNAVAILABLE
 ↑
L2  LINK UNAVAILABLE
 ↑
L1  ROOT CAUSE: PHYSICAL CONNECTION LOST
```

Layers 2 through 7 are affected.

They are not necessarily independently broken.

This leads to one of VNA's most important lessons:

> **Where a problem is experienced is not necessarily where the problem
> originates.**

---

# Tutorial Mission 1: The Unplugged Cable

The first OSI troubleshooting mission should be gloriously simple.

## Symptom

The application in another office is unavailable.

The player begins in their Layer 7 office.

They inspect the problem.

They ride down the tower.

Each layer provides further evidence.

Eventually:

```text
DING

LAYER 1
```

The player reaches the physical world.

The cable is unplugged.

That is the root cause.

---

# Recovery Propagates Upwards

When the Layer 1 problem is corrected, the rest of the tower can visibly
recover.

```text
L1  CONNECTED
      ↑
L2  LINK AVAILABLE
      ↑
L3  NETWORK AVAILABLE
      ↑
L4  TRANSPORT AVAILABLE
      ↑
L5  SESSION ESTABLISHED
      ↑
L6  DATA UNDERSTOOD
      ↑
L7  APPLICATION WORKING
```

The player experiences dependency rather than reading about it.

---

# Tutorial Mission 2: Not the Cable

Mission 2 begins almost identically.

The application is unavailable.

The confident player descends to Layer 1.

The cable is connected.

Layer 1 is healthy.

Nothing is wrong there.

Welcome to troubleshooting.

The game now begins teaching:

> Similar symptoms can have different causes.

The player must use evidence rather than memorising the solution to the
previous mission.

---

# Layer 1 Is the Physical World

Layer 1 is where VNA's abstract system meets physical reality.

At this level the player may physically travel between systems.

Early gameplay might look like:

```text
SOURCE
  L7
   |
   v
  L6
   |
   v
  L5
   |
   v
  L4
   |
   v
  L3
   |
   v
  L2
   |
   v
  L1
   |
   v
PHYSICAL JOURNEY
   |
   v
  L1
   |
   v
  L2
   |
   v
  L3
   |
   v
  L4
   |
   v
  L5
   |
   v
  L6
   |
   v
  L7
DESTINATION
```

Communication becomes geography.

---

# Layer 1 Transport

Technical progress at Layer 1 can be represented as better transportation.

The exact mappings are gameplay metaphors rather than literal networking
definitions.

Possible progression includes:

```text
Walking
Running
Bicycle
Motorcycle
Car
Service van
Bus
Train
High-speed rail
Aircraft
Specialist transport
```

Different transportation systems may communicate concepts such as:

- capacity
- latency
- shared media
- dedicated links
- congestion
- reliability
- distance

A huge data flow might resemble freight.

A low-capacity route might become congested.

A failed physical route might literally be impassable.

The metaphor should illuminate the technical concept rather than merely
decorate it.

---

# Technology Progression

VNA progression should primarily represent technological abstraction.

The player does not merely collect experience points.

They earn tools and conveniences that change how technical work can be
performed.

A broad progression might be:

```text
WALK
  |
  v
RUN
  |
  v
BICYCLE
  |
  v
MOTORCYCLE
  |
  v
CAR
  |
  v
SERVICE VAN
  |
  v
BUS
  |
  v
TRAIN
  |
  v
HIGH-SPEED RAIL
  |
  v
AIRCRAFT
  |
  v
TERMINAL ACCESS
  |
  v
REMOTE TERMINAL
  |
  v
REMOTE CONSOLE
  |
  v
CENTRALISED LOGGING
  |
  v
LIVE TELEMETRY
  |
  v
ORACLE ASSISTANCE
  |
  v
REMOTE PRESENCE
  |
  v
LAYER JUMP
  |
  v
SYSTEM TELEPORT
  |
  v
INCIDENT TELEPORT
```

Progression means:

```text
UNDERSTANDING
     |
     v
TRUST
     |
     v
BETTER TOOLS
     |
     v
FASTER ACCESS
     |
     v
MORE ABSTRACTION
     |
     v
GREATER RESPONSIBILITY
```

Not:

```text
GRIND
  |
  v
BIGGER NUMBER
```

---

# The Office as the Progress Bar

The player's Layer 7 office should evolve alongside their technical
capabilities.

The office effectively becomes a three-dimensional representation of career
progression.

---

# Era 1: Analogue Office

Possible environment:

```text
Paper records
Filing cabinets
Rotary telephone
Wall charts
Teletype
Clipboard
Basic testing tools
```

Information is local.

Searching is manual.

Travelling is normal.

---

# Era 2: Early Computing

Possible upgrades:

```text
Terminals
Electronic records
Better telecommunications
Local diagnostic systems
Early computerised inventory
```

The player begins to understand the value of remote information.

---

# Era 3: Network Operations

The office develops into an operations environment.

Possible upgrades:

```text
Multiple displays
Network maps
Remote terminals
Incident board
Centralised status
Searchable logs
```

Some journeys can now be replaced with remote access.

---

# Era 4: Telemetry Operations

The player gains:

```text
Live telemetry
Historical telemetry
Cross-system correlation
Structured findings
Activity timelines
Anomaly indicators
```

The world becomes increasingly observable.

---

# Era 5: Spatial Operations

The office can eventually become the command centre for a wider VNA digital
twin.

Possible capabilities include:

```text
FileCity
Charkive incidents
SysMedic snapshot investigations
Operating-system worlds
Technical Oracles
Vendor equipment
Multiplayer operations
```

---

# Fast Travel

Fast travel should be earned.

The player may gradually unlock shortcuts through systems and locations they
understand.

For example:

```text
KNOWN ROUTE
     |
     v
FAST TRAVEL AVAILABLE
```

or:

```text
KNOWN HEALTHY LAYERS
        |
        v
LAYER BYPASS AVAILABLE
```

A shortcut represents confidence grounded in evidence.

---

# Teleportation

Teleportation is the ultimate expression of administrative abstraction.

A mature player may inspect evidence and conclude:

> **This is a Layer 3 problem.**

Instead of physically walking the known route, they can request:

```text
TELEPORT

Asset:
EDDIE

Destination:
Layer 3
```

Teleportation should not be unlimited magic.

It may depend on:

- player capability
- authorisation
- known destination
- available telemetry
- established route
- known system state

If those assumptions fail, teleportation may fail too.

---

# Back to Layer 1

Even the most advanced administrator should sometimes lose all the convenient
abstractions.

Possible causes include:

```text
Remote access unavailable
Telemetry unavailable
Routing failure
Power failure
Unknown hardware
Disconnected system
Offline equipment
Broken management plane
```

Then the game gives the player:

```text
Torch
Clipboard
Diagnostic tool
Car keys
```

and says:

> **Go and look.**

Expertise makes work faster.

It does not abolish the physical world.

---

# OS Worlds

The OSI Tower describes communications.

Operating systems require their own environments.

VNA should eventually support distinct technical worlds for:

```text
Windows
Linux
macOS
```

These must not simply be identical maps with different textures.

Each environment should reflect the concepts and diagnostic surfaces relevant
to that operating system.

---

# Windows World

Possible areas might represent concepts such as:

```text
Applications
Services
Event logs
Registry
Updates
Drivers
Boot configuration
Storage
PowerShell
Security
```

A Windows problem can become a journey through these systems.

For example, a stuck update might appear as halted construction.

A failed service might appear as an inactive facility.

The visual metaphor remains tied to actual or simulated technical state.

---

# Linux World

Possible environments include:

```text
Processes
systemd
journal
kernel
packages
mounts
filesystems
networking
shell
configuration
```

A player familiar with Windows should gradually learn to recognise analogous
concepts without pretending the platforms are identical.

---

# macOS World

Possible environments include:

```text
Applications
Unified logging
launchd
volumes
APFS
recovery
security
system configuration
```

Again, platform-specific reality should remain visible beneath the common VNA
experience.

---

# Cross-Platform Understanding

Expertise should eventually allow the player to recognise shared concepts
across different systems.

Conceptually:

```text
CONCEPT           WINDOWS        LINUX          macOS

Service           Service        systemd unit   launchd job
System evidence   Event logs     journal        unified log
Storage           volumes        mounts         APFS volumes
Automation        PowerShell     shell          shell/tools
```

These mappings are learning aids.

Platform-specific behaviour still matters.

---

# T4 Difficulty

Telemetry4Toddlers and the broader T4 family can control how much technical
interpretation VNA provides.

The underlying fault does not have to become artificially harder for an
experienced player.

Instead, the assistance decreases.

---

# T4Tots

Highly explanatory.

```text
"The connection between these computers is broken."
```

Suitable for introducing fundamental ideas.

---

# T4Teens

Introduces real concepts.

```text
"The physical connection is down, so the higher networking layers cannot
communicate."
```

---

# T4Techies

Provides technical interpretation and evidence.

```text
Physical interface:
No carrier

Higher-layer connectivity:
Unavailable

Recommended investigation:
Physical connection
```

---

# Expert Mode

Let the administrator inspect the evidence directly.

Minimal interpretation.

The difficulty is not created by hiding reality.

It comes from requiring the player to interpret it.

---

# Oracles

AI assistants within VNA become Oracles.

An Oracle should help the player reason.

It should not simply hand over the answer.

---

# Oracle Mk I

Early capability:

```text
Search documentation
Explain terminology
Offer broad hints
```

---

# Oracle Mk II

Developing capability:

```text
Read selected evidence
Suggest diagnostic questions
Explain likely relationships
```

---

# Oracle Mk III

Advanced capability:

```text
Correlate findings
Compare systems
Analyse historical evidence
Generate hypotheses
Identify contradictory evidence
```

---

# Oracle Rules

The Oracle must distinguish:

```text
Evidence
Observation
Inference
Hypothesis
Recommendation
Action
```

It should not convert:

```text
"probably"
```

into:

```text
"definitely"
```

merely because the player asked confidently.

---

# Oracle Personalities

Different interfaces or characters may eventually present Oracle assistance in
different ways.

Personality may alter:

- tone
- explanation style
- humour
- verbosity
- teaching approach

It must not alter the underlying evidence.

---

# SysMedic Worlds

SysMedic brings machines into VNA that may not be permanently connected.

Possible sources include:

```text
Broken machine
Offline machine
Isolated machine
Customer machine
Historical machine
```

The workflow can become:

```text
REAL MACHINE
     |
     v
SYSMEDIC
     |
     v
ENCRYPTED SNAPSHOT
     |
     v
ANALYSIS
     |
     v
VNA
     |
     v
GENERALISED WORLD
     |
     v
PLAYER INVESTIGATION
```

This means VNA can create a playable investigation from a real system without
requiring live game access to that system.

---

# Snapshot Missions

A SysMedic snapshot might generate an investigation.

The game should state clearly that the world represents captured historical
state.

For example:

```text
SOURCE

SysMedic snapshot

Captured:
[time]

Live:
No

Production access:
None
```

The player may investigate and diagnose.

The original machine remains untouched.

---

# FileCity

FileCity provides the spatial data landscape.

Within VNA, it may become somewhere the player can physically visit.

```text
FileCity
├── Storage districts
├── Folder buildings
├── Cloud sky
├── Transfer routes
├── SANitorium
└── Storage incidents
```

The city is driven by FileCity's world model.

VNA provides infrastructure context.

---

# Charkive Services

Charkive provides storage-health, preservation, sanitation, and archaeological
findings.

Within VNA these may become city services.

Possible representations include:

```text
Sanitation Department
Fire Department
Police Department
Archaeology Service
SANitorium
```

A fire engine should never appear merely because dramatic scenery was needed.

There must be a finding beneath it.

---

# Data Archaeology

Charkive Data Archaeology can create investigation missions that do not involve
repairing anything.

A player might investigate:

```text
Old storage district
Historical activity cluster
Forgotten project
Photograph collection
Unknown archive
Possible preservation target
```

The outcome may simply be:

```text
Human review recommended.
```

Playing the mission has produced useful investigative work without modifying
the original data.

---

# Temporal Anomaly Missions

A Charkive TEMPORAL ANOMALY can become an archaeological mystery.

The world may contain:

```text
A district apparently built in the wrong decade

A collection stranded on the wrong timeline

A device whose clock disagrees with everything around it

Historical evidence contradicting recorded timestamps
```

The player gathers evidence.

They may conclude:

```text
Device clock probably incorrect.
```

The game records the conclusion.

It does not silently rewrite the original metadata.

---

# Vendor Worlds

Manufacturers and software vendors may eventually supply authoritative
training environments.

A vendor package could describe:

```text
Equipment
Physical layout
Components
Interfaces
Firmware controls
Telemetry
Normal behaviour
Known faults
Diagnostic procedures
Assessment criteria
```

This could allow players to gain experience with equipment they cannot
physically access.

---

# Vendor Scenarios

Possible progression:

```text
Guided introduction
        |
        v
Component identification
        |
        v
Guided fault
        |
        v
Open investigation
        |
        v
Complex diagnosis
        |
        v
Team incident
        |
        v
Verified assessment
```

The player is assessed by what they can actually do, not merely what they can
remember in a multiple-choice test.

---

# Multiplayer Mini

VNA eventually supports multiplayer investigation of technical environments.

Possible roles include:

```text
Network engineer
Systems administrator
Storage engineer
Field engineer
Security investigator
Archivist
Incident coordinator
Trainee
```

These are operational responsibilities, not fantasy character classes.

---

# Collaborative Investigation

Different players may investigate different parts of the same problem.

For example:

```text
PLAYER A
Network path

PLAYER B
Server

PLAYER C
Storage

PLAYER D
Application

        |
        v
SHARED EVIDENCE
        |
        v
TEAM DIAGNOSIS
```

The game should reward communication rather than encourage every player to
independently repeat the same work.

---

# Multiplayer Scoring

Scoring should consider useful professional behaviour.

Possible dimensions include:

```text
Diagnostic accuracy
Evidence quality
Safety
Communication
Documentation
Appropriate escalation
Unnecessary intervention avoided
Verification
Teamwork
```

Speed should matter only where the scenario genuinely makes time important.

---

# Real Corporate Worlds

Long term, VNA may represent large real estates.

A corporate environment may contain:

```text
Buildings
Offices
Data centres
Servers
Networks
Storage
Cloud systems
Applications
Users
Services
Vendor equipment
```

Players should not necessarily receive the real identity of everything they
see.

Generalisation provides a safety boundary.

---

# Generalised Corporate Worlds

Real infrastructure might become:

```text
REAL

Server:
customer-db-prod-4

Network:
10.x.x.x

Storage:
/sensitive/customer/path


GENERALISED

Server:
ASSET-104

District:
North Operations

Storage:
Archive Building 17
```

The technical shape of the problem remains.

Unnecessary sensitive information does not.

---

# Real and Synthetic Worlds

VNA should make world origin explicit.

Possible world classes:

```text
TUTORIAL
Handcrafted learning scenario

SYNTHETIC
Generated training scenario

VENDOR
Vendor-supplied simulation

SNAPSHOT
Captured real-world state

LIVE
Authorised current infrastructure
```

The player must always be able to tell which kind of world they are in.

---

# Missions

A mission represents work.

Possible mission classes include:

```text
Investigation
Diagnosis
Repair planning
Network troubleshooting
System troubleshooting
Storage incident
Archaeological investigation
Maintenance
Documentation
Assessment
Team incident
```

A mission should retain its source and evidence.

---

# Gameplay Is Initially Read-Only

VNA gameplay should begin with:

```text
OBSERVE
     |
     v
INVESTIGATE
     |
     v
DIAGNOSE
     |
     v
REPORT
```

not:

```text
GAME
 |
 v
ROOT SHELL
```

The read-only model is not a limitation.

It enables useful investigation while keeping the operational risk manageable.

---

# The Session Report

Every meaningful technical session should be capable of producing a report.

For example:

```text
MISSION REPORT

World:
Snapshot

Source:
SysMedic

Problem:
Application unavailable

Evidence examined:
- physical link state
- local interface state
- addressing
- route state
- connection attempts

Root cause:
Physical connection unavailable

Confidence:
High

Recommended action:
Inspect physical connection

Production changes:
None
```

This is where gameplay becomes useful work.

---

# Real Operations

If VNA later supports real remediation, it must cross a deliberate boundary.

```text
PLAYER
   |
   v
PROPOSED ACTION
   |
   v
VNA POLICY
   |
   v
AUTHORISATION
   |
   v
SPECIALIST TOOL
   |
   v
EXECUTION
   |
   v
VERIFICATION
   |
   v
AUDIT
```

The game does not become the privileged execution layer.

---

# Progression and Authority

Player progression can unlock greater capabilities.

However:

> **Greater capability requires greater accountability.**

Progression may unlock:

```text
Better diagnostic tools
Remote inspection
Historical telemetry
Cross-system correlation
Layer bypass
Teleportation
Advanced Oracles
Team coordination
Authorised operational requests
```

It should not unlock:

```text
Permission to stop explaining yourself.
```

---

# Failure of Convenience

Advanced capabilities should occasionally fail for realistic reasons.

Examples:

```text
No network
No telemetry
No power
Unknown device
Management interface unavailable
Authentication unavailable
Remote access blocked
System physically damaged
```

This deliberately returns experienced players to fundamentals.

The administrator who normally teleports may have to walk.

---

# Maintenance Still Matters

VNA may become spectacular.

It may contain:

```text
Digital twins
Cloud cities
Vendor equipment
AI Oracles
Multiplayer incidents
Teleportation
Procedural worlds
```

It should never imply that mundane work has disappeared.

Administrators still:

- document
- inspect
- label
- check
- clean
- replace
- organise
- verify
- maintain

Some missions should deliberately remain ordinary.

A senior administrator may still need to:

```text
Inspect cable.
Label equipment.
Check airflow.
Clean workspace.
Document rack.
Replace battery.
Verify backup.
```

Technology changes the work.

It does not eliminate maintenance.

---

# The Hoover Principle

VNA inherits one philosophical lesson from BoreDOOM!:

> **Nobody becomes too important for basic maintenance.**

A world-class infrastructure administrator can still be standing in a
beautiful futuristic operations centre when a maintenance objective appears:

```text
FACILITIES NOTICE

Layer 7 office requires cleaning.

Objective:
Hoover.
```

They may possess incident teleportation.

They still need to deal with the carpet.

---

# First Playable VNA Slice

The first playable implementation should remain deliberately small.

Build:

```text
ONE 1960s L7 OFFICE
        |
        v
ONE OSI TOWER
        |
        v
ONE WORKING ELEVATOR
        |
        v
SEVEN INSPECTABLE FLOORS
        |
        v
ONE L1 PHYSICAL EXIT
        |
        v
ONE SECOND SYSTEM
        |
        v
ONE SYNTHETIC FAULT
        |
        v
ONE DIAGNOSIS
        |
        v
ONE REPORT
```

Do not begin with:

- live infrastructure
- FileCity
- SysMedic integration
- Charkive
- vendor worlds
- multiplayer
- procedural corporate environments
- teleportation
- advanced AI

Prove the central experience first.

---

# Prototype Mission 1

## Scenario

The application in Office B cannot communicate.

## Root cause

The physical connection is unplugged.

## Player experience

```text
Receive problem at L7
        |
        v
Inspect symptom
        |
        v
Descend OSI Tower
        |
        v
Observe propagated failures
        |
        v
Reach L1
        |
        v
Travel to physical connection
        |
        v
Identify disconnected cable
        |
        v
Restore symbolically
        |
        v
Watch recovery propagate upwards
        |
        v
Complete report
```

## Learning outcome

> **A failure at Layer 1 can cause symptoms throughout every layer above it.**

---

# Prototype Mission 2

Use the same visible Layer 7 symptom.

This time:

```text
L1 is healthy.
```

The player must continue investigating.

The purpose is to break the dangerous lesson:

```text
Application broken = cable unplugged
```

and replace it with:

```text
Symptoms require investigation.
```

---

# Prototype Mission 3

Introduce a problem at another layer.

Do not tell the player which.

Give enough evidence to establish the fault through investigation.

No new major game mechanic is required.

The purpose is to prove that one OSI Tower can support multiple technically
distinct missions.

---

# Gameplay Prototype Success Criteria

The first prototype succeeds if a player can:

1. Understand that the tower represents a technical stack.
2. Navigate between the layers.
3. Observe how a lower-layer failure affects higher layers.
4. Identify the root-cause layer using evidence.
5. Explain the diagnosis afterwards.
6. Produce a meaningful mission report.

Visual spectacle is secondary.

The conceptual experience is the product.

---

# Public Language Goal

VNA should make technical language useful enough that players adopt it
naturally.

Players should eventually understand statements such as:

```text
"Layer 1 is down."

"Layer 2 looks fine."

"I think this is Layer 3."

"Layer 4 can reach the host but not the service."

"Layer 7 is where we're seeing the symptom."
```

The player learns these terms because they help solve problems.

Not because the game demanded memorisation.

---

# Cultural Goal

The ambition is larger than producing another IT training product.

Terms such as:

```text
Download
Upload
Buffer
Wi-Fi
Cloud
App
Server
```

have escaped specialist computing language and entered ordinary conversation.

VNA should aim to make concepts such as:

```text
Layer 1
Layer 2
Layer 3
Layer 4
Layer 7
```

similarly intuitive.

Someone should be able to say:

> **It sounds like Layer 1.**

and a non-specialist player should understand:

> **Start with the physical connection.**

That familiarity comes from experience.

The player has walked the layers.

They have become stranded in them.

They have repaired them.

They have watched faults propagate through them.

Eventually they have earned the right to teleport through them.

---

# Technology Progression North Star

The player's career begins like this:

```text
1960s office
     |
     v
Problem arrives
     |
     v
Pick up clipboard
     |
     v
Walk to elevator
     |
     v
Inspect layers
     |
     v
Walk to other machine
```

It may eventually become:

```text
VNA COMMAND CENTRE
        |
        v
LIVE FINDING
        |
        v
CORRELATED EVIDENCE
        |
        v
"Layer 3."
        |
        v
TELEPORT
```

The second experience is satisfying because the player lived through the
first.

> **You only earn the shortcut after learning what it hides.**

---

# North Star

VNA succeeds when a player stops thinking of a computer as a mysterious box.

The computer becomes somewhere they have been.

They know there are layers beneath the application.

They know data has to travel.

They know operating systems have machinery underneath their interfaces.

They know logs are evidence.

They know symptoms and causes are different things.

They know AI assistance is useful but fallible.

They know sophisticated tools are abstractions over real mechanisms.

They know when to trust the dashboard.

And they know when to pick up the torch and go downstairs.

A beginner enters a 1960s Layer 7 office with a clipboard.

An expert may eventually command an entire digital estate with live telemetry,
specialist Oracles, vendor equipment, multiplayer teams, FileCity, Charkive,
SysMedic, and instantaneous travel.

But both are doing the same fundamental job:

> **Observe. Understand. Investigate. Explain. Act carefully. Verify.**

And however advanced the technology becomes:

> **Sometimes the cable is just unplugged.**
