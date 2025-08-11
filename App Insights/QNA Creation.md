# 📄 QA README

This README describes a multi-step QNA (app insight)Creation task involving MongoDB operations, HTML dashboard creation, Python-AIRFLOW DAG conversion, S3 uploads, and metadata updates.

Follow the below task properly to create a QNA (app insight) structure.

---

## 🧩 Task 1: Insert Question into `menu_files` Collection (MongoDB)

**Server:** MongoDB MCP  
**Database:** `NYK_APP_URL`  
**Collection:** `menu_files`

### ✅ Insert Format:
```json
{
  "name": "<Question Name>",
  "type": 0,
  "questionNo": "<highest questionNo (with type: 0) + 1>"
}
```

### 🔍 Notes:
- To determine the next `questionNo`, run the following:
```json
Query: { "type": 0 }
Sort: { "questionNo": -1 }
```

---

## 🧠 Task 2: Insert Workspace Menu Item (MongoDB)

**Server:** MongoDB MCP  
**Database:** `NYK_APP_URL`  
**Collection:** `workspace_menus`

### ✅ Insert Format:

```
"folders": [
    {
      "name": "<Question Name>",
      "_id": { "$oid": "<Folder Object ID>" },
      "files": [
       {"$oid": "<menu_files Document ID from Task 1>" },
      ]
    }
  ]
```

### 🔍 Query:
- To add the name object inside `folder`, run the following:
```json
Query: { "name": "Application Insights for Vessel"}

```


### 🔍 Notes:
- Ensure all `ObjectId` fields are BSON objects (not strings).
- Use `bson.ObjectId("<hex>")` for creating ObjectIds programmatically.
- The field `files` is an array (List of ID inside files array).
- Each item in the array is an object with:
  - A string key (e.g., "0", "1", etc.) representing the file index.
  - A value of type ObjectId, representing the MongoDB document ID.
- Query that and get the data and need to add only the name structrure inside folder. 
  


---



## 🔁 Task 3: Get the Vessel Name and IMO 
**Server:** MongoDB MCP  
**Database:** `NYK_ETL_URL`  
**Collection:** `common_vessel_details`

**Uses :**

- Get all the Vessel Name and IMO
for each vessel Name need to make the HTML file and file name for that  HTML is imo number

`Example:`

- 123.html
### 🔍 Notes:
- To get the  `vesselName` and `imo` run the following:

```json
Query: {status:"ACTIVE"}

```
---


## 💻 Task 4: Create HTML Dashboard for Use Case
Create a python script that needs to create a HTML dashboard for Use Case Below,

### 🧾 Use Case: <>

### 🧠 Logic: <>


### 📌 Features: <>


### 📁 Deliverables:
- `<imo>.html`
- `<QuestionName>.py`
- `<QuestionName_testReport>.md`


### 🛠️ Instructions:
- User will provide the Usecase based on the create the Python and HTML file
- Before generating the HTML file from the Python script, first create test cases to validate the Python logic. Execute all possible test scenarios and generate a test report. 
- Once all tests pass, Create a Markdown file for that test report.
- And then proceed to run the Python script to generate the HTML dashboard.
- Use **only HTML and CSS**—no external libraries.
- Design must be **modern, Professional ,minimal, and responsive**.
- Use **CSS Flexbox/Grid** and **media queries** for layout.
- All elements (cards, tables, buttons) should be cleanly aligned.

---
## 🔁 Task 5: Upload to Github Repo 

**Server:** S3 MCP

**Tool:** upload_file_to_github


- Upload the Final python file `<QuestionName>.py` after running all test cases in repo.
- Upload this file  `<QuestionName_testReport>.md` inside the testReport folder.

---

## 🔁 Task 6: Upload to S3 & Convert to Airflow DAG

**Server:** S3 MCP

**Tool:** upload_file_to_s3



### 📁 Deliverables:
- Upload both `<imo>.html` and `<QuestionName>.py` to S3.  (Refer Subtask)

  ### 🔄 Subtask 6.1: Convert `.py` to Airflow DAG

  #### ⚙️ Default DAG Configuration:
  ```python
  default_args = {
      "owner": "Airflow",
      "retries": 2,
      "start_date": datetime(2025, 7, 24),  # Current Date
      "catchup": False,
      "schedule":None
  }
  ```

  ### 🔄 Subtask 6.2: Generate multiple `<imo>.html` 
     - Generate multiple `HTML` file , Get the IMO from `Task -3`.
     - For each IMO need sepreate html file and upload to S3,
     - Path for saving `HTML` is  bucket/{QuestionName}/
     - If not exist create folder and save in that folder

