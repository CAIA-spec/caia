# CAIA Specification v0.10.1
Composable Audio Interface Architecture (CAIA)

Status: Clarification Release
This version aligns Aggregator conformance, Transport Profile governance,
and transport-level claim boundaries with the CAIA Stewardship Charter.
No architectural model changes are introduced relative to v0.10.

---

## 1. Purpose

The Composable Audio Interface Architecture (CAIA) defines a modular
system for composing physical audio input and output devices into a
single host-visible audio interface.

CAIA is designed to:

- Preserve physical fidelity.
- Provide deterministic composition behavior.
- Match contemporary professional audio interface workflows.
- Avoid digital signal processing (DSP) creep, mixer behavior, or
  workstation-level functionality.

CAIA Core is transport-agnostic with respect to electrical signaling,
bus topology, and enumeration mechanics. However, CAIA Core establishes
minimum modern audio interface capability requirements to preserve
industry-typical performance expectations across composed systems.

Transport neutrality SHALL NOT be used to weaken these minimum
capability requirements.

CAIA defines composition and activation semantics. It does not define
signal processing, mixing, digital audio workstation (DAW) behavior,
or audio production workflows.

---

## 2. Terminology

### 2.1 IO Device

A hardware device that provides one or more physical audio input or
output endpoints.

An IO Device does not independently constitute a CAIA System.
It participates in a CAIA System through a Coordinator.

### 2.2 Logical Module

A declared grouping of physical audio endpoints presented as a coherent
unit by a Coordinator.

A Logical Module may be realized within a single physical device or
across multiple physical components. If represented as a single Logical
Module, its channels SHALL share clock coherence.

### 2.3 Coordinator

An implementation that:

- Discovers and characterizes IO Devices.
- Composes IO Devices into Logical Modules.
- Exposes those Logical Modules to an Aggregator via the
  Coordinator ↔ Aggregator interface.

A Coordinator may be:

- Host-resident (software running on the host operating system), or
- External (hardware managing attached IO Devices).

### 2.4 Aggregator

The implementation that composes one or more Coordinators into a single
Host-Visible Audio Device.

There SHALL be exactly one active Aggregator per Aggregator Domain.

### 2.5 Aggregator Domain

The set of Coordinators and IO Devices composed by a single Aggregator.

### 2.6 Host-Visible Audio Device

The audio interface presented to the host operating system and
applications.

When presented, it SHALL be named:

    CAIA Audio Device

The device SHALL only be presented when one or more physical endpoints
are available.

---

## 3. Core Architectural Invariants

### 3.1 Single Host Interface Invariant

A CAIA System SHALL present exactly one Host-Visible Audio Device per
Aggregator Domain when at least one physical endpoint is available.

If zero endpoints are available, no Host-Visible Audio Device SHALL be
presented.

### 3.2 Physical Fidelity Invariant

The Host-Visible Audio Device SHALL faithfully represent currently
available physical endpoints.

The CAIA System SHALL NOT present:

- Virtual audio channels.
- Software-only endpoints.
- Placeholder channels for absent devices.

Persistent mapping state SHALL NOT create host-visible channels in the
absence of physical endpoints.

### 3.3 No Mixer / No DSP Invariant

CAIA Core does not define:

- Mixing
- Real-time sample rate conversion
- Drift correction
- Per-channel delay compensation
- Signal processing beyond endpoint composition

---

## 4. Minimum Audio Format Capability (Core)

All CAIA-conformant Coordinators and IO Devices that participate in
audio streaming SHALL support:

- A minimum sample rate capability of 48 kHz.
- A minimum effective sample resolution of 24 bits.

Operation below 24-bit effective resolution is not permitted under
CAIA conformance.

CAIA Systems MAY operate at sample rates other than 48 kHz provided
such rates are universally supported by all activated endpoints.

In heterogeneous systems, 48 kHz / 24-bit is the only universally
guaranteed format.

---

## 5. Sample Rate and Format Advertisement

The CAIA System SHALL operate at a single system-wide sample rate and
bit depth at any given time.

The Aggregator SHALL advertise only formats universally achievable by
all activated endpoints.

