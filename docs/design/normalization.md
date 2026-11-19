# Normalisation: UNF → 1NF → 2NF → 3NF

**Dataset:** recruiting-pipeline-v4 (5 CSV files, 1,783 records). **Student:** Bill Frazer Elia (20252SEN030), Group D.

All rows below are real rows copied from `data/raw/`. Blank raw cells are shown as `(blank)` before cleaning and as `NULL` once they become real optional columns.

## 1. How the raw data arrived, and how UNF was built

The export is already split into five related CSV files. A recruiting system, or an API for it, would normally give one **nested record per application**, with the candidate, job opening, interviews and offer inside it. I rebuilt that view from the foreign keys (`applications.candidate_id`, `applications.job_opening_id`, `interviews.application_id`, `offers.application_id`) so the normalisation starts from the realistic unnormalised shape and every step can be traced back to the raw files.

## 2. Entities identified (7 tables)

| # | Table | Why it exists | Rule applied |
|---|---|---|---|
| 1 | CANDIDATE | the nested `candidate` object | nested object becomes a table |
| 2 | DEPARTMENT | `department` repeats the same 4 values on all 100 openings | repeated value becomes a table |
| 3 | JOB_OPENING | the nested `job_opening` object | nested object becomes a table |
| 4 | APPLICATION | one candidate applying to one opening | **junction table** resolving the many-to-many between CANDIDATE and JOB_OPENING (a candidate applies to many openings, up to 5 in the data; an opening receives many candidates, up to 12), carrying its own attributes (status, date, rating) |
| 5 | INTERVIEWER | the interviewer name repeats (753 non-blank interview rows, 713 distinct names) | repeated value becomes a table |
| 6 | INTERVIEW | the repeating `interviews` list inside an application | **list becomes a child table**; also **junction table** resolving the many-to-many between APPLICATION and INTERVIEWER |
| 7 | OFFER | the nested `offer` object (0 or 1 per application) | nested object becomes a table |

That meets the "at least 5 related tables" aim with 7.

## 3. Step by step

### 3.1 UNF (unnormalised): one nested record per application

Three real records (applications 19, 21 and 2). The `interviews` list is a **repeating group**, and candidate, job opening and offer data are nested objects.

```json
{
  "application_id": 19,
  "status": "Offer",
  "applied_at": "2023-08-01 11:20:30",
  "application_date": "2022-06-03 17:55:07",
  "interview_count": 1,
  "candidate_rating": 1,
  "candidate": {
    "id": 149,
    "full_name": "James Lee",
    "email": "james.lee@example.org",
    "applied_at": "2023-08-05 22:18:23"
  },
  "job_opening": {
    "id": 25,
    "title": "Trainer Researcher",
    "department": "Marketing",
    "created_at": "2022-11-15 17:45:30",
    "status": "Open"
  },
  "interviews": [
    {
      "id": 43,
      "interviewer": "Theo Miller",
      "score": 10,
      "interviewed_at": "2023-09-23 11:29:59"
    },
    {
      "id": 415,
      "interviewer": "Sofia Bennett",
      "score": 7,
      "interviewed_at": "2023-09-09 16:06:32"
    },
    {
      "id": 711,
      "interviewer": "Elena Hughes",
      "score": 3,
      "interviewed_at": "2022-05-19 16:29:19"
    }
  ],
  "offer": {
    "id": 5,
    "offer_status": "Accepted",
    "offered_at": "2023-09-15 14:27:48",
    "salary": 50471.2
  }
}
```

```json
{
  "application_id": 21,
  "status": "Applied",
  "applied_at": "2023-06-30 20:42:28",
  "application_date": "2023-04-23 20:21:27",
  "interview_count": 3,
  "candidate_rating": 4,
  "candidate": {
    "id": 198,
    "full_name": "Maja Lee",
    "email": "maja.lee@example.org",
    "applied_at": "2023-03-06 14:18:40"
  },
  "job_opening": {
    "id": 39,
    "title": "Associate",
    "department": "HR",
    "created_at": "2022-06-07 10:28:51",
    "status": "Open"
  },
  "interviews": [
    {
      "id": 578,
      "interviewer": "Mei Ruiz",
      "score": 4,
      "interviewed_at": "2022-02-12 12:04:42"
    },
    {
      "id": 802,
      "interviewer": null,
      "score": 8,
      "interviewed_at": "2023-06-11 14:53:18"
    }
  ],
  "offer": null
}
```

