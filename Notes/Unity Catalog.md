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
#### Level one:
##### Catalogs:-
Catalogs are used to organize your data assets and are typically used as the top level in your data isolation scheme. Catalogs often mirror organizational units or software development lifecycle scopes. 
Non-data securable objects, such as storage credentials and external locations, are used to manage your data governance model in Unity Catalog. These also live directly under the metastore. They are described in more detail in Securable objects that Unity Catalog uses to manage access to external data sources.

#### Level two:
##### Schemas:- 
Schema (also known as databases) contain tables, views, volumes, AI models, and functions. Schemas organize data and AI assets into logical categories that are more granular than catalogs. Typically a schema represents a single use case, project, or team sandbox. See What are schemas in Azure Databricks?.

#### Level three:
##### Tables:- 
are collections of data organized by rows and columns. Tables can be either managed, with Unity Catalog managing the full lifecycle of the table, or external, with Unity Catalog managing access to the data from within Azure Databricks, but not managing access to the data in cloud storage from other clients. See Azure Databricks tables and Managed versus external tables and volumes.

In short:
- Managed = Unity Catalog fully owns storage + metadata.
- External = Unity Catalog only owns metadata, data stays outside.

##### View:- 
are saved queries against one or more tables. See What is a view?.
##### Volumes:-
represent logical volumes of data in cloud object storage. You can use volumes to store, organize, and access files in any format, including structured, semi-structured, and unstructured data. Typically they are used for non-tabular data. Volumes can be either managed, with Unity Catalog managing the full lifecycle and layout of the data in storage, or external, with Unity Catalog managing access to the data from within Azure Databricks, but not managing access to the data in cloud storage from other clients. See What are Unity Catalog volumes? and Managed versus external tables and volumes.
##### Functions:- 
are units of saved logic that return a scalar value or set of rows. See User-defined functions (UDFs) in Unity Catalog.
##### Models:- 
are AI models packaged with MLflow and registered in Unity Catalog as functions. See Manage model lifecycle in Unity Catalog.


### To create a metastore:
     Manage account in databricks--> Catalog--> Create metastore
#### - Always provide storage location to unity metastore its a good practice

### Access Connector:
With an Access Connector:
- Secure, role-based authentication (no secrets in code).
- Centralized least-privilege access control.
- Auditability (you can see which Databricks workspace identity accessed storage).

How It Works (Azure Example)
- You create an Access Connector in Azure (via Portal, CLI, or Terraform).
- This Access Connector is backed by a Managed Identity.
- You give that identity RBAC permissions (e.g., Storage Blob Data Contributor) on your storage account.
- In Unity Catalog, when you create an External Location (external table pointing to ADLS), you attach it to the Access Connector.
- Databricks uses the Access Connector’s identity to securely read/write data
  
#### What Are Databricks Storage Credentials?

Storage credentials in Unity Catalog are named objects that securely store authentication information (like keys or identities) so Databricks can access external cloud storage on your behalf (e.g. S3, ADLS Gen2, GCS).

They are part of Unity Catalog’s data governance model and allow you to securely connect external data sources without embedding keys in code.

- Create a Microsoft Entra ID Managed Identity via an Access Connector
    
- Assign Storage Blob Data Contributor role on your ADLS Gen2 storage account to that identity
    
    In Databricks:
    
            CREATE STORAGE CREDENTIAL my_adls_cred
            WITH AZURE_MANAGED_IDENTITY = '<client-id-of-access-connector>'
 - Create an external location using that credential:
    
            CREATE EXTERNAL LOCATION my_ext_loc
            URL 'abfss://container@storageaccount.dfs.core.windows.net/'
            WITH STORAGE CREDENTIAL my_adls_cred;
- Now you can create external tables from that location.

#### Unity Catalog Storage Resolution Matrix
| Catalog                    | Schema                                       | Table                       | Where Data Is Stored                   | Table Type | Who Deletes Data on DROP          |
| -------------------------- | -------------------------------------------- | --------------------------- | -------------------------------------- | ---------- | --------------------------------- |
| **Managed** (has location) | **Managed** (no location → inherits catalog) | **Managed** (no location)   | `<catalog_location>/<schema>/<table>/` | Managed    |  UC                              |
| **Managed** (has location) | **External** (has location)                  | **Managed** (no location)   | `<schema_location>/<table>/`           | Managed    |  UC                              |
| **External** (no location) | **External** (has location)                  | **Managed** (no location)   | `<schema_location>/<table>/`           | Managed    |  UC                              |
| **External** (no location) | **External** (has location)                  | **External** (has location) | `<table_location>`                     | External   |  User (UC deletes only metadata) |

*We can DROP and UNDROP data from Managed table*

**What is a View**
A view is a saved SQL query that behaves like a table when you query it.
It does not store data, only stores the SQL logic and fetches fresh results each time you query it.
It is a logical object inside a schema, just like a table or function.

        CREATE VIEW retail.sales.high_value_orders AS
        SELECT * FROM retail.sales.orders
        WHERE amount > 1000;


        Temporary view:
        CREATE VIEW retail.sales.high_value_orders AS SELECT * FROM retail.sales.orders WHERE amount > 1000;
                
