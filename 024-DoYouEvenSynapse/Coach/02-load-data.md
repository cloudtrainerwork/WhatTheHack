

## Coach’s Guide: Challenge 2 - Load Data into a Relational Database/Warehouse

### Objective
Guide students through selecting and justifying a relational database or data warehouse in Azure, creating a data model, loading the Open Powerlifting dataset from the storage service, and explaining the process. Ensure the solution is scalable for 20 TB+ datasets and supports future data loads (INSERTs only).

### Preparation
- Verify students have completed Challenge 1, with the Open Powerlifting dataset stored in an Azure storage service (e.g., Azure Data Lake Storage Gen2).
- Ensure students have access to an Azure subscription with permissions to create resources (e.g., Synapse Workspace, SQL Pool, or other database services).
- Confirm students understand the requirements:
  - Scalability for 20 TB+ datasets.
  - Support for future data loads (new records only).
  - Security features (e.g., data masking, encryption) for future challenges.
  - Visualization and querying capabilities for later challenges.
- Provide access to learning resources (listed in the challenge description).
- Optionally, set up a Synapse Workspace for students to use.

### Step-by-Step Guidance
1. **Evaluate Database/Warehouse Selection**
   - **Task**: Students must choose a database or data warehouse (e.g., Azure Synapse Analytics Dedicated SQL Pool, Azure SQL Database, or Azure Databricks) and justify their choice.
   - **Key Points to Emphasize**:
     - Azure Synapse Dedicated SQL Pool is ideal for large-scale analytics (20 TB+) due to its massively parallel processing (MPP) architecture and integration with Data Lake Storage Gen2.
     - Azure SQL Database is better for transactional workloads but less suited for 20 TB+ analytics.
     - Azure Databricks with Delta Lake can be used for a lakehouse approach but may require more setup for relational querying.
     - Ensure the choice supports security (encryption, data masking, column-level access) and visualization (e.g., Power BI integration).
   - **Evaluation**: Check if the student’s justification addresses scalability, Synapse integration, and future visualization/security needs.

2. **Review Data Model**
   - **Task**: Students create and describe a data model for the Open Powerlifting dataset.
   - **Key Points to Emphasize**:
     - The dataset (CSV files in `openpowerlifting-latest.zip`) includes tables like `meet.csv`, `lifter.csv`, and `results.csv`. Students should define tables with appropriate columns, data types, and relationships (e.g., foreign keys).
     - Use a star or snowflake schema for analytics, with fact tables (e.g., lift results) and dimension tables (e.g., meets, lifters).
     - Consider partitioning for scalability (e.g., by year or meet ID).
     - Ensure the model supports INSERT-only future loads.
   - **Evaluation**: Verify the data model is logical, normalized/denormalized appropriately, and scalable. Check for primary/foreign keys and partitioning strategies.

3. **Assess Data Loading Process**
   - **Task**: Students load the dataset into the chosen database/warehouse and explain the process, including considered options.
   - **Key Points to Emphasize**:
     - Preferred method: Use Synapse Pipelines with Copy Activity or PolyBase/COPY statement for efficient bulk loading from Data Lake Storage Gen2.
     - Alternatives: Azure Data Factory, Databricks notebooks, or manual SQL INSERTs (less scalable).
     - Highlight automation for future loads (e.g., pipeline triggers for new files).
     - Ensure error handling and data validation (e.g., checking for duplicates).
   - **Evaluation**: Confirm the data is loaded correctly, the process is scalable, and the student explains trade-offs (e.g., Copy Activity vs. PolyBase).

4. **Verify Populated Data**
   - **Task**: Students demonstrate the populated database/warehouse to the coach.
   - **Key Points to Emphasize**:
     - Run sample queries to show data (e.g., `SELECT TOP 10 * FROM results`).
     - Verify row counts match the source dataset.
     - Check for correct data types and relationships.
   - **Evaluation**: Ensure the data is accessible, queryable, and matches the data model.

5. **Bonus: Data Distribution Methodologies**
   - **Task**: Students explain data distribution strategies (if applicable, e.g., in Synapse Dedicated SQL Pool).
   - **Key Points to Emphasize**:
     - Synapse SQL Pool supports distribution types: Hash, Round-Robin, or Replicated.
     - Hash distribution (e.g., on `meet_id`) optimizes joins and aggregations.
     - Round-Robin is simpler but less efficient for large datasets.
     - Replicated tables are suitable for small dimension tables.
   - **Evaluation**: Assess if the student’s explanation aligns with the data model and scalability needs.

