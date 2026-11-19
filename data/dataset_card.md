# Dataset Card

## Dataset Name
Recruiting Pipeline Dataset

## Project
Online Job Recruitment and Applicant Tracking System

## Source
https://databasesample.com/datasets/recruiting-pipeline

## License
CC0 1.0 Public Domain

## Format
CSV

## Primary Dataset
applications.csv

## Supporting Datasets
- candidates.csv
- job_openings.csv
- interviews.csv
- offers.csv

## Number of Records

| Dataset | Records |
|----------|--------:|
| applications.csv | 506 |
| candidates.csv | 200 |
| job_openings.csv | 100 |
| interviews.csv | 822 |
| offers.csv | 155 |

Total records across all tables: **1,783**

## Date Collected

Downloaded on: **19 November 2026**

## Description

This dataset represents the recruitment process of an organization. It contains information about job openings, candidates, submitted applications, interviews, and job offers. The data models the complete hiring pipeline and is suitable for relational database design and normalization.

## One Record Represents

One record in **applications.csv** represents a single application submitted by one candidate for one job opening.

## Intended Use

This dataset will be used to design a relational database in Third Normal Form (3NF) for an Online Job Recruitment and Applicant Tracking System.

## Notes

The original dataset is stored in the `data/raw/` directory exactly as downloaded and has not been modified.
