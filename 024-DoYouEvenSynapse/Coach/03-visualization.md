

## Coach’s Guide: Challenge 3 - Visualization

### Objective
Guide students through selecting a visualization tool (e.g., Power BI), creating reports and dashboards based on the Open Powerlifting dataset in the Synapse SQL Pool, and deploying them to the Power BI cloud service. Ensure the visuals meet the executive board’s requirements and support filtering and additional insights.

### Preparation
- Verify students have completed Challenge 2, with the Open Powerlifting dataset loaded into a Synapse Dedicated SQL Pool (e.g., `results`, `meet`, `lifter` tables).
- Ensure students have access to an Azure subscription with Synapse and Power BI Pro licenses.
- Confirm students understand the visualization requirements:
  - Lifter Performance: Top 20 lifters by Wilks score (past year), Commitment Leaderboard.
  - Sport Management: Meets and lifters per year, by federation and country, plus creative insights.
  - Filtering by sex, federation, country, and event type (default: SBD, BD).
  - Minimum: Desktop report; Bonus: Cloud deployment.
- Provide access to learning resources (listed in the challenge description).
- Optionally, set up a Power BI Pro account for students to use.

### Step-by-Step Guidance
1. **Evaluate Visualization Tool Selection**
   - **Task**: Students must choose a visualization tool (e.g., Power BI) and justify their choice.
   - **Key Points to Emphasize**:
     - Power BI is ideal due to its native integration with Azure Synapse, DirectQuery support, and cloud deployment options.
     - Alternatives (e.g., Tableau, Excel) lack Synapse integration or cloud scalability.
     - Highlight filtering, dashboard sharing, and report generation capabilities.
   - **Evaluation**: Check if the student’s justification addresses Synapse connectivity, scalability, and executive needs.

2. **Review Visuals for Executive Requirements**
   - **Task**: Students create visuals for lifter performance and sport management perspectives.
   - **Key Points to Emphasize**:
     - **Lifter Performance**:
       - Top 20 lifters by Wilks score (past year): Use a table or bar chart, filtered by date (last 12 months from 08/03/2025), with filters for sex, federation, country, and event (default SBD, BD).
       - Commitment Leaderboard: Calculate participation duration (max `MeetDate` - min `MeetDate` per lifter), ranked descending, with filters.
     - **Sport Management**:
       - Meets per year: Line chart with `MeetDate` (year).
       - Lifters per year: Line chart with distinct `LifterID` per year.
       - Meets and lifters by federation/country: Bar or stacked charts over time.
       - Creative insight: Suggest trends (e.g., average Wilks score by country over time).
     - Ensure visuals use the uploaded image as a reference for layout and metrics.
   - **Evaluation**: Verify visuals meet all requirements and include at least one creative insight.

3. **Assess Filtering Capabilities**
   - **Task**: Students demonstrate filtering in their visuals.
   - **Key Points to Emphasize**:
     - Implement slicers for sex, federation, country, and event type.
     - Default filter: Event type = SBD, BD; Date range = last 12 months.
     - Test filters to ensure dynamic updates (e.g., switching to female lifters only).
   - **Evaluation**: Confirm all filters work and default settings are applied.

4. **Review Generated Report**
   - **Task**: Students create a desktop report in Power BI.
   - **Key Points to Emphasize**:
     - Use Power BI Desktop to combine visuals into a single report.
     - Include titles, legends, and annotations for clarity (e.g., “Top 20 Lifters by Wilks Score”).
     - Export as a .pbix file or PDF for review.
   - **Evaluation**: Ensure the report is functional, well-organized, and meets minimum requirements.

5. **Verify Cloud Deployment**
   - **Task**: Students deploy the report/dashboard to the Power BI service.
   - **Key Points to Emphasize**:
     - Publish the report from Power BI Desktop to the Power BI service.
     - Create a dashboard with pinned visuals for broader access.
     - Share the link or workspace with the coach (requires Pro license).
   - **Evaluation**: Confirm deployment and accessibility in the cloud.

### Success Criteria Checklist
- [ ] Student justifies the visualization tool choice (e.g., Power BI for Synapse integration).
- [ ] Visuals are created for lifter performance and sport management perspectives.
- [ ] Filtering is demonstrated (sex, federation, country, event).
- [ ] A desktop report is generated and shared.
- [ ] Reports/dashboards are deployed to the Power BI cloud service.