If host facilities do not permit dynamic restriction of advertised
formats, unsupported selections SHALL be rejected with a user-visible
error.

---

## 6. Buffer Size and Latency Model

### 6.1 Buffer Model

The Host-Visible Audio Device SHALL operate with a single system-wide
buffer size selected by the host.

Coordinators SHALL declare supported buffer size ranges.

The Aggregator SHALL enforce only buffer sizes universally supported by
all activated endpoints.

Unsupported buffer requests SHALL be rejected visibly.

### 6.2 Latency Reporting Integrity

Coordinators SHALL report latency reflecting:

- Active buffer latency.
- Fixed internal processing delays.
- Transport-induced buffering.

Latency reporting SHALL be stable and deterministic for a given
configuration.

Where per-endpoint latencies differ, the Aggregator SHALL report a
conservative value and SHALL NOT under-report effective latency.

CAIA Core does not mandate numeric latency tolerance or measurement
procedure.

---

## 7. Clocking and Coherence

Within a single Logical Module realized in hardware, all channels SHALL
share a common sample clock and maintain phase coherence.

Logical Modules realized across multiple physical components MUST ensure
clock coherence across their channels.

CAIA Core does not require clock coherence between independently
realized IO Devices.

CAIA does not require drift correction between independent devices.

Long-term clock drift between independent hardware devices is outside
Core scope.

---

## 8. Channel Mapping and Ordering

### 8.1 Default Layout

Upon first discovery of a Logical Module with no prior mapping state,
the Aggregator SHALL assign its channels as a contiguous block appended
after existing channels.

Ordering within a Logical Module SHALL be stable and deterministic.

### 8.2 Persistent Mapping

User-defined channel mappings SHALL persist per mapping context.

Rediscovery behavior:

- If prior mapping exists and the previously occupied channel range is
  fully available, the Aggregator SHALL restore it.
- If any previously occupied channels have been explicitly reassigned,
  the module SHALL be treated as newly discovered and appended.

Persistent state SHALL NOT create host-visible channels without
corresponding physical endpoints.

---

## 9. Activation Authority

Each Transport Profile SHALL define an Activation Authority responsible
for determining which Coordinator activates a given IO Device.

Activation SHALL be exclusive within an Aggregator Domain.

Within an Aggregator Domain, activation authority SHALL be mediated by
the Aggregator or by a Profile-defined mechanism operating under the
Aggregator’s control.

Physical attachment MAY constitute Activation Authority in wired
profiles.

Host-resident transport profiles SHALL define a deterministic
Activation Authority mechanism.

---

## 10. Hot-Plug Behavior

The CAIA System SHALL reflect physical device availability at all times.

Physical removal events MAY interrupt streaming without confirmation and
SHALL result in visible host-level interruption.

User-initiated disruptive updates (software, firmware, configuration)
SHALL require explicit initiation and SHALL NOT occur silently during
active streaming.

CAIA does not require seamless continuity across removal or reinsertion
events.

---

## 11. Scalability and Limits

CAIA defines no arbitrary maximum channel count.

Aggregator implementations SHALL make implementation-defined composition
limits accessible to their intended audience.

If composition exceeds documented limits, the Aggregator SHALL fail
visibly and identify the exceeded constraint.

---

## 12. Aggregator Conformance

There SHALL be exactly one active Aggregator per Aggregator Domain.

Any implementation claiming CAIA Aggregator conformance SHALL:

- Implement the Coordinator ↔ Aggregator interface contract fully.
- Preserve all Core invariants defined in this specification.
- Present the Host-Visible Audio Device as "CAIA Audio Device".
- Support all Core Transport Profiles explicitly designated in this
  specification version.

Core Transport Profiles are version-bound. Addition of new Transport
Profiles in future specification versions SHALL NOT retroactively
invalidate conformance to earlier versions.

A CAIA-conformant Aggregator SHALL be a drop-in replacement for any
other CAIA-conformant Aggregator within the same Aggregator Domain
for the same specification version.

Any CAIA-conformant Aggregator SHALL interoperate with any
CAIA-conformant Coordinator without modification, provided both claim
conformance to the same specification version and supported Transport
Profile set.

