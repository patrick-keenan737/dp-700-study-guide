# DP-700 Study Guide — Implementing Data Engineering Solutions Using Microsoft Fabric

**Based on the official skills measured as of January 26, 2026.**

-----

## How to use this guide

This guide is organized to match the three official exam domains, each weighted 30–35%:

1. **Implement and manage an analytics solution**
1. **Ingest and transform data**
1. **Monitor and optimize an analytics solution**

This version has **no answers included** — use it as a pure self-test. Write your answers on paper or in a separate file, then check them against the answer-key version of this guide.

**Recommended workflow:**

1. Work through one sub-domain at a time.
1. Write your chosen answer down for each question (on paper or in a separate file).
1. When you’ve completed a domain, check your answers against the answer-key version of this guide.
1. Note the number of any question you miss in the “Weak Areas Tracker” at the end and re-attempt it a day or two later.

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

-----

**Question 2.** You have a workflow where Notebook A produces a cached DataFrame that Notebook B needs to read from the same in-memory cache. Which workspace setting makes this possible?

A. Dynamic allocation in the Spark pool
B. High concurrency for notebooks
C. Auto-scale on the starter pool
D. Native execution engine (NEE)

-----

**Question 3.** You administer 40 Fabric workspaces across Finance, Sales, and HR. You want each business area’s workspaces grouped together in the OneLake catalog, and you want a designated person in each area to manage their own grouping without granting them tenant-wide admin rights. Which feature should you use?

A. Capacities
B. Workspace tags
C. Domains and subdomains
D. Folders

-----

**Question 4.** You want a user to create subdomains inside the existing Sales domain and assign workspaces to them, but the user must NOT be able to create new top-level domains. Following least privilege, which role should you assign?

A. Fabric admin
B. Domain admin
C. Domain contributor
D. Workspace admin

-----

**Question 5.** You enable the OneLake cache for shortcuts in Workspace1 to reduce egress when reading from Amazon S3. Three shortcut-backed files were last accessed at: 4 hours ago, 12 hours ago, and 48 hours ago. Which will be served from cache?

A. All three
B. Only the one accessed 4 hours ago
C. The ones accessed 4 and 12 hours ago
D. Only the one accessed 48 hours ago

-----

### 1.2 Implement lifecycle management

-----

**Question 6.** Your company uses GitHub for source control. You need to give Fabric the minimum GitHub permissions required to commit workspace items. Following least privilege, which permission set should you grant?

A. Contents (Read and write) only
B. Actions (Read and write) and Contents (Read and write)
C. Administration (Read) and Contents (Read and write)
D. Contents (Read) and Commit statuses (Read and write)

-----

**Question 7.** You are configuring Git integration for Workspace1 with an Azure DevOps repository. Which details are required to establish the connection?

A. Personal access token, repository URL, Git folder
B. Organization, project, repository, and branch
C. Project, repository, and Git folder only
D. Azure subscription ID, project, and repository

-----

**Question 8.** A deployment pipeline promotes items from Dev → Test → Prod. The connection string for a data source must differ per stage so Prod connects to the production database instead of the test database. What should you configure?

A. Workspace assignments
B. Stage-level access controls
C. Deployment rules (parameterization)
D. Pairing rules

-----

**Question 9.** Workspace1 and Workspace2 both contain a lakehouse named “Sales” that were created independently (not via a deployment pipeline). You add Workspace1 and Workspace2 to a new deployment pipeline and run a deployment from Workspace1 → Workspace2. What happens?

A. The pipeline automatically pairs and overwrites the Workspace2 lakehouse
B. The items are treated as unpaired; Workspace2 ends up with two lakehouses both named “Sales”
C. The deployment fails until the items are manually paired
D. The Workspace2 lakehouse is deleted before deployment

-----

**Question 10.** You need to validate that a Fabric warehouse’s schema contains no invalid references (broken views, missing columns) before deploying it to a higher environment. Which option minimizes development effort?

A. Write a T-SQL script that iterates sys.objects and sys.sql_dependencies
B. Use a database project with schema compare
C. Write a Python script with pyodbc
D. Manually review each object in the portal

-----

### 1.3 Configure security and governance

-----

**Question 11.** Data analysts must read gold-layer tables in a lakehouse using T-SQL only. They must NOT have Spark access and must NOT see the Files section. What is the correct configuration?

