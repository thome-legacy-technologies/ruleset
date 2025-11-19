# ruleset

Organization-wide reusable workflows and rulesets for Thome Legacy Technologies.

## Workflows

### Backstage Catalog Integration

A reusable workflow that automatically creates Backstage `catalog.yaml` files in repositories that don't have them.

**Features:**
- Checks if `catalog.yaml` exists in the main/default branch
- Creates a branch named `{reponame}-backstage-integration` if file doesn't exist
- Generates a default catalog.yaml with repository metadata
- Commits and creates a Pull Request automatically
- Supports custom catalog content and file paths
- Works with both `main` and `master` branches

**Usage:**

To use this workflow in any repository, create `.github/workflows/setup-backstage.yml`:

```yaml
name: Setup Backstage Catalog

on:
  workflow_dispatch:  # Manual trigger

jobs:
  setup-catalog:
    uses: thome-legacy-technologies/ruleset/.github/workflows/backstage-catalog-integration.yml@main
    secrets:
      GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

See [`.github/workflows/README.md`](.github/workflows/README.md) for detailed documentation and configuration options.

## Contributing

To add new workflows or update existing ones:
1. Create or update workflow files in `.github/workflows/`
2. Test the workflow in a test repository
3. Submit a pull request with your changes
4. Update documentation as needed

## License

MIT