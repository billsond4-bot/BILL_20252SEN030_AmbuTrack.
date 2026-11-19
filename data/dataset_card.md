Dataset Card: Recruiting Pipeline (v4, CSV)

Project: INSY 8311 Final Exam Capstone, Phase II. Student: Bill Frazer Elia (20252SEN030), Group D. Instructor: Eric Maniraguha.
Name	recruiting-pipeline-v4-csv
Source link	[https://databasesample.com/datasets/recruiting-pipeline?utm_source]
Licence	[ Not stated ]
Format	5 CSV files, comma-separated, header row, CRLF line endings, no byte-order mark, all values stored as text
Size	1,783 records in 5 files, 91,047 bytes (88.9 KB)
Date collected	[19-11-2026]
Period covered	2022-01-01 to 2023-12-31 (all timestamps, YYYY-MM-DD HH:MM:SS)
Domain	Recruiting and applicant tracking

The two largest files, applications (506 records) and interviews (822 records), each exceed the 500-record minimum.

File	Records	Columns	Bytes	SHA-256
recruiting-pipeline-v4-csv-candidates.csv	200	4	12,115	dc6d6e69229403cf86156cc288f1026662849683222324d1e1cd3b46672d6ead
recruiting-pipeline-v4-csv-job_openings.csv	100	5	5,319	bca19568d0ad8ea0daee354e603b86208012884e4e84bc2b50ca44071cb46f87
recruiting-pipeline-v4-csv-applications.csv	506	8	32,755	68d3621e7bb49ac78fd8eecd6384253bf3ce1ba2e85cfea96883ac7ed0abbe37
recruiting-pipeline-v4-csv-interviews.csv	822	5	34,492	242516f399d447ac57f02afddee998aca54e2691c4de15e608e8cfec70aeca83
recruiting-pipeline-v4-csv-offers.csv	155	5	6,366	4a9c20275c0423c06abba75dc45aa00dfa516a1bc70971e3a603e9ee3d7aecbd

The SHA-256 values prove the files are byte-for-byte the originals. Recompute them with sha256sum at any time.

File	One record is...	Columns
candidates	one candidate profile (a person who entered the system)	id, full_name, email, applied_at
job_openings	one vacancy a department opened	id, title, department, created_at, status
applications	one candidate applying to one job opening	id, candidate_id, job_opening_id, status, applied_at, application_date, interview_count, candidate_rating
interviews	one interview of one application, by one interviewer, with a score	id, application_id, interviewer, score, interviewed_at
offers	one job offer made on one application	id, application_id, offer_status, offered_at, salary

Relationships: applications.candidate_id → candidates.id, applications.job_opening_id → job_openings.id, interviews.application_id → applications.id, offers.application_id → applications.id.
   
Personal data and anonymisation
Personal fields present: candidates.full_name, candidates.email, interviews.interviewer (a person's name).
All 200 candidate emails use the reserved example domains (example.com 66, example.net 69, example.org 65), which cannot belong to real mailboxes, and the names are not linked to phone numbers or ID numbers. The data therefore looks synthetic or already anonymised.
[CONFIRM before submitting: state in one sentence that the names are fictitious or were anonymised by the source. If any name is a real person, replace it before committing the file.]
No phone numbers, national ID numbers or addresses exist in any file.

Known gaps in the documentation
Salary has no currency or period (per month or per year); values run from 50,003.80 to 199,459.82.
The source does not explain candidates.applied_at or why applications holds two date columns. Phase II assumes candidates.applied_at is when the person entered the system.

Data quality report 

Checks were run on the unmodified files. Percentages use the file's own record count as the base.

File	Check	Result
candidates	Missing values in any column	0 of 200 (0.0%)
candidates	Duplicate people (same name and email under different id): ids 12 & 46, 23 & 61, 122 & 150	3 of 200 (1.5%)
candidates	Invalid email format	0 of 200 (0.0%)
job_openings	Blank title	19 of 100 (19.0%)
job_openings	Openings with no application at all (ids 35 and 47)	2 of 100 (2.0%)
job_openings	Spelling variants of the same department (4 distinct values: Engineering, HR, Marketing, Sales) or status (Open, Closed)	0
applications	Blank candidate_rating	49 of 506 (9.7%)
applications	Rows where applied_at and application_date differ (they never fall on the same day)	506 of 506 (100.0%)
applications	applied_at earlier than the candidate's own candidates.applied_at	273 of 506 (54.0%)
applications	applied_at earlier than the job opening's created_at	242 of 506 (47.8%)
applications	application_date earlier than the job opening's created_at	234 of 506 (46.2%)
applications	interview_count different from the real number of rows in interviews.csv	326 of 506 (64.4%)
applications	interview_count = 0 although the application has at least one interview row	8 of 506 (1.6%)
applications	Same candidate applying to the same job twice (application ids 205/494, 262/293, 20/132)	3 of 506 (0.6%)
applications	Status "Offer" but no row in offers.csv	57 of 85 (67.1%)
interviews	Blank interviewer	69 of 822 (8.4%)
interviews	Blank score	75 of 822 (9.1%)
interviews	interviewed_at earlier than the application's applied_at	399 of 822 (48.5%)
interviews	Interviewer names repeated on more than one interview (no interviewer id exists, so identity is by name only)	40 of 713 (5.6%)
offers	Blank offered_at (all 40 are "Declined")	40 of 155 (25.8%)
offers	Blank salary (11 Accepted, 5 Declined)	16 of 155 (10.3%)
offers	offered_at earlier than the application's applied_at	46 of 115 (40.0%)
offers	Offer attached to an application whose status is not "Offer"	127 of 155 (81.9%)
offers	Two offers on the same application	0 of 155 (0.0%)
all files	Orphan foreign keys (candidate_id, job_opening_id, application_id)	0
all files	Exact duplicate rows (all columns, or all columns except id)	0

Type problems. CSV has no types, so every column is text. Phase IV must convert: 6 timestamp columns to DATE or TIMESTAMP (candidates.applied_at, job_openings.created_at, applications.applied_at, applications.application_date, interviews.interviewed_at, offers.offered_at), offers.salary to NUMBER with 2 decimals, interviews.score and applications.candidate_rating to NUMBER, and every id column to NUMBER. Blank text in optional columns must become NULL.