Conformance is defined by adherence to this specification and the
Transport Profiles the implementation claims to support, not by
implementation identity.

---

## 13. Conformance and Claims

Devices and Coordinators SHALL meet all Core requirements to claim
CAIA conformance.

Aggregator conformance does not imply transport conformance.

An implementation SHALL NOT claim CAIA conformance over a specific
transport unless it conforms to a CAIA Transport Profile governing
that transport.

Implementations introducing transport-specific behavior not defined
in an approved CAIA Transport Profile SHALL NOT represent such
behavior as CAIA-conformant transport functionality.

Vendors MAY advertise additional capabilities (e.g., 96 kHz support in
homogeneous deployments) but SHALL NOT imply that such capabilities are
guaranteed by CAIA conformance.

Transport-level CAIA claims SHALL only be made for Transport Profiles
defined or ratified under the CAIA governance process.

---

## 14. Transport Profiles

Transport Profiles define transport-specific behavior (e.g., USB).

Profiles SHALL:

- Satisfy all Core requirements.
- Define Activation Authority.
- Define identity and enumeration semantics.
- Define transport-specific constraints necessary for
  heterogeneous interoperability.

Profiles MAY impose stricter requirements but SHALL NOT weaken Core
requirements.

Profiles SHALL be intentionally minimal and limited to vendor-neutral
requirements necessary to preserve Core invariants and enable
heterogeneous interoperability over the specified transport.

Profiles SHALL NOT introduce functionality unrelated to cross-vendor
interoperability.

Profiles SHALL NOT require steward-operated runtime infrastructure.

---

## 15. Non-Goals

CAIA does not define:

- Digital mixing
- Routing engines
- DSP effects
- Automatic delay compensation
- Real-time resampling
- Seamless device hot-swap continuity
- Arbitrary transport interoperability without an approved
  CAIA Transport Profile

---

End of CAIA Specification v0.10.1industry-typical performance expectations across composed systems.

Transport neutrality SHALL NOT be used to weaken these minimum
capability requirements.

CAIA defines composition and activation semantics. It does not define
signal processing, mixing, digital audio workstation (DAW) behavior,
or audio production workflows.

---

## 2. Terminology

### 2.1 IO Device
A hardware device that provides one or more physical audio input or
output endpoints.

An IO Device does not independently constitute a CAIA System.
It participates in a CAIA System through a Coordinator.

### 2.2 Logical Module
A declared grouping of physical audio endpoints presented as a coherent
unit by a Coordinator.

A Logical Module may be realized within a single physical device or
across multiple physical components. If represented as a single Logical
Module, its channels SHALL share clock coherence.

### 2.3 Coordinator
An implementation that:

- Discovers and characterizes IO Devices.
- Composes IO Devices into Logical Modules.
- Exposes those Logical Modules to an Aggregator via the
  Coordinator ↔ Aggregator interface.

A Coordinator may be:

- Host-resident (software running on the host operating system), or
- External (hardware managing attached IO Devices).

### 2.4 Aggregator
The implementation that composes one or more Coordinators into a single
Host-Visible Audio Device.

There SHALL be exactly one active Aggregator per Aggregator Domain.

### 2.5 Aggregator Domain
The set of Coordinators and IO Devices composed by a single Aggregator.

### 2.6 Host-Visible Audio Device
The audio interface presented to the host operating system and
applications.

When presented, it SHALL be named:

    CAIA Audio Device

The device SHALL only be presented when one or more physical endpoints
are available.

---

## 3. Core Architectural Invariants

### 3.1 Single Host Interface Invariant

A CAIA System SHALL present exactly one Host-Visible Audio Device per
Aggregator Domain when at least one physical endpoint is available.

If zero endpoints are available, no Host-Visible Audio Device SHALL be
presented.

### 3.2 Physical Fidelity Invariant

The Host-Visible Audio Device SHALL faithfully represent currently
available physical endpoints.

The CAIA System SHALL NOT present:

- Virtual audio channels.
- Software-only endpoints.
- Placeholder channels for absent devices.

