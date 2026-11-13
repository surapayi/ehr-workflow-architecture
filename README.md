# EHR End-to-End Patient Journey Architecture: Outpatient Orthopedic Consult

## Executive Summary

This repository documents the end-to-end clinical and system architecture for a standard outpatient encounter. As healthcare shifts heavily toward interoperability and automated APIs, understanding the precise data handoffs between core Electronic Health Record (EHR) modules and ancillary systems is critical.

This project maps the human workflows and technical data exchanges—specifically utilizing **HL7 v2, FHIR, and NCPDP standards**—required to move a patient from intake and clinical charting through radiology ordering, e-prescribing, and final revenue cycle charge capture.

## The Clinical Scenario

**Patient Presentation:** A new patient presents to an outpatient orthopedic clinic with chronic right knee pain.
**Encounter Scope:**

1. Front desk registration and real-time eligibility verification.

2. Clinical triage and provider examination.

3. In-house computerized provider order entry (CPOE) for a Right Knee X-ray (2 views).

4. Electronic prescription generation for an NSAID (Meloxicam).

5. Chart closure and automated charge dropping for the medical biller.

## Technical Architecture & Interoperability Standards

This workflow demonstrates how siloed healthcare systems communicate seamlessly during a live patient encounter. The architecture maps the following data standards:

* **Registration & Intake:** `HL7 ADT-A04` (Register Patient) broadcasts patient demographics to ancillary systems. `ANSI 270/271` handles payer eligibility.

* **Radiology Orders (RIS):** `HL7 ORM-O01` (Order Message) transmits the X-ray request from the core EHR to the Radiology Information System. `HL7 ORU-R01` (Observation Result) routes the finalized imaging link back to the provider's chart.

* **E-Prescribing:** `NCPDP SCRIPT NewRx` securely transmits the medication order to the external pharmacy network, bypassing HL7 for pharmacy-specific protocols.

* **Revenue Cycle / Billing:** `HL7 DFT-P03` (Detailed Financial Transaction) packages the captured CPT codes (e.g., *73221 Knee X-ray*) and ICD-10 diagnosis codes (e.g., *M25.561 Pain in right knee*) for the billing engine.

## Repository Structure

Navigate through the documentation using the links below:

* [`/docs/01_Business_Requirements.md`](./docs/01_Business_Requirements.md) - Agile user stories and acceptance criteria for the Registrar, Attending Provider, and Medical Biller personas.

* [`/docs/02_Data_Mapping_Dictionary.md`](./docs/02_Data_Mapping_Dictionary.md) - Technical data dictionary mapping specific HL7/FHIR segments (e.g., `PID-3` for MRN, `OBR-4` for Order Codes) to the clinical workflow.

* [`/diagrams/EHR_Sequence_Diagram.png`](./diagrams/EHR_Sequence_Diagram.png) - System sequence diagram illustrating the HL7 message routing.

* [`/diagrams/Clinical_Process_Flow.png`](./diagrams/Clinical_Process_Flow.png) - Swimlane flowchart detailing the operational human touchpoints.

## System Sequence Flow

*(Embed your Mermaid.js or Draw.io sequence diagram here)*

```
sequenceDiagram
    autonumber
    actor Patient
    actor Registrar
    participant EHR as Core EHR System
    participant Payer as Payer Portal
    actor Provider
    participant RIS as Radiology (RIS)
    participant Surescripts as Pharmacy Network
    actor Biller

    Patient->>Registrar: Check-in / Demographics
    Registrar->>EHR: Update Intake Info
    EHR->>Payer: ANSI 270 (Eligibility Request)
    Payer-->>EHR: ANSI 271 (Eligibility Response)
    EHR->>RIS: HL7 ADT-A04 (Patient Register)

    Provider->>EHR: Clinical Assessment & CPOE (X-ray)
    EHR->>RIS: HL7 ORM-O01 (Radiology Order)
    RIS-->>EHR: HL7 ORU-R01 (Radiology Result)

    Provider->>EHR: Prescription Order (Meloxicam)
    EHR->>Surescripts: NCPDP SCRIPT NewRx
    
    Provider->>EHR: Close & Sign Chart
    EHR->>Biller: HL7 DFT-P03 (Financial Charge Drop)

```

## Business Value

By architecting the patient journey at the data-segment level, this documentation bridges the gap between clinical operations and technical engineering. It ensures that clinical workflows are supported by robust, standard-compliant data architecture, ultimately reducing manual data entry, preventing dropped revenue charges, and accelerating care delivery.

*Created as a demonstration of Business Systems Analysis, clinical workflow mapping, and healthcare interoperability standards.*
