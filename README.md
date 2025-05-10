## BLM Data Quality Testing BigQuery Implementation Work Log

### 05/10/2025

* Converted SYT_Basic tests into SQL queries and saved each as an individual query.
    * this allows for running of individual tests to obtain the actual records which fail the test rather than just receiving the count.

* Created a compiled version of the SYT_Basic query which runs all SYT_Basic attribute tests for all loaded snapshots.
    * output is a table by snapshot date with results counts for each test
    * output table is saved as a BQ table titled "SYT_Basic"

* Created saved query for Jira ticket 15252
    * CSE_DISP 
    * SRC
    * Modified date 
    * case action testing

* Created query for Jira ticket 16052

* Designed custom CSE_DISP waterfall ploty chart in dev.ipynb for tracking case dispositon synchronization changes

### 05/07/2025

* Got new snapshot data, began extracting for upload to Google Cloud  
Completed extract

* Loaded all Feb snapshot data into new vintage snapshot folder `20250201`

* Converted nlsdbs to Parquets in format acceptable to BQ  
Got snapshot pieces consolidated  
Loaded Feb snapshot data into BQ

---

### 05/05/2025

* Removed NLSDB case/case_lands Parquets from April snapshot folder. Discussed with Huy and decided spatially enabling data avoid for now.

* Loaded in CSV version of NLSDB into each snapshot folder  
March snapshot folder needs consolidating still.

* Opened CMD as admin and `cd`’d to schema/ folder  
Sourced the `load_commands_march.txt` file from load commands folder

* Confirmed Google setup still correct with:

```bash
gcloud auth list
```

* Confirmed the correct project is active:

```bash
gcloud config get-value project
```

* Ran the combine and delete of pieces for March snapshots.

* Had a big learning curve on loading NLSDB.  
Process is to extract from FileGDB as Parquet, then use pandas to load in a Notebook and drop the Shape column, then upload that to Cloud Storage.

* Converted the first `SYT_Basic` test into a SQL query and saved as `SYT01`

* Learned you have to run a query in same region of BigQuery project, which in this case is `us-central1`

* Built the following tests and saved as queries:  
`SYT 1, 3, 4, 5`

---

### 05/01/2025

* Elii set up an updated sandbox file structure for snapshots:  
`sandbox-blm-dqimp-qaqc/snapshots/2050401`

* All CSV files have been moved into the subfolder:

```bash
gsutil mv gs://sandbox-blm-seta-dqimp-qaqc/*.csv gs://sandbox-blm-seta-dqimp-qaqc/snapshots/20250401/
```

* Elii created a `.json` file with the `MC_CASE_ACTION` schema.

* Elii used `gsutil compose` to combine `MC_CASE_ACTION.csv` and `CR_FULL_CASE_ACTION.csv` into a single CSV titled `CASE_ACTION.csv`:

```bash
gsutil compose gs://sandbox-blm-seta-dqimp-qaqc/snapshots/20250401/MC_CASE_ACTION.csv \
gs://sandbox-blm-seta-dqimp-qaqc/snapshots/20250401/CR_FULL_CASE_ACTION.csv \
gs://sandbox-blm-seta-dqimp-qaqc/snapshots/20250401/CASE_ACTION.csv
```

* Created a `case_action_schema.json` to load into BigQuery dataset.

* Loaded combined `CASE_ACTION.csv` with a schema as `blm_dqimp_qaqc.CASE_ACTION_20250401`:

```bash
bq load --source_format=CSV --field_delimiter="|" --skip_leading_rows=0 \
--schema=case_action_schema.json blm_dqimp_qaqc.case_action_20250401 \
gs://sandbox-blm-seta-dqimp-qaqc/snapshots/20250401/CASE_ACTION.csv
```

* Date formats non-conformant to BigQuery; loading all as string to `CAST` later.

* Created schemas for all tables and loaded all tables for April snapshot.

* Created new snapshot subfolder for March data titled `20250301/`  
Loaded all CSVs to this subfolder  
Created load scripts for these tables to BigQuery

* ~~Loaded NLSDB case and case_land feature classes as Parquet files with WKT geometry column.  
Will have to `CAST` to GEOMETRY column compatible with BigQuery later for spatial analyses.~~
    * ^^ditching geospatial for now and loaded nlsdb as attribute table only

---

### 04/30/2025

* Huy and Elii met and Huy showed Elii how to load a CSV from sandbox into BigQuery dataset.

* Huy showed how snapshots can be handled with low LOE via date suffix naming convention.

**Setup**

* Huy created a sandbox for this data:  
<https://console.cloud.google.com/storage/browser/sandbox-blm-seta-dqimp-qaqc>

* Elii loaded all CSVs for one snapshot into that sandbox.

---

### Notes

* Huy suggested using **DataForm** instead of **dbt**… this is under the BigQuery left sidebar.