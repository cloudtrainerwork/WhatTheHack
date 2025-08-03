
## Coach’s Guide: Challenge 4 - Loading Incrementals

### Objective
Guide students through designing and implementing a process to load incremental data (new event results as INSERTs) into the Synapse SQL Pool, explaining and demonstrating the process, updating visualizations from Challenge 3, and optionally setting up alerts for new personal records (PRs).

### Preparation
- Verify students have completed Challenges 1-3, with the Open Powerlifting dataset in a Synapse Dedicated SQL Pool and visualizations in Power BI.
- Ensure students have access to an Azure subscription with Synapse, Power BI, and Azure Monitor (for bonus alerts).
- Confirm students understand the requirements:
  - Incremental data is new records (INSERTs), not updates.
  - Process must be explained and demonstrated.
  - Visualizations from Challenge 3 must be updated.
  - Bonus: Alerts for new PRs.
- Provide sample incremental data (e.g., new `results.csv` files) or instruct students to simulate new data.
- Provide access to the prereqs markdown (assumed to detail daily reports data structure).

### Step-by-Step Guidance
1. **Evaluate Incremental Loading Process**
   - **Task**: Students must design and explain a method to load incremental data into the SQL Pool.
   - **Key Points to Emphasize**:
     - Use Synapse Pipelines with Copy Activity or PolyBase/COPY statement to detect and load new files from Data Lake Storage Gen2.
     - Automate with a trigger (e.g., event-based on new file arrival).
     - Ensure only new records are inserted (e.g., check `MeetID` or `MeetDate`).
   - **Evaluation**: Check if the process is scalable, automated, and handles INSERTs correctly.

2. **Review Demonstration of Loading**
   - **Task**: Students demonstrate the loading process with sample incremental data.
   - **Key Points to Emphasize**:
     - Show pipeline execution or SQL script execution in Synapse Studio.
     - Verify new records in the `results` table (e.g., query `SELECT COUNT(*) FROM results` before/after).
     - Highlight how duplicates are avoided (e.g., unique `MeetID` constraint).
   - **Evaluation**: Confirm the demo shows new data loaded and table updates.

3. **Assess Updated Visualizations**
   - **Task**: Students update and show the Power BI visualizations from Challenge 3.
   - **Key Points to Emphasize**:
     - Refresh the Power BI dataset to reflect new data.
     - Verify updated visuals (e.g., Top 20 lifters, Commitment Leaderboard) include new records.
     - Ensure filters (e.g., last 12 months from 08/03/2025) still apply.
   - **Evaluation**: Ensure visuals reflect the new data and remain functional.

4. **Review Bonus Alerts for PRs**
   - **Task**: Students set up alerts for new personal records (optional).
   - **Key Points to Emphasize**:
     - Define a PR as a new `Total` exceeding the lifter’s previous best (per `LifterID`).
     - Use Azure Monitor or Power BI data alerts to notify on new PRs.
     - Example: Alert when `Total` > `MAX(Total)` for a `LifterID` in the last load.
   - **Evaluation**: Assess if the alert logic is correct and triggers appropriately.

### Success Criteria Checklist
- [ ] Student explains the incremental loading process (e.g., Synapse Pipeline).
- [ ] Student demonstrates the loading of incremental data.
- [ ] Updated visualizations are shown in Power BI.
- [ ] (Bonus) Alerts for new PRs are set up and tested.

### Common Pitfalls to Watch For
- Loading incremental data as updates instead of INSERTs.
- Failing to automate the loading process.
- Not refreshing Power BI to reflect new data.
- Setting up alerts without clear PR definition or testing.

### Additional Notes
- Encourage students to use metadata (e.g., file timestamps) to detect new data.
- Discuss how the process scales for daily incremental loads.
- Prepare students for future challenges by emphasizing data integrity and automation.

---

## Student’s Guide: Challenge 4 - Loading Incrementals

### Introduction
In this challenge, you’ll load incremental data (new event results as INSERTs) into your Synapse SQL Pool, explain and demonstrate the process, update your Power BI visualizations from Challenge 3, and optionally set up alerts for new personal records (PRs). The focus is on automating and scaling the solution for daily data updates.

### Prerequisites
- Completed Challenges 1-3, with the Open Powerlifting dataset in a Synapse SQL Pool and Power BI visualizations.
- Azure subscription with Synapse, Power BI, and Azure Monitor access.
- Sample incremental data (e.g., new `results.csv` with recent meets) or ability to simulate data.
- Review the prereqs markdown for daily reports data structure.

### Step-by-Step Instructions