Persistent mapping state SHALL NOT create host-visible channels in the
absence of physical endpoints.

### 3.3 No Mixer / No DSP Invariant

CAIA Core does not define:

- Mixing
- Real-time sample rate conversion
- Drift correction
- Per-channel delay compensation
- Signal processing beyond endpoint composition

---

## 4. Minimum Audio Format Capability (Core)

All CAIA-conformant Coordinators and IO Devices that participate in
audio streaming SHALL support:

- A minimum sample rate capability of 48 kHz.
- A minimum effective sample resolution of 24 bits.

Operation below 24-bit effective resolution is not permitted under
CAIA conformance.

CAIA Systems MAY operate at sample rates other than 48 kHz provided
such rates are universally supported by all activated endpoints.

In heterogeneous systems, 48 kHz / 24-bit is the only universally
guaranteed format.

---

## 5. Sample Rate and Format Advertisement

The CAIA System SHALL operate at a single system-wide sample rate and
bit depth at any given time.

The Aggregator SHALL advertise only formats universally achievable by
all activated endpoints.

If host facilities do not permit dynamic restriction of advertised
formats, unsupported selections SHALL be rejected with a user-visible
error.

---

## 6. Buffer Size and Latency Model

### 6.1 Buffer Model

The Host-Visible Audio Device SHALL operate with a single system-wide
buffer size selected by the host.

Coordinators SHALL declare supported buffer size ranges.

The Aggregator SHALL enforce only buffer sizes universally supported by
all activated endpoints.

Unsupported buffer requests SHALL be rejected visibly.

### 6.2 Latency Reporting Integrity

Coordinators SHALL report latency reflecting:

- Active buffer latency.
- Fixed internal processing delays.
- Transport-induced buffering.

Latency reporting SHALL be stable and deterministic for a given
configuration.

Where per-endpoint latencies differ, the Aggregator SHALL report a
conservative value and SHALL NOT under-report effective latency.

CAIA Core does not mandate numeric latency tolerance or measurement
procedure.

---

## 7. Clocking and Coherence

Within a single Logical Module realized in hardware, all channels SHALL
share a common sample clock and maintain phase coherence.

Logical Modules realized across multiple physical components MUST ensure
clock coherence across their channels.

CAIA Core does not require clock coherence between independently
realized IO Devices.

CAIA does not require drift correction between independent devices.

Long-term clock drift between independent hardware devices is outside
Core scope.

---

## 8. Channel Mapping and Ordering

### 8.1 Default Layout

Upon first discovery of a Logical Module with no prior mapping state,
the Aggregator SHALL assign its channels as a contiguous block appended
after existing channels.

Ordering within a Logical Module SHALL be stable and deterministic.

### 8.2 Persistent Mapping

User-defined channel mappings SHALL persist per mapping context.

Rediscovery behavior:

- If prior mapping exists and the previously occupied channel range is
  fully available, the Aggregator SHALL restore it.
- If any previously occupied channels have been explicitly reassigned,
  the module SHALL be treated as newly discovered and appended.

Persistent state SHALL NOT create host-visible channels without
corresponding physical endpoints.

---

## 9. Activation Authority

Each Transport Profile SHALL define an Activation Authority responsible
for determining which Coordinator activates a given IO Device.

Activation SHALL be exclusive within an Aggregator Domain.

Within an Aggregator Domain, activation authority SHALL be mediated by
the Aggregator or by a Profile-defined mechanism operating under the
Aggregator’s control.

Physical attachment MAY constitute Activation Authority in wired
profiles.

Host-resident transport profiles SHALL define a deterministic
Activation Authority mechanism.

---

## 10. Hot-Plug Behavior

The CAIA System SHALL reflect physical device availability at all times.

Physical removal events MAY interrupt streaming without confirmation and
SHALL result in visible host-level interruption.

User-initiated disruptive updates (software, firmware, configuration)
SHALL require explicit initiation and SHALL NOT occur silently during
active streaming.

CAIA does not require seamless continuity across removal or reinsertion
events.

---

## 11. Scalability and Limits

CAIA defines no arbitrary maximum channel count.

