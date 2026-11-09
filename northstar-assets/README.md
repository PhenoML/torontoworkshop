# Northstar EMR workshop assets

Synthetic materials for the PhenoML workshop "Hidden in Plain Text" (Toronto, 2026-11-12). Everything here is fictional: no real patients, clinicians, clinics or health card numbers. Health card numbers are written as `[HCN]`, phone numbers use the 416-555-01xx range, and Northstar EMR does not exist.

These files live in the workshop repository at https://github.com/PhenoML/torontoworkshop under `northstar-assets/`.

The demo runs one clinical note (Tremblay) through Construe for OHIP billing codes, then through Lang2FHIR and FHIR2OMOP for real-world evidence. The build block asks each table to design a billing or RWE product for Northstar EMR, a fictional Ontario EMR vendor.

## Files and where they are used

| File | Used in | What it is |
|---|---|---|
| `notes/tremblay-2026-03-04.txt` | Billing demo (slides 7 to 9), RWE demo (slides 13 to 15), Build Round 3 | The live demo note. 25 minute diabetes visit, glucose log review, metformin increase, A1c 8.9%, BP 152/94, diet counselling. Dr. A. Rahman. |
| `notes/okafor-2026-03-09.txt` | Billing demo second note (slide 7, amlodipine and smoking cessation rows), Build Round 2 | New hypertension plus smoking cessation counselling. Started amlodipine and varenicline. Dr. A. Rahman. |
| `notes/kowalski-2026-03-11.txt` | RWE discussion (slide 14, MedicationRequest and referral rows), Build Round 2 | New atrial fibrillation with in-clinic ECG. Started apixaban and bisoprolol, cardiology eReferral. Dr. L. Bianchi, same clinic. |
| `notes/samuels-2026-03-16.txt` | RWE demo (slide 12, hospital documents arriving by fax), Build Round 2 | Post-discharge heart failure visit with medication reconciliation against a faxed discharge summary. Dr. A. Rahman. |
| `documents/samuels-discharge-summary-fax-2026-03-10.pdf` | RWE demo (slide 12, faxes and scanned PDFs), Build Rounds 2 and 3 | Two-page hospital discharge summary as it arrived by fax: image only, no text layer, so it needs OCR. Input for `POST /lang2fhir/document/multi`. The matching `.txt` is the source text for checking output against ground truth. |
| `documents/kowalski-cardiology-consult-2026-04-02.pdf` | RWE demo (slide 12, consult letters), Build Rounds 2 and 3 | Two-page cardiology consult letter answering the 2026-03-11 eReferral, text-based PDF. Also input for `POST /lang2fhir/document/multi`. Source text alongside. |
| `fact-sheet.md` | Slide 19 (printed handout), Build Rounds 1 to 4 | One page on Northstar EMR: what it is, what it holds, what it exposes, and the three questions its privacy officer asks every vendor. |
| `api-samples/patient.json` | RWE demo (slide 13, stage 1 provenance), Build Round 3 | FHIR R4 Patient for Tremblay as Northstar's read-only API returns it. |
| `api-samples/encounter.json` | Slide 14 (Encounter row), Build Round 3 | FHIR R4 Encounter for the 2026-03-04 visit, 25 minutes, ambulatory. |
| `api-samples/documentreference.json` | Slide 13 (document id as provenance), Build Round 3 | FHIR R4 DocumentReference pointing at the Tremblay note as a Binary, with size and SHA-1 hash of the note file. |
| `api-samples/observation-a1c.json` | Slide 14 (Observation row, LOINC 4548-4) | FHIR R4 laboratory Observation, A1c 8.9%, arrived through the OLIS HL7 v2 feed. |
| `api-samples/observation-blood-pressure.json` | Slide 14, Build Round 2 | FHIR R4 vital signs Observation, 152/94 mmHg, conforms to the R4 blood pressure vital signs profile. |
| `api-samples/webhook-note-signed.json` | Build Round 3 (the trigger most teams build on), Round 4 | The `note.signed` payload Northstar sends when a physician signs a note. Identifiers and links only, no note text. |
| `patient-panel.csv` | RWE demo (slide 11, cohort question), Build Round 2 | 20 synthetic patients on Dr. A. Rahman's roster, including the four above. Six have a diabetes diagnosis of some kind, which makes a useful cohort definition exercise. |

