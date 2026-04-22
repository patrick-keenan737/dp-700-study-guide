# Study Guide

# DP-700 Study Guide — Implementing Data Engineering Solutions Using Microsoft Fabric

**Based on the official skills measured as of January 26, 2026.**

-----

## How to use this guide

This guide is organized to match the three official exam domains, each weighted 30–35%:

1. **Implement and manage an analytics solution**
1. **Ingest and transform data**
1. **Monitor and optimize an analytics solution**

Each question has its **answer and explanation hidden inside a collapsible block**. Try to answer first, then expand the block to check.

**Recommended workflow:**

1. Work through one sub-domain at a time.
1. Note the number of any question you miss in the “Weak Areas Tracker” at the end.
1. After finishing a domain, go back to your missed questions and re-attempt them a day later.
1. If a concept keeps tripping you up, follow the “Dig deeper” link to the relevant Microsoft Learn page.

**A note on accuracy:** These questions are written from scratch based on the official exam objectives and current Fabric documentation. The answers reflect Microsoft’s documented behavior as of April 2026. Fabric evolves monthly, so if anything looks outdated when you’re studying, check Microsoft Learn as the source of truth.

-----

## Domain 1 — Implement and manage an analytics solution (30–35%)

### 1.1 Configure Microsoft Fabric workspace settings

-----

**Question 1.** Your workspace has high concurrency enabled for notebooks. Multiple data engineers complain that their Spark sessions interfere with each other when they import different versions of the same Python library. What should you configure?

A. Disable high concurrency for notebooks
B. Increase the executor count in the default pool
C. Create separate environments and attach them to the relevant notebooks
D. Move each notebook to its own workspace

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: C**

Environments in Fabric package libraries, Spark compute configuration, and runtime settings. Attaching a notebook to a dedicated environment isolates its library versions from other notebooks. High concurrency is about sharing one Spark **session** to reduce startup time — it does not isolate library conflicts. Disabling it would hurt startup performance without fixing the underlying library clash.

*Dig deeper: “Create, configure and use an environment in Microsoft Fabric” on Microsoft Learn.*

</details>

-----

**Question 2.** You have a workflow where Notebook A produces a cached DataFrame that Notebook B needs to read from the same in-memory cache. Which workspace setting makes this possible?

A. Dynamic allocation in the Spark pool
B. High concurrency for notebooks
C. Auto-scale on the starter pool
D. Native execution engine (NEE)

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

High concurrency for notebooks allows multiple notebooks to attach to the same Spark session, which means they share the session state, cached DataFrames, and temp views. Without high concurrency, each notebook starts its own isolated session and cannot see another session’s cache.

</details>

-----

**Question 3.** You administer 40 Fabric workspaces across Finance, Sales, and HR. You want each business area’s workspaces grouped together in the OneLake catalog, and you want a designated person in each area to manage their own grouping without granting them tenant-wide admin rights. Which feature should you use?

A. Capacities
B. Workspace tags
C. Domains and subdomains
D. Folders

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: C**

Domains organize workspaces by business area. A Fabric admin creates top-level domains, and **domain admins** manage the domain’s workspaces. A **domain contributor** can further manage subdomains within a domain. This is the least-privilege way to delegate grouping without giving tenant admin rights. Capacities are for billing/compute, not organization. Workspace tags don’t exist as a native grouping feature.

</details>

-----

**Question 4.** You want a user to create subdomains inside the existing Sales domain and assign workspaces to them, but the user must NOT be able to create new top-level domains. Following least privilege, which role should you assign?

A. Fabric admin
B. Domain admin
C. Domain contributor
D. Workspace admin

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: C**

Role hierarchy for domains:

- **Fabric admin** — creates top-level domains (most privilege).
- **Domain admin** — manages an existing domain, creates subdomains, assigns workspaces.
- **Domain contributor** — manages subdomains and workspace assignments within an already-created domain, but cannot create new top-level domains.

For the task described, domain contributor is the least-privilege role that works. (Note: some older sources merge “contributor” and “admin” — the current documented split is the one above. Always confirm against Microsoft Learn.)

</details>

-----

**Question 5.** You enable the OneLake cache for shortcuts in Workspace1 to reduce egress when reading from Amazon S3. Three shortcut-backed files were last accessed at: 4 hours ago, 12 hours ago, and 48 hours ago. Which will be served from cache?

A. All three
B. Only the one accessed 4 hours ago
C. The ones accessed 4 and 12 hours ago
D. Only the one accessed 48 hours ago

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: C**

The OneLake shortcut cache retains data for **up to 24 hours from the last access**. Files accessed within that window (4h, 12h) are eligible to serve from cache. The file last accessed 48 hours ago has aged out and will be re-fetched from the source. There are also size limits (files over 1 GB are not cached), but they are not relevant here.

</details>

-----

### 1.2 Implement lifecycle management

-----

**Question 6.** Your company uses GitHub for source control. You need to give Fabric the minimum GitHub permissions required to commit workspace items. Following least privilege, which permission set should you grant?

A. Contents (Read and write) only
B. Actions (Read and write) and Contents (Read and write)
C. Administration (Read) and Contents (Read and write)
D. Contents (Read) and Commit statuses (Read and write)

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: A**

Fabric’s GitHub integration needs **Contents: Read and write** to read and push workspace items. Actions, Administration, and Commit statuses are not required for basic commit/sync functionality.

</details>

-----