### Success Criteria Checklist
- [ ] Student justifies the database/warehouse choice (e.g., Synapse Dedicated SQL Pool for scalability).
- [ ] Data model is created, described, and supports scalability and future INSERTs.
- [ ] Data loading process is explained, with options considered and trade-offs discussed.
- [ ] Populated data is demonstrated via queries or screenshots.
- [ ] (Bonus) Data distribution methodology is explained (e.g., Hash distribution).

### Common Pitfalls to Watch For
- Choosing a non-scalable database (e.g., Azure SQL Database for 20 TB+).
- Creating a data model without relationships or partitioning.
- Using manual SQL INSERTs instead of bulk loading methods.
- Failing to automate the loading process for future files.
- Ignoring security features (e.g., encryption) in the database setup.

### Additional Notes
- Encourage students to use Synapse Pipelines for automation and scalability.
- Discuss how the data model supports visualization (e.g., Power BI) in future challenges.
- Highlight Synapse’s integration with Data Lake Storage Gen2 for seamless data access.
- Prepare students for Challenge 3 by mentioning visualization and security requirements.

---

## Student’s Guide: Challenge 2 - Load Data into a Relational Database/Warehouse

### Introduction
In this challenge, you’ll load the Open Powerlifting dataset from your Azure storage service (set up in Challenge 1) into a relational database or data warehouse, such as Azure Synapse Analytics. You’ll choose a platform, create a data model, load the data, and ensure the solution scales to 20 TB+ while supporting future data loads (new records only).

### Prerequisites
- Completed Challenge 1, with the Open Powerlifting dataset in Azure storage (e.g., Data Lake Storage Gen2).
- Azure subscription with permissions to create resources (e.g., Synapse Workspace, SQL Pool).
- Basic knowledge of SQL, Azure Portal, and Synapse Pipelines or PolyBase.
- Review the learning resources provided.

### Step-by-Step Instructions

1. **Choose a Database/Warehouse**
   - **Task**: Select a database or data warehouse and explain your choice to the coach.
   - **Steps**:
     1. Research Azure Synapse Analytics, Azure SQL Database, and Azure Databricks using the provided resources.
     2. Recommended: Choose **Azure Synapse Analytics Dedicated SQL Pool** because:
        - It’s designed for large-scale analytics (20 TB+) with massively parallel processing (MPP).
        - It integrates with Data Lake Storage Gen2 for efficient data loading.
        - It supports security features (encryption, data masking) and visualization (Power BI).
     3. Prepare a brief explanation (2-3 sentences) for your coach, covering:
        - Why you chose the platform (e.g., scalability, Synapse features).
        - How it supports 20 TB+ datasets and future visualization/security needs.
   - **Example Explanation**:
     > I chose Azure Synapse Dedicated SQL Pool because its MPP architecture scales to 20 TB+ datasets and integrates with Data Lake Storage Gen2 for efficient loading. It also supports security features like encryption and data masking, preparing for future visualization with Power BI.

2. **Create a Synapse Workspace and SQL Pool**
   - **Task**: Set up a Synapse Workspace and Dedicated SQL Pool.
   - **Steps**:
     1. In the Azure Portal, navigate to **Azure Synapse Analytics** > **Create**.
     2. Configure the workspace:
        - **Resource Group**: Use the same group as Challenge 1 (e.g., `powerlifting-rg`).
        - **Workspace Name**: Choose a unique name (e.g., `powerlifting-synapse`).
        - **Storage Account**: Select the Data Lake Storage Gen2 account from Challenge 1.
        - Assign your AAD user the **Storage Blob Data Contributor** role for the storage account.
     3. Create a Dedicated SQL Pool:
        - In the Synapse Workspace, go to **SQL pools** > **New**.
        - Name: `powerlifting-sqlpool`.
        - Performance Level: Start with DW100c (scale up later for 20 TB+).
     4. Enable encryption (default in Synapse) for security.