### Common Pitfalls to Watch For
- Choosing a tool without Synapse integration (e.g., Excel without DirectQuery).
- Missing required visuals or filters.
- Failing to set default filters (e.g., SBD, BD events).
- Not deploying to the cloud or sharing access.
- Overcomplicating creative insights without clear value.

### Additional Notes
- Encourage students to use DirectQuery for real-time data from Synapse.
- Suggest creative visuals (e.g., heatmaps for country participation trends).
- Prepare students for future challenges by discussing data security in visuals.
- Use the uploaded leaderboard image as a template for layout and metrics.

---

## Student’s Guide: Challenge 3 - Visualization

### Introduction
In this challenge, you’ll create reports and dashboards in Power BI to visualize the Open Powerlifting dataset from your Synapse SQL Pool, meeting the executive board’s needs for lifter performance and sport management analysis. You’ll connect to the database, build interactive visuals, generate a desktop report, and deploy to the Power BI cloud service.

### Prerequisites
- Completed Challenge 2, with the Open Powerlifting dataset in a Synapse Dedicated SQL Pool.
- Azure subscription with Synapse and Power BI Pro licenses.
- Power BI Desktop installed.
- Review the learning resources provided.

### Step-by-Step Instructions

1. **Choose a Visualization Tool**
   - **Task**: Select a tool and explain your choice to the coach.
   - **Steps**:
     1. Research Power BI, Tableau, and Excel using the provided resources.
     2. Recommended: Choose **Power BI** because:
        - It integrates natively with Azure Synapse via DirectQuery.
        - Supports cloud deployment and interactive filtering.
        - Meets executive needs for reports and dashboards.
     3. Prepare a brief explanation (2-3 sentences) for your coach, covering:
        - Why you chose Power BI (e.g., Synapse compatibility, cloud features).
        - How it supports the executive’s visualization requirements.
   - **Example Explanation**:
     > I chose Power BI because it integrates seamlessly with Azure Synapse for real-time data access and supports cloud deployment for broader access. It offers interactive filtering and dashboard capabilities, meeting the executive board’s needs for analysis and reporting.

2. **Set Up Power BI and Connect to Synapse**
   - **Task**: Connect Power BI to the Synapse SQL Pool.
   - **Steps**:
     1. Open Power BI Desktop.
     2. Go to **Get Data** > **Azure** > **Azure Synapse Analytics SQL**.
     3. Enter the SQL Pool server name (e.g., `powerlifting-sqlpool.sql.azuresynapse.net`) and database name.
     4. Use your AAD credentials to authenticate.
     5. Load the `results`, `meet`, and `lifter` tables.
     6. Enable **DirectQuery** mode for real-time updates.

3. **Create Visuals for Lifter Performance**
   - **Task**: Build visuals for the Top 20 lifters and Commitment Leaderboard.
   - **Steps**:
     1. **Top 20 Lifters by Wilks Score**:
        - Create a **Table** visual.
        - Add columns: `Name`, `Average of Goodlift` (calculated as Wilks score, see note below), `Latest Meet Date`.
        - Filter: `MeetDate` >= 08/03/2024 (last 12 months from 08/03/2025).
        - Top N: 20 rows by `Average of Goodlift`.
        - Add slicers for `Sex`, `Federation`, `Country`, and `Event` (default: SBD, BD).
     2. **Commitment Leaderboard**:
        - Create a **Table** visual.
        - Add a calculated column: `Participation Duration` = `MAX(MeetDate) - MIN(MeetDate)` grouped by `LifterID`.
        - Sort descending by `Participation Duration`.
        - Add slicers for `Sex`, `Federation`, and `Country`.
     3. Use the uploaded image as a layout guide (e.g., similar table format).
     - **Note**: Wilks score requires a formula (not provided in dataset). Assume `Average of Goodlift` is a placeholder; calculate it as `(BestSquat + BestBench + BestDeadlift) * WeightClassFactor` (approximate) if needed.