**Question 7.** You are configuring Git integration for Workspace1 with an Azure DevOps repository. Which details are required to establish the connection?

A. Personal access token, repository URL, Git folder
B. Organization, project, repository, and branch
C. Project, repository, and Git folder only
D. Azure subscription ID, project, and repository

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

To connect a Fabric workspace to Azure DevOps, you select the **organization, project, Git repository, and branch** — and optionally a Git folder to scope the sync. Authentication happens through your Entra identity, so a PAT is not the selected input in the UI.

</details>

-----

**Question 8.** A deployment pipeline promotes items from Dev → Test → Prod. The connection string for a data source must differ per stage so Prod connects to the production database instead of the test database. What should you configure?

A. Workspace assignments
B. Stage-level access controls
C. Deployment rules (parameterization)
D. Pairing rules

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: C**

Deployment rules let you override data source bindings and parameter values per stage. For example, “when deploying to Prod, point the pipeline’s source to prod-server instead of dev-server.” Pairing happens automatically by item name/ID and is not about parameters.

</details>

-----

**Question 9.** Workspace1 and Workspace2 both contain a lakehouse named “Sales” that were created independently (not via a deployment pipeline). You add Workspace1 and Workspace2 to a new deployment pipeline and run a deployment from Workspace1 → Workspace2. What happens?

A. The pipeline automatically pairs and overwrites the Workspace2 lakehouse
B. The items are treated as unpaired; Workspace2 ends up with two lakehouses both named “Sales”
C. The deployment fails until the items are manually paired
D. The Workspace2 lakehouse is deleted before deployment

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

Deployment pipelines pair items **only when they were deployed through the pipeline itself** (or explicitly paired afterward). Two independently-created items with matching names are unpaired by default, so the deployment creates a new copy in the target, resulting in duplicates. You must manually pair them if you want the newer one to overwrite the older one.

</details>

-----

**Question 10.** You need to validate that a Fabric warehouse’s schema contains no invalid references (broken views, missing columns) before deploying it to a higher environment. Which option minimizes development effort?

A. Write a T-SQL script that iterates sys.objects and sys.sql_dependencies
B. Use a database project with schema compare
C. Write a Python script with pyodbc
D. Manually review each object in the portal

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

Database projects in Fabric (the same concept as SSDT for SQL Server) let you compare schemas, catch broken references, and generate deployment scripts with no custom code. This is the lowest-effort, sanctioned path for warehouse schema lifecycle.

</details>

-----

### 1.3 Configure security and governance

-----

**Question 11.** Data analysts must read gold-layer tables in a lakehouse using T-SQL only. They must NOT have Spark access and must NOT see the Files section. What is the correct configuration?

A. Give them the Viewer role on the workspace
B. Share the lakehouse and grant “Read all SQL analytics endpoint data”
C. Share the lakehouse and grant “Read all Apache Spark”
D. Grant them Contributor and revoke Spark manually

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

Lakehouse sharing exposes three separate read permissions: “Read all SQL analytics endpoint data” (T-SQL endpoint access), “Read all Apache Spark” (Spark + Files access), and “Build reports on the default semantic model.” Granting only the first gives exactly the access described. Workspace Viewer would also let them see all other items in the workspace, which the question does not mention but is less precise.

</details>

-----

**Question 12.** User3 must view every item in Workspace1 and be able to update tables in a warehouse in that workspace. Object-level permissions on the warehouse are already granted. Following least privilege, which workspace role should User3 have?

A. Admin
B. Member
C. Contributor
D. Viewer

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: C**

Workspace roles, from most to least privileged: Admin > Member > Contributor > Viewer. Contributor can create and edit items, which is required to update warehouse tables. Viewer is read-only. Member and Admin grant more than required (Member can share items; Admin manages workspace settings and roles).

</details>

-----

**Question 13.** You need to implement row-level security in a Fabric warehouse so each salesperson sees only their rows. Which object type must you create in addition to a security policy?

A. A stored procedure with dynamic SQL
B. A view with a WHERE clause
C. An inline table-valued function
D. A CHECK constraint

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: C**

Fabric warehouse RLS mirrors SQL Server: create an **inline table-valued function** that returns 1 when a row should be visible, then bind it to a table in a **SECURITY POLICY** using FILTER PREDICATE. Views and stored procedures are not the defined mechanism.

</details>

-----

**Question 14.** You need to mask a CreditCard column so analysts see only the first 2 digits and last 4 digits. Which dynamic data masking function should you use?

A. default()
B. email()
C. partial(2, “XXXXXX”, 4)
D. random()

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: C**

`partial(prefix_length, padding, suffix_length)` reveals the specified number of leading and trailing characters and replaces the middle with the padding string. `default()` masks the whole value, `email()` reveals only the first letter and domain suffix, and `random()` returns a random number (for numeric columns).

</details>

-----

**Question 15.** You want to mark a semantic model as officially reviewed and trusted by the data governance team, requiring formal approval before the label can be applied. Which endorsement should you use?

A. Promoted
B. Certified
C. Master data
D. Featured

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

**Certified** endorsement requires a tenant-designated reviewer to approve the item — it signals authoritative, governance-approved content. **Promoted** is a lightweight self-endorsement any workspace contributor can apply.

</details>

-----

**Question 16.** A data engineer must write to the Customer table in Lakehouse1 but must NOT read the Employee table in the same lakehouse (which contains PII). Which three steps best satisfy this? (Choose three.)