## The four patients, for consistency

| Patient | DOB | Sex | Physician | Active problems | Medications | Note date |
|---|---|---|---|---|---|---|
| M. Tremblay (NS-0048213) | 1968-07-19 | M | Dr. A. Rahman | Type 2 diabetes, hypertension, dyslipidemia | Metformin 500 mg BID, increased to 1000 mg BID; ramipril 10 mg daily; atorvastatin 20 mg daily | 2026-03-04 |
| J. Okafor (NS-0051877) | 1979-02-02 | F | Dr. A. Rahman | New hypertension, tobacco use (ready to quit) | Amlodipine 5 mg daily and varenicline, both started | 2026-03-09 |
| A. Kowalski (NS-0032605) | 1951-10-30 | F | Dr. L. Bianchi (same-day visit; rostered to Dr. A. Rahman) | New atrial fibrillation, hypertension, diet-controlled type 2 diabetes, hypothyroidism | Apixaban 5 mg BID and bisoprolol 2.5 mg daily, both started; candesartan 16 mg daily; levothyroxine 88 mcg daily | 2026-03-11 |
| B. Samuels (NS-0019442) | 1948-05-14 | M | Dr. A. Rahman | HFrEF (LVEF 30%), CKD stage 3a, type 2 diabetes, permanent atrial fibrillation; discharged 2026-03-09 | Sacubitril/valsartan, carvedilol, spironolactone, empagliflozin, furosemide, apixaban, metformin | 2026-03-16 |

All four are patients of the fictional Northstar Family Health Team in Mississauga (clinic phone 416-555-0100, fax 416-555-0101).

Two external documents extend the story and match the notes: Samuels' discharge summary from the fictional Cedarbank General Hospital (admitted 2026-03-03, discharged 2026-03-09, LVEF 30%, faxed 2026-03-10) and Kowalski's cardiology consult from the fictional Harbourline Cardiology Associates (seen 2026-04-01, echo LVEF 60%, rate control continued, letter dated 2026-04-02).

## PhenoML documentation

The deck prints `developer.pheno.ml/docs`. That host now redirects to `console.pheno.ml/docs`, and deep links on the old host land on the docs home rather than the matching page, so these links use the current host.

### Start here

