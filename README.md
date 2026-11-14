# Online Job Recruitment System

## Student 

**Name:** Bill Frazer Elia  
**Student ID:** 202052SEN030  
**Course:** INSY 8311 – Database Development with PL/SQL  
**Academic Year:** 2026–2027 Semester I  
**Instructor:** Eric Maniraguha

---

# Project Title

**Online Job Recruitment System**

---

# Problem Statement

Many organizations still manage recruitment manually through emails, paper documents, and spreadsheets. These methods make it difficult to organize applicant information, monitor job applications, schedule interviews, and generate recruitment reports.

The Online Job Recruitment System addresses these challenges by providing a centralized database that stores recruitment information securely and automates major recruitment processes.

---

# Main Objective

To develop a secure and efficient Oracle database system that manages online recruitment activities using PL/SQL.

---

# Specific Objectives

- Manage employer information.
- Manage applicant information.
- Store job vacancies.
- Record job applications.
- Schedule interviews.
- Track application status.
- Generate recruitment reports.

---

# Data Source

A real-world recruitment dataset will be obtained from a public source such as Kaggle. The selected dataset will contain job vacancies, applicants, skills, and recruitment information suitable for educational purposes.

---

# Planned Database Tables

- Users
- Employers
- Applicants
- Jobs
- Applications
- Interviews
- Skills
- Applicant_Skills
- Departments
- Notifications
- Audit_Log

---

# Planned PL/SQL Features

## Procedures

- Add New Job
- Register Applicant
- Submit Application
- Schedule Interview
- Update Application Status

## Functions

- Count Applicants per Job
- Check Job Availability
- Calculate Total Applications

## Triggers

- Prevent applications after the deadline.
- Automatically update job status when vacancies are filled.
- Record all application status changes in the audit log.

## Cursors

- Display applicants for a selected job.
- Generate recruitment reports.

## Packages

A PL/SQL package will group recruitment-related procedures and functions for easier maintenance and reuse.

## Exception Handling

The system will handle:

- Duplicate applications
- Invalid Job IDs
- Missing applicant information
- Database errors

---

# Innovation

The project will include an Oracle APEX web application that connects directly to the Oracle database. Employers will be able to post jobs, applicants will submit applications online, and administrators will manage recruitment activities through a user-friendly interface. The application will execute PL/SQL procedures and display real-time information from the database.


#  12: References

1. National Institute of Statistics of Rwanda (NISR). (2025). *Labour Force Survey Annual Report 2025*. https://www.statistics.gov.rw/

2. Rwanda Labour Market Information System (LMIS). (2026). *Rwanda Labour Market Information System*. https://lmis.ippis.rw/

3. Oracle. (2026). *Oracle AI Database PL/SQL Language Reference (26ai).* https://docs.oracle.com/en/database/oracle/oracle-database/26/lnpls/

4. LinkedIn Job Postings Dataset. (2024). *Kaggle*. https://www.kaggle.com/datasets/arshkon/linkedin-job-postings

5. Elmasri, R., & Navathe, S. B. (2016). *Fundamentals of Database Systems* (7th ed.). Pearson.

6. Silberschatz, A., Korth, H. F., & Sudarshan, S. (2019). *Database System Concepts* (7th ed.). McGraw-Hill Education.