A. Share Lakehouse1 with the engineer
B. Assign Contributor on Workspace1
C. Assign Viewer on Workspace1
D. Migrate the Employee table to a separate lakehouse that the engineer cannot access
E. Delete the Employee table
F. Grant “Read all Apache Spark” on Lakehouse1

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: A, B, D**

Fabric lakehouses do not support table-level permissions directly when accessing via Spark/Files — access is all-or-nothing at the lakehouse level. To meet the requirement:

1. Move the PII Employee table out of Lakehouse1 into a separate, access-controlled lakehouse (D).
1. Share the now-PII-free Lakehouse1 with the engineer (A).
1. Give them Contributor on Workspace1 so they can write (B).

Viewer would block writes. You cannot simply delete the Employee table.

</details>

-----

**Question 17.** You want analysts to see a warehouse table, but the CreditCard column must be restricted so only HR can view its values. Other users should see the table but NOT see that column at all. What should you implement?

A. Row-level security
B. Column-level security via GRANT/DENY on the column
C. Dynamic data masking on the column
D. Sensitivity labels

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

**Column-level security** uses `GRANT SELECT ON table(column)` to control who can read each column. Users without permission get an error if they SELECT that column. **Dynamic data masking** still lets users query the column — they just see masked values; the column still appears. RLS filters rows, not columns. Sensitivity labels tag content but don’t block access.

</details>

-----

**Question 18.** You classify a semantic model as “Confidential — Financial.” Downstream reports built on the model should automatically inherit the classification without manual tagging. Which feature provides this?

A. Endorsement
B. Sensitivity label
C. Domain assignment
D. Row-level security

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

**Sensitivity labels** (from Microsoft Purview Information Protection) propagate from upstream items to downstream items that consume them. Endorsement is a trust signal but does not propagate automatically. Domains group workspaces; they do not classify content.

</details>

-----

### 1.4 Orchestrate processes

-----

**Question 19.** You need a data pipeline to start each time a new file is written to an Azure Blob Storage container. Which approach is most appropriate?

A. Schedule the pipeline every minute and scan for new files
B. Configure a storage event trigger on the pipeline
C. Call the pipeline from a stored procedure that polls the container
D. Create an eventstream that converts blob events to a pipeline trigger

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

Fabric Data Factory pipelines support **storage event triggers** that listen to Azure Storage blob-created / blob-deleted events directly. This is the purpose-built, lowest-latency approach. Polling wastes capacity; eventstreams can process blob events but are a heavier solution for simply triggering a pipeline. Activator is a related option; a storage event trigger on the pipeline is the most direct.

</details>

-----

**Question 20.** You must populate bronze, then silver, then gold layers sequentially. Any failure at any layer should email the data engineering team, and you want to minimize operational overhead. Which pattern best fits?

A. Three independent pipelines scheduled 30 minutes apart
B. A parent pipeline that invokes child pipelines with On success / On failure connections to an email activity
C. A single notebook that runs all three layers end-to-end
D. A scheduled notebook that calls pipelines via REST API

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

Parent/child pipeline orchestration is the standard pattern: each child pipeline handles one layer, the parent sequences them with **On success** links (and **On failure** links to an Office 365 Outlook email activity or Activator). Separate schedules cannot guarantee ordering. A monolithic notebook couples concerns and makes retries/alerting harder.

</details>

-----

**Question 21.** A team needs to ingest raw JSON files into the bronze layer of a lakehouse with no transformation. The solution must minimize development effort. Which tool is most appropriate?

A. Dataflow Gen2
B. A notebook with PySpark
C. A pipeline with a Copy data activity
D. A KQL queryset

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: C**

For “raw ingestion with no transformation,” the **Copy data activity** in a pipeline is the lowest-effort choice — it has native connectors and writes directly to Delta with minimal configuration. Dataflow Gen2 adds Power Query overhead. Notebooks add code to maintain. KQL is for Real-Time Intelligence, not general JSON ingestion.

</details>

-----

**Question 22.** Three data engineering teams need tools that match their skills:

- Team1: strong Power BI / Power Query skills, wants low-code transformations.
- Team2: Python experts, want to transform with PySpark.
- Team3: ex-Azure Data Factory users, want to move data with minimal effort.

Which tools fit each team?

A. Dataflow Gen2, Notebook, Data pipeline
B. Notebook, Dataflow Gen2, Data pipeline
C. Data pipeline, Notebook, Dataflow Gen2
D. Dataflow Gen2, Data pipeline, Notebook

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: A**

- Team1 → **Dataflow Gen2** (Power Query M, low-code).
- Team2 → **Notebook** (PySpark).
- Team3 → **Data pipeline** (familiar ADF-style drag-and-drop, minimal code for data movement).

</details>

-----

## Domain 2 — Ingest and transform data (30–35%)

### 2.1 Design and implement loading patterns

-----

**Question 23.** A fact table is updated daily. Data older than one month never changes. Source systems can return any rows changed within the past month. Which incremental refresh configuration minimizes data moved while preserving a full history?

A. Full refresh daily
B. Store rows from the past 1 Year, Refresh rows from the past 1 Month
C. Store rows from the past 1 Month, Refresh rows from the past 1 Year
D. Append-only with no refresh window

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

The **store** window defines how much history to retain. The **refresh** window defines how far back rows are re-queried during each run. Because data older than one month doesn’t change, there’s no reason to re-fetch anything older than 1 month — but you still want to keep 1 year (or however long) of history available.

