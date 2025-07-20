# GitHub Actions Workflows

This directory contains the GitHub Actions workflows for the CloudNimble.Build.Documentation project.

## Workflows

### 🚀 Build and Deploy (`build-and-deploy.yml`)

Handles building, testing, and deploying packages to NuGet using .NET 10 preview.

**Triggers:**
- Push to `main` or `dev` branches
- Pull requests to `main` or `dev` (build only, no deployment)

**Platform:** Windows (all jobs run on `windows-latest`)

**Versioning:**
- **Main branch**: Standard SemVer (e.g., `1.0.42`)
- **Dev branch**: Preview versioning (e.g., `1.0.42-preview.1`)
- **Feature/other branches**: CI versioning (e.g., `1.0.0-CI-20250716-193214`)

**Features:**
- Automatic .NET 10 preview installation via PowerShell
- NuGet package deployment to NuGet.org
- GitHub release creation for main branch builds
- Artifact upload with 7-day retention

### ✅ PR Validation (`pr-validation.yml`)

Validates pull requests with comprehensive testing.

**Triggers:**
- Pull requests to `main` or `dev` branches

**Platform:** Windows only (for .NET 10 preview support)

**Testing:**
- .NET: 10.0.x (preview)
- Package validation
- Test result upload

## Configuration

### Repository Variables

Set these in your repository settings under Settings → Secrets and variables → Actions → Variables:

| Variable | Description | Default | Example |
|----------|-------------|---------|---------|
| `MAJOR_VERSION` | Major version number | `1` | `2` |
| `MINOR_VERSION` | Minor version number | `0` | `1` |

### Repository Secrets

Set these in your repository settings under Settings → Secrets and variables → Actions → Secrets:

| Secret | Description | Required |
|--------|-------------|----------|
| `NUGET_API_KEY` | NuGet.org API key for package publishing | Yes |

## Version Number Format

### Main Branch (Production)
```
{MAJOR_VERSION}.{MINOR_VERSION}.{BUILD_NUMBER}
```
**Example:** `1.0.42`

**Usage:** Production releases with auto-incrementing build numbers

### Dev Branch (Preview Builds)
```
{MAJOR_VERSION}.{MINOR_VERSION}.{BUILD_NUMBER}-preview.{RUN_ATTEMPT}
```
**Example:** `1.0.42-preview.1`

**Usage:** Development previews for testing and staging

### Feature/Other Branches (CI Builds)
```
{MAJOR_VERSION}.{MINOR_VERSION}.0-CI-{YYYYMMDD}-{HHMMSS}
```
**Example:** `1.0.0-CI-20250716-193214`

**Usage:** CI builds for feature branches and pull requests

### Variable Definitions
- `BUILD_NUMBER` = Auto-incrementing build number that resets to 0 when major/minor versions change
- `YYYYMMDD` = UTC date (e.g., `20250716`)
- `HHMMSS` = UTC time in 24-hour format (e.g., `193214` = 7:32:14 PM)
- `RUN_ATTEMPT` = Attempt number for the current workflow run

## .NET 10 Preview Setup

The workflows automatically install .NET 10 preview using:

```powershell
Invoke-WebRequest -Uri "https://dot.net/v1/dotnet-install.ps1" -OutFile "dotnet-install.ps1"
./dotnet-install.ps1 -Version 10.0 -Channel Preview -InstallDir "$env:ProgramFiles\dotnet"
```

**Benefits:**
- Latest .NET features and performance improvements
- C# 13 support
- Enhanced SDK capabilities

## Deployment Strategy

### Automatic Deployment
- **Main branch pushes:** Deploy to NuGet with production version
- **Dev branch pushes:** Deploy to NuGet with preview version
- **Feature branches:** Build only, no deployment

### Release Creation
- Automatic GitHub releases for main branch deployments
- Includes NuGet package as release asset
- Generated release notes with installation instructions

## Manual Version Management

### Bumping Versions

1. **Major Version:** Update `MAJOR_VERSION` repository variable (resets build number to 0)
2. **Minor Version:** Update `MINOR_VERSION` repository variable (resets build number to 0)
3. **Patch/Build:** Automatically handled by workflow runs (increments for each major.minor)

### Version Reset Logic

The build number automatically resets to 0 when major or minor versions change:
- When you bump from `1.0.x` to `1.1.x`, build numbers start at `1.1.0`
- When you bump from `1.x.x` to `2.x.x`, build numbers start at `2.0.0`
- Build numbers are tracked by examining existing Git tags for each major.minor combination

### Version Strategy Examples

**Version Flow Example:**
```
1.0.0 → 1.0.1 → 1.0.2 → 1.1.0 → 1.1.1 → 2.0.0 → 2.0.1
```

Given `MAJOR_VERSION=2` and `MINOR_VERSION=1`, and assuming the highest existing tag is `v2.1.5`:

| Branch Type | Next Build Result | When to Use |
|-------------|------------------|-------------|
| `main` | `2.1.6` | Production releases |
| `dev` | `2.1.6-preview.1` | Development testing |
| `feature/new-feature` | `2.1.0-CI-20250716-193214` | Feature development |

## Workflow Environment Variables

Both workflows use consistent environment variables:

```yaml
env:
  DOTNET_VERSION: '10.0.x'
  SOLUTION_FILE: 'src/CloudNimble.Build.Documentation.slnx'
  PROJECT_FILE: 'src/CloudNimble.Build.Documentation/CloudNimble.Build.Documentation.csproj'
```

## Troubleshooting

### .NET Installation Issues
If .NET 10 preview installation fails:
1. Check if the Preview channel is available
2. Verify the installation script URL is accessible
3. Try using a specific version number if the channel approach fails

### Package Already Exists
The workflow uses `--skip-duplicate` to handle cases where a package version already exists on NuGet.

### Version Conflicts
If you need to force a new version:
1. Update the repository variables (`MAJOR_VERSION`/`MINOR_VERSION`)
2. Manually trigger a workflow run
3. Create a new commit to trigger the build
4. For CI builds, the timestamp ensures automatic uniqueness

### Missing Secrets
If deployment fails with authentication errors:
1. Verify `NUGET_API_KEY` secret is configured
2. Ensure the API key has package publishing permissions
3. Check if the API key has expired

### Windows-Specific Issues
Since all jobs run on Windows:
1. PowerShell syntax is used throughout
2. Path separators use backslashes in some contexts
3. File operations use PowerShell cmdlets

## Adding New Workflows

When creating additional workflows:

1. **Use consistent environment variables** from existing workflows
2. **Include .NET 10 preview installation** step
3. **Use PowerShell** for scripting consistency
4. **Run on Windows** for .NET preview compatibility
5. **Follow naming convention:** `{purpose}.yml`
6. **Update this README** with new workflow details

## Dependencies

### Dependabot Configuration
The repository includes automatic dependency updates via `.github/dependabot.yml`:

- **GitHub Actions:** Weekly updates on Mondays
- **NuGet packages:** Weekly updates on Mondays
- **Intelligent ignoring:** Skips major/minor updates for pinned SDK dependencies

### Action Dependencies
- `actions/checkout@v4` - Repository checkout
- `actions/upload-artifact@v4` - Artifact management
- `actions/download-artifact@v4` - Artifact retrieval
- `softprops/action-gh-release@v1` - GitHub release creation