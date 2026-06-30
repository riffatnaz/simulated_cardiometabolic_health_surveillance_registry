# Simulated Cardiometabolic Health Surveillance Registry (12-Month Cohort)

A longitudinal simulated REDCap database tracking a patient cohort undergoing a lifestyle and genetic risk management intervention, with automated quarterly-ish follow-up over 12 months.

**Why this exists:** REDCap is institutional research software — most projects live inside a university or hospital's private instance and are never made public. This project was built end-to-end on REDCap's public demo server specifically to demonstrate database design, longitudinal data modeling, and automation skills in a format a recruiter or hiring manager can actually open and review, without needing REDCap access themselves. Every file here describes *schema and structure* (variable names, validation rules, branching logic, calculated fields) — there is no real patient data anywhere in this repository; all records are synthetic.

## Project Overview

- **Type:** Longitudinal, event-based data collection
- **Events:** Baseline (Day 0), Month 4 (Day 120), Month 8 (Day 240), Month 12 (Day 365)
- **Instruments:**
  - *Demographics & Clinical Intake* — Baseline only (includes `participant_email`, the designated communications field, and `consent_signature`, a file-upload field for the signed consent document)
  - *Biometrics & Lab Values* — Baseline & Month 12 (enabled as a participant-facing survey for Month 12)
  - *Symptom & Compliance Check* — Month 4 & Month 8 (enabled as a survey for both; the same instrument is reused across two timepoints rather than duplicated)
- **Automation:** Three Automated Survey Invitations (ASIs), each triggered off Baseline completion and scheduled to fire a fixed number of days later:
  - Symptom & Compliance Check → Month 4, sent 120 days after Baseline
  - Symptom & Compliance Check → Month 8, sent 240 days after Baseline
  - Biometrics & Lab Values → Month 12, sent 365 days after Baseline

## Key Features Demonstrated

| Feature | Where it's used |
|---|---|
| Longitudinal event structure | Baseline → Month 4 → Month 8 → Month 12 (4 evenly-paced check-ins) |
| Calculated field | `bmi` = `[weight_kg] / ([height_m]*[height_m])`, verified live across multiple records and events |
| Data validation | `age` (18–100), `weight_kg` (30–250), `height_m` (1.0–2.5), `fbg_mgdl` (50–500), `med_compliance_pct` (0–100) |
| Branching logic | `diabetic_status` only displays when `[fbg_mgdl] >= 126` — verified both hidden and visible across two test records |
| Informed consent capture | `consent_signature` (File Upload field) on the Demographics form |
| Reused mid-study instrument | `symptom_checklist` and `med_compliance_pct` collected identically at both Month 4 and Month 8, instead of building duplicate forms |
| Automated Survey Invitations | 3 alerts, each correctly scoped to its own event via `[survey-link:instrument:event_name]` tokens |
| Data Dictionary import/export | `cardio-registry-datadictionary.csv` — round-tripped through REDCap's own upload/validation/commit flow |
| Custom Report | "High-Risk Diabetic Patients" — filters all 16 record-events down to the 2 patients flagged diabetic |

## Contents

- `cardio-registry-datadictionary.csv` — the authoritative Data Dictionary, downloaded directly from the live REDCap project (not hand-written) — defines every variable, validation rule, calculation, and branching condition.
- `mock_test_data.csv` — synthetic longitudinal dataset (4 participants × 4 events = 16 record-events) used to test the import pipeline and validate the calculated/branching fields.
- `screenshots/` — visual evidence of the build (see list below).

### Screenshots included

1. Longitudinal events table (Baseline/Month 4/Month 8/Month 12, day offsets)
2. Instrument-to-event mapping grid
3. Data Dictionary upload validation (zero errors)
4. BMI calculated field, editor view
5. Branching logic editor for `diabetic_status`
6. Branching logic in practice — field hidden (FBG below threshold) vs. visible (FBG above threshold), two records side by side
7. Record Status Dashboard — full longitudinal grid, all records complete
8. Automated Survey Invitation configuration (all 3 alerts)
9. "High-Risk Diabetic Patients" report output

## Build Process Summary

1. Created the project from scratch with longitudinal data collection enabled.
2. Defined 4 events at roughly 4-month intervals: Baseline, Month 4, Month 8, Month 12.
3. Designed a 14-field Data Dictionary covering 3 instruments, including a calculated BMI field, a branching-logic-gated diabetes flag, range validation on all numeric fields, and a file-upload consent field.
4. Uploaded and committed the Data Dictionary directly into the project (rather than building fields one-by-one in the GUI), then mapped each instrument to its correct event(s).
5. Enabled two instruments as participant-facing surveys and designated an email field for communications.
6. Built three Automated Survey Invitations, each correctly scoped to its specific instrument/event pair using REDCap's `survey-link` piping syntax.
7. Imported a 16-row synthetic longitudinal dataset and verified that the calculated and branching-logic fields behaved correctly on live data.
8. Generated the project Codebook