</details>

-----

**Question 24.** You are designing a Type 2 slowly changing dimension for Customer. Which column set is essential for correct Type 2 history tracking?

A. CustomerID (natural key), CustomerName, CurrentFlag
B. CustomerKey (surrogate), CustomerID, tracked attributes, ValidFrom, ValidTo, IsCurrent
C. CustomerID, LastUpdatedDate
D. CustomerID, attributes, VersionNumber

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

Type 2 keeps multiple versions of the same natural key, so you need a **surrogate key** (otherwise the natural key isn’t unique), plus validity dates (ValidFrom/ValidTo) and usually an IsCurrent flag for efficient lookups. Facts then reference the surrogate that was current at the time of the event.

</details>

-----

**Question 25.** A 20-million-row Type 2 dimension must be compared to source data daily to detect changes. You want to minimize compute and data transfer. How should you identify attribute changes?

A. Direct attribute-by-attribute comparison on the source side
B. Direct attribute-by-attribute comparison on the target side
C. Compute a hash of the tracked attributes on the source, compare to the stored hash in the target
D. Full outer join with NULL checks on every attribute

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: C**

Computing a hash (e.g., SHA2_256 of concatenated attributes) on the source produces a single small value per row, so only the hash needs to travel to the target for comparison. This minimizes data transferred and avoids per-column CPU work. (Note: the source PDF claimed MD5 on a 500M-row fact table was a performance problem — that’s a surrogate-key sizing issue, different from this hash-for-change-detection scenario.)

</details>

-----

**Question 26.** Your eventstream aggregates sensor readings into 5-minute tumbling windows. Due to network latency, up to 30 seconds of events arrive after their event-time window would close. You want to include late events without stalling the pipeline. What should you configure?

A. Disable windowing
B. Increase the window size to 10 minutes
C. Add a watermark with a 30-second allowed lateness
D. Switch to a session window

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: C**

**Watermarks with allowed lateness** are the canonical streaming mechanism for handling late-arriving data. The watermark advances as time progresses and the “allowed lateness” keeps the window open for a grace period. Making the window larger changes the aggregation semantics; session windows don’t apply to fixed intervals.

</details>

-----

**Question 27.** Incoming rows contain duplicate CustomerID values with the same ID appearing multiple times with slightly different attributes. You need to keep only the most recent record per CustomerID (by LastModified) using PySpark. Which approach is most deterministic?

A. df.dropDuplicates([“CustomerID”])
B. df.distinct()
C. Window.partitionBy(“CustomerID”).orderBy(col(“LastModified”).desc()), then filter row_number() == 1
D. groupBy(“CustomerID”).agg(max(“LastModified”))

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: C**

`dropDuplicates(["CustomerID"])` does keep one row per key but picks which row non-deterministically. `distinct()` requires all columns to match. The aggregate in (D) gives you the max date but not the full row of attributes at that date. Using a **Window with row_number()** gives you deterministic “latest per key, full row” semantics.

</details>

-----

### 2.2 Ingest and transform batch data

-----

**Question 28.** You store semi-structured data where Spark writes it and T-SQL, KQL, and Spark all read it. Which store is the best fit?

A. Warehouse
B. Lakehouse
C. Eventhouse
D. Datamart

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

A **lakehouse** supports writes via Spark and reads via Spark (notebooks), T-SQL (SQL analytics endpoint), and KQL (via OneLake shortcuts into an eventhouse). A warehouse is T-SQL-first and less suited to semi-structured writes from Spark. An eventhouse is KQL-centric, not designed as the primary read target for T-SQL.

</details>

-----

**Question 29.** You need to copy data from an on-premises SQL Server (accessed via an on-premises data gateway) into a Fabric warehouse. Which item should you use?

A. Dataflow Gen1
B. A data pipeline with a Copy data activity
C. A KQL queryset
D. An Apache Spark job definition

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

**Pipelines** with the Copy data activity support on-premises data gateways and have a native SQL Server connector. Dataflow Gen2 also supports the gateway, but Dataflow Gen1 is legacy Power BI and is not a Fabric item. Notebooks can go through a VNet, but the gateway scenario is the pipeline’s domain.

</details>

-----

**Question 30.** You want to expose data stored in Amazon S3 inside a Fabric lakehouse without making a copy of the files. Which feature should you use?

A. Mirroring
B. A shortcut
C. A Dataflow Gen2 with the S3 connector
D. An external table with an OPENROWSET

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

**Shortcuts** virtualize access to external locations (S3, GCS, ADLS, Dataverse, etc.) without copying data. Mirroring **does** copy data (into OneLake as Delta). Dataflow Gen2 would physically materialize the data. External tables are a warehouse concept, not a lakehouse shortcut.

</details>

-----

**Question 31.** You want a near-real-time replica of an Azure SQL operational database inside OneLake that continually stays in sync for analytics, without writing any pipeline. What should you use?

A. Scheduled Copy data activity every minute
B. Mirroring for Azure SQL Database
C. A shortcut
D. CDC into an eventstream

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

**Mirroring** is the purpose-built, low-code feature that continuously replicates supported databases (Azure SQL DB, Azure SQL MI, Snowflake, Cosmos DB, and others over time) into OneLake as Delta. A shortcut cannot reach into a relational DB; pipelines require orchestration; eventstreams + CDC work but involve more moving parts.