A. Give them the Viewer role on the workspace
B. Share the lakehouse and grant “Read all SQL analytics endpoint data”
C. Share the lakehouse and grant “Read all Apache Spark”
D. Grant them Contributor and revoke Spark manually

-----

**Question 12.** User3 must view every item in Workspace1 and be able to update tables in a warehouse in that workspace. Object-level permissions on the warehouse are already granted. Following least privilege, which workspace role should User3 have?

A. Admin
B. Member
C. Contributor
D. Viewer

-----

**Question 13.** You need to implement row-level security in a Fabric warehouse so each salesperson sees only their rows. Which object type must you create in addition to a security policy?

A. A stored procedure with dynamic SQL
B. A view with a WHERE clause
C. An inline table-valued function
D. A CHECK constraint

-----

**Question 14.** You need to mask a CreditCard column so analysts see only the first 2 digits and last 4 digits. Which dynamic data masking function should you use?

A. default()
B. email()
C. partial(2, “XXXXXX”, 4)
D. random()

-----

**Question 15.** You want to mark a semantic model as officially reviewed and trusted by the data governance team, requiring formal approval before the label can be applied. Which endorsement should you use?

A. Promoted
B. Certified
C. Master data
D. Featured

-----

**Question 16.** A data engineer must write to the Customer table in Lakehouse1 but must NOT read the Employee table in the same lakehouse (which contains PII). Which three steps best satisfy this? (Choose three.)

A. Share Lakehouse1 with the engineer
B. Assign Contributor on Workspace1
C. Assign Viewer on Workspace1
D. Migrate the Employee table to a separate lakehouse that the engineer cannot access
E. Delete the Employee table
F. Grant “Read all Apache Spark” on Lakehouse1

-----

**Question 17.** You want analysts to see a warehouse table, but the CreditCard column must be restricted so only HR can view its values. Other users should see the table but NOT see that column at all. What should you implement?

A. Row-level security
B. Column-level security via GRANT/DENY on the column
C. Dynamic data masking on the column
D. Sensitivity labels

-----

**Question 18.** You classify a semantic model as “Confidential — Financial.” Downstream reports built on the model should automatically inherit the classification without manual tagging. Which feature provides this?

A. Endorsement
B. Sensitivity label
C. Domain assignment
D. Row-level security

-----

### 1.4 Orchestrate processes

-----

**Question 19.** You need a data pipeline to start each time a new file is written to an Azure Blob Storage container. Which approach is most appropriate?

A. Schedule the pipeline every minute and scan for new files
B. Configure a storage event trigger on the pipeline
C. Call the pipeline from a stored procedure that polls the container
D. Create an eventstream that converts blob events to a pipeline trigger

-----

**Question 20.** You must populate bronze, then silver, then gold layers sequentially. Any failure at any layer should email the data engineering team, and you want to minimize operational overhead. Which pattern best fits?

A. Three independent pipelines scheduled 30 minutes apart
B. A parent pipeline that invokes child pipelines with On success / On failure connections to an email activity
C. A single notebook that runs all three layers end-to-end
D. A scheduled notebook that calls pipelines via REST API

-----

**Question 21.** A team needs to ingest raw JSON files into the bronze layer of a lakehouse with no transformation. The solution must minimize development effort. Which tool is most appropriate?

A. Dataflow Gen2
B. A notebook with PySpark
C. A pipeline with a Copy data activity
D. A KQL queryset

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

-----

## Domain 2 — Ingest and transform data (30–35%)

### 2.1 Design and implement loading patterns

-----

**Question 23.** A fact table is updated daily. Data older than one month never changes. Source systems can return any rows changed within the past month. Which incremental refresh configuration minimizes data moved while preserving a full history?

A. Full refresh daily
B. Store rows from the past 1 Year, Refresh rows from the past 1 Month
C. Store rows from the past 1 Month, Refresh rows from the past 1 Year
D. Append-only with no refresh window

-----

**Question 24.** You are designing a Type 2 slowly changing dimension for Customer. Which column set is essential for correct Type 2 history tracking?

A. CustomerID (natural key), CustomerName, CurrentFlag
B. CustomerKey (surrogate), CustomerID, tracked attributes, ValidFrom, ValidTo, IsCurrent
C. CustomerID, LastUpdatedDate
D. CustomerID, attributes, VersionNumber

-----

**Question 25.** A 20-million-row Type 2 dimension must be compared to source data daily to detect changes. You want to minimize compute and data transfer. How should you identify attribute changes?

