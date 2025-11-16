# UiPath End-to-End CI/CD Pipeline  
[![CI](https://github.com/Kenneth-Knapp/uipath-cicd-pipeline/actions/workflows/ci.yml/badge.svg)](https://github.com/Kenneth-Knapp/uipath-cicd-pipeline/actions/workflows/ci.yml)

An end-to-end UiPath DevOps pipeline using GitHub Actions, UiPath CLI, and UiPath Cloud Orchestrator.
This project demonstrates how modern RPA development follows SDLC and CI/CD best practices, including automated packaging, analysis, artifact management, deployment, and simulated environment promotion.

---

## Objectives

### Source Control  
- Full Git + GitHub workflow  
- Feature branches → Pull Request → main

### Continuous Integration (CI)  
Triggered on push to any non-main branch:
1. Package UiPath project using UiPath CLI  
2. Run Workflow Analyzer with strict rules  
3. Upload artifacts  
4. Deploy package into Dev folder in UiPath Orchestrator  
5. Fail if version already exists (enforces version control)

### Continuous Delivery (CD)  
Triggered when a Pull Request into main is merged:
1. Fetch artifact created during CI  
2. Extract package name and version  
3. Output a full deployment summary  
4. Simulate PROD promotion (actual PROD deployment is not possible on Community licensing)

---

## Architecture Overview

Feature Branch Push  
│  
▼  
[CI Build: Pack → Analyze → Store Artifact → Deploy to DEV]  
│  
PR → main  
│  
Merge  
│  
[PROD Promotion Simulation: Retrieve Artifact → Parse Version → Summary]

---

## Key Features

### Automated Packaging  
Uses UiPath CLI to pack the project.

### Automated Workflow Analyzer  
- Stops on rule violations  
- Treats warnings as errors  
- Ignores ST‑USG‑034

### Authentication via GitHub Secrets  
Uses:
UIP_ORCH_URL, UIP_TENANT_NAME, UIP_ORG_NAME,  
UIP_CLIENT_ID, UIP_CLIENT_SECRET, UIP_APP_SCOPE,  
UIP_FOLDER_DEV, UIP_FOLDER_PROD  

### DEV Deployment (Real)  
Packages are deployed to the Dev folder and update process entries.  
Fails if package version already exists.

### PROD Promotion (Simulated)  
Community tenants cannot host multiple environments.
Instead:
- Artifact is retrieved  
- Package info extracted  
- Deployment summary printed  
- Protected GitHub prod environment used for approval

---

## Trigger Model

### Push to Feature Branch  
Runs:
- pack  
- analyze  
- publish-dev  

### Pull Request → main  
Allows reviewers to validate Dev results.

### Merge into main  
Runs:
- promote-to-prod (simulation)

---

## Project Structure

uipath-cicd-pipeline/  
 ├── .github/workflows/ci.yml  
 ├── UiPathCICD/  
 ├── dist/  
 └── README.md

---

## Versioning Behavior

- Version is defined in project.json  
- If version exists, deployment fails  
- User must update version before re-running CI  

---

## Requirements

- UiPath Studio 2024+  
- UiPath Community Cloud  
- GitHub Actions  
- External App configured for authentication  
- GitHub Secrets populated  
- .NET 8 (handled in workflow)

---

## How to Use the Pipeline

1. Create a feature branch  
2. Update UiPath project  
3. Push commits  
4. CI deploys to DEV  
5. Create Pull Request → main  
6. Merge  
7. PROD simulation runs

---

## Summary

This project demonstrates a complete UiPath DevOps workflow using free Community tooling:
- GitHub Actions  
- UiPath CLI  
- Cloud Orchestrator  
- Secure external-app authentication  
- Quality gates  
- Version governance  
- Automated DEV deployment  
- PR-driven PROD promotion (simulated)

A strong template for enterprise-ready automation.

