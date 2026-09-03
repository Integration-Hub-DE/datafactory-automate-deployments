# Data Factory Automatic Deployment using general ARM template

### Overview

This project demonstrates a complete Continuous Integration and Continuous Deployment (CI/CD) implementation for Azure Data Factory (ADF) using GitHub Actions and Azure Resource Manager (ARM) templates.

The solution automates the deployment lifecycle of Azure Data Factory resources using general ARM Templates by validating ADF artifacts, generating ARM templates, storing deployment artifacts, and deploying changes to a target Data Factory environment whenever changes are merged into the repository's master branch.

The objective is to eliminate manual deployment activities and provide a repeatable, version-controlled, and auditable deployment process across environments.

### What This Repository Does
The GitHub workflow performs the following actions automatically:

Continuous Integration (CI)

- Checks out the GitHub repository.
- Installs the required npm packages used by Azure Data Factory build utilities.
- Validates Data Factory resources.
- Performs ARM template validation.
- Generates deployment-ready ARM templates.
- Publishes ARM templates as build artifacts.

Continuous Deployment (CD)

- Downloads generated deployment artifacts.
- Authenticates with Azure using a Service Principal.
- Deploys ARM templates to the destination resource group.
- Updates the target Azure Data Factory environment.
- Supports environment-specific overrides through deployment parameters and GitHub Secrets.

## Deployment Workflow

### Important Prerequisites:

- Source Azure Data Factory using Git integration
- GitHub repository connected to ADF
- Service Principal with Contributor access
- build folder containing package.json
- ARM template generation enabled

### Create Azure Service Principal

### Step 1 - Create an App Registration

1. Open the Azure Portal and Navigate to:

   ```text
   Microsoft Entra ID → App registrations → New registration
   ```

3. Provide the following:
   - Name: `<Your SPN Name>`
   - Supported account type: Single tenant (recommended)

4. Select **Register**.

---

### Step 2 - Create a Client Secret

1. Open the newly created App Registration.
2. Navigate to:

   ```text
   Certificates & Secrets
   → Client Secrets
   → New Client Secret
   ```

3. Enter:
   - Description: `GitHub ADF Deployment Secret`
   - Expiration: According to your organization's policy

4. Select **Add**.

5. Immediately copy and save the secret value.

> **Important:** The secret value is displayed only once and cannot be viewed again after leaving the page.

---

### Step 3 - Grant Contributor Access

The Service Principal must have permissions to deploy Azure Data Factory resources.

1. Navigate to:

   ```text
   Subscription
   → Access Control (IAM)
   → Add Role Assignment
   ```

2. Select:

   ```text
   Role: Contributor
   ```

3. Select:

   ```text
   Assign access to:
   User, Group or Service Principal
   ```

4. Search for the App Registration created earlier.

5. Select **Review + Assign**.

> Alternatively, Contributor access can be granted at the Resource Group level if deployment should be limited to a specific environment.

---

### Step 4 - Collect Required Values

The following values will be required by the GitHub workflow.

| Value | Location |
|---------|---------|
| Azure Client ID | App Registration → Overview → Application (client) ID |
| Azure Tenant ID | App Registration → Overview → Directory (tenant) ID |
| Azure Client Secret | App Registration → Certificates & Secrets |
| Azure Subscription ID | Subscription → Overview |

---

### Step 5 - Configure GitHub Repository Secrets

Navigate to:

```text
GitHub Repository
→ Settings
→ Secrets and variables
→ Actions
```

Create the following repository secrets:

| Secret Name | Description |
|------------|-------------|
| AZURE_CLIENT_ID | Application (Client) ID |
| AZURE_CLIENT_SECRET | Client Secret Value |
| AZURE_TENANT_ID | Directory (Tenant) ID |
| AZURE_SUBSCRIPTION_ID | Azure Subscription ID |
| AZURE_RESOURCEGROUP_NAME | Resource Group hosting Azure Data Factory |
| SOURCE_DATA_FACTORY | Source Azure Data Factory Name |
| DESTINATION_DATA_FACTORY | Destination Azure Data Factory Name |
| DESTINATION_DATA_FACTORY_LOCATION | Deployment Location |

---

### Authentication Flow

```text
GitHub Actions
      |
      v
Azure Service Principal
      |
      v
Microsoft Entra ID Authentication
      |
      v
Azure Subscription
      |
      v
Azure Resource Group
      |
      v
Azure Data Factory Deployment
```








## Build Folder

The build folder contains:

- package.json
- ADF Utilities package
- Build and export commands used for ARM generation

GitHub Actions executes npm commands from this location to validate and export ARM templates.

## Repository Secrets

