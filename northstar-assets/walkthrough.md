# Walkthrough: one note, three calls

This walkthrough takes one synthetic clinical note, `notes/tremblay-2026-03-04.txt` (M. Tremblay, 25 minute diabetes visit, 2026-03-04), through three PhenoML services:

1. **Construe** proposes billing codes from a custom code system, with a citation back to the note for each code.
2. **Lang2FHIR** turns the same note into FHIR R4 resources.
3. **FHIR2OMOP** maps those resources to OMOP CDM rows.

Use it to check your own setup, or as a starting point for your own pipeline. Every call needs a bearer token from [POST /v2/auth/token](https://console.pheno.ml/docs/api-reference/auth/v2/auth/token). Client credentials come from the [PhenoML console](https://console.pheno.ml).

## Step 0: upload the code system

Construe has no built-in Canadian billing codes, so this walkthrough uses WHIP, a fictional plan modelled on the structure of OHIP billing (see [codesystems/README.md](codesystems/README.md)). Custom code systems need a paid plan.

```
POST /construe/upload
{
  "name": "WHIP",
  "version": "2026-11",
  "format": "csv",
  "file": "<base64 of codesystems/whip-codes.csv>",
  "code_col": "code",
  "desc_col": "description",
  "defn_col": "definition"
}
```

The upload returns 202 and builds embeddings in the background. Poll `GET /construe/codes/systems/WHIP?version=2026-11` until `status` is `ready`. If the system already exists, you get a 409; add `"replace": true` to overwrite it.

## Step 1: Construe, billing codes with citations

```
POST /construe/extract
{
  "text": "<full text of notes/tremblay-2026-03-04.txt>",
  "system": { "name": "WHIP", "version": "2026-11" },
  "config": {
    "chunking_method": "sentences",
    "include_citations": true,
    "include_rationale": true
  }
}
```

Citations only come back when `chunking_method` is `sentences` (the default) or `clinical_ner_extract`. With `none`, `paragraphs`, `topics` or `soap_note`, you get codes without citations.

Expected codes:

| Code | Description | Supporting text in the note |
|---|---|---|
| WF-210 | Diabetes management visit | "Time spent: 25 minutes, most of it on diabetes care and counselling", glucose log review, metformin increase |
| WD-101 | Diabetes mellitus | "Type 2 diabetes, poorly controlled, A1c 8.9%" |
| WD-201 | Hypertension | "Hypertension, above target today" |
| WD-105 | Disorders of lipid metabolism | "Dyslipidemia, LDL at target on statin" |

Each code has `valid`, `reason` and `citations` (the cited text plus `begin_offset` and `end_offset` into the note). The response has no numeric confidence score. Construe may also suggest WF-120 (intermediate assessment). A reviewer would drop it, because the visit is billed as WF-210 alone.

## Step 2: Lang2FHIR, the note as FHIR R4

```
POST /lang2fhir/create/multi
{
  "text": "<full text of notes/tremblay-2026-03-04.txt>",
  "version": "R4"
}
```

The response is a transaction Bundle. Expect Condition (type 2 diabetes, hypertension, dyslipidemia), MedicationRequest (metformin 1000 mg BID, ramipril, atorvastatin), Observation (A1c 8.9%, blood pressure, weight) and Encounter. Each entry in `resources[]` has an `originalText` field that quotes the part of the note it came from.

## Step 3: FHIR2OMOP, the Bundle as OMOP rows

```
POST /fhir2omop/create
{
  "fhir_resources": <Bundle from step 2>
}
```

FHIR2OMOP needs a top-level Patient in the Bundle. If the Bundle from step 2 has none, add `api-samples/patient.json` as an entry first. Expect rows in `condition_occurrence`, `drug_exposure`, `measurement` and `visit_occurrence`. The response also has `mappings[]`, which traces each row back to its FHIR resource, and `rows_for_review[]`, which lists anything that did not map.

## Going further

- Run steps 1 and 2 on the other notes in `notes/`, or send the PDFs in `documents/` to `POST /lang2fhir/document/multi`. The `.txt` next to each PDF is the source text, so you can check the output against it.
- Upload `whip-fee-codes.csv` and `whip-diagnostic-codes.csv` as two separate systems if your pipeline handles fee and diagnostic codes in separate steps.
- API reference for each call: [Construe](https://console.pheno.ml/docs/api-reference/construe), [Lang2FHIR](https://console.pheno.ml/docs/api-reference/lang2fhir), [FHIR2OMOP](https://console.pheno.ml/docs/api-reference/fhir2omop).
