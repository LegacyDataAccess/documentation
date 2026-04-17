### Table of Contents
- [UI Data Mapping Document](#ui-data-mapping-document)
  - [Widget: PatientInfo (Vital Signs)](#widget-patientinfo-vital-signs)
  - [Widget: PatientInfo (Height & Weight)](#widget-patientinfo-height--weight)
  - [Widget: PatientInfo (Comorbidities)](#widget-patientinfo-comorbidities)
  - [Widget: Medications & Allergies (Medications)](#widget-medications--allergies-medications)
  - [Widget: Medications & Allergies (Allergies)](#widget-medications--allergies-allergies)
  - [Widget: Clinical Notes (Progress Notes)](#widget-clinical-notes-progress-notes)
  - [Widget: Clinical Notes (Review of Systems)](#widget-clinical-notes-review-of-systems)
  - [Widget: Clinical Notes (Examination Notes)](#widget-clinical-notes-examination-notes)
  - [Widget: Documents](#widget-documents)
  - [Widget: Symptoms](#widget-symptoms)
  - [Widget: Personal Info](#widget-personal-info)
  - [Widget: Visit History](#widget-visit-history)
  - [Widget: Assessment & Planning (Assessments)](#widget-assessment--planning-assessments)
  - [Widget: Assessment & Planning (Treatment Plan)](#widget-assessment--planning-treatment-plan)
  - [Widget: Assessment & Planning (Problem List)](#widget-assessment--planning-problem-list)
  - [Widget: Trends (Lab Results)](#widget-trends-lab-results)
  - [Table: Vitals Table](#table-vitals-table)
  - [Table: Examination Table](#table-examination-table)
  - [Table: Symptoms Table](#table-symptoms-table)
  - [Table: Review of Systems Table (Systems)](#table-review-of-systems-table-systems)
  - [Table: DICOM Studies Table](#table-dicom-studies-table)
  - [Table: Documents Table](#table-documents-table)
  - [Table: Allergies Table](#table-allergies-table)
  - [Table: Assessments Table](#table-assessments-table)
  - [Table: Comorbidities Table](#table-comorbidities-table)
  - [Table: Height & Weight Table](#table-height--weight-table)
  - [Table: Medications Table](#table-medications-table)
  - [Table: Problem List Table](#table-problem-list-table)
  - [Table: Progress Notes Table](#table-progress-notes-table)
  - [Table: Treatment Plan Table](#table-treatment-plan-table)
  - [Table: Visit History Table (Visit Info)](#table-visit-history-table-visit-info)
  - [Table: Lab Results Table](#table-lab-results-table)
  - [Table: Procedures Table](#table-procedures-table)
  - [Table: Clinical Interactions Table](#table-clinical-interactions-table)
  - [Table: Vital Signs Table (Trends)](#table-vital-signs-table-trends)

# UI Data Mapping Document

## Widget: PatientInfo (Vital Signs)

### Location
Dashboard Widget

### Data Source
Tables: encounters_vitals
Notes: values are selected from `encounters_vitals` by `patientid` and `itemname` using `VitalSignsItemType` constants. For each vital (Temperature, Blood Pressure, Pulse, SpO2), the most recent row by `encounter_date` is chosen. `lastUpdate` is formatted from `encounter_date`.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Temperature | Patient temperature | encounters_vitals | value, uom | Latest row where `itemname = 'Temperature'`; `tempValue = value`, `tempUom = uom`. `lastUpdate` from `encounter_date` formatted `MM/dd/yyyy`. |
| Blood Pressure | Blood pressure | encounters_vitals | value, uom | Latest row where `itemname = 'Blood Pressure'`; `bloodPressureValue = value`, `bloodPressureUom = uom`. |
| Pulse | Pulse rate | encounters_vitals | value, uom | Latest row where `itemname = 'Pulse'`; `pulseValue = value`, `pulseUom = uom`. |
| SpO2 | Oxygen saturation | encounters_vitals | value, uom | Latest row where `itemname = 'SpO2'`; `spO2Value = value`, `spO2Uom = uom`. |

---

## Widget: PatientInfo (Height & Weight)

### Location
Dashboard Widget

### Data Source
Tables: encounters_vitals
Notes: values are selected from `encounters_vitals` by `patientid` and `itemname` using `HWItemNameType` constants. For each of Height, Weight, BMI, the most recent row by `encounter_date` is chosen. `lastUpdate` is taken from BMI’s `encounter_date` if present.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Height | Patient height | encounters_vitals | value, uom | Latest row where `itemname = 'Height'`; `heightValue = value`, `heightUom = uom`. |
| Weight | Patient weight | encounters_vitals | value, uom | Latest row where `itemname = 'Weight'`; `weightValue = value`, `weightUom = uom`. |
| BMI | Body Mass Index | encounters_vitals | value, uom | Latest row where `itemname = 'BMI'`; `bmiValue = value`, `bmiUom = uom`. `lastUpdate` from this row’s `encounter_date` formatted `MM/dd/yyyy`. |

---

## Widget: PatientInfo (Comorbidities)

### Location
Dashboard Widget

### Data Source
Tables: encounters_problems, patients
Notes: Top 5 problems from `encounters_problems` by `encounter_date` DESC for the patient. Age at diagnosis is computed using patient’s `date_of_birth` from `patients` and problem’s `encounter_date`.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Comorbidity Name | Name of the condition | encounters_problems | icd_description | Direct mapping. |
| Details | Code and age | encounters_problems, patients | icd_code; date_of_birth (patients); encounter_date (problems) | `codeAndAge = "{icd_code}, diagnosed at age {ageAtEncounter} ({yearsSinceEncounter} years ago)"`; where `ageAtEncounter = currentAge - yearsSinceEncounter`, `currentAge` computed from `patients.date_of_birth`, `yearsSinceEncounter` from `now - problems.encounter_date`. If `encounter_date` null, returns just `{icd_code}`. |
| Date | Recorded date | encounters_problems | encounter_date | Formatted as `MM/dd/yyyy`. |

---

## Widget: Medications & Allergies (Medications)

### Location
Dashboard Widget

### Data Source
Tables: encounters_medications
Notes: Direct read from `encounters_medications` for the patient.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Medicine Name | Name of the medicine | encounters_medications | medicinename | Direct mapping. |
| Date | Encounter date | encounters_medications | encounter_date | Direct mapping. |
| Instructions | Dosage/Instructions | encounters_medications | instructions | Direct mapping. |
| Frequency | Frequency of use | encounters_medications | frequency | Direct mapping. |

---

## Widget: Medications & Allergies (Allergies)

### Location
Dashboard Widget

### Data Source
Tables: encounters_allergies
Notes: Direct read from `encounters_allergies` for the patient.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Agent Substance | Allergic substance | encounters_allergies | agent_substance | Direct mapping. |
| Date | Encounter date | encounters_allergies | encounter_date | Direct mapping. |
| Reaction | Type of reaction | encounters_allergies | reaction | Direct mapping. |

---

## Widget: Clinical Notes (Progress Notes)

### Location
Dashboard Widget

### Data Source
Tables: encounters_progress_notes
Notes: Direct read from `encounters_progress_notes` for the patient.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Reason | Reason for appointment | encounters_progress_notes | reason_for_appt | Direct mapping. |
| Past History | Past medical history | encounters_progress_notes | past_history | Direct mapping. |
| Assessment Notes | Assessment notes | encounters_progress_notes | asmt_notes | Direct mapping. |
| Date | Encounter date | encounters_progress_notes | encounter_date | Direct mapping. |

---

## Widget: Clinical Notes (Review of Systems)

### Location
Dashboard Widget

### Data Source
Tables: encounters_systems_overview
Notes: Direct read from `encounters_systems_overview` for the patient.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Value | ROS finding | encounters_systems_overview | value | Direct mapping. |
| Category | System category | encounters_systems_overview | category | Direct mapping. |
| Date | Encounter date | encounters_systems_overview | encounter_date | Direct mapping. |

---

## Widget: Clinical Notes (Examination Notes)

### Location
Dashboard Widget

### Data Source
Tables: encounters_examination
Notes: Direct read from `encounters_examination` for the patient.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Area | Examination area | encounters_examination | area | Direct mapping. |
| Note | Examination note | encounters_examination | note | Direct mapping. |
| Date | Encounter date | encounters_examination | encounter_date | Direct mapping. |

---

## Widget: Documents

### Location
Dashboard Widget

### Data Source
Tables: documents
Notes: Direct read from `documents`. UI may prefer `customname` over `name` when present.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Name | Document name | documents | customname, name | Prefer `customname` if not null/empty; otherwise fall back to `name`. |
| Date | Scan date | documents | scandate | Direct mapping. |

---

## Widget: Symptoms

### Location
Dashboard Widget

### Data Source
Tables: symptoms
Notes: Direct read from `symptoms` for the patient.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Symptom | Symptom name | symptoms | symptom | Direct mapping. |
| Date | Encounter date | symptoms | encounter_date_ | Direct mapping. |
| Encounter ID | ID of the encounter | symptoms | encounter_id | Direct mapping. |

---

## Widget: Personal Info

### Location
Dashboard Widget

### Data Source
Tables: patients, encounters_vitals
Notes: Base demographics from `patients`. `admissionWeight` is taken from the most recent `encounters_vitals` row where `itemname = 'Weight'` and uses `display_value`. `age` is computed from `patients.date_of_birth`.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Name | Patient full name | patients | name_first, name_last | Concatenation in UI: `firstName + ' ' + lastName` from `patients.name_first` and `patients.name_last`. |
| Age | Patient age | patients | date_of_birth | Calculated as full years between `now` and `date_of_birth`. |
| Gender | Patient gender | patients | gender | Direct mapping. |
| Admission Weight | Admission weight | encounters_vitals | display_value | Latest row where `itemname = 'Weight'`; uses `display_value`. |
| EMR Number | EMR identifier | patients | mrn | Direct mapping. |

---

## Widget: Visit History

### Location
Dashboard Widget

### Data Source
Tables: encounters
Notes: Direct read from `encounters` for the patient.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Reason | Visit reason | encounters | reason | Direct mapping. |
| Date | Encounter date | encounters | encounter_date | Direct mapping. |
| Action Taken | Action taken during visit | encounters | action_taken | Direct mapping. |

---

## Widget: Assessment & Planning (Assessments)

### Location
Dashboard Widget

### Data Source
Tables: encounters_assessments
Notes: Direct read from `encounters_assessments` for the patient.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Description | ICD Description | encounters_assessments | icd_description | Direct mapping. |
| Code | ICD Code | encounters_assessments | icd_code | Direct mapping. |
| Date | Encounter date | encounters_assessments | encounter_date | Direct mapping. |

---

## Widget: Assessment & Planning (Treatment Plan)

### Location
Dashboard Widget

### Data Source
Tables: encounters_treatment_notes
Notes: Direct read from `encounters_treatment_notes` for the patient.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Title | Treatment item name | encounters_treatment_notes | item_name | Direct mapping. |
| Details | Treatment notes | encounters_treatment_notes | notes | Direct mapping. |
| Date | Encounter date | encounters_treatment_notes | encounter_date | Direct mapping. |

---

## Widget: Assessment & Planning (Problem List)

### Location
Dashboard Widget

### Data Source
Tables: encounters_problems
Notes: Direct read from `encounters_problems` for the patient.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Description | ICD Description | encounters_problems | icd_description | Direct mapping. |
| Code | ICD Code | encounters_problems | icd_code | Direct mapping. |
| Date | Encounter date | encounters_problems | encounter_date | Direct mapping. |

---

## Widget: Trends (Lab Results)

### Location
Dashboard Widget

### Data Source
Tables: lab_results
Notes: Direct read from `lab_results` for the patient.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Test Name | Name of lab test | lab_results | order_item_name | Direct mapping. |
| Result Value | Value of the test | lab_results | value | Direct mapping. |
| Range | Reference range | lab_results | range | Direct mapping. |
| Date | Result date | lab_results | result_date | Direct mapping. |

---

## Table: Vitals Table

### Location
Page: /vitals

### Data Source
Tables: encounters_vitals
Notes: Direct read from `encounters_vitals` filtered by `patientid` and `isActive=true` at API level (filtering not in entity). Sorting/paging controlled by API.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Name | Vital sign name | encounters_vitals | itemname | Direct mapping. |
| Value | Display value | encounters_vitals | display_value | Direct mapping (pre-formatted). |
| Provider | Healthcare provider | encounters_vitals | provider | Direct mapping. |
| Facility | Healthcare facility | encounters_vitals | facility | Direct mapping. |
| Date | Encounter date | encounters_vitals | encounter_date | Direct mapping. |

---

## Table: Examination Table

### Location
Page: /examination

### Data Source
Tables: encounters_examination
Notes: Direct read from `encounters_examination` filtered by `patientid`.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Area | Examination area | encounters_examination | area | Direct mapping. |
| Notes | Examination findings | encounters_examination | note | Direct mapping. |
| Provider | Healthcare provider | encounters_examination | provider | Direct mapping. |
| Facility | Healthcare facility | encounters_examination | facility | Direct mapping. |
| Date | Encounter date | encounters_examination | encounter_date | Direct mapping. |

---

## Table: Symptoms Table

### Location
Page: /symptoms

### Data Source
Tables: symptoms
Notes: Direct read from `symptoms` filtered by `patientid`.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Category | Symptom category | symptoms | category | Direct mapping. |
| Symptom | Symptom name | symptoms | symptom | Direct mapping. |
| Encounter ID | Encounter identifier | symptoms | encounter_id | Direct mapping. |
| Provider | Healthcare provider | symptoms | provider | Direct mapping. |
| Facility | Healthcare facility | symptoms | facility | Direct mapping. |
| Date | Encounter date | symptoms | encounter_date_ | Direct mapping. |

---

## Table: Review of Systems Table (Systems)

### Location
Page: /systems

### Data Source
Tables: encounters_systems_overview
Notes: Direct read from `encounters_systems_overview` filtered by `patientid`.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Name | System category name | encounters_systems_overview | category | Direct mapping. |
| Value | Finding value | encounters_systems_overview | value | Direct mapping. |
| Provider | Healthcare provider | encounters_systems_overview | provider | Direct mapping. |
| Facility | Healthcare facility | encounters_systems_overview | facility | Direct mapping. |
| Date | Encounter date | encounters_systems_overview | encounter_date | Direct mapping. |

---

## Table: DICOM Studies Table

### Location
Page: /dicom-studies

### Data Source
Tables: dicom_studies
Notes: Direct read from `dicom_studies`. Additional viewer/server URLs come from controlling DB settings for viewer link generation (not displayed in this table).

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Study Date | Date of study | dicom_studies | study_date | Direct mapping. |
| Modality | Imaging modality | dicom_studies | modality | Direct mapping. |
| Study Description | Description of study | dicom_studies | study_description | Direct mapping. |

---

## Table: Documents Table

### Location
Page: /documents

### Data Source
Tables: documents
Notes: Direct read from `documents` filtered by `patientid`.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Document Name | Name of document | documents | name | Direct mapping. If a dashboard card shows custom name, that uses `customname` with fallback to `name`. |
| Document Type | Type of document | documents | doc_type | Direct mapping. |
| Category | Document category | documents | doc_sub_type | Direct mapping. |
| Scan Date | Date scanned | documents | scandate | Direct mapping. |
| File Type | MIME/File type | documents | content_type | Direct mapping. |

---

## Table: Allergies Table

### Location
Page: /allergies

### Data Source
Tables: encounters_allergies
Notes: Direct read from `encounters_allergies` filtered by `patientid`.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Agent | Allergic agent substance | encounters_allergies | agent_substance | Direct mapping. |
| Reaction | Allergic reaction | encounters_allergies | reaction | Direct mapping. |
| Provider | Healthcare provider | encounters_allergies | provider | Direct mapping. |
| Facility | Healthcare facility | encounters_allergies | facility | Direct mapping. |
| Date | Encounter date | encounters_allergies | encounter_date | Direct mapping. |

---

## Table: Assessments Table

### Location
Page: /assessments

### Data Source
Tables: encounters_assessments
Notes: Direct read from `encounters_assessments` filtered by `patientid`.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Item Number | Display index | encounters_assessments | displayindex | Direct mapping. |
| Description | ICD Description | encounters_assessments | icd_description | Direct mapping. |
| Code | ICD Code | encounters_assessments | icd_code | Direct mapping. |
| Provider | Healthcare provider | encounters_assessments | provider | Direct mapping. |
| Facility | Healthcare facility | encounters_assessments | facility | Direct mapping. |
| Date | Encounter date | encounters_assessments | encounter_date | Direct mapping. |

---

## Table: Comorbidities Table

### Location
Page: /comorbidities

### Data Source
Tables: encounters_problems, patients
Notes: For each row, `name` from `encounters_problems.icd_description`. `codeAndAge` is computed using `encounters_problems.icd_code`, `encounter_date`, and patient `date_of_birth` from `patients` (see logic below). `date` formatted from `encounter_date`.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Description | Condition name | encounters_problems | icd_description | Direct mapping. |
| Сode and age | Code and age info | encounters_problems, patients | icd_code; date_of_birth (patients); encounter_date (problems) | `codeAndAge = "{icd_code}, diagnosed at age {ageAtEncounter} ({yearsSinceEncounter} years ago)"` with same logic as dashboard Comorbidities. If `encounter_date` null, value is `{icd_code}` only. |
| Date | Recorded date | encounters_problems | encounter_date | Formatted `MM/dd/yyyy`. |

---

## Table: Height & Weight Table

### Location
Page: /height-weight

### Data Source
Tables: encounters_vitals
Notes: Data read from `encounters_vitals` filtered by `patientid` and constrained to Height/Weight/BMI-related `itemname` values. `display_value` is used for the value column. BMI in this table corresponds to the BMI measurement associated with the same date/context.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Vital Name | Height or Weight label | encounters_vitals | itemname | Direct mapping (e.g., 'Height', 'Weight'). |
| Value | Measurement value | encounters_vitals | display_value | Direct mapping (pre-formatted). |
| BMI | Body Mass Index | encounters_vitals | value | The BMI value for the same date where `itemname = 'BMI'`. Grouped by `encounter_date`. |
| Provider | Healthcare provider | encounters_vitals | provider | Direct mapping. |
| Facility | Healthcare facility | encounters_vitals | facility | Direct mapping. |
| Date | Encounter date | encounters_vitals | encounter_date | Direct mapping. |

---

## Table: Medications Table

### Location
Page: /medications

### Data Source
Tables: encounters_medications
Notes: Direct read from `encounters_medications` filtered by `patientid`.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Name | Medicine name | encounters_medications | medicinename | Direct mapping. |
| Dosage | Strength | encounters_medications | strength | Direct mapping. |
| Frequency | Frequency | encounters_medications | frequency | Direct mapping. |
| Duration | Duration | encounters_medications | duration | Direct mapping. |
| Route | Route | encounters_medications | route | Direct mapping. |
| Formulation | Formulation | encounters_medications | formulation | Direct mapping. |
| Instructions | Instructions | encounters_medications | instructions | Direct mapping. |
| Provider | Healthcare provider | encounters_medications | provider | Direct mapping. |
| Facility | Healthcare facility | encounters_medications | facility | Direct mapping. |
| Date | Encounter date | encounters_medications | encounter_date | Direct mapping. |

---

## Table: Problem List Table

### Location
Page: /problem-list

### Data Source
Tables: encounters_problems
Notes: Direct read from `encounters_problems` filtered by `patientid`.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Description | ICD Description | encounters_problems | icd_description | Direct mapping. |
| Code | ICD Code | encounters_problems | icd_code | Direct mapping. |
| Provider | Healthcare provider | encounters_problems | provider | Direct mapping. |
| Facility | Healthcare facility | encounters_problems | facility | Direct mapping. |
| Date | Encounter date | encounters_problems | encounter_date | Direct mapping. |

---

## Table: Progress Notes Table

### Location
Page: /progress-notes

### Data Source
Tables: encounters_progress_notes
Notes: Direct read from `encounters_progress_notes` filtered by `patientid`.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Reason | Reason for appointment | encounters_progress_notes | reason_for_appt | Direct mapping. |
| Past history | Past medical history | encounters_progress_notes | past_history | Direct mapping. |
| Assessment notes | Assessment notes | encounters_progress_notes | asmt_notes | Direct mapping. |
| Provider | Healthcare provider | encounters_progress_notes | provider | Direct mapping. |
| Facility | Healthcare facility | encounters_progress_notes | facility | Direct mapping. |
| Date | Encounter date | encounters_progress_notes | encounter_date | Direct mapping. |

---

## Table: Treatment Plan Table

### Location
Page: /treatment-plan

### Data Source
Tables: encounters_treatment_notes
Notes: Direct read from `encounters_treatment_notes` filtered by `patientid`.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Name | Treatment item name | encounters_treatment_notes | item_name | Direct mapping. |
| Treatment Note | Treatment notes | encounters_treatment_notes | notes | Direct mapping. |
| Provider | Healthcare provider | encounters_treatment_notes | provider | Direct mapping. |
| Facility | Healthcare facility | encounters_treatment_notes | facility | Direct mapping. |
| Date | Encounter date | encounters_treatment_notes | encounter_date | Direct mapping. |

---

## Table: Visit History Table (Visit Info)

### Location
Page: /visit-info

### Data Source
Tables: encounters
Notes: Direct read from `encounters` filtered by `patientid`.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Reason | Visit reason | encounters | reason | Direct mapping. |
| Provider | Healthcare provider | encounters | provider | Direct mapping. |
| Facility | Healthcare facility | encounters | facility | Direct mapping. |
| Date | Encounter date | encounters | encounter_date | Direct mapping. |

---

## Table: Lab Results Table

### Location
Page: /trends (Lab Results tab)

### Data Source
Tables: lab_results
Notes: Direct read from `lab_results` filtered by `patientid`.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Test Name | Name of lab test | lab_results | order_item_name | Direct mapping. |
| Result Value | Value of the test | lab_results | value | Direct mapping. |
| Range | Reference range | lab_results | range | Direct mapping. |
| Result Status | Status of the result | lab_results | result_status | Direct mapping. |
| Provider | Healthcare provider | lab_results | provider | Direct mapping. |
| Facility | Healthcare facility | lab_results | facility | Direct mapping. |
| Date | Result date | lab_results | result_date | Direct mapping. |

---

## Table: Procedures Table

### Location
Page: /trends (Procedures tab)

### Data Source
Tables: procedures
Notes: Direct read from `procedures` filtered by `patientid`.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Procedure Name | Name of the procedure | procedures | procedure_name | Direct mapping. |
| Date | Procedure date | procedures | encounter_date | Direct mapping. |
| Encounter ID | ID of the encounter | procedures | encounter_id | Direct mapping. |
| Procedure Code | Code of the procedure | procedures | procedure_code | Direct mapping. |

---

## Table: Clinical Interactions Table

### Location
Page: /trends (Clinician Interactions tab)

### Data Source
Tables: encounters
Notes: Read from `encounters`, with fields mapped directly; `type` corresponds to visit type.

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Date | Interaction date | encounters | encounter_date | Direct mapping. |
| Reason | Reason for interaction | encounters | reason | Direct mapping. |
| Type | Type of interaction | encounters | visittype | Direct mapping. |

---

## Table: Vital Signs Table (Trends)

### Location
Page: /trends (Vital Signs tab)

### Data Source
Tables: encounters_vitals
Notes: For each vital name (including Height/Weight/BMI), values are fetched from `encounters_vitals` by `patientid` and `itemname`, filtered by `encounter_date >= cutoff`. `uom` taken from the first record per vital in range. `data` points come from each matching row’s `value` and `encounter_date` (formatted ISO date-only).

### UI Mapping

| UI Element | Description | Database Table | Database Column | Calculation / Notes |
|------------|-------------|----------------|-----------------|---------------------|
| Vital Name | Name of the vital | encounters_vitals | itemname | Direct mapping. |
| Value | Measurement value | encounters_vitals | value | Direct mapping (time series). |
| Unit | Unit of measure | encounters_vitals | uom | Taken from the first record for each vital in range. |
| Date | Record date | encounters_vitals | encounter_date | Formatted as ISO local date (yyyy-MM-dd). |