A. Direct attribute-by-attribute comparison on the source side
B. Direct attribute-by-attribute comparison on the target side
C. Compute a hash of the tracked attributes on the source, compare to the stored hash in the target
D. Full outer join with NULL checks on every attribute

-----

**Question 26.** Your eventstream aggregates sensor readings into 5-minute tumbling windows. Due to network latency, up to 30 seconds of events arrive after their event-time window would close. You want to include late events without stalling the pipeline. What should you configure?

A. Disable windowing
B. Increase the window size to 10 minutes
C. Add a watermark with a 30-second allowed lateness
D. Switch to a session window

-----

**Question 27.** Incoming rows contain duplicate CustomerID values with the same ID appearing multiple times with slightly different attributes. You need to keep only the most recent record per CustomerID (by LastModified) using PySpark. Which approach is most deterministic?

A. df.dropDuplicates([“CustomerID”])
B. df.distinct()
C. Window.partitionBy(“CustomerID”).orderBy(col(“LastModified”).desc()), then filter row_number() == 1
D. groupBy(“CustomerID”).agg(max(“LastModified”))

-----

### 2.2 Ingest and transform batch data

-----

**Question 28.** You store semi-structured data where Spark writes it and T-SQL, KQL, and Spark all read it. Which store is the best fit?

A. Warehouse
B. Lakehouse
C. Eventhouse
D. Datamart

-----

**Question 29.** You need to copy data from an on-premises SQL Server (accessed via an on-premises data gateway) into a Fabric warehouse. Which item should you use?

A. Dataflow Gen1
B. A data pipeline with a Copy data activity
C. A KQL queryset
D. An Apache Spark job definition

-----

**Question 30.** You want to expose data stored in Amazon S3 inside a Fabric lakehouse without making a copy of the files. Which feature should you use?

A. Mirroring
B. A shortcut
C. A Dataflow Gen2 with the S3 connector
D. An external table with an OPENROWSET

-----

**Question 31.** You want a near-real-time replica of an Azure SQL operational database inside OneLake that continually stays in sync for analytics, without writing any pipeline. What should you use?

A. Scheduled Copy data activity every minute
B. Mirroring for Azure SQL Database
C. A shortcut
D. CDC into an eventstream

-----

**Question 32.** A lakehouse has three shortcuts into external storage, with the shortcut cache enabled. The files were last accessed at the following times. Given a 24-hour cache window, which will be served from cache?

- ProductFile: 12 hours ago
- StoreFile: 4 hours ago
- TripsFile: 48 hours ago

A. All three
B. ProductFile and StoreFile only
C. TripsFile only
D. None

-----

**Question 33.** You must populate the bronze layer from a SaaS app that exposes 7 entities, each via its own REST endpoint. Which two pipeline activities are needed? (Choose two.)

A. ForEach
B. Copy data
C. Stored procedure
D. WebHook
E. If Condition

-----

**Question 34.** Your team needs to transform data using PySpark DataFrames with complex windowing functions and the MLlib library. Which tool is most appropriate?

A. Dataflow Gen2
B. A notebook
C. A T-SQL stored procedure
D. A KQL queryset

-----

**Question 35.** A Copy data activity moves large volumes from Snowflake to a Fabric warehouse. Runs fail due to memory pressure on the path between clouds. What should you enable?

A. Degree of copy parallelism
B. Fault tolerance
C. Staging (interim storage)
D. Logging

-----

**Question 36.** A Copy data activity intermittently fails on a REST source with transient errors. You want the pipeline to recover automatically, with minimal development effort. What should you configure?

A. Fault tolerance
B. Retry settings on the Copy activity
C. A Webhook to notify on error
D. A second Copy activity on the failure branch

-----

**Question 37.** You denormalize an Employee and Contract table and need to include only contract types with more than 2 employees. Which SQL clause combination is correct?

A. INNER JOIN + WHERE + COUNT
B. LEFT JOIN + GROUP BY + HAVING COUNT(*) > 2
C. INNER JOIN + GROUP BY + WHERE
D. CROSS JOIN + GROUP BY

-----

**Question 38.** A flat transaction table has columns: TransactionID, Date, ProductID, ProductName, ProductColor, SalesAmount. You split it into FactSales and DimProduct and will track changes in DimProduct. Which three columns belong in DimProduct?

