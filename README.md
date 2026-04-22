# The Adverse Drug Reactions Safety Intelligence Dashboard

## Context 
Adverse Drug reaction data is critical for drug safety monitoring yet its raw data requires significant cleaning and transofmation vefore it can support decision-making. 

## Problem statement 
Raw pharmaceutical safety data presents several analytical challenges: 
1. Messy text fields - Inconsistent capitalisation, extra spaces and varying formates for the same medical concepts
2. Multiple representations - Adverse reaction terms appear with slashes (e.g Abdominal Discomfort / Stomach Discomfort) requiring exrtaction of standardise terms
3. Verbose categorical values - Frequency, Gender and Age fields used long discriptive strings instead of standardised labels
4. Missing standard mappings -
5. Null Values - 

## Approach 
**Data Cleaning:** Standardised text and trimmed all whitespaces across all categorical columns. 
  Extracted preferred reaction terms by splitting on slashes using RegEx 
  
**Analysis (SQL):** Built 8 SQL queries using CTEs, Window functions, conditional aggregation and percentage calculations

**Visualisation:** Designed 6 Databricks SQL Dashboards

## Outcome 
- A clean production-ready table in Databricks containing standardised ADR data
- Dashboard with 6 visualisations supporting drug safety explorations
- 8 reusable sql queries documenting analytical patterns 

Interactive dashboard with 6 visualisations

## Objectives 
Primary objective: 
* Transform raw, unstructured pharmaceutical safety data into a clean, analysable format and build an interactive dashboard that enables drug safety signal detection and pattern identification.