```json
{
  "application_id": 2,
  "status": "Applied",
  "applied_at": "2023-04-03 18:01:19",
  "application_date": "2023-10-20 06:45:52",
  "interview_count": 1,
  "candidate_rating": 5,
  "candidate": {
    "id": 80,
    "full_name": "Selin Demir",
    "email": "selin.demir@example.net",
    "applied_at": "2023-09-20 16:39:15"
  },
  "job_opening": {
    "id": 17,
    "title": "Executive",
    "department": "HR",
    "created_at": "2022-09-28 09:37:23",
    "status": "Open"
  },
  "interviews": [
    {
      "id": 628,
      "interviewer": "Elif Hoffmann",
      "score": 8,
      "interviewed_at": "2022-09-02 11:18:26"
    }
  ],
  "offer": {
    "id": 67,
    "offer_status": "Accepted",
    "offered_at": "2023-05-03 18:04:29",
    "salary": 147924.22
  }
}
```

### 3.2 1NF: remove the repeating group

**Change:** the `interviews` list is flattened into one row per interview, so every column holds exactly one value. The nested `candidate`, `job_opening` and `offer` objects are flattened into plain columns. **Key: (application_id, interview_id).**

**Dependency removed: repeating group.** Each application held a list of 1 to 3 interviews inside one record; they are now separate rows.

Rows for applications 19 (3 interviews) and 21 (2 interviews):

| application_id | interview_id | candidate_id | candidate_name | candidate_email | candidate_applied_at | job_opening_id | job_title | department | job_created_at | job_status | application_status | applied_at | candidate_rating | interviewer | score | interviewed_at | offer_id | offer_status | offered_at | salary |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 19 | 43 | 149 | James Lee | james.lee@example.org | 2023-08-05 22:18:23 | 25 | Trainer Researcher | Marketing | 2022-11-15 17:45:30 | Open | Offer | 2023-08-01 11:20:30 | 1 | Theo Miller | 10 | 2023-09-23 11:29:59 | 5 | Accepted | 2023-09-15 14:27:48 | 50471.20 |
| 19 | 415 | 149 | James Lee | james.lee@example.org | 2023-08-05 22:18:23 | 25 | Trainer Researcher | Marketing | 2022-11-15 17:45:30 | Open | Offer | 2023-08-01 11:20:30 | 1 | Sofia Bennett | 7 | 2023-09-09 16:06:32 | 5 | Accepted | 2023-09-15 14:27:48 | 50471.20 |
| 19 | 711 | 149 | James Lee | james.lee@example.org | 2023-08-05 22:18:23 | 25 | Trainer Researcher | Marketing | 2022-11-15 17:45:30 | Open | Offer | 2023-08-01 11:20:30 | 1 | Elena Hughes | 3 | 2022-05-19 16:29:19 | 5 | Accepted | 2023-09-15 14:27:48 | 50471.20 |
| 21 | 578 | 198 | Maja Lee | maja.lee@example.org | 2023-03-06 14:18:40 | 39 | Associate | HR | 2022-06-07 10:28:51 | Open | Applied | 2023-06-30 20:42:28 | 4 | Mei Ruiz | 4 | 2022-02-12 12:04:42 | (blank) | (blank) | (blank) | (blank) |
| 21 | 802 | 198 | Maja Lee | maja.lee@example.org | 2023-03-06 14:18:40 | 39 | Associate | HR | 2022-06-07 10:28:51 | Open | Applied | 2023-06-30 20:42:28 | 4 | (blank) | 8 | 2023-06-11 14:53:18 | (blank) | (blank) | (blank) | (blank) |

Notice the redundancy that remains: the candidate, job and offer columns are copied on every interview row of the same application (rows 1 to 3 repeat everything about application 19).

### 3.3 2NF: remove partial dependencies

The key is composite (application_id, interview_id), so a column must depend on **both** parts. They do not:

- `application_id` alone determines the candidate, job opening, application status, date, rating and offer columns.
- `interview_id` alone determines `interviewer`, `score` and `interviewed_at` (an interview id is unique across all 822 interviews); `application_id` is only a foreign key to the parent.

**Dependency removed: partial dependency.** Application-level columns depended on only part of the key, so they move to APPLICATION_2NF (key: application_id); interview-level columns stay in INTERVIEW_2NF (key: interview_id, with application_id as foreign key).

**APPLICATION_2NF** (applications 19, 21, 2):

| application_id | candidate_id | candidate_name | candidate_email | candidate_applied_at | job_opening_id | job_title | department | job_created_at | job_status | application_status | applied_at | candidate_rating | offer_id | offer_status | offered_at | salary |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 19 | 149 | James Lee | james.lee@example.org | 2023-08-05 22:18:23 | 25 | Trainer Researcher | Marketing | 2022-11-15 17:45:30 | Open | Offer | 2023-08-01 11:20:30 | 1 | 5 | Accepted | 2023-09-15 14:27:48 | 50471.20 |
| 21 | 198 | Maja Lee | maja.lee@example.org | 2023-03-06 14:18:40 | 39 | Associate | HR | 2022-06-07 10:28:51 | Open | Applied | 2023-06-30 20:42:28 | 4 | (blank) | (blank) | (blank) | (blank) |
| 2 | 80 | Selin Demir | selin.demir@example.net | 2023-09-20 16:39:15 | 17 | Executive | HR | 2022-09-28 09:37:23 | Open | Applied | 2023-04-03 18:01:19 | 5 | 67 | Accepted | 2023-05-03 18:04:29 | 147924.22 |

**INTERVIEW_2NF** (5 interviews of applications 19 and 21):

| interview_id | application_id | interviewer | score | interviewed_at |
|---|---|---|---|---|
| 43 | 19 | Theo Miller | 10 | 2023-09-23 11:29:59 |
| 415 | 19 | Sofia Bennett | 7 | 2023-09-09 16:06:32 |
| 711 | 19 | Elena Hughes | 3 | 2022-05-19 16:29:19 |
| 578 | 21 | Mei Ruiz | 4 | 2022-02-12 12:04:42 |
| 802 | 21 | (blank) | 8 | 2023-06-11 14:53:18 |

### 3.4 3NF: remove transitive dependencies

In APPLICATION_2NF, non-key columns still depend on other non-key columns instead of directly on `application_id`:

| Transitive dependency | Meaning | Removed by |
|---|---|---|
| application_id → candidate_id → candidate_name, candidate_email, candidate_applied_at | candidate details belong to the candidate, not to each application | new table **CANDIDATE** |
| application_id → job_opening_id → job_title, department, job_created_at, job_status | job details belong to the opening | new table **JOB_OPENING** |
| job_opening_id → department | the department name repeats on every opening of that department | new table **DEPARTMENT** |
| application_id → offer_id → offer_status, offered_at, salary | offer details belong to the offer, and many applications have no offer | new table **OFFER** |
| interview_id → interviewer (name) | the same interviewer appears on many interviews | new table **INTERVIEWER**; INTERVIEW keeps only `interviewer_id` |

**Dependency removed: transitive dependency** (non-key → non-key) in every row above. Every non-key column now depends on the key, the whole key and nothing but the key.

**Final 3NF tables** (real rows; `interviewer_id` and `department_id` values are provisional surrogate ids that Phase IV will generate; candidate, job, application, interview and offer ids keep the raw ids).

**CANDIDATE** (`candidates.applied_at` is renamed `registered_at`; see section 4)

| candidate_id | full_name | email | registered_at |
|---|---|---|---|
| 149 | James Lee | james.lee@example.org | 2023-08-05 22:18:23 |
| 198 | Maja Lee | maja.lee@example.org | 2023-03-06 14:18:40 |
| 80 | Selin Demir | selin.demir@example.net | 2023-09-20 16:39:15 |

**DEPARTMENT** (all 4 rows)

| department_id | department_name |
|---|---|
| 1 | Engineering |
| 2 | HR |
| 3 | Marketing |
| 4 | Sales |

