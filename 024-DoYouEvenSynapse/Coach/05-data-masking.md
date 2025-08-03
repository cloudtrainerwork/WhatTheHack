# Challenge 5: Mask Data for Privacy

This challenge focuses on implementing data masking in Azure Synapse Analytics to protect sensitive personally identifiable information (PII), specifically first names and last names. The key concepts are dynamic data masking (DDM) for privacy and granular control over who can see the unmasked data. Dynamic data masking obscures sensitive data in query results for non-privileged users without changing the underlying data in the database.

As a coach, your role is to guide students through the setup, explain the "why" behind each step, and demonstrate the masking in action. Emphasize that DDM is a security feature in dedicated SQL pools (not serverless) that helps comply with privacy regulations like GDPR. Highlight potential pitfalls, such as masking not applying to privileged users (e.g., admins) by default, and the importance of testing with different user permissions.

The student guide (which you can provide separately) should include simplified instructions, SQL snippets, and prompts for them to experiment. For the coach's guide here, we'll break it down step by step with explanations, SQL examples, and coaching tips.

## Prerequisites
- An Azure Synapse workspace with a dedicated SQL pool provisioned.
- Access to Azure Synapse Studio or SQL Server Management Studio (SSMS) to run queries.
- Sample data: If not already available, we'll create a simple table with names.
- Admin privileges for the coach to set up masking and permissions.

Coaching Tip: Ensure students have contributor access to the workspace but start with limited database permissions to simulate real-world scenarios. If they're new to Synapse, review connecting to the SQL pool via Synapse Studio.

## Step 1: Understand Dynamic Data Masking Concepts
Explain to students:
- **What is DDM?** It's a policy-based security feature that masks data in real-time during queries. The data remains unchanged in storage; masking happens at the database engine level.
- **Focus on Names:** First and last names are PII. We'll use masking functions to obscure them (e.g., show only the first letter and replace the rest with 'X').
- **Masking Functions for Strings (Suitable for Names):**
  - **Default:** Replaces the entire string with 'XXXX' (simple but not granular).
  - **Partial:** Custom masking, e.g., partial(prefix_length, padding_char, suffix_length). Ideal for names to retain some usability (e.g., show first letter for sorting).
  - **Email:** Not ideal for names but could be used if names are in email format.
- **Granular Control:** Masking applies to all users except those with UNMASK permission. You can grant/revoke this permission to roles or users for fine-tuned access. Admins (db_owner) see unmasked data by default.
- **Limitations:** DDM doesn't encrypt data; it's for query results only. It doesn't work on encrypted columns or in serverless SQL pools. Always test with non-admin users.

Coaching Tip: Use analogies—like "blurring faces in photos"—to make it relatable. Ask students: "Why might we want partial masking instead of full obscuring?"

## Step 2: Set Up a Sample Table
If a table with names doesn't exist, create one. Run this SQL in the dedicated SQL pool:

```sql
CREATE TABLE dbo.Users (
    UserID INT IDENTITY(1,1) PRIMARY KEY,
    FirstName VARCHAR(50),
    LastName VARCHAR(50),
    Email VARCHAR(100)
);

-- Insert sample data
INSERT INTO dbo.Users (FirstName, LastName, Email)
VALUES 
('John', 'Doe', 'john.doe@example.com'),
('Jane', 'Smith', 'jane.smith@example.com'),
('Alice', 'Johnson', 'alice.johnson@example.com');
```

Coaching Tip: Have students query the table first (`SELECT * FROM dbo.Users;`) to see unmasked data. Explain schema design: Use VARCHAR for names to match masking functions.

## Step 3: Apply Data Masking Rules
Apply masks to FirstName and LastName columns. Use the PARTIAL function for granular control (show first letter, mask the rest with 'X', no suffix).

Run this SQL:

```sql
ALTER TABLE dbo.Users
ALTER COLUMN FirstName ADD MASKED WITH (FUNCTION = 'partial(1, "X", 0)');

ALTER TABLE dbo.Users
ALTER COLUMN LastName ADD MASKED WITH (FUNCTION = 'partial(1, "X", 0)');
```