GitHub itself cannot deploy resources to Azure unless it authenticates with Azure. A Service Principal is created in Azure AD (Microsoft Entra ID), and this Service Principal serves as the identity that GitHub uses for authentication.

Assign the Contributor role to the Service Principal. The GitHub workflow then uses GitHub Repository Secrets to securely authenticate to Azure.

Configure the following GitHub Secrets before running the workflow:

| Secret | Description |
|----------|-------------|
| AZURE_CLIENT_ID | Service Principal Client Id |
| AZURE_CLIENT_SECRET | Service Principal Secret |
| AZURE_SUBSCRIPTION_ID | Azure Subscription Id |
| AZURE_TENANT_ID | Microsoft Entra Tenant Id |
| AZURE_RESOURCEGROUP_NAME | Resource Group Hosting ADF |
| SOURCE_DATA_FACTORY | Source ADF Name |
| DESTINATION_DATA_FACTORY | Destination ADF Name |
| DESTINATION_DATA_FACTORY_LOCATION | Destination ADF Region |

Step 1 - Development

Developers create or modify:

- Pipelines
- Datasets
- Linked Services
- Triggers
- Data Flows
- Global Parameters

within the Development Azure Data Factory.

Step 2 - Source Control

Changes are committed and pushed to the GitHub repository.

Step 3 - Workflow Trigger

The GitHub workflow automatically starts when changes are merged into the configured branch.

Step 4 - Validation

ADF build utilities validate the Data Factory structure and verify deployment readiness.

Step 5 - ARM Template Generation

Deployment-ready ARM templates are generated and stored as workflow artifacts.

Step 6 - Deployment

The generated ARM template is deployed to the target Azure Data Factory using Azure Resource Manager deployment.

Step 7 - Verification

Deployment results can be reviewed directly from the GitHub Actions execution logs.


# CICD Deployment Best Practices

## Development Factory Only for Git Integration
Use Git integration only for the development Data Factory. Test, UAT, and Production environments should receive changes through the deployment pipeline rather than direct source control integration. This ensures consistency and controlled promotion across environments.

## Automate Trigger Management During Deployments
Triggers may need to be stopped before deployment and restarted after deployment to avoid deployment failures and unintended executions. Microsoft provides deployment scripts that can automate trigger handling and cleanup activities during CI/CD processes.

## Maintain Consistent Integration Runtime Configuration
When promoting resources across environments, Integration Runtime names, types, and configurations should remain consistent. This is especially important for Self-Hosted Integration Runtime deployments across Development, Test, and Production environments.

## Parameterize Environment-Specific Settings
Resources such as Data Factory names, Key Vault names, endpoints, and connection configurations should be parameterized to enable seamless deployments across multiple environments.

## Use Dedicated Key Vaults per Environment
Store secrets in environment-specific Azure Key Vaults and reference them through deployment parameters. Keeping secret names consistent across environments simplifies deployment and reduces configuration complexity.

## Include Global Parameters in ARM Templates
Global Parameters should be included in ARM template deployments to ensure configuration consistency across environments and simplify CI/CD implementations. 

## Follow Consistent Naming Standards
Avoid spaces in ADF resource names. Prefer using underscores (_) or hyphens (-) for improved compatibility and maintainability.

## Keep the Repository Clean
Only maintain files required for Azure Data Factory source control and deployment. Unnecessary backup or temporary files may lead to repository maintenance issues and deployment complications.

## Use Controlled Feature Rollout Strategies
When deploying changes that should not immediately execute in higher environments, consider feature toggles or environment-driven logic using Global Parameters and conditional execution patterns. This enables deployment without immediate exposure of new functionality.

## Support Hotfix Deployments
For urgent production issues, maintain a controlled hotfix deployment process so that critical fixes can be promoted independently without requiring a full release cycle. 


# Current Scope
This repository focuses on:

- GitHub Actions based CI/CD
- ARM template generation
- ARM template deployment
- Environment parameterization
- Automated deployment of Azure Data Factory assets


Supported Azure Data Factory Resources

- Pipelines
- Datasets
- Linked Services
- Triggers
- Data Flows
- Global Parameters
- Integration Runtime References
- Managed Private Endpoints (when appropriately parameterized)


# Known Limitations

## Selective Deployment Is Not Supported
Azure Data Factory deployments operate on the complete factory metadata. Deployments are intended to promote the entire validated factory state rather than individual resources. Microsoft recommends using a dedicated hotfix process for exceptional production scenarios.

## Publishing from Non-Collaboration Branches
Publishing and deployment processes are designed around the configured collaboration branch strategy and not private development branches.

## Resource Dependency Requirements
ADF resources are highly interconnected. Pipelines, datasets, triggers, and linked services have dependency relationships that must remain intact during deployments.

# References