**JOB_OPENING**

| job_opening_id | title | department_id | created_at | status |
|---|---|---|---|---|
| 25 | Trainer Researcher | 3 | 2022-11-15 17:45:30 | Open |
| 39 | Associate | 2 | 2022-06-07 10:28:51 | Open |
| 17 | Executive | 2 | 2022-09-28 09:37:23 | Open |

**APPLICATION** (junction between CANDIDATE and JOB_OPENING)

| application_id | candidate_id | job_opening_id | status | applied_at | candidate_rating |
|---|---|---|---|---|---|
| 19 | 149 | 25 | Offer | 2023-08-01 11:20:30 | 1 |
| 21 | 198 | 39 | Applied | 2023-06-30 20:42:28 | 4 |
| 2 | 80 | 17 | Applied | 2023-04-03 18:01:19 | 5 |

**INTERVIEWER**

| interviewer_id | full_name |
|---|---|
| 37 | Theo Miller |
| 366 | Sofia Bennett |
| 500 | Mei Ruiz |
| 615 | Elena Hughes |

**INTERVIEW** (the blank interviewer in the raw row becomes `NULL`, so the interviewer is optional)

| interview_id | application_id | interviewer_id | score | interviewed_at |
|---|---|---|---|---|
| 43 | 19 | 37 | 10 | 2023-09-23 11:29:59 |
| 415 | 19 | 366 | 7 | 2023-09-09 16:06:32 |
| 711 | 19 | 615 | 3 | 2022-05-19 16:29:19 |
| 578 | 21 | 500 | 4 | 2022-02-12 12:04:42 |
| 802 | 21 | NULL | 8 | 2023-06-11 14:53:18 |

**OFFER** (at most one per application; salary and offered_at are optional)

| offer_id | application_id | offer_status | offered_at | salary |
|---|---|---|---|---|
| 1 | 211 | Declined | NULL | 90637.16 |
| 5 | 19 | Accepted | 2023-09-15 14:27:48 | 50471.20 |
| 67 | 2 | Accepted | 2023-05-03 18:04:29 | 147924.22 |

Application 21 has no offer row, which is why OFFER is a separate optional table instead of columns that would be empty on most rows.

### 3.5 Design decisions to state in class

- **Derived column dropped:** `applications.interview_count` can be computed with `COUNT(*)` on INTERVIEW. Storing it would break 3NF (a value derivable from other rows). The raw column is also wrong on 326 of 506 applications.
- **Conflicting duplicate dropped:** `applications.application_date` and `applications.applied_at` both claim to be the application date, differ on 506 of 506 rows and never fall on the same day. I keep `applied_at` because it follows the `*_at` timestamp naming used by every other table, while `application_date` is the only `*_date` column; I record this as a choice. If the source documentation says otherwise, swap them.
- **Interviewer identity:** the raw data has no interviewer id, so two interviews with the same name are treated as the same person. This is an assumption.
- **Statuses** (application: Applied, Interviewing, Offer, Rejected; job: Open, Closed; offer: Accepted, Declined) stay as constrained columns because each is a short fixed list with no attributes of its own. Phase IV adds CHECK constraints.

## 4. Field mapping: every raw field to its target

Raw file `.column` → target table.column, with the type conversion Phase IV must apply.

