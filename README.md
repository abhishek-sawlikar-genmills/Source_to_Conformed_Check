# Source_to_Conformed_Check
The Source-to-Conformed Validation module provides automated data quality, integrity, and reconciliation checks between raw/source staging data (Bronze/Raw layer) and standardized, curated conformed models (Silver/Conformed layer). 

## 2. Key Objectives & Scope

- Completeness Verification: Ensure record counts and grain match expected lineage between source entities and conformed dimensions/facts.
Data Integrity & Consistency: Verify primary key uniqueness, foreign key referential integrity, and handle edge cases (e.g., surrogate key assignment, default fallback keys).
- Metric Reconciliation: Compare quantitative aggregates (e.g., volume, sales, market share percentages) before and after transformations to guarantee arithmetic fidelity.
- Schema & Type Validation: Flag unexpected nulls, truncation, type casting issues, or timestamp offset mismatches.
- Audit & Quarantine: Route mismatched records or failed assertions to quarantine logs/tables for developer and QA triage.

## 3. Validation Checks Performed

| Check Type | Method / Assertion | Description | Severity |
| :--- | :--- | :--- | :--- |
| Row Count Check | `count(source) == count(conformed) + quarantined` | Validates no unexpected drops or unhandled duplicates occurred during ETL. | Critical |
| Key Uniqueness | `count(distinct PK) == count(PK)` | Confirms grain integrity and absence of duplicate key generation. | Critical |
| Null / Completeness | `col(x).isNull() == 0` | Verifies mandatory fields (keys, partition dates, measure columns) are populated. | High |
| Referential Integrity | `anti_join(child, parent)` | Asserts all foreign keys map correctly to dimension surrogate keys without orphaned facts. | High |
| Aggregate Reconciliation | `abs(sum(src_val) - sum(conf_val)) < tolerance` | Compares aggregate measures within a configurable epsilon tolerance. | High / Warning |
| Schema & Types | `schema_compare(src, target)` | Validates data types, column names, and encoding compliance. | Medium |

---

## 5. Prerequisites & Dependencies

- **Python:** 3.9+
- **Core Libraries:**
  - `pyspark` (or `duckdb` / `pandas` for local lightweight runs)
