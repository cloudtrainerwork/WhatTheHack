Below is a step-by-step guide for both the **Coach’s Guide** and the **Student’s Guide** for Challenge 1: Gathering Open Powerlifting Data, focusing on Azure and Azure Synapse Analytics. The guides are tailored to the requirements provided, ensuring clarity for both the coach (who oversees the process) and the student (who executes the tasks). The focus is on selecting an appropriate storage service, uploading data securely, managing access keys, and leveraging Azure Active Directory (AAD) for access control.

---

## Coach’s Guide: Challenge 1 - Gathering Open Powerlifting Data

### Objective
Guide students through selecting an Azure storage service as a landing zone for Open Powerlifting data, uploading the data, securing access keys, and using Azure Active Directory (AAD) for access control, preparing for future relational database loading in Azure Synapse Analytics.

### Preparation
- Ensure students have access to an Azure subscription with permissions to create resources (e.g., Storage Account, Key Vault, Synapse Workspace).
- Verify students have the Open Powerlifting dataset URL: [openpowerlifting-latest.zip](https://github.com/sstangl/openpowerlifting-static/raw/gh-pages/openpowerlifting-latest.zip).
- Confirm students understand the security requirements:
  - No public (anonymous) access to data.
  - AAD-based access control for folders and files.
- Provide access to learning resources (listed in the challenge description).
- Set up a Synapse Workspace in advance (optional, for context on future challenges).

### Step-by-Step Guidance
1. **Explain Storage Service Selection**
   - **Task**: Students must choose a storage service (e.g., Azure Blob Storage or Azure Data Lake Storage Gen2) and justify their choice.
   - **Key Points to Emphasize**:
     - Azure Data Lake Storage Gen2 is preferred for its hierarchical namespace, which supports fine-grained access control (ACLs) using AAD, making it ideal for analytics workloads in Synapse.
     - Blob Storage is simpler but less optimized for analytics due to flat namespace limitations.
     - Highlight cost, scalability, and Synapse integration (Data Lake Storage Gen2 is natively supported).
   - **Evaluation**: Check if the student’s explanation considers security, scalability, and compatibility with Synapse for future relational database loading.

2. **Upload Data to Storage Service**
   - **Task**: Students upload the Open Powerlifting dataset to their chosen storage service.
   - **Key Points to Emphasize**:
     - Use Azure Portal, Azure CLI, or AzCopy to upload the dataset.
     - Ensure the storage account is configured with private access (no public access).
     - Verify the dataset is stored in a container or filesystem (e.g., `openpowerlifting-data`).
   - **Evaluation**: Confirm the data is uploaded and accessible only to authorized users.

3. **Secure Access Keys with Azure Key Vault**
   - **Task**: Students store storage account access keys in Azure Key Vault and explain how to reference them securely.
   - **Key Points to Emphasize**:
     - Avoid hardcoding keys in scripts or connection strings.
     - Use Key Vault to store keys and reference them via managed identities or service principals in Synapse.
     - Demonstrate retrieving keys programmatically (e.g., via Azure CLI or SDK).
   - **Evaluation**: Ensure students explain Key Vault usage and avoid exposing keys in their solution.

4. **Implement AAD-Based Access Control**
   - **Task**: Students configure AAD-based access for the storage service and explain its benefits.
   - **Key Points to Emphasize**:
     - Assign roles (e.g., Storage Blob Data Contributor) to AAD users or groups.
     - For Data Lake Storage Gen2, use ACLs for folder/file-level permissions.
     - Highlight security (centralized identity management) and manageability (role-based access control).
   - **Evaluation**: Verify students assign roles correctly and articulate security benefits.

### Success Criteria Checklist
- [ ] Student justifies storage service choice (e.g., Data Lake Storage Gen2 for Synapse compatibility).
- [ ] Data is uploaded to the storage service and is not publicly accessible.
- [ ] Access keys are stored in Key Vault, with a clear explanation of secure access.
- [ ] AAD-based access control is implemented, with benefits explained (security, manageability).

### Common Pitfalls to Watch For
- Choosing Blob Storage without considering Synapse integration.
- Enabling public access to the storage account.
- Hardcoding access keys in scripts or connection strings.
- Misconfiguring AAD roles or ACLs, leading to access issues.

### Additional Notes
- Encourage students to use Azure Portal for initial setup but explore Azure CLI for automation.
- Discuss how Data Lake Storage Gen2’s hierarchical namespace supports analytics workloads in Synapse.
- Prepare students for Challenge 2 by mentioning that the data will be loaded into a relational database (e.g., Synapse SQL).

---

## Student’s Guide: Challenge 1 - Gathering Open Powerlifting Data

### Introduction
In this challenge, you’ll set up an Azure storage service as a landing zone for the Open Powerlifting dataset, ensuring it’s secure and ready for analysis in Azure Synapse Analytics. You’ll choose a storage service, upload the data, secure access keys, and configure Azure Active Directory (AAD) for access control.

### Prerequisites
- Azure subscription with permissions to create resources.
- Access to the Open Powerlifting dataset: [openpowerlifting-latest.zip](https://github.com/sstangl/openpowerlifting-static/raw/gh-pages/openpowerlifting-latest.zip).
- Basic understanding of Azure Portal, Azure CLI, or AzCopy.
- Review the learning resources provided.

### Step-by-Step Instructions

1. **Choose a Storage Service**
   - **Task**: Select a storage service (Azure Blob Storage or Azure Data Lake Storage Gen2) and explain your choice to the coach.
   - **Steps**:
     1. Research Azure Blob Storage and Azure Data Lake Storage Gen2 using the provided resources.
     2. Recommended: Choose Data Lake Storage Gen2 because:
        - It supports hierarchical namespaces for folder/file-level access control.
        - It integrates seamlessly with Azure Synapse Analytics for future challenges.
        - It meets security requirements with AAD-based access control.
     3. Prepare a brief explanation (2-3 sentences) for your coach, covering:
        - Why you chose the service (e.g., Synapse compatibility, security features).
        - How it supports future relational database loading.
   - **Example Explanation**:
     > I chose Azure Data Lake Storage Gen2 because its hierarchical namespace allows fine-grained access control using Azure Active Directory, which meets the security requirements. It’s also optimized for analytics workloads in Azure Synapse, making it ideal for loading data into a relational database later.

2. **Set Up the Storage Account**
   - **Task**: Create a storage account and configure it for private access.
   - **Steps**:
     1. In the Azure Portal, navigate to **Storage Accounts** > **Create**.
     2. Configure the storage account:
        - **Resource Group**: Create or select a resource group (e.g., `powerlifting-rg`).
        - **Storage Account Name**: Choose a unique name (e.g., `powerliftingdata`).
        - **Performance**: Standard.
        - **Redundancy**: Locally-redundant storage (LRS) for cost efficiency.
        - **Advanced**: Enable **Hierarchical namespace** for Data Lake Storage Gen2.
     3. In the **Networking** tab, select **Disable public network access** to meet security requirements.
     4. Create the storage account and create a container or filesystem (e.g., `openpowerlifting-data`).

3. **Upload the Open Powerlifting Data**
   - **Task**: Upload the dataset to the storage account.
   - **Steps**:
     1. Download the dataset: [openpowerlifting-latest.zip](https://github.com/sstangl/openpowerlifting-static/raw/gh-pages/openpowerlifting-latest.zip).
     2. Use one of the following methods to upload:
        - **Azure Portal**: Navigate to the container/filesystem, select **Upload**, and upload the zip file.
        - **AzCopy** (recommended for automation):
          ```bash
          azcopy copy "https://github.com/sstangl/openpowerlifting-static/raw/gh-pages/openpowerlifting-latest.zip" "https://<storage-account-name>.dfs.core.windows.net/openpowerlifting-data/openpowerlifting-latest.zip" --sas-token "<sas-token>"
          ```
          - Generate a SAS token in the Azure Portal (Storage Account > Shared access signature).
        - **Azure CLI**:
          ```bash
          az storage blob upload --account-name <storage-account-name> --container-name openpowerlifting-data --file openpowerlifting-latest.zip --name openpowerlifting-latest.zip --auth-mode login
          ```
     3. Verify the file is uploaded in the container/filesystem.

4. **Secure Access Keys with Azure Key Vault**
   - **Task**: Store the storage account access key in Azure Key Vault and explain how to access it securely.
   - **Steps**:
     1. Create a Key Vault:
        - In the Azure Portal, navigate to **Key Vaults** > **Create**.
        - Set a unique name (e.g., `powerlifting-kv`), select the same resource group, and create.
     2. Add the storage account key to Key Vault:
        - In the storage account, go to **Access Keys**, copy `key1`.
        - In the Key Vault, go to **Secrets** > **Generate/Import**, create a secret (e.g., `StorageAccountKey`), and paste the key.
     3. Configure access:
        - Assign your AAD identity the **Key Vault Secrets User** role in the Key Vault’s **Access control (IAM)**.
     4. Explain to the coach:
        - Key Vault prevents hardcoding keys in scripts.
        - Synapse can use managed identities to retrieve keys securely.
        - Example CLI to retrieve the key:
          ```bash
          az key-vault secret show --vault-name powerlifting-kv --name StorageAccountKey --query value -o tsv
          ```
   - **Example Explanation**:
     > I stored the storage account key in Azure Key Vault to avoid hardcoding it in scripts, which improves security. Synapse can use a managed identity to retrieve the key securely, and I can access it programmatically using Azure CLI or SDKs without exposing it.

5. **Configure AAD-Based Access Control**
   - **Task**: Set up AAD-based access for the storage account and explain its benefits.
   - **Steps**:
     1. Assign a role to your AAD identity:
        - In the storage account, go to **Access control (IAM)** > **Add role assignment**.
        - Assign **Storage Blob Data Contributor** to your AAD user or group.
     2. For Data Lake Storage Gen2, set folder/file-level permissions:
        - Navigate to the container/filesystem (`openpowerlifting-data`).
        - Select the folder/file, go to **Manage ACL**, and grant your AAD user **Read/Write/Execute** permissions.
     3. Explain to the coach:
        - **Security**: AAD centralizes identity management, reducing reliance on shared keys.
        - **Manageability**: Role-based access control (RBAC) and ACLs allow granular permissions.
   - **Example Explanation**:
     > Using AAD, I assigned the Storage Blob Data Contributor role to my user for broad access and set ACLs for folder-level control in Data Lake Storage Gen2. This ensures secure, centralized identity management and simplifies permission management compared to key-based access.

### Deliverables
- **Explanation**: Why you chose the storage service (e.g., Data Lake Storage Gen2).
- **Screenshot**: Show the uploaded dataset in the Azure Portal.
- **Explanation**: How you stored and access keys using Key Vault.
- **Explanation**: Benefits of AAD-based access control.

### Tips
- Use Data Lake Storage Gen2 for better Synapse integration.
- Double-check that public access is disabled in the storage account.
- Test AAD access by logging in with a different user to ensure permissions work.
- Save your Azure CLI commands for automation in future challenges.

### Learning Resources
- [Introduction to Azure Blob Storage](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blobs-introduction)
- [Introduction to Azure Data Lake Storage Gen2](https://docs.microsoft.com/en-us/azure/storage/blobs/data-lake-storage-introduction)
- [Manage storage account keys with Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/secrets/overview-storage-keys)
- [AAD Authentication with Synapse SQL](https://docs.microsoft.com/en-us/azure/synapse-analytics/sql/active-directory-authentication)

---

### Notes for Both Guides
- **Synapse Context**: The guides assume the data will be used in Synapse for analytics (e.g., Synapse SQL or Spark). Data Lake Storage Gen2 is emphasized due to its native integration.
- **Security Focus**: Both guides reinforce the importance of private access and AAD-based control to meet the challenge’s requirements.
- **Scalability**: The coach’s guide helps evaluate the student’s choice based on scalability and future needs.
- **Practicality**: The student’s guide provides actionable steps with examples (e.g., CLI commands) to ensure success.

Let me know if you need further refinements or additional details for specific Azure services!
