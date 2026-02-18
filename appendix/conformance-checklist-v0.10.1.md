=========================================================
CAIA Specification v0.10.1
Conformance Checklist (Normative Extraction)
=========================================================

Purpose:
This document extracts all normative requirements from CAIA v0.10.1.

This checklist introduces no new requirements.
All statements are copied verbatim from the specification.

If a conflict exists, the CAIA specification is authoritative.

---------------------------------------------------------
1. Purpose
---------------------------------------------------------

[ ] Transport neutrality SHALL NOT be used to weaken these minimum capability requirements.

---------------------------------------------------------
2. Terminology
---------------------------------------------------------

2.2 Logical Module

[ ] If represented as a single Logical Module, its channels SHALL share clock coherence.

2.4 Aggregator

[ ] There SHALL be exactly one active Aggregator per Aggregator Domain.

2.6 Host-Visible Audio Device

[ ] When presented, it SHALL be named:
      CAIA Audio Device

[ ] The device SHALL only be presented when one or more physical endpoints are available.

---------------------------------------------------------
3. Core Architectural Invariants
---------------------------------------------------------

3.1 Single Host Interface Invariant

[ ] A CAIA System SHALL present exactly one Host-Visible Audio Device per Aggregator Domain when at least one physical endpoint is available.

[ ] If zero endpoints are available, no Host-Visible Audio Device SHALL be presented.

3.2 Physical Fidelity Invariant

[ ] The Host-Visible Audio Device SHALL faithfully represent currently available physical endpoints.

[ ] The CAIA System SHALL NOT present:
      - Virtual audio channels.
      - Software-only endpoints.
      - Placeholder channels for absent devices.

[ ] Persistent mapping state SHALL NOT create host-visible channels in the absence of physical endpoints.

---------------------------------------------------------
4. Minimum Audio Format Capability (Core)
---------------------------------------------------------

[ ] All CAIA-conformant Coordinators and IO Devices that participate in audio streaming SHALL support:
      - A minimum sample rate capability of 48 kHz.
      - A minimum effective sample resolution of 24 bits.

[ ] CAIA Systems MAY operate at sample rates other than 48 kHz provided such rates are universally supported by all activated endpoints.

---------------------------------------------------------
5. Sample Rate and Format Advertisement
---------------------------------------------------------

[ ] The CAIA System SHALL operate at a single system-wide sample rate and bit depth at any given time.

[ ] The Aggregator SHALL advertise only formats universally achievable by all activated endpoints.

[ ] If host facilities do not permit dynamic restriction of advertised formats, unsupported selections SHALL be rejected with a user-visible error.

---------------------------------------------------------
6. Buffer Size and Latency Model
---------------------------------------------------------

6.1 Buffer Model

[ ] The Host-Visible Audio Device SHALL operate with a single system-wide buffer size selected by the host.

[ ] Coordinators SHALL declare supported buffer size ranges.

[ ] The Aggregator SHALL enforce only buffer sizes universally supported by all activated endpoints.

[ ] Unsupported buffer requests SHALL be rejected visibly.

6.2 Latency Reporting Integrity

[ ] Coordinators SHALL report latency reflecting:
      - Active buffer latency.
      - Fixed internal processing delays.
      - Transport-induced buffering.

[ ] Latency reporting SHALL be stable and deterministic for a given configuration.

[ ] Where per-endpoint latencies differ, the Aggregator SHALL report a conservative value and SHALL NOT under-report effective latency.

---------------------------------------------------------
7. Clocking and Coherence
---------------------------------------------------------

[ ] Within a single Logical Module realized in hardware, all channels SHALL share a common sample clock and maintain phase coherence.

[ ] Logical Modules realized across multiple physical components MUST ensure clock coherence across their channels.

---------------------------------------------------------
8. Channel Mapping and Ordering
---------------------------------------------------------

8.1 Default Layout

[ ] Upon first discovery of a Logical Module with no prior mapping state, the Aggregator SHALL assign its channels as a contiguous block appended after existing channels.

[ ] Ordering within a Logical Module SHALL be stable and deterministic.

8.2 Persistent Mapping

[ ] User-defined channel mappings SHALL persist per mapping context.

