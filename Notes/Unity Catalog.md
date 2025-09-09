## What is Unity Catalog?

Unity Catalog is Databricks’ unified data governance and metadata management solution.
It allows you to centrally manage, secure, and track all your data and AI assets—including tables, views, machine learning models, and notebooks—across multiple Databricks workspaces.

### single source of truth for data governance with fine-grained access control and auditability

## Why Do We Need Unity Catalog?

Before Unity Catalog, Databricks had limitations in managing and securing data efficiently:

- Multiple workspaces = multiple metastores: Data governance was inconsistent.

- No centralized access control: Every table or schema needed separate permissions.

- Limited auditing & lineage: Hard to track who accessed or changed data.

- Security gaps: Fine-grained controls (like row-level or column-level security) were not easily available.

- Compliance challenges: GDPR, HIPAA, SOC 2 compliance requires unified control and auditability.

### Unity Catalog solves these by providing:

- Centralized Metastore: One place for all catalogs, schemas, and tables.

- Fine-Grained Access Control: Table-level, row-level, column-level security.

- Cross-Workspace Governance: Same metastore can be used by multiple workspaces.

- Audit Logging: Track who accessed or modified data.

- Data Lineage: See how data flows, transformations, and usage.

- Model Governance: Manage machine learning models like data.


### Disadvantages/Limitations Overcome by Unity Catalog

| Problem Before                                                  | How Unity Catalog Solves It                                         |
| --------------------------------------------------------------- | ------------------------------------------------------------------- |
| Multiple metastores across workspaces → inconsistent governance | Centralized metastore for all workspaces , just need to attach it to that metastore                          |
| Only workspace-level access control                             | Fine-grained access control at table, row, column, and schema level |
| Hard to track who accessed/modified data                        | Full audit logging and lineage tracking                             |
| Difficulty with external tables                                 | Simplified management of external tables with proper access control |
| Compliance & regulatory issues                                  | Unified governance helps with GDPR, SOC 2, HIPAA compliance         |


In short: Unity Catalog unifies data governance, making it secure, auditable, and easier to manage at scale.

### Important Points to remember
- We need can create one unity metastore in a particular region
- If we have one Databricks workspace attached to the unoity metastore then another databricks can access all the catalog.


### Unity Catalog Object Hierarchy
Unity Catalog organizes data objects in a 3-level namespace:

    <catalog>.<schema>.<table/view>

#### a. Metastore

The top-level container for all Unity Catalog objects.
Each region of a cloud account has one metastore.
A metastore can be attached to multiple Databricks workspaces.

#### b. Catalog

A container of schemas.
Example: retail_catalog, finance_catalog.

Useful for organizing data by business domain.
It provides logical grouping + centralized security + cross-workspace governance.

#### c. Schema (Database)

A container of tables and views inside a catalog.
Example: retail_catalog.sales, finance_catalog.reports.

#### d. Tables & Views

Tables: Store structured data (managed or external).
Views: Logical representation of data.

Types of Tables:
- Managed Table: Unity Catalog manages storage.
- External Table: Points to data in external storage (e.g., S3, ADLS).
- Delta Table: Optimized format for transactions.

#### e. Other Objects

- Functions (UDFs) → User-defined functions stored at schema level.
- Files & Volumes → For unstructured/semi-structured data.
- Models → ML models can be registered and governed in Unity Catalog.