- **Explanation:** `partial(1, "X", 0)` means: Show the first 1 character, replace the middle with 'X', show 0 characters at the end. For "John", it becomes "JXXX".
- Alternative: For full masking, use `default()`: `ALTER COLUMN FirstName ADD MASKED WITH (FUNCTION = 'default()');` (shows "xxxx").

To view existing masks:

```sql
SELECT 
    name AS TableName,
    c.name AS ColumnName, 
    masking_function
FROM sys.masked_columns AS c
JOIN sys.tables AS t ON c.[object_id] = t.[object_id]
WHERE is_masked = 1;
```

Coaching Tip: Demonstrate running a SELECT query as an admin—you'll see unmasked data. Stress that masks are applied per column and can be altered or dropped later (e.g., `ALTER COLUMN FirstName DROP MASKED;`).

## Step 4: Demonstrate Masking with Non-Privileged Users
Create a test user without UNMASK permission to show masking in action.

```sql
-- Create a test user (run as admin)
CREATE USER TestUser WITHOUT LOGIN;
GRANT SELECT ON dbo.Users TO TestUser;

-- Execute as TestUser to see masked data
EXECUTE AS USER = 'TestUser';
SELECT * FROM dbo.Users;
REVERT;  -- Switch back to admin
```

- **Expected Output (Masked):**
  | UserID | FirstName | LastName | Email              |
  |--------|-----------|----------|--------------------|
  | 1      | Jxxx     | Dxxx    | john.doe@example.com |
  | 2      | Jxxx     | Sxxx    | jane.smith@example.com |
  | 3      | Axxx     | Jxxx    | alice.johnson@example.com |

Coaching Tip: Guide students to run this and discuss: "What happens if you query as admin vs. TestUser?" This highlights that DDM doesn't affect storage—exporting data or backups remain unmasked.

## Step 5: Implement Granular Control with Permissions
Show how to grant exceptions for specific users/roles.

```sql
-- Grant UNMASK to TestUser
GRANT UNMASK TO TestUser;

-- Test again
EXECUTE AS USER = 'TestUser';
SELECT * FROM dbo.Users;  -- Now unmasked
REVERT;

-- Revoke if needed
REVOKE UNMASK FROM TestUser;
```

- **Explanation:** UNMASK can be granted at database level (all masked columns) or schema level. For more granularity, use database roles: `CREATE ROLE MaskedViewers; ALTER ROLE MaskedViewers ADD MEMBER TestUser; GRANT UNMASK TO MaskedViewers;`.

Coaching Tip: Role-play scenarios: "In a team, analysts see masked names, but managers see full data." Warn about over-granting permissions and audit logs in Azure for compliance.

## Step 6: Test and Validate
- Query as different users.
- Try edge cases: Empty strings, long names, or special characters.
- Monitor via Azure portal: Go to Synapse workspace > SQL pool > Data masking (if available in UI, though most config is SQL-based).

Coaching Tip: Encourage students to modify the partial function (e.g., show last 2 letters with `partial(0, "X", 2)`) and re-test. Discuss when to use DDM vs. other features like column-level security or encryption.

## Step 7: Clean Up (Optional)
```sql
-- Drop masks
ALTER TABLE dbo.Users ALTER COLUMN FirstName DROP MASKED;
ALTER TABLE dbo.Users ALTER COLUMN LastName DROP MASKED;

-- Drop user and table if needed
DROP USER TestUser;
DROP TABLE dbo.Users;
```

## Additional Notes for Coaches
- **Common Pitfalls:** Students might forget to revert after EXECUTE AS, leading to permission errors. Masking doesn't apply to WHERE clauses—queries can still filter on unmasked values internally.
- **Extensions:** If time allows, integrate with Azure AD groups for permissions or combine with row-level security.
- **Resources:** Direct students to Microsoft Docs: [Dynamic Data Masking in Synapse](https://learn.microsoft.com/en-us/azure/synapse-analytics/sql/develop-dynamic-data-masking). For hands-on, use the Synapse sample datasets if available.
- **Assessment:** Ask students to mask an additional column (e.g., Email with 'email()') and demonstrate granular control to a "colleague" (you).

This completes the coach's guide for Challenge 5. Adapt the student guide by removing explanations and focusing on action steps/SQL for self-paced learning.