| Raw field | Target | Conversion or reason |
|---|---|---|
| candidates.id | CANDIDATE.candidate_id | text → NUMBER; keep raw value |
| candidates.full_name | CANDIDATE.full_name | trim; merge 3 duplicate people |
| candidates.email | CANDIDATE.email | lowercase and trim; unique after merging duplicates |
| candidates.applied_at | CANDIDATE.registered_at | text → TIMESTAMP; renamed because it is the date the person entered the system, not an application date (assumption, see dataset card) |
| job_openings.id | JOB_OPENING.job_opening_id | text → NUMBER |
| job_openings.title | JOB_OPENING.title | blank → NULL (19 rows) |
| job_openings.department | DEPARTMENT.department_name, JOB_OPENING.department_id | 4 distinct values become 4 DEPARTMENT rows; each opening gets a foreign key |
| job_openings.created_at | JOB_OPENING.created_at | text → TIMESTAMP |
| job_openings.status | JOB_OPENING.status | CHECK in (Open, Closed) |
| applications.id | APPLICATION.application_id | text → NUMBER |
| applications.candidate_id | APPLICATION.candidate_id | text → NUMBER; re-point the 3 merged duplicate candidates |
| applications.job_opening_id | APPLICATION.job_opening_id | text → NUMBER |
| applications.status | APPLICATION.status | CHECK in (Applied, Interviewing, Offer, Rejected) |
| applications.applied_at | APPLICATION.applied_at | text → TIMESTAMP |
| applications.application_date | **dropped** | conflicting duplicate of applied_at (differs on 506 of 506 rows); see 3.5 |
| applications.interview_count | **dropped** | derived value, computed from INTERVIEW; wrong on 326 rows |
| applications.candidate_rating | APPLICATION.candidate_rating | text → NUMBER; blank → NULL (49 rows); CHECK 1 to 5 |
| interviews.id | INTERVIEW.interview_id | text → NUMBER |
| interviews.application_id | INTERVIEW.application_id | text → NUMBER |
| interviews.interviewer | INTERVIEWER.full_name, INTERVIEW.interviewer_id | distinct non-blank names become INTERVIEWER rows (713); blank → NULL foreign key (69 rows) |
| interviews.score | INTERVIEW.score | text → NUMBER; blank → NULL (75 rows); CHECK 1 to 10 |
| interviews.interviewed_at | INTERVIEW.interviewed_at | text → TIMESTAMP |
| offers.id | OFFER.offer_id | text → NUMBER |
| offers.application_id | OFFER.application_id | text → NUMBER; unique (one offer per application) |
| offers.offer_status | OFFER.offer_status | CHECK in (Accepted, Declined) |
| offers.offered_at | OFFER.offered_at | text → TIMESTAMP; blank → NULL (40 rows) |
| offers.salary | OFFER.salary | text → NUMBER with 2 decimals; blank → NULL (16 rows) |

All 27 raw fields are accounted for: 25 mapped, 2 dropped.

## 5. Relationships (see `erd.png` and `data_dictionary.md`)

| Parent | Child | Cardinality | Optionality |
|---|---|---|---|
| DEPARTMENT | JOB_OPENING | one to many | an opening must have a department; a department may have no openings |
| CANDIDATE | APPLICATION | one to many | an application must have a candidate; a candidate may have no application yet |
| JOB_OPENING | APPLICATION | one to many | an application must target an opening; an opening may have none (2 have none today) |
| APPLICATION | INTERVIEW | one to many | an interview must belong to an application; an application may have no interview yet |
| INTERVIEWER | INTERVIEW | one to many | an interview may have no recorded interviewer (69 today); an interviewer may have no interview |
| APPLICATION | OFFER | one to zero-or-one | an offer must belong to an application; an application has at most one offer |

## 6. Phase IV cleaning checklist (from the quality counts)

1. Merge the 3 duplicate candidate pairs (ids 12/46, 23/61, 122/150) and re-point their applications. Merging creates no new duplicate (candidate, job) pairs.
2. Resolve the 3 duplicate (candidate, job) applications (205/494, 262/293, 20/132) before adding the unique constraint on (candidate_id, job_opening_id).
3. Convert 6 timestamp columns from text to DATE or TIMESTAMP; convert salary, score, rating and ids to NUMBER.
4. Turn blank text in optional columns into NULL: 19 titles, 49 ratings, 69 interviewers, 75 scores, 40 offered_at, 16 salaries.
5. Decide how to treat date contradictions (applications before the candidate registered or the job existed, interviews and offers before the application): 273, 242, 399 and 46 rows respectively. Report them, then keep, correct or flag them; do not silently change them.
6. Decide how to treat status contradictions: 57 applications marked "Offer" without an offer row, and 127 offers attached to applications with another status.
7. Generate INTERVIEWER (713 rows) and DEPARTMENT (4 rows) with surrogate keys, and load the foreign keys.
8. Add CHECK constraints for the status lists, rating 1 to 5 and score 1 to 10.
