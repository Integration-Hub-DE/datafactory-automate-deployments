# Data Factory Automatic Deployment using general ARM template

## Overview

This project demonstrates a complete Continuous Integration and Continuous Deployment (CI/CD) implementation for Azure Data Factory (ADF) using GitHub Actions and Azure Resource Manager (ARM) templates.

The solution automates the deployment lifecycle of Azure Data Factory resources using general ARM Templates by validating ADF artifacts, generating ARM templates, storing deployment artifacts, and deploying changes to a target Data Factory environment whenever changes are merged into the repository's master branch.

The objective is to eliminate manual deployment activities and provide a repeatable, version-controlled, and auditable deployment process across environments.

## What This Repository Does
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

## Repository Secrets
The workflow uses GitHub Repository Secrets for Azure authentication.


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
Publishing and deployment processes are designed around the configured collaboration branch strategy and not private development branches. citeturn17search24

## Resource Dependency Requirements
ADF resources are highly interconnected. Pipelines, datasets, triggers, and linked services have dependency relationships that must remain intact during deployments.

# References