3. **Create a Data Model**
   - **Task**: Design a data model for the Open Powerlifting dataset and describe it to the coach.
   - **Steps**:
     1. Unzip and inspect the dataset (`openpowerlifting-latest.zip`) to understand its structure:
        - `meet.csv`: Meet details (e.g., `MeetID`, `MeetName`, `Date`).
        - `lifter.csv`: Lifter details (e.g., `LifterID`, `Name`, `Sex`).
        - `results.csv`: Lift results (e.g., `MeetID`, `LifterID`, `WeightClass`, `BestSquat`).
     2. Design a star schema:
        - **Fact Table**: `results` (columns: `MeetID`, `LifterID`, `WeightClass`, `BestSquat`, `BestBench`, `BestDeadlift`, `Total`, etc.).
        - **Dimension Tables**:
          - `meet` (columns: `MeetID`, `MeetName`, `Date`, `Location`).
          - `lifter` (columns: `LifterID`, `Name`, `Sex`, `BirthYear`).
        - Define primary keys (`MeetID`, `LifterID`) and foreign keys (e.g., `MeetID` in `results` references `meet`).
     3. Add partitioning for scalability:
        - Partition `results` by `MeetDate` (yearly or monthly).
        - Example: `PARTITION BY RANGE (YEAR(MeetDate))`.
     4. Describe the model to the coach, explaining:
        - Table structure and relationships.
        - Why you chose a star schema (optimized for analytics).
        - Partitioning for 20 TB+ scalability.
   - **Example SQL for Tables** (in Synapse SQL Pool):
     ```sql
     CREATE TABLE meet (
         MeetID INT PRIMARY KEY,
         MeetName NVARCHAR(100),
         MeetDate DATE,
         Location NVARCHAR(50)
     ) WITH (DISTRIBUTION = REPLICATED);

     CREATE TABLE lifter (
         LifterID INT PRIMARY KEY,
         Name NVARCHAR(100),
         Sex CHAR(1),
         BirthYear INT
     ) WITH (DISTRIBUTION = REPLICATED);

     CREATE TABLE results (
         MeetID INT,
         LifterID INT,
         WeightClass NVARCHAR(20),
         BestSquat FLOAT,
         BestBench FLOAT,
         BestDeadlift FLOAT,
         Total FLOAT,
         MeetDate DATE,
         FOREIGN KEY (MeetID) REFERENCES meet(MeetID),
         FOREIGN KEY (LifterID) REFERENCES lifter(LifterID)
     ) WITH (
         DISTRIBUTION = HASH(MeetID),
         PARTITION (MeetDate RANGE RIGHT FOR VALUES ('2020-01-01', '2021-01-01', '2022-01-01'))
     );
     ```
   - **Example Description**:
     > I created a star schema with a `results` fact table for lift data and `meet` and `lifter` dimension tables. The schema uses foreign keys to link tables and is partitioned by `MeetDate` for scalability to 20 TB+. The star schema optimizes queries for analytics and visualization in Power BI.

4. **Load the Data**
   - **Task**: Load the dataset into the SQL Pool and explain the process to the coach.
   - **Steps**:
     1. Choose a loading method:
        - **Recommended**: Synapse Pipeline with Copy Activity (scalable, automated).
        - **Alternative**: PolyBase/COPY statement (efficient for bulk loads).
     2. Set up a Synapse Pipeline:
        - In Synapse Studio, go to **Integrate** > **Pipeline** > **New Pipeline**.
        - Add a **Copy Data** activity.
        - **Source**: Data Lake Storage Gen2 (e.g., `openpowerlifting-data/results.csv`).
          - Configure a linked service using the storage account from Challenge 1.
          - Use AAD authentication or Key Vault for credentials.
        - **Sink**: Dedicated SQL Pool (`results` table).
          - Create a linked service for the SQL Pool.
        - Map CSV columns to table columns.
        - Enable **Staging** (use the same storage account for temporary data).
     3. Run the pipeline and verify data:
        - Monitor the pipeline run in Synapse Studio.
        - Query the tables: `SELECT COUNT(*) FROM results;`.
     4. Explain to the coach:
        - Why you chose Copy Activity (scalable, automated, error handling).
        - Alternatives considered (e.g., PolyBase, Databricks).
        - How the pipeline supports future INSERTs (e.g., trigger on new files).
   - **Example PolyBase Alternative**:
     ```sql
     CREATE EXTERNAL DATA SOURCE powerlifting_data
     WITH (
         LOCATION = 'abfss://openpowerlifting-data@<storage-account>.dfs.core.windows.net/',
         CREDENTIAL = [AAD or Key Vault credential]
     );

     CREATE EXTERNAL FILE FORMAT csv_format
     WITH (
         FORMAT_TYPE = DELIMITEDTEXT,
         FORMAT_OPTIONS (FIELD_TERMINATOR = ',', FIRST_ROW = 2)
     );

     CREATE EXTERNAL TABLE ext_results (
         MeetID INT,
         LifterID INT,
         WeightClass NVARCHAR(20),
         BestSquat FLOAT,
         BestBench FLOAT,
         BestDeadlift FLOAT,
         Total FLOAT,
         MeetDate DATE
     ) WITH (
         LOCATION = 'results.csv',
         DATA_SOURCE = powerlifting_data,
         FILE_FORMAT = csv_format
     );

     INSERT INTO results
     SELECT * FROM ext_results;
     ```
   - **Example Explanation**:
     > I used a Synapse Pipeline with Copy Activity to load data from Data Lake Storage Gen2 to the SQL Pool because it’s scalable and automates future loads. I considered PolyBase for bulk loading but chose Copy Activity for its error handling and pipeline triggers. The pipeline can be scheduled to handle new CSV files as INSERTs.

