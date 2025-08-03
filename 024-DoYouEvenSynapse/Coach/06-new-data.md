# Challenge 6: Add New Data Source(s)

This challenge focuses on expanding the Azure Synapse Analytics environment by integrating new data sources to enrich the existing Open Powerlifting dataset. The goal is to teach students how to source, ingest, and store additional datasets in Azure Data Lake Storage (ADLS) Gen2, preparing them for analysis in Synapse. Suggested datasets include country-level population data, health care indices, and body mass index (BMI) data, which can provide context for analyzing powerlifting performance (e.g., correlating lifter demographics with health metrics). Students are free to choose other datasets, but the provided sources are reliable starting points.

As a coach, emphasize the importance of data integration in analytics pipelines, maintaining security (Azure AD RBAC and no public access), and preparing data for relational storage (as hinted in earlier challenges). Demonstrate the ingestion process, highlight data quality checks, and optionally introduce Azure Key Vault for secure credential management (building on Challenge 1). The student guide should focus on concise steps and encourage experimentation with data selection.

## Prerequisites
- Azure Synapse Workspace with a dedicated SQL pool and ADLS Gen2 storage account (set up in Challenge 1).
- Access to Synapse Studio or tools like Azure Storage Explorer/AzCopy.
- Open Powerlifting data already in ADLS (from Challenge 1, e.g., in `/raw/openpowerlifting.csv`).
- Permissions: Contributor access to the storage account and Synapse workspace.
- Suggested datasets:
  - **Population Data**: United Nations 2024 World Population Prospects (Wikipedia: https://en.wikipedia.org/wiki/List_of_countries_by_population_(United_Nations)). Mid-year 2022/2023 estimates.
  - **Health Care Index**: Numbeo 2025 Mid-Year (https://www.numbeo.com/health-care/rankings_by_country.jsp). Country-level health care quality scores.
  - **BMI Data**: WHO 2014 (https://en.wikipedia.org/wiki/List_of_countries_by_body_mass_index). Mean BMI by country.
- Tools: Browser for downloading, Python/PowerShell for scripting (optional), Excel/Notepad for data inspection.

Coaching Tip: Encourage students to think about why these datasets are relevant (e.g., population for lifter distribution, health care/BMI for athlete wellness). If students struggle, guide them to use the suggested sources but challenge advanced learners to find their own (e.g., sports or demographic data).

## Step 1: Select and Understand New Data Sources
Guide students to choose at least one new dataset. For simplicity, use the suggested sources:
- **Population Data**: Wikipedia table with country populations (2022/2023 UN estimates). Columns: Country, Population (2022), Population (2023). Rich for demographic context.
- **Health Care Index**: Numbeo table with country-level health care indices (2025). Columns: Country, Health Care Index. Useful for correlating health infrastructure with athlete performance.
- **BMI Data**: Wikipedia table with 2014 WHO data. Columns: Country, Mean BMI. Relevant for physical health analysis.

Explain:
- **Why These Sources?** They’re structured (tabular), publicly available, and complement powerlifting data by adding demographic/health context.
- **Format**: Wikipedia tables can be copied as text or scraped as HTML. Numbeo offers a table view. Save as CSV for consistency with the powerlifting dataset.
- **Data Quality**: Check for completeness (e.g., missing countries), currency (BMI is 2014, others are recent), and consistency (country names should match across datasets).

Coaching Tip: Share screen to show the Wikipedia/Numbeo pages. Copy a table into Excel to demonstrate structure. Ask: “How might population or BMI relate to powerlifting?” (E.g., larger populations may have more lifters; BMI may correlate with weight classes.) Warn about country name mismatches (e.g., “USA” vs. “United States”).

## Step 2: Download and Prepare the Data
Instruct students to:
1. **Population Data**:
   - Visit https://en.wikipedia.org/wiki/List_of_countries_by_population_(United_Nations).
   - Copy the table (highlight, Ctrl+C) and paste into Excel/Notepad, then save as `population.csv`.
   - Alternative: Scrape using Python (e.g., pandas `read_html`):
     ```python
     import pandas as pd
     url = "https://en.wikipedia.org/wiki/List_of_countries_by_population_(United_Nations)"
     tables = pd.read_html(url)
     population_df = tables[0]  # First table
     population_df.to_csv("population.csv", index=False)
     ```
2. **Health Care Index**:
   - Visit https://www.numbeo.com/health-care/rankings_by_country.jsp.
   - Copy the table or export via “Download table” link. Save as `healthcare.csv`.
3. **BMI Data**:
   - Visit https://en.wikipedia.org/wiki/List_of_countries_by_body_mass_index.
   - Copy the table or scrape similarly to population data. Save as `bmi.csv`.

Explanation: CSV format ensures compatibility with Synapse and ADLS. Manual copying is beginner-friendly; scraping teaches automation but requires error handling (e.g., Wikipedia table structure changes).

Coaching Tip: Demonstrate copying a table and saving as CSV. Show a Python scrape if students are advanced. Discuss: “Why CSV over JSON or Excel?” (Simple, universal, efficient for Synapse.) Check for data issues (e.g., null values, inconsistent country names).

## Step 3: Upload Data to ADLS Gen2
- Navigate to the storage account > Containers > `powerlifting-data` (from Challenge 1).
- Create a new folder (e.g., `/raw/external/`) for new datasets.
- Upload CSVs using:
  - **Azure Portal**: Storage browser > Upload files to `/raw/external/`.
  - **Azure Storage Explorer**: Drag-and-drop CSVs.
  - **AzCopy**:
    ```bash
    azcopy copy "path\to\local\population.csv" "https://<storage-account>.dfs.core.windows.net/powerlifting-data/raw/external/population.csv" --recursive
    ```
    Repeat for `healthcare.csv` and `bmi.csv`.
- Verify files in ADLS via the portal or Synapse Studio (Data hub > Linked).

Explanation: Organizing new data in `/raw/external/` follows data lake best practices (separating raw external sources from curated data). ADLS supports large-scale storage and Synapse integration.

Coaching Tip: Share screen to show upload and folder structure. Emphasize: “Keep raw data untouched for traceability.” Test by previewing files in Synapse Studio.

## Step 4: Secure the New Data
- **Confirm No Public Access**: In storage account > Configuration, ensure “Allow Blob public access” is Disabled (set in Challenge 1).
- **Apply RBAC**:
  - Go to storage account > Access Control (IAM).
  - Assign “Storage Blob Data Reader” or “Contributor” to specific Azure AD users/groups for `/raw/external/`.
  - For granular control, set POSIX ACLs on the folder:
    - Navigate to Data Lake > Containers > `powerlifting-data` > `/raw/external/`.
    - Set permissions (e.g., read/execute for a group, no access for others).
- Test: Try accessing files as a non-privileged user (e.g., via incognito browser or another account).

Explanation: RBAC ensures only authorized users access the data, aligning with security requirements. ACLs allow file/folder-level granularity, critical for multi-dataset projects.

Coaching Tip: Demonstrate RBAC assignment and test with a dummy account. Discuss: “Why not share a SAS token?” (RBAC is more secure, revocable, and auditable.) Reference Challenge 1 for continuity.

## Step 5: Preview and Validate in Synapse
- In Synapse Studio:
  - Create a linked service to the ADLS storage account (if not done).
  - Go to Data hub > Linked > Navigate to `/raw/external/`.
  - Right-click each CSV > New SQL script > Select TOP 100 rows.
- Sample query:
  ```sql
  SELECT * FROM OPENROWSET(
      BULK 'https://<storage-account>.dfs.core.windows.net/powerlifting-data/raw/external/population.csv',
      FORMAT = 'CSV',
      HEADER_ROW = TRUE
  ) AS result;
  ```
- Validate: Check for missing values, correct column names, and data types (e.g., population as integer, BMI as float).

Explanation: Synapse’s serverless SQL pool allows quick data previews without loading into a dedicated pool. This step confirms data integrity before relational database ingestion (future challenge).

Coaching Tip: Share screen to run a query and show results. Ask: “What issues might arise when joining this with powerlifting data?” (E.g., country name standardization.) Encourage cleaning (e.g., trim spaces in country names).

## Bonus: Secure Credentials with Azure Key Vault
- Create a Key Vault (if not done in Challenge 1).
- Store storage account keys or connection strings as secrets.
- In Synapse pipelines or scripts, use managed identity to access Key Vault:
  ```python
  from azure.identity import DefaultAzureCredential
  from azure.keyvault.secrets import SecretClient
  vault_url = "https://<vault-name>.vault.azure.net/"
  credential = DefaultAzureCredential()
  client = SecretClient(vault_url, credential)
  secret = client.get_secret("storage-connection-string")
  ```
- Grant Synapse’s managed identity “Get” permission on the vault.

Explanation: Key Vault prevents hardcoding sensitive info, enhancing security and compliance (e.g., for GDPR with population/BMI data).

Coaching Tip: Demo Key Vault setup if time allows. Stress: “Secrets in code are a security risk.” Link to Challenge 1’s bonus.

## Additional Notes for Coaches
- **Common Pitfalls**: Inconsistent country names across datasets (e.g., “United States” vs. “USA”). Incomplete data (BMI from 2014 may miss countries). Upload errors if hierarchical namespace isn’t enabled.
- **Extensions**: For advanced students, create a Synapse pipeline to automate ingestion (Web activity to download, Copy activity to ADLS). Introduce data quality checks (e.g., PySpark in Synapse to detect nulls).
- **Resources**: Microsoft Docs: [Synapse Data Ingestion](https://learn.microsoft.com/en-us/azure/synapse-analytics/data-integration/), [Key Vault](https://learn.microsoft.com/en-us/azure/key-vault/general/overview). Data sources: [UN Population](https://en.wikipedia.org/wiki/List_of_countries_by_population_(United_Nations)), [Numbeo Health](https://www.numbeo.com/health-care/rankings_by_country.jsp), [WHO BMI](https://en.wikipedia.org/wiki/List_of_countries_by_body_mass_index).
- **Assessment**: Students should upload at least one dataset, apply RBAC, and show a Synapse query result. Challenge them to explain how their dataset enhances powerlifting analysis (e.g., BMI vs. weight class).

This completes the coach’s guide for Challenge 6. For the student guide, provide numbered steps, sample scripts, and prompts to choose their own dataset for creativity.
