# DP-700 Answer Key — Quick Review

## Domain 1 — Implement and manage an analytics solution

### 1.1 Configure Microsoft Fabric workspace settings

**Q1 — Answer: C**
Environments package libraries and runtime settings. Attaching a notebook to a dedicated environment isolates its library versions from other notebooks. High concurrency shares a Spark **session** to reduce startup time — it does not isolate library conflicts.

---

**Q2 — Answer: B**
High concurrency allows multiple notebooks to attach to the same Spark session, sharing session state, cached DataFrames, and temp views. Without it, each notebook starts its own isolated session.

---

**Q3 — Answer: C**
Domains organize workspaces by business area. Domain admins manage a domain's workspaces; domain contributors manage subdomains — all without tenant-wide admin rights. Capacities are for billing/compute, not organization.

---

**Q4 — Answer: C**
- **Fabric admin** — creates top-level domains.
- **Domain admin** — manages an existing domain, creates subdomains, assigns workspaces.
- **Domain contributor** — manages subdomains within an existing domain but cannot create new top-level domains.

Domain contributor is the least-privilege role that satisfies the requirement.

---

**Q5 — Answer: C**
The OneLake shortcut cache retains data for **up to 24 hours from last access**. Files accessed at 4h and 12h are within the window; the file accessed 48h ago has aged out and will be re-fetched. Files over 1 GB are also never cached.

---

### 1.2 Implement lifecycle management

**Q6 — Answer: A**
Fabric's GitHub integration only requires **Contents: Read and write** to read and push workspace items. Actions, Administration, and Commit statuses are not needed.

---

**Q7 — Answer: B**
Connecting to Azure DevOps requires **organization, project, Git repository, and branch**. A Git folder is optional. Authentication uses Entra identity — not a PAT.

---

**Q8 — Answer: C**
**Deployment rules** let you override data source bindings and parameter values per stage (e.g., point Prod to the production server instead of the dev server).

---

**Q9 — Answer: B**
Deployment pipelines only pair items deployed through the pipeline itself (or explicitly paired). Independently-created items with matching names remain unpaired, so a new copy is created — resulting in two lakehouses both named "Sales."

---

**Q10 — Answer: B**
**Database projects** (like SSDT for SQL Server) let you compare schemas, catch broken references, and generate deployment scripts with no custom code — the lowest-effort approach.

---

### 1.3 Configure security and governance

**Q11 — Answer: B**
Sharing a lakehouse and granting **"Read all SQL analytics endpoint data"** gives T-SQL-only access without Spark access or visibility into the Files section.

---

**Q12 — Answer: C**
**Contributor** can create and edit items (required to update warehouse tables). Viewer is read-only. Member and Admin grant more than required.

---

**Q13 — Answer: C**
Fabric warehouse RLS requires an **inline table-valued function** that returns 1 when a row should be visible, bound to a table via a **SECURITY POLICY** using FILTER PREDICATE.

---

**Q14 — Answer: C**
`partial(2, "XXXXXX", 4)` reveals the first 2 and last 4 characters, masking the middle. `default()` masks everything; `email()` shows only the first letter and domain; `random()` applies to numeric columns.

---

**Q15 — Answer: B**
**Certified** endorsement requires a tenant-designated reviewer to approve the item. **Promoted** is a self-endorsement any workspace contributor can apply without approval.

---

**Q16 — Answer: A, B, D**
Lakehouses do not support table-level permissions via Spark/Files — it's all-or-nothing. Solution:
1. Migrate the PII Employee table to a separate lakehouse (D).
2. Share the now-PII-free Lakehouse1 with the engineer (A).
3. Assign Contributor on Workspace1 so they can write (B).

---

**Q17 — Answer: B**
**Column-level security** (`GRANT/DENY SELECT ON table(column)`) blocks access to the column entirely. Dynamic data masking still shows the column — just with masked values. RLS filters rows, not columns.

---

**Q18 — Answer: B**
**Sensitivity labels** (Microsoft Purview) automatically propagate from upstream items to downstream consumers. Endorsement is a trust signal but does not propagate.

---

### 1.4 Orchestrate processes

**Q19 — Answer: B**
**Storage event triggers** on pipelines listen directly to Azure Storage blob-created/deleted events — the purpose-built, lowest-latency approach.

---

**Q20 — Answer: B**
A **parent pipeline** invoking child pipelines with **On success / On failure** dependencies to an email activity is the standard pattern. Separate schedules can't guarantee ordering; a monolithic notebook makes retries and alerting harder.

---

**Q21 — Answer: C**
For raw ingestion with no transformation, the **Copy data activity** in a pipeline is lowest-effort — native connectors, writes directly to Delta with minimal configuration.

---

**Q22 — Answer: A**
- Team1 (Power Query skills) → **Dataflow Gen2**
- Team2 (Python/PySpark) → **Notebook**
- Team3 (ex-ADF users) → **Data pipeline**

