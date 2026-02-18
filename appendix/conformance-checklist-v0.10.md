=========================================================
CAIA Specification v0.10
Conformance Checklist (Normative Extraction)
=========================================================

Purpose:
This document extracts all normative requirements from CAIA v0.10.

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

[ ] If zero endpoints are available,