</details>

-----

**Question 32.** A lakehouse has three shortcuts into external storage, with the shortcut cache enabled. The files were last accessed at the following times. Given a 24-hour cache window, which will be served from cache?

- ProductFile: 12 hours ago
- StoreFile: 4 hours ago
- TripsFile: 48 hours ago

A. All three
B. ProductFile and StoreFile only
C. TripsFile only
D. None

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

The shortcut cache window is 24 hours from last access. TripsFile is outside that window and is re-fetched. The other two are served from cache. (Also remember: files larger than 1 GB are not cached regardless of age.)

</details>

-----

**Question 33.** You must populate the bronze layer from a SaaS app that exposes 7 entities, each via its own REST endpoint. Which two pipeline activities are needed? (Choose two.)

A. ForEach
B. Copy data
C. Stored procedure
D. WebHook
E. If Condition

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: A, B**

- **ForEach** iterates over the list of entity endpoints.
- **Copy data** (nested inside ForEach) reads each endpoint and writes to the lakehouse.

Stored procedures don’t apply — the target is a lakehouse, and the sources are REST APIs. WebHook is for calling external services and waiting on a callback; If Condition is for branching, not iteration.

</details>

-----

**Question 34.** Your team needs to transform data using PySpark DataFrames with complex windowing functions and the MLlib library. Which tool is most appropriate?

A. Dataflow Gen2
B. A notebook
C. A T-SQL stored procedure
D. A KQL queryset

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

**Notebooks** give you the full PySpark API including Spark SQL windowing and MLlib. Dataflow Gen2 uses Power Query M — no PySpark or ML. T-SQL and KQL support windowing but are different languages and can’t use MLlib.

</details>

-----

**Question 35.** A Copy data activity moves large volumes from Snowflake to a Fabric warehouse. Runs fail due to memory pressure on the path between clouds. What should you enable?

A. Degree of copy parallelism
B. Fault tolerance
C. Staging (interim storage)
D. Logging

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: C**

**Enable staging** lands data temporarily in an intermediate storage location, which is the recommended approach for cross-cloud transfers and large volumes. It avoids holding the entire dataset in the activity’s runtime memory. Parallelism helps throughput but doesn’t fix memory; fault tolerance skips bad rows but doesn’t help with scale.

</details>

-----

**Question 36.** A Copy data activity intermittently fails on a REST source with transient errors. You want the pipeline to recover automatically, with minimal development effort. What should you configure?

A. Fault tolerance
B. Retry settings on the Copy activity
C. A Webhook to notify on error
D. A second Copy activity on the failure branch

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

Built-in **activity-level retry** (count and retry interval) handles transient errors declaratively. Fault tolerance is about skipping incompatible rows, not retrying the whole activity. A failure-branch copy duplicates logic and is higher effort.

</details>

-----

**Question 37.** You denormalize an Employee and Contract table and need to include only contract types with more than 2 employees. Which SQL clause combination is correct?

A. INNER JOIN + WHERE + COUNT
B. LEFT JOIN + GROUP BY + HAVING COUNT(*) > 2
C. INNER JOIN + GROUP BY + WHERE
D. CROSS JOIN + GROUP BY

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

- **LEFT JOIN** preserves all employees even if they have no contract (needed for “all rows from Employee preserved”).
- **GROUP BY** contract type followed by **HAVING COUNT(*) > 2** filters after aggregation.

WHERE cannot reference aggregates; it filters rows before grouping.

</details>

-----

**Question 38.** A flat transaction table has columns: TransactionID, Date, ProductID, ProductName, ProductColor, SalesAmount. You split it into FactSales and DimProduct and will track changes in DimProduct. Which three columns belong in DimProduct?

A. TransactionID, ProductID, ProductName
B. ProductID, ProductName, ProductColor
C. Date, ProductID, SalesAmount
D. SalesAmount, ProductColor, ProductID

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

Dimension tables hold descriptive attributes: ProductID (natural key), ProductName, ProductColor. TransactionID, Date, and SalesAmount are transactional/measurement data — they live in FactSales, which references DimProduct via the product key.

</details>

-----

### 2.3 Ingest and transform streaming data

-----

**Question 39.** You must ingest events from an Azure Event Hub, keep only events where Neighborhood = “Chelsea”, and land them in a lakehouse. Minimize development effort. What should you use?

A. A KQL queryset
B. An eventstream with a filter operation
C. A streaming dataset in Power BI
D. Apache Spark Structured Streaming in a notebook

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

**Eventstreams** have native Event Hub input, a no-code Filter operator, and a lakehouse destination. This is the lowest-effort solution. Spark Structured Streaming is more powerful but requires code. Streaming datasets don’t write to lakehouses directly.

</details>

-----

**Question 40.** You stream car speeds from motorway sensors and need the average speed grouped by non-overlapping, contiguous 1-minute intervals where every event belongs to exactly one window. Which windowing function?

A. Sliding
B. Hopping
C. Tumbling
D. Session

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: C**

**Tumbling** windows are fixed-size, non-overlapping, and every event maps to exactly one window. Hopping windows overlap (a 5-minute window hopping every 1 minute produces overlapping windows). Sliding windows emit output when events enter/leave a window. Session windows group events separated by inactivity gaps.

</details>

-----

**Question 41.** You need a rolling 5-minute average computed every 1 minute (so each minute you see the average of the last 5 minutes). Which window type?