---

## Domain 2 — Ingest and transform data

### 2.1 Design and implement loading patterns

**Q23 — Answer: B**
**Store 1 Year, Refresh 1 Month.** The store window defines history retained; the refresh window defines how far back rows are re-queried. Data older than 1 month never changes, so re-fetching beyond 1 month wastes compute.

---

**Q24 — Answer: B**
Type 2 SCD requires a **surrogate key** (natural key isn't unique across versions), plus **ValidFrom, ValidTo, IsCurrent** for correct history tracking. Facts reference the surrogate current at the time of the event.

---

**Q25 — Answer: C**
Computing a **hash** (e.g., SHA2_256) of tracked attributes on the source produces a single small value per row — only the hash travels to the target for comparison, minimizing data transfer and CPU work.

---

**Q26 — Answer: C**
**Watermarks with allowed lateness** are the canonical streaming mechanism for late-arriving data. The grace period keeps the window open without stalling the pipeline. Enlarging the window changes aggregation semantics.

---

**Q27 — Answer: C**
`Window.partitionBy("CustomerID").orderBy(col("LastModified").desc())` with `row_number() == 1` is fully deterministic — it always picks the latest full row. `dropDuplicates()` picks non-deterministically; `groupBy().agg(max())` returns only the date, not the full row.

---

### 2.2 Ingest and transform batch data

**Q28 — Answer: B**
A **lakehouse** supports Spark writes and reads via Spark, T-SQL (SQL analytics endpoint), and KQL (via OneLake shortcuts). A warehouse is T-SQL-first; an eventhouse is KQL-centric.

---

**Q29 — Answer: B**
**Data pipelines** with the Copy data activity support on-premises data gateways and have a native SQL Server connector. Dataflow Gen1 is legacy Power BI, not a Fabric item.

---

**Q30 — Answer: B**
**Shortcuts** virtualize access to external locations (S3, GCS, ADLS, etc.) without copying data. Mirroring copies data into OneLake; Dataflow Gen2 physically materializes it.

---

**Q31 — Answer: B**
**Mirroring for Azure SQL Database** continuously replicates the database into OneLake as Delta — no pipeline required. Shortcuts can't reach relational DBs; pipelines require orchestration.

---

**Q32 — Answer: B**
The shortcut cache window is 24 hours from last access. ProductFile (12h) and StoreFile (4h) are within the window; TripsFile (48h) has aged out. Files over 1 GB are also never cached.

---

**Q33 — Answer: A, B**
- **ForEach** iterates over the 7 entity endpoints.
- **Copy data** (nested inside ForEach) reads each endpoint and writes to the lakehouse.

---

**Q34 — Answer: B**
**Notebooks** provide the full PySpark API including Spark SQL windowing and MLlib. Dataflow Gen2 uses Power Query M — no PySpark or ML support.

---

**Q35 — Answer: C**
**Staging** lands data in intermediate storage, avoiding holding the full dataset in runtime memory — the recommended approach for cross-cloud, large-volume transfers.

---

**Q36 — Answer: B**
**Activity-level retry** (Retry count + Retry interval) handles transient errors declaratively. Fault tolerance skips incompatible rows; it does not retry the whole activity.

---

**Q37 — Answer: B**
- **LEFT JOIN** preserves all employee rows.
- **GROUP BY** + **HAVING COUNT(*) > 2** filters after aggregation.

WHERE cannot reference aggregates — it filters before grouping.

---

**Q38 — Answer: B**
DimProduct holds descriptive attributes: **ProductID, ProductName, ProductColor**. TransactionID, Date, and SalesAmount are transactional/measurement data that belong in FactSales.

---

### 2.3 Ingest and transform streaming data

**Q39 — Answer: B**
**Eventstreams** have native Event Hub input, a no-code Filter operator, and a lakehouse destination — the lowest-effort solution. Spark Structured Streaming requires code.

---

**Q40 — Answer: C**
**Tumbling** windows are fixed-size, non-overlapping, and every event maps to exactly one window — matching the requirement for contiguous 1-minute intervals.

---

**Q41 — Answer: B**
A **hopping window** (size: 5 min, hop: 1 min) produces overlapping windows updated every minute — exactly what "rolling average updated every minute" means. Tumbling windows don't overlap.

---

**Q42 — Answer: C**
Azure SQL → eventstream sourcing requires **Change Data Capture (CDC)** to expose row-level inserts/updates/deletes. Without CDC, there are no change events to propagate.

---

**Q43 — Answer: C**
**Filter pushdown** — reducing rows before the join — is the highest-impact KQL optimization. Outer joins produce more rows; `extend` keeps all columns; `make_list()` adds aggregation work.

---

**Q44 — Answer: B**
**Eventhouses** (Real-Time Intelligence) are engineered for high-ingestion, low-latency KQL queries with built-in time-series and anomaly-detection functions (e.g., `series_decompose_anomalies`).

---

**Q45 — Answer: D**
The **Aggregate** operator (with a window) provides statistical functions including Standard Deviation. Group by alone doesn't compute statistics; Expand flattens arrays; Union combines streams.

---

## Domain 3 — Monitor and optimize an analytics solution

### 3.1 Monitor Fabric items

**Q46 — Answer: B**
**Monitoring hub** is the central cross-item execution history view. The admin portal is for tenant governance; Real-Time hub catalogs streaming sources; Capacity Metrics tracks consumption, not item runs.

---

**Q47 — Answer: D**
The **Location** column in Monitoring hub identifies the workspace where each run executed.

---

**Q48 — Answer: B**
**Semantic link (`sempy`)** lets notebooks read semantic model metadata — including refresh history — as DataFrames that can be charted and shared. The built-in dialog is not shareable.

---

**Q49 — Answer: B**
**Activator (Data Activator / Reflex)** is Fabric's native rule-based alerting engine. It can count failures across runs and trigger alerts. In-pipeline email activities only handle a single run's failure.

---

**Q50 — Answer: B**
**`queryinsights.long_running_queries`** includes runtime, last_run_command, and `number_of_failed_runs` — the right view for "long-running AND repeatedly failing." `sys.dm_exec_requests` shows only currently executing work.

---

### 3.2 Identify and resolve errors

**Q51 — Answer: B**
The **input JSON** of an activity run shows the resolved configuration passed to the activity — including any dynamically built SQL query. Output JSON contains the result or error, not the inputs.

---

**Q52 — Answer: B**
**Activity-level retries** are set directly on the Notebook activity (Retry count + Retry interval in seconds) — the declarative way to handle transient failures in a pipeline.

---

**Q53 — Answer: C**
**Incremental refresh** (Store 1 Year, Refresh 1 Month) is the targeted fix — retain the needed history but only re-fetch the window where data can still change. Switching dataflow generation alone doesn't reduce volume.

---

**Q54 — Answer: C**
**OPTIMIZE** compacts small files into larger ones. **VACUUM** removes older unreferenced file versions. Both together address performance and storage — OPTIMIZE alone leaves old small files on disk.

---

**Q55 — Answer: A**
**OPTIMIZE** only rewrites/compacts current files; it does not delete old versions. VACUUM is the deletion command.

---

**Q56 — Answer: B**
**VACUUM** removes unreferenced Delta files. The default 7-day retention minimum can be lowered if needed. DELETE only marks rows as deleted in the log — old files remain until VACUUM runs.

---

**Q57 — Answer: B**
The **eventstream item's runtime / data insights view** surfaces per-processor error counts, details, and timestamps. Monitoring hub is less granular for streaming processor errors.

---

**Q58 — Answer: B**
Shortcuts use the credential established at creation time. If that identity is rotated, deleted, or has its ADLS RBAC/ACL changed, authorization errors appear intermittently.

---

### 3.3 Optimize performance

**Q59 — Answer: B**
**Disable V-Order** for write-intensive staging tables. V-Order adds write-time overhead for a read optimization benefit that never materializes on short-lived staging tables.

---

**Q60 — Answer: B**
**Change surrogate keys to int/bigint.** A 128-bit MD5 hash is far heavier than a 4- or 8-byte integer for storage, scans, joins, and memory. SHA-256 would be even larger — not smaller.

---

**Q61 — Answer: B**
Direct Lake falls back to DirectQuery when a table **exceeds per-SKU row/column count limits** or uses unsupported features (e.g., calculated columns, certain relationship types). Always verify current limits on Microsoft Learn.

---

**Q62 — Answer: B**
**Custom Spark pools** provide dedicated, isolated compute per workload. High concurrency would share a session — the opposite of isolation.

---

**Q63 — Answer: B**
**Create / update statistics** gives the optimizer accurate cardinality estimates for better query plans. Primary keys in Fabric warehouse are not enforced; VACUUM is a Delta file operation unrelated to warehouse query plans.

---

**Q64 — Answer: B**
**Data pipelines** are the orchestration item — they support schedules, triggers, sequential dependencies, parameters, and activity-level retry.

---

**Q65 — Answer: A**
`@pipeline().parameters.param1` returns the value in its **native type** (int). `@{ }` forces string interpolation; `@concat(...)` also returns a string.

---

**Q66 — Answer: A, B, D**
- **(A)** Set Notebook activity Retry to 2 — handles transient failures.
- **(B)** Link Refresh with **On success** — ensures refresh only runs after a successful notebook run.
- **(D)** Schedule **the pipeline** — scheduling the notebook directly would cause it to run twice (once standalone, once via the pipeline).
