# github-actions-azure-webapp

# GitHub Actions CI/CD to Azure

This project demonstrates how to build a simple **CI/CD pipeline using GitHub Actions** to test and deploy a Python web application to **Azure App Service**.

The main goal of the project is to understand how GitHub Actions can be used for real-world deployment automation while securely authenticating to Azure using **OpenID Connect (OIDC)**.

## Project Overview

The project contains a small Python Flask application with automated tests.

GitHub Actions is used to:

- Run automated tests
- Package the application
- Store the deployment package as an artifact
- Authenticate to Azure using OIDC
- Deploy the application to Azure App Service
- Perform a health check after deployment

## Project Structure

```text
github-actions-azure-webapp/
│
├── app.py
├── requirements.txt
│
├── tests/
│   └── test_app.py
│
├── .github/
│   └── workflows/
│       └── deploy.yml
│
└── README.md
```

## Technologies Used

- Git
- GitHub
- GitHub Actions
- Python
- Flask
- Pytest
- Microsoft Azure
- Azure App Service
- Microsoft Entra ID
- Azure RBAC
- OpenID Connect (OIDC)
- Azure CLI
- YAML

## CI/CD Pipeline

The GitHub Actions workflow follows this process:

```text
Git Push / Pull Request
        ↓
GitHub Actions
        ↓
Run Automated Tests
        ↓
Package Application
        ↓
Upload Artifact
        ↓
Production Environment
        ↓
Authenticate to Azure using OIDC
        ↓
Deploy to Azure App Service
        ↓
Application Health Check
```

Pull requests only run the testing stage.

Production deployment occurs when code is pushed or merged into the `main` branch.

## Application Endpoints

The application contains two endpoints.

Main endpoint:

```text
/
```

Returns:

```json
{
  "message": "Deployed using GitHub Actions"
}
```

Health endpoint:

```text
/health
```

Returns:

```json
{
  "status": "healthy"
}
```

The health endpoint is used by the GitHub Actions workflow to verify that the application is responding after deployment.

## Running the Application Locally

Clone the repository:

```bash
git clone https://github.com/abdulaiabdulwahab/github-actions-azure-webapp.git
```

Navigate into the project:

```bash
cd github-actions-azure-webapp
```

Install dependencies:

```bash
python -m pip install -r requirements.txt
```

Run the automated tests:

```bash
pytest -v
```

Run the Flask application:

```bash
python app.py
```

The application should be available locally at:

```text
http://localhost:8000
```

## Azure Resources

The project uses the following Azure resources:

```text
Azure Resource Group
        ↓
Azure App Service Plan
        ↓
Azure App Service
```

Microsoft Entra ID is also used to create an identity that GitHub Actions can use to access Azure.

## OIDC Authentication

Instead of storing a long-lived Azure client secret inside GitHub, this project uses **OpenID Connect federation**.

The authentication flow looks like this:

```text
GitHub Actions
      ↓
OIDC Token
      ↓
Microsoft Entra ID
      ↓
Temporary Azure Access Token
      ↓
Azure App Service
```

This provides a more secure authentication method because a permanent Azure password or client secret does not need to be stored in GitHub.

## Azure RBAC

The GitHub Actions service principal is assigned the:

```text
Website Contributor
```

role at the Azure App Service scope.

Example:

```bash
az role assignment create \
  --assignee-object-id "$SP_OBJECT_ID" \
  --assignee-principal-type ServicePrincipal \
  --role "Website Contributor" \
  --scope "$WEBAPP_ID"
```

This follows the principle of **least privilege** by giving the identity access only to the web application instead of the entire Azure subscription.

## Verify the Role Assignment

The Azure RBAC assignment can be verified with:

```bash
az role assignment list \
  --assignee-object-id "$SP_OBJECT_ID" \
  --scope "$WEBAPP_ID" \
  --output table
```

A successful configuration should show:

```text
Website Contributor
```

assigned to the service principal at the App Service scope.

## GitHub Variables

The workflow uses the following GitHub repository variables:

```text
AZURE_CLIENT_ID
AZURE_TENANT_ID
AZURE_SUBSCRIPTION_ID
AZURE_WEBAPP_NAME
```

These values allow the GitHub Actions workflow to identify the Azure application, tenant, subscription and App Service.

## GitHub Environment

A GitHub environment named:

```text
production
```

is used for the deployment job.

The environment can be configured with deployment protection rules such as manual approval before production deployment.

The deployment flow can therefore become:

```text
Test
 ↓
Package
 ↓
Approval
 ↓
Deploy
```

## GitHub Actions Concepts Practiced

This project demonstrates:

- Workflows
- Jobs
- Steps
- Runners
- Events
- Job dependencies
- Workflow artifacts
- GitHub environments
- Repository variables
- OIDC authentication
- Azure authentication
- Azure RBAC
- CI/CD pipelines
- Deployment validation
- Health checks

## Troubleshooting Practiced

The project includes troubleshooting scenarios such as:

- OIDC authentication failures
- Incorrect federated credential configuration
- Missing `id-token: write` permission
- Azure RBAC permission errors
- Incorrect App Service names
- Missing GitHub variables
- Python dependency installation failures
- Application deployment failures
- HTTP 500 errors
- Failed application health checks
- Deployment waiting for approval

Useful Azure commands include:

```bash
az account show --output table
```

```bash
az webapp list \
  --resource-group "$RG" \
  --output table
```

```bash
az role assignment list \
  --assignee-object-id "$SP_OBJECT_ID" \
  --scope "$WEBAPP_ID" \
  --output table
```

```bash
az webapp log tail \
  --resource-group "$RG" \
  --name "$WEBAPP"
```

## What I Learned

By completing this project, I gained practical experience building a real-world CI/CD pipeline with GitHub Actions.

I learned how to:

- Automatically test application code
- Create dependencies between pipeline jobs
- Pass deployment packages between jobs using artifacts
- Securely authenticate GitHub Actions to Azure using OIDC
- Configure Microsoft Entra ID federation
- Apply Azure RBAC using least privilege
- Deploy applications automatically to Azure App Service
- Use GitHub environments for production deployments
- Verify an application after deployment using health checks

## Pipeline Summary

```text
Developer
   ↓
GitHub
   ↓
GitHub Actions
   ↓
Test
   ↓
Package
   ↓
Artifact
   ↓
Production Environment
   ↓
OIDC
   ↓
Microsoft Entra ID
   ↓
Azure App Service
   ↓
Health Check
```