A. Tumbling 1 minute
B. Hopping 5 minutes with a 1-minute hop
C. Session window
D. Snapshot window

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

A **hopping window** has both a size (5 min) and a hop interval (1 min). Successive windows overlap, which is exactly what “rolling average, updated every minute” means. Tumbling windows don’t overlap, so you’d only see a new average every 5 minutes.

</details>

-----

**Question 42.** An eventstream is configured to consume row-level changes from an Azure SQL database, but no events appear. What must you enable on the source database?

A. Read-only replica
B. Extended Events
C. Change Data Capture (CDC)
D. Transparent Data Encryption

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: C**

Azure SQL → eventstream sourcing relies on **Change Data Capture (CDC)** to expose row-level inserts/updates/deletes. Without CDC, there are no change events to propagate. Extended Events track query behavior, not data changes.

</details>

-----

**Question 43.** A KQL queryset joins two tables with millions of rows each and runs slowly. Which change yields the biggest improvement?

A. Change join kind to kind=outer
B. Replace project with extend
C. Move a filter to before the join so fewer rows participate
D. Wrap results with make_list()

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: C**

Filter pushdown — reducing the rows that enter the join — is the highest-impact optimization. Outer joins produce MORE rows (slower). `extend` keeps all columns (more data), while `project` is leaner. `make_list()` aggregates into arrays and adds work.

</details>

-----

**Question 44.** You need sub-second queryability on live sensor events and ad-hoc anomaly detection using KQL. Which item is most appropriate?

A. A lakehouse with Spark Structured Streaming
B. An eventhouse with a KQL database
C. A warehouse with a scheduled pipeline
D. A Dataflow Gen2 streaming dataset

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

**Eventhouses** (Real-Time Intelligence) are engineered for high-ingestion, low-latency analytical queries using KQL — with built-in time-series and anomaly-detection functions (series_decompose_anomalies, etc.). Lakehouses can handle streaming ingestion but have higher query latency.

</details>

-----

**Question 45.** You need to enrich an eventstream with the rolling standard deviation of temperature. Which transform operator provides this?

A. Expand
B. Group by
C. Union
D. Aggregate (with a window)

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: D**

The **Aggregate** operator offers statistical functions (Average, Sum, Max, Min, **Standard Deviation**, and more) over a specified window. Group by alone doesn’t apply statistical aggregations; Expand flattens arrays; Union combines streams.

</details>

-----

## Domain 3 — Monitor and optimize an analytics solution (30–35%)

### 3.1 Monitor Fabric items

-----

**Question 46.** You need a single place to track recent executions of pipelines, notebooks, dataflows, and semantic model refreshes across your workspaces. Which tool is the right starting point?

A. Fabric admin portal
B. Monitoring hub
C. Real-Time hub
D. Microsoft Fabric Capacity Metrics app

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

**Monitoring hub** is the central, cross-item execution history view for a user. The admin portal is tenant-level governance. Real-Time hub catalogs streaming sources. The Capacity Metrics app reports on capacity consumption, not individual item runs.

</details>

-----

**Question 47.** In Monitoring hub, which column identifies the workspace where a specific run executed?

A. Start time
B. Capacity
C. Activity name
D. Location

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: D**

The **Location** column surfaces the containing workspace for each run, which is what you need when multiple workspaces share a Monitoring hub view.

</details>

-----

**Question 48.** You need to monitor a semantic model’s refresh history and visualize it as a chart you can share. Which approach works?

A. Screenshot the refresh history dialog in the model settings
B. Use a notebook with the semantic link (sempy) library to query refresh history and plot it
C. Query sys.dm_exec_requests
D. Use a Dataflow Gen2

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

**Semantic link** (`sempy`) lets notebooks read Power BI/semantic model metadata — including refresh history — as DataFrames, which you can then chart with matplotlib, seaborn, or Plotly. The built-in refresh history dialog shows runs but does not produce a shareable chart.

</details>

-----

**Question 49.** You want to be alerted when a pipeline fails more than twice in one hour. What should you use?

A. An email activity at the end of the pipeline
B. Activator (Data Activator) with a rule on pipeline run failure events
C. A Monitoring hub filter
D. A notebook that polls Monitoring hub

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

**Activator** (Data Activator / Reflex) is Fabric’s native, rule-based alerting engine. It subscribes to streams of events (including Fabric item events like pipeline completions) and triggers actions (email, Teams, Power Automate) when your rule fires. In-pipeline email activities handle a single run’s failure; they cannot count failures across runs.

</details>

-----

**Question 50.** You need to find long-running queries against a warehouse that have also failed more than once, as part of debugging slow reports. Which view should you query?

A. sys.dm_exec_requests
B. queryinsights.long_running_queries
C. queryinsights.frequently_run_queries
D. sys.dm_exec_sessions

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

The **queryinsights.long_running_queries** view includes runtime, last_run_command, and a number_of_failed_runs column, making it the right source for “long-running AND repeatedly failing.” `sys.dm_exec_requests` shows currently executing work only. `frequently_run_queries` is for hot-spot analysis.

</details>

-----

### 3.2 Identify and resolve errors

-----

**Question 51.** A pipeline’s Copy data activity uses a dynamic SQL query source and keeps failing. You need to see the exact SQL that was built at the failure time. Where do you look?