1. **Design the Incremental Loading Process**
   - **Task**: Plan and explain how to load incremental data to the coach.
   - **Steps**:
     1. Choose a method: Use a **Synapse Pipeline with Copy Activity** for automation.
     2. Configure the pipeline:
        - **Source**: Data Lake Storage Gen2 (e.g., new `results_incremental.csv`).
        - **Sink**: `results` table in the SQL Pool.
        - Use a trigger to detect new files (e.g., event-based trigger).
     3. Ensure INSERT-only:
        - Add a filter to load only new `MeetID` values not in the `results` table.
        - Example: Use a `WHERE NOT EXISTS` clause in PolyBase.
     4. Prepare a brief explanation (2-3 sentences) for the coach, covering:
        - Why you chose the method (e.g., automation, scalability).
        - How it handles new INSERTs and avoids duplicates.
   - **Example Explanation**:
     > I used a Synapse Pipeline with Copy Activity to load incremental data because it automates the process and scales for daily updates. It filters for new `MeetID` values to ensure only INSERTs occur, avoiding duplicates in the `results` table.

2. **Set Up and Demonstrate the Loading Process**
   - **Task**: Load sample incremental data and show the coach.
   - **Steps**:
     1. Create a sample incremental file (e.g., `results_incremental.csv`) with new meets post-08/03/2025 (e.g., 08/04/2025).
     2. Upload to Data Lake Storage Gen2 (e.g., `openpowerlifting-data/results_incremental.csv`).
     3. In Synapse Studio, go to **Integrate** > **Pipeline** > **Edit** the existing pipeline.
     4. Update the source to point to the incremental file.
     5. Add a filter or `WHERE NOT EXISTS` logic:
        ```sql
        INSERT INTO results
        SELECT * FROM ext_results
        WHERE NOT EXISTS (
            SELECT 1 FROM results r WHERE r.MeetID = ext_results.MeetID
        );
        ```
     6. Run the pipeline and monitor in Synapse Studio.
     7. Demonstrate to the coach:
        - Show pipeline run status.
        - Run `SELECT COUNT(*) FROM results` before/after to verify new rows (e.g., +100 rows).
        - Share a screenshot or query results.

3. **Update and Show Visualizations**
   - **Task**: Refresh and display updated Power BI visuals.
   - **Steps**:
     1. Open Power BI Desktop with the Challenge 3 report.
     2. Go to **Home** > **Refresh** to update the dataset from Synapse.
     3. Verify updated visuals:
        - **Top 20 Lifters**: Check for new lifters (filter: `MeetDate` >= 08/03/2024).
        - **Commitment Leaderboard**: Ensure new participation durations reflect.
        - **Meets per Year**: Confirm 2025 data appears.
     4. Take screenshots of updated visuals and share with the coach.
     5. Ensure filters (e.g., SBD, BD events) still work.

4. **Bonus: Set Up Alerts for New PRs**
   - **Task**: Create alerts for new personal records (optional).
   - **Steps**:
     1. Define a PR: A new `Total` exceeding the lifter’s previous best per `LifterID`.
     2. In Synapse, create a view to detect PRs:
        ```sql
        CREATE VIEW vw_PR_Alerts AS
        SELECT r.LifterID, r.MeetDate, r.Total
        FROM results r
        WHERE r.Total > (
            SELECT MAX(r2.Total)
            FROM results r2
            WHERE r2.LifterID = r.LifterID
            AND r2.MeetDate < r.MeetDate
        );
        ```
     3. In Power BI, add a **Card** visual for `COUNT(vw_PR_Alerts)`.
     4. Set up a data alert:
        - Go to the Power BI service, open the dashboard, select the card, and click **Manage Alerts**.
        - Create an alert for when the count > 0.
        - Set email notification to the coach.
     5. Test by adding a record with a higher `Total` and verify the alert triggers.

### Deliverables
- **Explanation**: How you load incremental data (e.g., Synapse Pipeline).
- **Demonstration**: Pipeline run or query results showing new data.
- **Screenshots**: Updated Power BI visuals (e.g., Top 20, meets per year).
- **Bonus**: Alert setup and test notification (if completed).

### Tips
- Use file timestamps or `MeetDate` to identify new data.
- Test the pipeline with a small incremental file first.
- Refresh Power BI immediately after loading to see updates.
- For PR alerts, ensure the view logic is efficient for large datasets.
- Save pipeline configurations for future use.

### Learning Resources
- Refer to the prereqs markdown for daily reports data structure.
- [Synapse Pipeline Documentation](https://docs.microsoft.com/en-us/azure/synapse-analytics/pipeline-designer/overview)
- [Power BI Data Alerts](https://docs.microsoft.com/en-us/power-bi/create-reports/service-set-data-alerts)

---

### Notes for Both Guides
- **Incremental Focus**: The guides emphasize automation and INSERT-only logic, aligning with the challenge’s intent.
- **Synapse Integration**: Pipelines leverage Data Lake Storage Gen2 and SQL Pool for seamless updates.
- **Visualization Update**: Both guides ensure Power BI reflects new data, using the current date (08/03/2025) for context.
- **PR Alerts**: The bonus task introduces Azure Monitor/Power BI alerts, adding real-time value.
- **Scalability**: The process is designed to handle daily increments for a 20 TB+ dataset.

Let me know if you need further refinements or additional details!