5. **Show Populated Data**
   - **Task**: Demonstrate the loaded data to the coach.
   - **Steps**:
     1. In Synapse Studio, go to **Data** > **SQL Pool** > **New SQL Script**.
     2. Run queries to verify:
        ```sql
        SELECT TOP 10 * FROM results;
        SELECT COUNT(*) FROM meet;
        SELECT COUNT(*) FROM lifter;
        SELECT COUNT(*) FROM results;
        ```
     3. Take screenshots or share query results with the coach.
     4. Confirm row counts match the source CSV files.

6. **Bonus: Explain Data Distribution**
   - **Task**: Describe the data distribution strategy used in the SQL Pool.
   - **Steps**:
     1. Explain Synapse SQL Pool distribution types:
        - **Hash**: Distributes data based on a column (e.g., `MeetID`) for efficient joins.
        - **Round-Robin**: Evenly distributes data but less optimized for queries.
        - **Replicated**: Copies small tables (e.g., `meet`, `lifter`) to all nodes.
     2. Justify your choice:
        - Used Hash on `MeetID` for `results` to optimize joins with `meet`.
        - Used Replicated for `meet` and `lifter` due to their smaller size.
     3. Prepare a brief explanation for the coach.
   - **Example Explanation**:
     > I used Hash distribution on `MeetID` for the `results` table to optimize joins with the `meet` table, improving query performance for 20 TB+ datasets. I used Replicated distribution for `meet` and `lifter` because they’re small dimension tables, reducing data movement during queries.

### Deliverables
- **Explanation**: Why you chose the database/warehouse (e.g., Synapse SQL Pool).
- **Data Model**: SQL scripts or diagram of tables, with description.
- **Explanation**: Data loading process, including options considered.
- **Screenshot/Results**: Query results showing populated data.
- **Bonus**: Explanation of data distribution strategy.

### Tips
- Use Synapse Dedicated SQL Pool for scalability and Synapse integration.
- Automate loading with Synapse Pipelines for future files.
- Partition large tables to handle 20 TB+ datasets.
- Test queries to ensure relationships and data integrity.
- Save pipeline configurations for Challenge 3.

### Learning Resources
- [What is Azure Synapse Analytics?](https://docs.microsoft.com/en-us/azure/synapse-analytics/overview-what-is)
- [What is Azure Databricks?](https://docs.microsoft.com/en-us/azure/databricks/scenarios/what-is-azure-databricks)
- [Quickstart: Create a Synapse Workspace](https://docs.microsoft.com/en-us/azure/synapse-analytics/quickstart-create-workspace)
- [Load Data with the COPY Statement](https://docs.microsoft.com/en-us/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql)
- [Load Data with an Integration Pipeline](https://docs.microsoft.com/en-us/azure/synapse-analytics/quickstart-copy-activity-load-sql-pool)

---

### Notes for Both Guides
- **Synapse Focus**: The guides emphasize Synapse Dedicated SQL Pool for its scalability and integration with Data Lake Storage Gen2, aligning with Azure analytics workflows.
- **Scalability**: Both guides address the 20 TB+ requirement through partitioning, distribution, and bulk loading.
- **Automation**: The student guide prioritizes Synapse Pipelines for future loads, while the coach guide evaluates automation feasibility.
- **Security**: Encryption is enabled by default, preparing for future security challenges (e.g., data masking).
- **Visualization Prep**: The data model is designed for analytics, supporting visualization in Challenge 3.

Let me know if you need further refinements or additional details for specific Azure services or tools!