A. Monitoring hub → failed run → output JSON
B. Monitoring hub → failed run → input JSON
C. Pipeline variables tab
D. Real-Time hub → Fabric events

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

The **input JSON** of an activity run shows the resolved configuration that was passed to the activity — including any dynamically built query. Output JSON contains the result (or error) of the activity, not the inputs.

</details>

-----

**Question 52.** A Notebook activity inside a pipeline fails intermittently with transient network errors. You want the pipeline to retry the activity up to twice automatically. What should you set?

A. The notebook’s internal retry count
B. The Notebook activity’s Retry setting to 2
C. Add a Wait activity after the Notebook
D. Enable fault tolerance on the pipeline

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

**Activity-level retries** are configured on the activity in the pipeline (Retry count and Retry interval in seconds). This is the declarative way to handle transient failures. There’s no “notebook’s internal retry count” surfaced to the pipeline.

</details>

-----

**Question 53.** Sales users report stale data in the morning. A Dataflow Gen1 is transferring excessive data every refresh. Older-than-1-month data never changes. What should you configure to minimize data transferred?

A. Scheduled refresh only
B. Split into two dataflows
C. Incremental refresh — Store rows from the past 1 Year, Refresh rows from the past 1 Month
D. Switch to Dataflow Gen2 with no other change

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: C**

Incremental refresh is the targeted fix: store the history you need (1 Year), only re-fetch the window where rows can still change (1 Month). Changing dataflow generation alone doesn’t reduce volume.

</details>

-----

**Question 54.** A Delta table contains 2,000 Parquet files of 1 MB each, and queries are slow because of too many small files. You need to consolidate files and reclaim storage from old versions. Which commands should you run?

A. OPTIMIZE only
B. VACUUM only
C. OPTIMIZE, then VACUUM
D. CLONE the table

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: C**

**OPTIMIZE** compacts small files into larger ones (typically ~1 GB targets) and can apply Z-order and V-Order. **VACUUM** then removes older, unreferenced file versions once the retention threshold has elapsed. Doing both addresses performance and storage. OPTIMIZE alone leaves the old small files on disk until VACUUM cleans them.

</details>

-----

**Question 55.** You only want to consolidate the current Parquet files of a Delta table into larger ones. You do NOT want to delete previous file versions yet. Which command?

A. OPTIMIZE
B. VACUUM
C. REORG
D. REFRESH TABLE

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: A**

**OPTIMIZE** only rewrites/compacts files; it does not delete old versions. VACUUM is the deletion command (subject to retention).

</details>

-----

**Question 56.** You want to permanently remove Delta files older than 7 days that are no longer referenced by the transaction log, to reclaim storage. Which command?

A. OPTIMIZE
B. VACUUM with retention set appropriately
C. DELETE
D. DROP TABLE

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

**VACUUM** is the file-level cleanup operation. By default it honors a 7-day retention minimum (for safety with concurrent readers); you can lower it if you truly accept the risk. DELETE only marks rows as deleted in the log — old files remain until VACUUM cleans them.

</details>

-----

**Question 57.** An eventstream transformation has been failing. You need the error details, the occurrence time, and the total number of errors. Where should you look first?

A. Monitoring hub only
B. The eventstream item’s runtime / data insights view, where errors are surfaced per processor
C. Fabric admin activity log
D. Real-Time hub subscriptions tab

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

Each eventstream has its own runtime view showing per-processor error counts and details with timestamps. Monitoring hub aggregates runs but is less granular for streaming processor errors.

</details>

-----

**Question 58.** A lakehouse shortcut to ADLS Gen2 works at first, then intermittently returns authorization errors. What is the most likely cause?

A. The Delta log became corrupted
B. The credential (token / managed identity) used to create the shortcut was revoked or rotated
C. The workspace cache is disabled
D. V-Order was enabled on the source

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

Shortcuts use the credential established when the shortcut was created. If that identity is rotated, deleted, or has its ADLS RBAC/ACL changed, you see authorization errors. Delta log corruption would look different; caching doesn’t cause auth errors.

</details>

-----

### 3.3 Optimize performance

-----

**Question 59.** DW1 is write-intensive: staging tables are loaded, read once, dropped, and recreated. You want to minimize load time. What should you do?

A. Enable V-Order
B. Disable V-Order
C. Add indexes to every staging table
D. Increase capacity

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

**V-Order** adds write-time overhead to produce a read-optimized Parquet layout (it especially helps Power BI Direct Lake). For write-heavy, short-lived staging tables, the read-time benefit never materializes, so disabling V-Order saves the write cost.

</details>

-----

**Question 60.** A 500-million-row fact table uses MD5 hash surrogate keys (128-bit). Performance has degraded over time and some visuals error out. You must minimize operational cost. What should you do?

A. Switch hashing to SHA-256
B. Change surrogate keys to int/bigint
C. Increase capacity
D. Create views over the fact table

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

A 128-bit hash as a join key is much heavier than a 4-byte int or 8-byte bigint — storage, scan, join cost, and memory footprint all scale with key width. SHA-256 is bigger, not smaller. Integer surrogates are the standard data-warehouse pattern. Increasing capacity trades money for a problem you can fix by design.

</details>

-----

**Question 61.** A Direct Lake semantic model on a large fact table is falling back to DirectQuery, degrading Power BI performance. What is a commonly documented cause to investigate first?

A. V-Order was enabled on the source table
B. The table has exceeded the row count / column count limits for the capacity SKU, or uses unsupported features
C. The workspace is in Pro mode
D. The fact table uses an integer surrogate key

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