4. **Create Visuals for Sport Management**
   - **Task**: Build visuals for meets and lifters over time, by federation and country.
   - **Steps**:
     1. **Meets per Year**:
        - Create a **Line Chart**.
        - X-Axis: `YEAR(MeetDate)`.
        - Values: Count of `MeetID`.
     2. **Lifters per Year**:
        - Create a **Line Chart**.
        - X-Axis: `YEAR(MeetDate)`.
        - Values: Distinct count of `LifterID`.
     3. **Meets and Lifters by Federation**:
        - Create a **Stacked Bar Chart**.
        - Axis: `Federation`.
        - Values: Count of `MeetID` and distinct `LifterID`.
     4. **Meets and Lifters by Country**:
        - Create a **Line Chart** over time.
        - X-Axis: `YEAR(MeetDate)`.
        - Legend: `Country`.
        - Values: Count of `MeetID` and distinct `LifterID`.
     5. **Creative Insight**:
        - Add a **Line Chart** for average Wilks score by country over time to show performance trends.
        - X-Axis: `YEAR(MeetDate)`.
        - Legend: `Country`.
        - Values: Average of `Average of Goodlift`.

5. **Implement Filtering**
   - **Task**: Add and test filters in the report.
   - **Steps**:
     1. Go to **Visualizations** > **Slicers**.
     2. Add slicers for `Sex`, `Federation`, `Country`, and `Event`.
     3. Set default filter: `Event` = SBD, BD; `MeetDate` >= 08/03/2024.
     4. Test by filtering (e.g., select “USA” and “Female”) and verify visual updates.

6. **Generate a Desktop Report**
   - **Task**: Combine visuals into a report.
   - **Steps**:
     1. Arrange visuals on a single page in Power BI Desktop.
     2. Add a title: “Worldwide Goodlift Leaderboard”.
     3. Include legends and annotations (e.g., “Top 20 Lifters”).
     4. Save the report as a .pbix file and export as PDF.
     5. Share the file with the coach.

7. **Deploy to Power BI Cloud Service**
   - **Task**: Publish the report and create a dashboard.
   - **Steps**:
     1. In Power BI Desktop, click **Publish** and select your workspace.
     2. In the Power BI service, go to the workspace, open the report, and pin key visuals to a new dashboard (e.g., “Goodlift Dashboard”).
     3. Share the dashboard link or workspace with the coach (requires Pro license).
     4. Verify access by logging in with a different account.

### Deliverables
- **Explanation**: Why you chose Power BI (e.g., Synapse integration).
- **Screenshots**: Visuals for Top 20, Commitment Leaderboard, and sport management (e.g., meets per year).
- **Demonstration**: Show filtering in action.
- **Report**: Desktop .pbix file or PDF.
- **Link**: Power BI cloud deployment URL.

### Tips
- Use DirectQuery for real-time Synapse data.
- Match the uploaded image’s layout (table format, filters).
- Test filters to ensure all slicers work.
- Save the .pbix file before publishing.
- Add creative visuals to impress the coach.

### Learning Resources
- [Microsoft Power BI](https://docs.microsoft.com/en-us/power-bi/)
- [Data Sources in Power BI Desktop](https://docs.microsoft.com/en-us/power-bi/connect-data/desktop-data-sources)
- [Create Reports and Dashboards in Power BI](https://docs.microsoft.com/en-us/power-bi/create-reports/)
- [Use DirectQuery in Power BI Desktop](https://docs.microsoft.com/en-us/power-bi/connect-data/desktop-directquery-about)
- [What is the Power BI Service?](https://docs.microsoft.com/en-us/power-bi/service-overview)
- [Introduction to Dashboards for Power BI Designers](https://docs.microsoft.com/en-us/power-bi/create-reports/service-dashboards)

---

### Notes for Both Guides
- **Power BI Focus**: The guides emphasize Power BI for its Synapse integration and cloud deployment, aligning with Azure workflows.
- **Visualization Requirements**: Both guides ensure all required visuals (lifter performance, sport management) and filters are addressed, using the uploaded image as a reference.
- **Scalability**: Cloud deployment supports broader access, while DirectQuery ensures real-time data.
- **Creativity**: The student guide encourages additional insights (e.g., Wilks trends), while the coach guide evaluates their value.
- **Date Context**: The current date (08/03/2025) is used to set the 12-month filter.

Let me know if you need further refinements or additional details!
