## What is Unity Catalog?

Unity Catalog is Databricks’ unified data governance and metadata management solution.
It allows you to centrally manage, secure, and track all your data and AI assets—including tables, views, machine learning models, and notebooks—across multiple Databricks workspaces.
(single source of truth for data governance with fine-grained access control and auditability)

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

Data Lineage: See how data flows, transformations, and usage.

Model Governance: Manage machine learning models like data.