A. TransactionID, ProductID, ProductName
B. ProductID, ProductName, ProductColor
C. Date, ProductID, SalesAmount
D. SalesAmount, ProductColor, ProductID

-----

### 2.3 Ingest and transform streaming data

-----

**Question 39.** You must ingest events from an Azure Event Hub, keep only events where Neighborhood = “Chelsea”, and land them in a lakehouse. Minimize development effort. What should you use?

A. A KQL queryset
B. An eventstream with a filter operation
C. A streaming dataset in Power BI
D. Apache Spark Structured Streaming in a notebook

-----

**Question 40.** You stream car speeds from motorway sensors and need the average speed grouped by non-overlapping, contiguous 1-minute intervals where every event belongs to exactly one window. Which windowing function?

A. Sliding
B. Hopping
C. Tumbling
D. Session

-----

**Question 41.** You need a rolling 5-minute average computed every 1 minute (so each minute you see the average of the last 5 minutes). Which window type?

A. Tumbling 1 minute
B. Hopping 5 minutes with a 1-minute hop
C. Session window
D. Snapshot window

-----

**Question 42.** An eventstream is configured to consume row-level changes from an Azure SQL database, but no events appear. What must you enable on the source database?

A. Read-only replica
B. Extended Events
C. Change Data Capture (CDC)
D. Transparent Data Encryption

-----

**Question 43.** A KQL queryset joins two tables with millions of rows each and runs slowly. Which change yields the biggest improvement?

A. Change join kind to kind=outer
B. Replace project with extend
C. Move a filter to before the join so fewer rows participate
D. Wrap results with make_list()

-----

**Question 44.** You need sub-second queryability on live sensor events and ad-hoc anomaly detection using KQL. Which item is most appropriate?

A. A lakehouse with Spark Structured Streaming
B. An eventhouse with a KQL database
C. A warehouse with a scheduled pipeline
D. A Dataflow Gen2 streaming dataset

-----

**Question 45.** You need to enrich an eventstream with the rolling standard deviation of temperature. Which transform operator provides this?

A. Expand
B. Group by
C. Union
D. Aggregate (with a window)

-----

## Domain 3 — Monitor and optimize an analytics solution (30–35%)

### 3.1 Monitor Fabric items

-----

**Question 46.** You need a single place to track recent executions of pipelines, notebooks, dataflows, and semantic model refreshes across your workspaces. Which tool is the right starting point?

A. Fabric admin portal
B. Monitoring hub
C. Real-Time hub
D. Microsoft Fabric Capacity Metrics app

-----

**Question 47.** In Monitoring hub, which column identifies the workspace where a specific run executed?

A. Start time
B. Capacity
C. Activity name
D. Location

-----

**Question 48.** You need to monitor a semantic model’s refresh history and visualize it as a chart you can share. Which approach works?

A. Screenshot the refresh history dialog in the model settings
B. Use a notebook with the semantic link (sempy) library to query refresh history and plot it
C. Query sys.dm_exec_requests
D. Use a Dataflow Gen2

-----

**Question 49.** You want to be alerted when a pipeline fails more than twice in one hour. What should you use?

A. An email activity at the end of the pipeline
B. Activator (Data Activator) with a rule on pipeline run failure events
C. A Monitoring hub filter
D. A notebook that polls Monitoring hub

-----

**Question 50.** You need to find long-running queries against a warehouse that have also failed more than once, as part of debugging slow reports. Which view should you query?

A. sys.dm_exec_requests
B. queryinsights.long_running_queries
C. queryinsights.frequently_run_queries
D. sys.dm_exec_sessions

-----

### 3.2 Identify and resolve errors

-----

**Question 51.** A pipeline’s Copy data activity uses a dynamic SQL query source and keeps failing. You need to see the exact SQL that was built at the failure time. Where do you look?

A. Monitoring hub → failed run → output JSON
B. Monitoring hub → failed run → input JSON
C. Pipeline variables tab
D. Real-Time hub → Fabric events

-----

**Question 52.** A Notebook activity inside a pipeline fails intermittently with transient network errors. You want the pipeline to retry the activity up to twice automatically. What should you set?

A. The notebook’s internal retry count
B. The Notebook activity’s Retry setting to 2
C. Add a Wait activity after the Notebook
D. Enable fault tolerance on the pipeline

-----

