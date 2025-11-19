# Backstage Catalog Integration Workflow

This reusable workflow creates a Backstage `catalog.yaml` file in repositories across your organization if it doesn't already exist.

## Overview

The workflow:
1. Checks if `catalog.yaml` exists in the main/default branch
2. If it doesn't exist:
   - Creates a new branch named `{reponame}-backstage-integration`
   - Creates a `catalog.yaml` file with default Backstage metadata
   - Commits the file
   - Creates a Pull Request for review

## Usage

### As a Reusable Workflow

To use this workflow in any repository, create a workflow file (e.g., `.github/workflows/setup-backstage.yml`) with the following content:

```yaml
name: Setup Backstage Catalog

on:
  workflow_dispatch:  # Manual trigger
  # Or schedule it to run automatically
  # schedule:
  #   - cron: '0 0 * * 0'  # Weekly on Sunday at midnight

jobs:
  setup-catalog:
    uses: thome-legacy-technologies/ruleset/.github/workflows/backstage-catalog-integration.yml@main
    secrets:
      GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### With Custom Configuration

You can customize the catalog file path and content:

```yaml
name: Setup Backstage Catalog

on:
  workflow_dispatch:

jobs:
  setup-catalog:
    uses: thome-legacy-technologies/ruleset/.github/workflows/backstage-catalog-integration.yml@main
    with:
      catalog_file_path: 'backstage/catalog.yaml'
      base_branch: 'main'
      catalog_content: |
        apiVersion: backstage.io/v1alpha1
        kind: Component
        metadata:
          name: my-service
          description: Custom service description
        spec:
          type: service
          lifecycle: production
          owner: platform-team
    secrets:
      GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `catalog_file_path` | Path to the catalog.yaml file | No | `catalog.yaml` |
| `catalog_content` | Custom content for catalog.yaml | No | Default template |
| `base_branch` | Base branch to check and create PR against | No | `main` |

## Secrets

| Secret | Description | Required |
|--------|-------------|----------|
| `GH_TOKEN` | GitHub token for creating PR (uses `GITHUB_TOKEN` if not provided) | No |

## Default Catalog Template

If no custom content is provided, the workflow creates a catalog.yaml with the following structure:

```yaml
apiVersion: backstage.io/v1alpha1
kind: Component
metadata:
  name: {repository-name}
  description: {repository-name} component
  annotations:
    github.com/project-slug: {org}/{repository-name}
spec:
  type: service
  lifecycle: production
  owner: team
```

## Organization-Wide Deployment

To deploy this across all repositories in your organization:

1. **Store this workflow in a central repository** (like this `ruleset` repository)
2. **Create a workflow in each target repository** that calls this reusable workflow
3. **Use GitHub's organization-level workflow templates** to automatically add the caller workflow to new repositories

### Option 1: Manual Addition

Add the caller workflow to each repository manually or via script.

### Option 2: Organization Workflow Templates

Create a `.github/workflow-templates` directory in a `.github` repository in your organization with a workflow template that calls this reusable workflow.

### Option 3: Centralized Workflow Dispatch

Create a separate workflow that uses the GitHub API to trigger this workflow across multiple repositories.

## Permissions

The workflow requires the following permissions:
- `contents: write` - To create branches and commit files
- `pull-requests: write` - To create pull requests

These are typically available through the default `GITHUB_TOKEN`.

## Troubleshooting

### PR Creation Fails

If the PR creation fails, ensure:
1. The `GITHUB_TOKEN` has sufficient permissions
2. The default branch protection rules allow the bot to create branches
3. The repository allows GitHub Actions to create pull requests

### Branch Already Exists

If the branch `{reponame}-backstage-integration` already exists, the workflow will:
1. Check out the existing branch
2. Update or create the catalog.yaml file
3. Commit and push changes
4. The PR may already exist

### Catalog File Already Exists

If `catalog.yaml` already exists in the default branch, the workflow will:
1. Skip all operations
2. Report that no action is needed
3. Exit successfully

## Example Scenarios

### Scenario 1: New Repository Setup
- Trigger: Manual workflow dispatch
- Result: Creates branch, adds catalog.yaml, creates PR

### Scenario 2: Existing Catalog
- Trigger: Manual workflow dispatch  
- Result: Detects existing file, no action taken

### Scenario 3: Custom Location
- Trigger: Workflow with custom `catalog_file_path: 'docs/catalog.yaml'`
- Result: Creates catalog.yaml in docs/ directory

## Contributing

To improve this workflow:
1. Fork the repository
2. Make changes to the workflow file
3. Test in a test repository
4. Submit a pull request
