## Data Factory Automatic Deployment using general ARM template

Overview

This project demonstrates a complete Continuous Integration and Continuous Deployment (CI/CD) implementation for Azure Data Factory (ADF) using GitHub Actions and Azure Resource Manager (ARM) templates.

The solution automates the deployment lifecycle of Azure Data Factory resources using general ARM Templates by validating ADF artifacts, generating ARM templates, storing deployment artifacts, and deploying changes to a target Data Factory environment whenever changes are merged into the repository's master branch.

The objective is to eliminate manual deployment activities and provide a repeatable, version-controlled, and auditable deployment process across environments.