- [Docs home](https://console.pheno.ml/docs): the "Start here" path is authentication, then a first Construe extract call.
- [What PhenoML is](https://console.pheno.ml/docs/guides/overview) and [Architecture](https://console.pheno.ml/docs/guides/architecture): how the primitives (Construe, Lang2FHIR), tools and orchestration (agents, workflows) fit together.
- [Authentication guide](https://console.pheno.ml/docs/guides/authentication) and [POST /v2/auth/token](https://console.pheno.ml/docs/api-reference/auth/v2/auth/token): OAuth 2.0 client credentials, bearer token valid 48 hours. [Common headers](https://console.pheno.ml/docs/api-reference/common-headers).
- [API reference index](https://console.pheno.ml/docs/api-reference): every service in one place.
- [llms.txt](https://console.pheno.ml/llms.txt) and [llms-full.txt](https://console.pheno.ml/llms-full.txt): machine-readable docs index and full export. Point your coding agent at these during the build block.
- [PhenoML console](https://console.pheno.ml): sign in to get client credentials.

### Billing demo: Construe with a custom code system (slides 7 to 10)

- [Construe service overview](https://console.pheno.ml/docs/api-reference/construe): all endpoints, built-in terminologies.
- [Bring your own terminology](https://console.pheno.ml/docs/guides/custom-codes): the guide for the custom OHIP code system. Upload is asynchronous, poll until `ready`, custom systems use semantic search.
- [POST /construe/upload](https://console.pheno.ml/docs/api-reference/construe/upload): CSV with `code_col` and `desc_col`, file content base64, returns 202. Requires a paid plan.
- [GET /construe/codes/systems/{codesystem}](https://console.pheno.ml/docs/api-reference/construe/codes/systems/codesystem/get): the polling endpoint. `status` moves from `processing` to `ready` or `failed`.
- [GET /construe/codes/systems](https://console.pheno.ml/docs/api-reference/construe/codes/systems): lists built-in and custom systems. Built-in names are spelled `SNOMED_CT_US`, `RXNORM`, `LOINC`, `ICD-10-CM`, `CPT`, `HCPCS`, `HPO`, `ICD-10`, `ICD-10-PCS`.
- [POST /construe/extract](https://console.pheno.ml/docs/api-reference/construe/extract): the extraction call. `config.include_citations` returns the text span for each code; `config.include_rationale` returns the reason. The response carries `valid` and `reason` per code rather than a numeric confidence score; the confidence numbers on slide 9 are mock values.
- [Extract codes you can trust](https://console.pheno.ml/docs/guides/trustworthy-codes): chunking methods, validation, citations, rationale, consistency effort.
- [Semantic search on a code system](https://console.pheno.ml/docs/api-reference/construe/codes/codesystem/search/semantic): works for built-in and custom systems; useful for "look up a fee code" during the build.

### RWE demo: Lang2FHIR, then FHIR2OMOP (slides 13 to 16)

- [Lang2FHIR service overview](https://console.pheno.ml/docs/api-reference/lang2fhir): create, create/multi, document, document/multi, search, batch, cohort.
- [From clinical text to a populated EHR](https://console.pheno.ml/docs/guides/text-to-ehr): the guide for the pipeline.
- [POST /lang2fhir/create](https://console.pheno.ml/docs/api-reference/lang2fhir/create): one resource from text. Profile ids include `condition-encounter-diagnosis`, `condition-problems-health-concerns`, `medicationrequest`, `observation-lab`, `vital-signs`, `encounter`, `patient`.
- [POST /lang2fhir/create/multi](https://console.pheno.ml/docs/api-reference/lang2fhir/create/multi): a whole note to a FHIR transaction Bundle, with `originalText` per resource as the citation. This is the call for slide 14.
- [POST /lang2fhir/document/multi](https://console.pheno.ml/docs/api-reference/lang2fhir/document/multi): PDF or image in, Bundle out, with `sourcePages`. Use this for Northstar's faxes and scanned consult letters. Try it with the two PDFs in `documents/`: the Samuels fax has no text layer, the Kowalski letter does.
- [POST /tools/lang2fhir-and-create](https://console.pheno.ml/docs/api-reference/tools/lang2fhir-and-create): generate and write to a connected FHIR provider in one call.
- [FHIR2OMOP service](https://console.pheno.ml/docs/api-reference/fhir2omop) and [POST /fhir2omop/create](https://console.pheno.ml/docs/api-reference/fhir2omop/create): a FHIR R4 resource or Bundle (with a Patient) in, OMOP CDM v5.4 tables out, plus `mappings[]` for row-level lineage and `rows_for_review[]` for anything that did not map. Condition goes to `condition_occurrence`, MedicationRequest to `drug_exposure`, numeric Observation to `measurement`, Encounter to `visit_occurrence`.
- [Changelog](https://console.pheno.ml/docs/changelog): the most recent entry covers domain-based table routing in FHIR2OMOP.

### Code and SDKs (slide 15 repo links, build block)

- [Workshop repository](https://github.com/PhenoML/torontoworkshop): these assets, under `northstar-assets/`. Use this link wherever the deck says `github.com/PhenoML` (slides 15 and 23) so participants can download the notes and PDFs.
- [pheno-rwe](https://github.com/PhenoML/pheno-rwe): local-first, deterministic engine for traceable RWE studies. Pulls FHIR, maps through the FHIR2OMOP API into DuckDB OMOP tables, then resolves cohorts, de-identifies, validates and analyses. Starts from FHIR, so chain Lang2FHIR in front of it for the notes-in version shown on slide 13.
- [SDKs](https://console.pheno.ml/docs/sdks): TypeScript (`npm install phenoml`), Python (`pip install phenoml`), Java. Source: [phenoml-python-sdk](https://github.com/PhenoML/phenoml-python-sdk), [phenoml-ts-sdk](https://github.com/PhenoML/phenoml-ts-sdk), [phenoml-java-sdk](https://github.com/PhenoML/phenoml-java-sdk).
- [phenoml-skills](https://github.com/PhenoML/phenoml-skills): Claude Code plugin with `phenoml-workflow` and `rwe-analyze` skills, for tables using a coding agent.
- [phenoml-demos](https://github.com/PhenoML/phenoml-demos): smaller examples, including a Construe typeahead and a trial protocol coder.
- [phenoml_workshop](https://github.com/PhenoML/phenoml_workshop): an earlier workshop repo showing Lang2FHIR writing into Medplum, Canvas and Cerner.

### Agents, tools and workflows (slide 17, "use agents and search")

- [Agent guide](https://console.pheno.ml/docs/guides/agents) and [Agent API](https://console.pheno.ml/docs/api-reference/agent): prompts, tools, provider binding, streaming.
- [Extend agents with MCP](https://console.pheno.ml/docs/guides/mcp): PhenoML agents can call external MCP servers registered through `/tools/mcp-server/create`.
- [Tools](https://console.pheno.ml/docs/api-reference/tools): cohort, lang2fhir-and-create, lang2fhir-and-search.

### Plans and compliance (Round 4, trust)

- [Pricing](https://phenoml.com/pricing): which plans include custom code systems and a BAA.
- [Compliance](https://console.pheno.ml/docs/guides/compliance): HIPAA, SOC 2 Type II, PIPEDA, dedicated instances. Note that PHIPA is provincial; the privacy officer's questions at the end of the fact sheet ask where inference runs.

## Facilitator notes

- Before the session, point the GitHub references on slides 15 and 23 at the workshop repository, https://github.com/PhenoML/torontoworkshop. The repository is private at the time of writing; make it public or share an export before the room needs it. For the FHIR2OMOP placeholder on slide 15, link the API reference above; there is no separate FHIR2OMOP repository, it is a hosted endpoint.
- The Samuels fax PDF is image only on purpose, to match the roughly 40 percent of Northstar's inbound documents that arrive as scans. The `.txt` next to each PDF is the exact source text, so teams can score what Lang2FHIR extracted against it.
- Slide 8 pseudocode uses `construe.upload_code_system` and `construe.get_code_system`. The real paths are `POST /construe/upload` and `GET /construe/codes/systems/{codesystem}`; the Python SDK namespaces are `construe.code_systems.upload` and `construe.codes.extract`.
- Custom code systems need a paid plan. Have the OHIP CSV uploaded and `ready` before the room arrives; the embedding build is asynchronous.
- A. Kowalski appears on Dr. A. Rahman's panel because she is rostered to him; her 2026-03-11 note was written by Dr. L. Bianchi during a same-day visit. This is how a Family Health Team works in practice and gives teams a provider-versus-custodian question for Round 4.
- `api-samples/documentreference.json` carries the byte size and SHA-1 of `notes/tremblay-2026-03-04.txt`. If you edit the note, recompute both (`wc -c` and `openssl dgst -sha1 -binary | base64`) and the `sha1` and `word_count` fields in the webhook payload.
- The five FHIR resources were run through the HL7 FHIR validator (`validator_cli.jar -version 4.0.1`) with zero errors and zero warnings, apart from one expected warning that the Binary URL in the DocumentReference cannot be fetched because the Northstar host is fictional. The blood pressure Observation was checked against the R4 blood pressure vital signs profile, which the validator applies automatically for LOINC 85354-9. References to Practitioner, Organization and Location resources are display-only pointers; Northstar's API does not expose those resource types.
