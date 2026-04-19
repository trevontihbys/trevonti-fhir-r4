# Trevonti HBYS — FHIR R4 Implementation

<div align="center">

![FHIR R4](https://img.shields.io/badge/FHIR-R4%20(4.0.1)-orange?style=for-the-badge)
![NestJS](https://img.shields.io/badge/NestJS-10-E0234E?style=for-the-badge&logo=nestjs)
![TypeScript](https://img.shields.io/badge/TypeScript-5-3178C6?style=for-the-badge&logo=typescript)
![SMART on FHIR](https://img.shields.io/badge/SMART%20on%20FHIR-Ready-green?style=for-the-badge)

**HL7 FHIR R4 compliant server for Trevonti HBYS**
*Enabling e-Nabız integration and Turkish national health data exchange*

</div>

---

## Overview

This repository contains the FHIR R4 server implementation for Trevonti HBYS.
It exposes patient clinical data via standard HL7 FHIR R4 REST endpoints,
enabling integration with Turkey's e-Nabız patient health platform and
international health data exchange.

## Supported Resources

| Resource | Read | Create | Search | History |
|----------|------|--------|--------|---------|
| Patient | ✅ | ✅ | ✅ | ✅ |
| Encounter | ✅ | ✅ | ✅ | - |
| Observation (Vitals + Lab) | ✅ | ✅ | ✅ | - |
| MedicationRequest | ✅ | ✅ | ✅ | - |
| Condition (ICD-10) | ✅ | ✅ | ✅ | - |
| DiagnosticReport | ✅ | ✅ | ✅ | - |
| ServiceRequest | ✅ | ✅ | ✅ | - |
| Bundle (transaction/batch) | ✅ | ✅ | - | - |

## LOINC Mappings — Vital Signs

| Vital Sign | LOINC Code | Unit |
|-----------|-----------|------|
| Heart Rate | 8867-4 | /min |
| Systolic BP | 8480-6 | mm[Hg] |
| Diastolic BP | 8462-4 | mm[Hg] |
| Temperature | 8310-5 | Cel |
| Respiratory Rate | 9279-1 | /min |
| SpO₂ | 2708-6 | % |
| Body Weight | 29463-7 | kg |
| Pain Score (NRS) | 72514-3 | {score} |
| GCS | 35088-4 | {score} |

## Quick Start

```bash
# Server
GET https://fhir.trevonti.com/fhir/r4/metadata
# → CapabilityStatement

# Search patient
GET https://fhir.trevonti.com/fhir/r4/Patient?identifier=HKN-2025-0001234

# Get observations
GET https://fhir.trevonti.com/fhir/r4/Observation?patient={id}&category=vital-signs
```

## Security

- **SMART on FHIR** — Keycloak OIDC authentication
- **Patient scopes** — `patient/*.read`, `patient/*.write`
- **TC Kimlik No** — Stored as HMAC-SHA256 hash only, never exposed via FHIR

## e-Nabız Integration

| e-Nabız Request | FHIR Endpoint |
|----------------|--------------|
| Patient history | `GET /Patient?identifier={tc_hash}` |
| Visit history | `GET /Encounter?patient={id}` |
| Lab results | `GET /Observation?category=laboratory` |
| Medications | `GET /MedicationRequest?patient={id}` |
| Diagnoses | `GET /Condition?patient={id}` |

## Structure

```
src/
├── fhir.types.ts          # FHIR R4 TypeScript type definitions
├── fhir.service.ts        # REST CRUD + Search + Bundle processor
├── fhir.controller.ts     # HTTP endpoints
└── converters/
    ├── patient.converter.ts
    ├── encounter.converter.ts
    ├── observation.converter.ts
    ├── medication-request.converter.ts
    └── condition.converter.ts
```

---

Part of [Trevonti HBYS](https://github.com/trevonti-hbys/trevonti-hbys) — Turkey's next-generation Hospital Information System.