Direct Lake has documented per-SKU limits (rows, columns per table, table count per model) and restrictions on certain features (calculated columns, some relationship types). When a model exceeds a limit, it silently falls back to DirectQuery. Confirm limits against the current Microsoft Learn documentation for Direct Lake — these numbers change over time.

</details>

-----

**Question 62.** Bronze and silver Spark jobs run in the same workspace and interfere with each other’s resources. You want each layer to run in its own isolated resource pool. What should you configure?

A. Disable high concurrency
B. Create a custom Spark pool for each layer
C. Increase executors in the starter pool
D. Use a single environment for both

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

**Custom Spark pools** give you dedicated, isolated compute per workload, preventing bronze and silver from competing. High concurrency would share a session — the opposite of isolation.

</details>

-----

**Question 63.** Business users running ad-hoc queries against a warehouse see inconsistent performance. You suspect stale optimizer statistics are leading to poor plans. What should you do?

A. Enable V-Order
B. Create / update statistics
C. Introduce enforced primary keys
D. Run VACUUM

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

Up-to-date statistics are critical for cardinality estimation. Fabric warehouse supports manual `CREATE STATISTICS` and auto-created statistics; refreshing them helps the optimizer pick better plans. Primary keys in Fabric warehouse are not enforced and don’t drive plans. VACUUM is a Delta file operation and wouldn’t help warehouse query plans.

</details>

-----

**Question 64.** You need an orchestrator that runs every 15 minutes and executes multiple items sequentially. Which item type fits?

A. Warehouse
B. Data pipeline
C. Dataflow Gen2
D. Notebook

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: B**

**Pipelines** are the orchestration item: they have schedules, triggers, sequential dependencies, parameters, and activity-level retry. Dataflows and notebooks are processing items, not orchestrators.

</details>

-----

**Question 65.** You have a pipeline parameter named `param1` of type `int`. Which expression returns `param1` as an int value (not as a string)?

A. `@pipeline().parameters.param1`
B. `@{pipeline().parameters.param1}`
C. `@pipeline.parameters['param1']`
D. `@concat(pipeline().parameters.param1)`

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: A**

In the pipeline expression language, `@expression` returns the value in its native type. Wrapping with `@{ }` forces string interpolation, which converts the int to a string — not what you want here. `@concat(...)` also returns a string.

</details>

-----

**Question 66.** A notebook must run daily at 07:00 UTC, retry up to 2 times on failure, and trigger a semantic model refresh on success. Which three configurations are correct? (Choose three.)

A. Set the Notebook activity’s Retry to 2
B. Link the Refresh activity to the Notebook activity using an “On success” dependency
C. Link the Refresh activity to the Notebook activity using an “On completion” dependency
D. Schedule the pipeline at 07:00 UTC
E. Schedule the notebook directly at 07:00 UTC
F. Set the Refresh activity’s Retry to 2

<details>
<summary><b>Answer &amp; explanation</b></summary>

**Answer: A, B, D**

- Retry on the Notebook activity handles transient failures (A).
- **On success** ensures the refresh only runs when the notebook succeeded (B); On completion would also run it after a failure (wrong).
- Schedule **the pipeline** so the single scheduled entry point orchestrates both steps (D). Scheduling the notebook separately would cause it to run twice (once directly, once via the pipeline).

</details>

-----

## Appendix — Weak Areas Tracker

Use this table to log questions you miss. Come back to them in a day or two and re-attempt. Track recurring themes (e.g., “I keep missing Delta file maintenance questions”) — that’s where to focus next.

|Date|Question #|Topic|What I got wrong|Root-cause concept|
|----|----------|-----|----------------|------------------|
|    |          |     |                |                  |
|    |          |     |                |                  |
|    |          |     |                |                  |
|    |          |     |                |                  |
|    |          |     |                |                  |
|    |          |     |                |                  |
|    |          |     |                |                  |

-----

## Appendix — Study paths for each domain

If you keep missing questions in a domain, the corresponding Microsoft Learn learning path is the best next step:

**Domain 1 — Implement and manage an analytics solution**

- Learning path: *Administer a Microsoft Fabric environment*
- Learning path: *Implement CI/CD in Microsoft Fabric*
- Learning path: *Secure a Microsoft Fabric data warehouse*

**Domain 2 — Ingest and transform data**

- Learning path: *Ingest data with Microsoft Fabric*
- Learning path: *Implement a lakehouse with Microsoft Fabric*
- Learning path: *Implement Real-Time Intelligence with Microsoft Fabric*

**Domain 3 — Monitor and optimize an analytics solution**

- Learning path: *Monitor activities in Microsoft Fabric*
- Learning path: *Optimize workloads in Microsoft Fabric*

All paths include hands-on exercises in a free Fabric trial tenant. The hands-on reps are what make scenario questions click.

-----

## Beyond this guide

When you finish this guide and want more practice:

1. **Microsoft’s free Practice Assessment** for DP-700 on the certification page — written by Microsoft, mirrors the real exam’s style.
1. **Exam sandbox** at `aka.ms/examdemo` — lets you get comfortable with question formats (case studies, hotspots, drag-drop) before you sit the real exam.
1. **Applied Skills labs** (APL-3008, APL-3010) — scenario-based hands-on credentials that build the muscle memory the exam rewards.

Good luck with your studying!
