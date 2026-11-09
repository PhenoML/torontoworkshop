# Northstar EMR: fact sheet

Northstar EMR is a fictional company created for the "Hidden in Plain Text" workshop (Toronto, 2026-11-12). It is your table's first customer. Invent anything else you need about Northstar and state your assumptions in your pitch.

## What it is

- Cloud EMR for family practice and community specialists, founded in Toronto in 2016.
- 88 clinics across Ontario, about 510 physicians and nurse practitioners, roughly 700,000 active charts. Alberta launch planned for 2027.
- Each physician is the health information custodian under PHIPA. Northstar acts as the custodian's agent and electronic service provider under a written agreement. Northstar does not own the data and cannot use it for its own purposes without the custodian's direction.
- 41 staff. Product team of six: two product managers and four engineers. No machine learning expertise in house. Infrastructure runs in a Canadian public cloud region.
- Revenue is a per-physician monthly subscription plus a small per-claim fee from the billing module.
- Leadership: a CEO measured on revenue and physician churn, who asks every vendor who pays and what happens when the model is wrong; a privacy officer who signs off on every integration before it ships.

## What it holds

- SOAP notes and a cumulative patient profile (CPP), typed or dictated, stored as free text.
- Consult letters and inbound faxes stored as PDF. About 40 percent of these are scanned images with no text layer. Two samples are in `documents/`: a scanned discharge summary and a typed consult letter.
- Lab results as HL7 v2 ORU messages from the provincial feed (OLIS), mapped to LOINC when the sending lab supplies a LOINC code.
- Medications: free text in charts created before 2021, coded (DIN plus drug name, dose and sig) in newer charts.
- A billing module that builds OHIP claims from fee and diagnostic codes the physician picks at sign-off, then submits them through MC EDT and reconciles the remittance advice.
- No research data warehouse, no de-identification tooling, no OMOP instance, no Condition or MedicationRequest resources. Problems and medications live in the note text and the CPP.

## What it exposes

- Read-only FHIR R4 API: Patient, Encounter, DocumentReference, Observation. OAuth 2.0 client credentials, one client per clinic, scoped to that clinic's custodians. Base URL `https://api.northstar-emr.example/fhir/r4` (fictional). Note text is fetched as a Binary via the DocumentReference.
- A `note.signed` webhook fired when a physician signs a note. The payload carries identifiers and links, not the note text; the subscriber fetches the DocumentReference and Binary with its own credentials. Deliveries are signed with HMAC-SHA256 and retried for 24 hours.
- A claims export: each clinic's submitted claims in the OHIP MC EDT submission format, nightly, plus the matching remittance advice file.
- No write API. Nothing a partner produces reaches the chart unless a user enters it. There is also no audit log API; access logs exist internally only.

Sample payloads for the FHIR API and the webhook are in `api-samples/`.

## Three questions the privacy officer always asks vendors

1. Where does processing run, and does any personal health information leave Canada at any point, including model inference, logging, backups and support access?
2. Under whose authority do you handle the data? Are you our agent under PHIPA, what exactly are you permitted to do with it, and is any use beyond the custodian's purpose, including training or product improvement, a secondary use that needs its own consent or authority?
3. For any output you produce, can you show the exact source text, document and date it came from, and can you delete everything you hold about one patient when a custodian asks?