Aggregator implementations SHALL make implementation-defined composition
limits accessible to their intended audience.

If composition exceeds documented limits, the Aggregator SHALL fail
visibly and identify the exceeded constraint.

---

## 12. Aggregator Conformance

There SHALL be exactly one active Aggregator per Aggregator Domain.

Any implementation claiming CAIA Aggregator conformance SHALL:

- Implement the Coordinator ↔ Aggregator interface contract fully.
- Support all steward-approved CAIA Transport Profiles.
- Preserve all Core invariants.
- Present the Host-Visible Audio Device as "CAIA Audio Device".

A CAIA-conformant Aggregator SHALL be a drop-in replacement for any
other CAIA-conformant Aggregator within the same Aggregator Domain.

Conformance is defined by adherence to this specification and all
steward-approved Transport Profiles, not by implementation identity.

Any CAIA-conformant Aggregator SHALL interoperate with any
CAIA-conformant Coordinator without modification.

---

## 13. Conformance and Claims

Devices and Coordinators SHALL meet all Core requirements to claim
CAIA conformance.

Vendors MAY advertise additional capabilities (e.g., 96 kHz support in
homogeneous deployments) but SHALL NOT imply that such capabilities are
guaranteed by CAIA conformance.

Transport-level CAIA claims SHALL only be made for steward-approved
Transport Profiles.

---

## 14. Transport Profiles

Transport Profiles define transport-specific behavior (e.g., USB).

Profiles SHALL:

- Satisfy all Core requirements.
- Define Activation Authority.
- Define identity and enumeration semantics.
- Define transport-specific constraints.

Profiles MAY impose stricter requirements but SHALL NOT weaken Core
requirements.

---

## 15. Non-Goals

CAIA does not define:

- Digital mixing
- Routing engines
- DSP effects
- Automatic delay compensation
- Real-time resampling
- Seamless device hot-swap continuity
- Arbitrary transport interoperability without approved profile

---

End of CAIA Specification v0.10
CAIA is written for the benefit of end users (musicians and sound engineers) while
remaining implementable by system designers, hardware engineers, firmware engineers,
software engineers, and test engineers.

---

## 2. Scope and Non-Goals

### 2.1 In Scope

CAIA specifies:
- Logical modules and their identities
- Composition into a single logical interface
- Capability discovery and declaration
- Control surfaces relevant to signal integrity
- Timing and state visibility requirements
- Fault containment and isolation principles
- Conformance and compliance criteria

### 2.2 Out of Scope

CAIA does not specify:
- Transports or physical interconnects
- Operating systems or drivers
- Digital signal processing algorithms
- Digital Audio Workstation (DAW) integration
- Plugin, extension, or virtual instrument formats
- Business models or pricing
- Certification, governance, or enforcement bodies

---

## 3. Terminology

**Module**  
A logical unit that provides one or more audio-related capabilities.

**Logical Module**  
An abstract, declared module visible to the host system.

**Physical Module**  
The concrete hardware realization of one or more logical modules.

**System**  
A composed collection of modules presented as a single interface.

**Host**  
The computer or device consuming the composed interface.

**Implementer**  
Any party designing, building, testing, or integrating a CAIA system.

**End User**  
Musicians and sound engineers using the system for audio work.

---

## 4. Core Architectural Principles

### 4.1 Composability

A CAIA system is composed of independent logical modules that may be added, removed,
or replaced without redefining the entire system.

### 4.2 Granularity

Logical module boundaries define interface-level behavior, not manufacturing units.
A single physical device may expose multiple logical modules.

### 4.3 Truthful Declaration

All declared capabilities shall reflect real, usable resources.
Over-declaration is prohibited.

### 4.4 Optional Interoperability

Interoperability is encouraged but not mandatory. Systems may optimize for internal
hardware efficiency without guaranteeing cross-vendor composition.

---

## 5. Logical Modules

### 5.1 Logical Module Identity

Each logical module shall declare:
- A stable identifier
- Module type (input, output, monitoring, control)
- Channel count
- Control capabilities
- Resource dependencies

A single logical module may be realized across multiple physical components, provided
all declarations remain accurate and coherent.