**Question 53.** Sales users report stale data in the morning. A Dataflow Gen1 is transferring excessive data every refresh. Older-than-1-month data never changes. What should you configure to minimize data transferred?

A. Scheduled refresh only
B. Split into two dataflows
C. Incremental refresh — Store rows from the past 1 Year, Refresh rows from the past 1 Month
D. Switch to Dataflow Gen2 with no other change

-----

**Question 54.** A Delta table contains 2,000 Parquet files of 1 MB each, and queries are slow because of too many small files. You need to consolidate files and reclaim storage from old versions. Which commands should you run?

A. OPTIMIZE only
B. VACUUM only
C. OPTIMIZE, then VACUUM
D. CLONE the table

-----

**Question 55.** You only want to consolidate the current Parquet files of a Delta table into larger ones. You do NOT want to delete previous file versions yet. Which command?

A. OPTIMIZE
B. VACUUM
C. REORG
D. REFRESH TABLE

-----

**Question 56.** You want to permanently remove Delta files older than 7 days that are no longer referenced by the transaction log, to reclaim storage. Which command?

A. OPTIMIZE
B. VACUUM with retention set appropriately
C. DELETE
D. DROP TABLE

-----

**Question 57.** An eventstream transformation has been failing. You need the error details, the occurrence time, and the total number of errors. Where should you look first?

A. Monitoring hub only
B. The eventstream item’s runtime / data insights view, where errors are surfaced per processor
C. Fabric admin activity log
D. Real-Time hub subscriptions tab

-----

**Question 58.** A lakehouse shortcut to ADLS Gen2 works at first, then intermittently returns authorization errors. What is the most likely cause?

A. The Delta log became corrupted
B. The credential (token / managed identity) used to create the shortcut was revoked or rotated
C. The workspace cache is disabled
D. V-Order was enabled on the source

-----

### 3.3 Optimize performance

-----

**Question 59.** DW1 is write-intensive: staging tables are loaded, read once, dropped, and recreated. You want to minimize load time. What should you do?

A. Enable V-Order
B. Disable V-Order
C. Add indexes to every staging table
D. Increase capacity

-----

**Question 60.** A 500-million-row fact table uses MD5 hash surrogate keys (128-bit). Performance has degraded over time and some visuals error out. You must minimize operational cost. What should you do?

A. Switch hashing to SHA-256
B. Change surrogate keys to int/bigint
C. Increase capacity
D. Create views over the fact table

-----

**Question 61.** A Direct Lake semantic model on a large fact table is falling back to DirectQuery, degrading Power BI performance. What is a commonly documented cause to investigate first?

A. V-Order was enabled on the source table
B. The table has exceeded the row count / column count limits for the capacity SKU, or uses unsupported features
C. The workspace is in Pro mode
D. The fact table uses an integer surrogate key

-----

**Question 62.** Bronze and silver Spark jobs run in the same workspace and interfere with each other’s resources. You want each layer to run in its own isolated resource pool. What should you configure?

A. Disable high concurrency
B. Create a custom Spark pool for each layer
C. Increase executors in the starter pool
D. Use a single environment for both

-----

**Question 63.** Business users running ad-hoc queries against a warehouse see inconsistent performance. You suspect stale optimizer statistics are leading to poor plans. What should you do?

A. Enable V-Order
B. Create / update statistics
C. Introduce enforced primary keys
D. Run VACUUM

-----

**Question 64.** You need an orchestrator that runs every 15 minutes and executes multiple items sequentially. Which item type fits?

A. Warehouse
B. Data pipeline
C. Dataflow Gen2
D. Notebook

-----

**Question 65.** You have a pipeline parameter named `param1` of type `int`. Which expression returns `param1` as an int value (not as a string)?

A. `@pipeline().parameters.param1`
B. `@{pipeline().parameters.param1}`
C. `@pipeline.parameters['param1']`
D. `@concat(pipeline().parameters.param1)`

-----

**Question 66.** A notebook must run daily at 07:00 UTC, retry up to 2 times on failure, and trigger a semantic model refresh on success. Which three configurations are correct? (Choose three.)

A. Set the Notebook activity’s Retry to 2
B. Link the Refresh activity to the Notebook activity using an “On success” dependency
C. Link the Refresh activity to the Notebook activity using an “On completion” dependency
D. Schedule the pipeline at 07:00 UTC
E. Schedule the notebook directly at 07:00 UTC
F. Set the Refresh activity’s Retry to 2

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