[ ] If prior mapping exists and the previously occupied channel range is fully available, the Aggregator SHALL restore it.

[ ] If any previously occupied channels have been explicitly reassigned, the module SHALL be treated as newly discovered and appended.

[ ] Persistent state SHALL NOT create host-visible channels without corresponding physical endpoints.

---------------------------------------------------------
9. Activation Authority
---------------------------------------------------------

[ ] Each Transport Profile SHALL define an Activation Authority responsible for determining which Coordinator activates a given IO Device.

[ ] Activation SHALL be exclusive within an Aggregator Domain.

[ ] Within an Aggregator Domain, activation authority SHALL be mediated by the Aggregator or by a Profile-defined mechanism operating under the Aggregator’s control.

[ ] Physical attachment MAY constitute Activation Authority in wired profiles.

[ ] Host-resident transport profiles SHALL define a deterministic Activation Authority mechanism.

---------------------------------------------------------
10. Hot-Plug Behavior
---------------------------------------------------------

[ ] The CAIA System SHALL reflect physical device availability at all times.

[ ] Physical removal events MAY interrupt streaming without confirmation and SHALL result in visible host-level interruption.

[ ] User-initiated disruptive updates (software, firmware, configuration) SHALL require explicit initiation and SHALL NOT occur silently during active streaming.

---------------------------------------------------------
11. Scalability and Limits
---------------------------------------------------------

[ ] Aggregator implementations SHALL make implementation-defined composition limits accessible to their intended audience.

[ ] If composition exceeds documented limits, the Aggregator SHALL fail visibly and identify the exceeded constraint.

---------------------------------------------------------
12. Aggregator Conformance
---------------------------------------------------------

[ ] There SHALL be exactly one active Aggregator per Aggregator Domain.

[ ] Any implementation claiming CAIA Aggregator conformance SHALL:
      - Implement the Coordinator ↔ Aggregator interface contract fully.
      - Preserve all Core invariants defined in this specification.
      - Present the Host-Visible Audio Device as "CAIA Audio Device".
      - Support all Core Transport Profiles explicitly designated in this specification version.

[ ] Core Transport Profiles are version-bound. Addition of new Transport Profiles in future specification versions SHALL NOT retroactively invalidate conformance to earlier versions.

[ ] A CAIA-conformant Aggregator SHALL be a drop-in replacement for any other CAIA-conformant Aggregator within the same Aggregator Domain for the same specification version.

[ ] Any CAIA-conformant Aggregator SHALL interoperate with any CAIA-conformant Coordinator without modification, provided both claim conformance to the same specification version and supported Transport Profile set.

---------------------------------------------------------
13. Conformance and Claims
---------------------------------------------------------

[ ] Devices and Coordinators SHALL meet all Core requirements to claim CAIA conformance.

[ ] An implementation SHALL NOT claim CAIA conformance over a specific transport unless it conforms to a CAIA Transport Profile governing that transport.

[ ] Implementations introducing transport-specific behavior not defined in an approved CAIA Transport Profile SHALL NOT represent such behavior as CAIA-conformant transport functionality.

[ ] Vendors MAY advertise additional capabilities (e.g., 96 kHz support in homogeneous deployments) but SHALL NOT imply that such capabilities are guaranteed by CAIA conformance.

[ ] Transport-level CAIA claims SHALL only be made for Transport Profiles defined or ratified under the CAIA governance process.

---------------------------------------------------------
14. Transport Profiles
---------------------------------------------------------

[ ] Profiles SHALL:
      - Satisfy all Core requirements.
      - Define Activation Authority.
      - Define identity and enumeration semantics.
      - Define transport-specific constraints necessary for heterogeneous interoperability.

[ ] Profiles MAY impose stricter requirements but SHALL NOT weaken Core requirements.

[ ] Profiles SHALL be intentionally minimal and limited to vendor-neutral requirements necessary to preserve Core invariants and enable heterogeneous interoperability over the specified transport.

[ ] Profiles SHALL NOT introduce functionality unrelated to cross-vendor interoperability.

[ ] Profiles SHALL NOT require steward-operated runtime infrastructure.