### 5.2 One-to-Many and Many-to-One Realizations

- One physical device may expose many logical modules
- One logical module may be backed by multiple physical components
- Identity is defined by behavior and declaration, not packaging

---

## 6. Audio Inputs and Outputs

### 6.1 Inputs

Input modules may represent:
- Microphone inputs
- Instrument inputs
- Line-level inputs

Each input channel shall declare:
- Nominal signal level
- Supported gain or trim range
- Clipping behavior

Gain and trim controls belong at the module level to ensure adequate signal depth
without clipping and cannot be deferred to higher system layers.

### 6.2 Outputs

Output modules may represent:
- Line outputs
- Headphone outputs
- Monitoring paths

Each output channel shall declare:
- Nominal output level
- Impedance characteristics where relevant
- Independent or shared control behavior

---

## 7. Controls

Controls exposed by modules shall be limited to those necessary for:
- Signal integrity
- Safe operation
- Predictable monitoring

Controls shall not imply or embed signal processing behavior beyond gain, trim, and
routing required to expose declared capabilities.

---

## 8. Timing and State

### 8.1 Deterministic Behavior

Modules shall operate such that their declared behavior is deterministic with respect
to audio signal flow.

### 8.2 State Querying

Querying module state shall not introduce observable timing changes under normal
operation. Implementations may satisfy this requirement through buffering, caching,
or relaxed real-time guarantees, provided audio behavior is unaffected.

---

## 9. Fault Containment

Faults shall be isolated to the smallest practical scope.

A fault in one logical module shall not:
- Corrupt unrelated audio streams
- Cause misrepresentation of other modules
- Require system-wide reinitialization where avoidable

---

## 10. System Builder Obligations

For the purposes of this section, a “system builder” refers to any implementer
responsible for composing or declaring a CAIA system.

System builders shall:
- Accurately declare all logical modules
- Avoid hidden coupling that violates declared independence
- Ensure shared resources are truthfully represented
- Avoid designs that require unused capacity to enable declared functionality

System builders may:
- Optimize internally for cost or efficiency
- Combine multiple logical modules into a single product
- Provide vendor-specific enhancements outside the CAIA declaration surface

---

## 11. Resource Accounting

Logical module declarations shall align with available system resources.

Excessive or misleading declarations that exceed reasonable host capabilities are
non-conformant.

No absolute channel limits are imposed by CAIA; practical limits are left to market
forces and host capabilities.

---

## 12. Security, Isolation, and Safety

Modules shall not:
- Expose unintended control over unrelated modules
- Allow malformed declarations to destabilize the system

Isolation is architectural, not cryptographic; CAIA does not mandate security
mechanisms beyond containment and correctness.

---

## 13. Conformance and Compliance

### 13.1 Conformance

A system conforms to CAIA if it satisfies all mandatory (“shall”) requirements of
this specification.

### 13.2 Scope of Conformance

Conformance applies to the complete system as presented to the host, including
hardware, firmware, and software components that participate in module declaration,
composition, and behavior.

### 13.3 Conformance Levels

Partial conformance is permitted. Systems may expose only a subset of CAIA features
provided all exposed features conform fully.

### 13.4 Demonstration of Compliance

Compliance may be demonstrated through:
- Self-asserted declarations
- Documentation
- Test artifacts

No certification authority is defined by this specification.

### 13.5 Prohibited Claims

Non-conformant systems shall not claim CAIA compliance.
Marketing language shall not imply interoperability, certification, or endorsement
beyond what is truthfully implemented.

---

## 14. Versioning and Evolution

CAIA versions are monotonic.
Backward compatibility is encouraged but not mandated.
Future revisions shall not retroactively invalidate conformant systems.

---

## 15. Non-Goals and Explicit Exclusions

CAIA intentionally avoids:
- Defining “best” audio quality
- Mandating workflows
- Standardizing musical practice
- Enforcing ecosystem participation

---

## 16. Summary

CAIA defines a minimal, truthful, and modular architecture for audio interface
composition. It empowers end users to assemble systems that match their needs while
preserving implementation freedom and market-driven evolution.