#### 🛠️ Notes:
- Only convert Python code to DAG (not HTML).
- Ensure final DAG is **production-ready** with valid structure.
- Upload the DAG Python file to the **`Python` bucket** (nyk-airflow) in S3.
- Upload the HTML file to **`HTML` bucket** (nyk-etl-prod) in S3.
- Upload the files in their respective buckets and path correctly.
- Don't upload python file only dag python file and HTML file need to add in S3.
- start_date is current date, Format (yyyy , m, d).

---

## 🧾 Task 7: Update `vesselinfos` Collection (MongoDB)

**Server:** MongoDB MCP  
**Database:** `NYK_PROD_URL`  
**Collection:** `vesselinfos`

### ✅ Document Format:
```json
{
  "questionNo": {Get from task 1},
  "imo": {Get IMO from task 3},
  "answer": “””

			## <Question Name>
			> <create a short summary in 3 -5 lines about usecase in markdown format>

                         <iframe src=‘{add s3 html presigned (ACL : Public access)(Based in IMO's)}’ width='100%' height='900px'></iframe>
					

”””,
  "question": "{Get from task 1}",
  "refreshDate": datetime.now(),   (ISO Timestamp format)
  "vesselName": {Get vesselName from task 3}
}
```



### 🔍 Notes:
- `refreshDate` must be a valid **ISO timestamp** (MongoDB's Date BSON type), not a string or object or array.
- `answer` contains an iframe embedding the uploaded S3 HTML.
- Upload the S3 URL to with ACL read access so that link is visible for public.
- Upload S3 URL to MongoDB by convert to Pre-Signed URL (convert by yourself) and valid for 3 days.
- Use tool `convert_s3_url_to_presigned_url` from `S3 Mcp server` for converting S3 url to pre-Signed URL.
- Create a Multiple entries and add in `vesselinfos` collections.
- Multiple entries means how much imo from task -3.
- For `answer` field I need to neat and clean markdown format with proper alignment.
- Short summary must include usecase ,logic used etc.
- Short summary must be in clear and neat format like point ,bold etc..

---

## 🔄 Task 8: Clear Cache
**Server:** S3 MCP  
**Tool:** Clear Cache

After completing  all task above call this tool Clear Cache:

- Clear the cache on the S3 MCP server to ensure that the latest updates are displayed correctly on the website.

---

## 🔍 Important Notes:

- If user asked to create QNA (App Insights) make sure to follow and complete all above task properly to create QNA to visible in website.

---

# 🗄️ Database Environment Overview

This document describes the purpose and usage of the three primary databases used within the NYK system. Each database URL points to a separate MongoDB instance that handles a specific stage of data handling: Application-level metadata, ETL raw ingestion, and finalized production-ready records.

---

## 📂 NYK_APP_URL

### 🔹 Description:
This database stores **application-level data** that powers the user-facing features of the NYK platform.

### 🔸 Typical Contents:
- User metadata (IDs, preferences, last login)
- Dashboard layout configurations
- App-specific feature toggles or UI state
- Session history or activity logs
- Non-sensitive operational data required by the frontend/backend

### 🛠 Usage:
Used primarily by the **web or mobile application layer**, this database enables fast reads/writes for app experience and personalization.

---

## 📂 NYK_ETL_URL

### 🔹 Description:
This database holds **raw or intermediate data** extracted from upstream systems (files, APIs, logs, etc.).

### 🔸 Typical Contents:
- Unprocessed CSV/JSON dumps
- Ingested third-party feeds or APIs
- Temporary staging tables
- Logs or audit trails during ETL workflows

### 🛠 Usage:
Serves as the **staging zone** for the ETL (Extract, Transform, Load) pipeline. Data is cleaned, transformed, and validated here before promotion to production.

---

## 📂 NYK_PROD_URL

### 🔹 Description:
This is the **production-grade data store** that contains clean, validated, and structured data used for final analytics, dashboards, or reporting.

### 🔸 Typical Contents:
- Aggregated metrics and reports
- Finalized master data entities
- Time-series records used for business insights
- Data used by external BI tools or APIs

### 🛠 Usage:
Used by **data consumers**, such as:
- Dashboards
- Reporting tools
- External API services
- Internal analytics teams

---

## ✅ Summary Table

| Database Name   | Purpose                           | Accessed By            |
|------------------|------------------------------------|--------------------------|
| `NYK_APP_URL`   | Application-level runtime data     | Web/Mobile Apps          |
| `NYK_ETL_URL`   | Raw ingested or staging data       | ETL Pipelines            |
| `NYK_PROD_URL`  | Finalized, production-ready data   | Dashboards, Reports, APIs|

---
