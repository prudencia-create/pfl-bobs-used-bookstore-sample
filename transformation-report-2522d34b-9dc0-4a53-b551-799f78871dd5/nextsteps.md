# Next Steps

## Issues resolved
- Transformed Bookstore.Domain.csproj to net8.0
- Transformed Bookstore.Data.csproj to net8.0
- Transformed Bookstore.Web.csproj to net8.0
- Transformed Bookstore.Cdk.csproj to net8.0
- Transformed Bookstore.Domain.Tests.csproj to net8.0

## Overview

The solution build produced no errors across all five projects after transformation:

- `Bookstore.Data`
- `Bookstore.Domain.Tests`
- `Bookstore.Cdk`
- `Bookstore.Web`
- `Bookstore.Domain`

This indicates the migration to cross-platform .NET was successful. The following steps outline how to validate, test, and deploy the solution.

---

## 1. Restore and Build the Solution

Run the following commands from the solution root to confirm a clean restore and build:

```bash
dotnet restore
dotnet build --configuration Release
```

Verify that no warnings or errors appear in the output. Address any warnings that could indicate compatibility issues, such as deprecated API usage or nullable reference warnings.

---

## 2. Run the Unit Tests

Execute the test project to confirm all existing tests pass on the new runtime:

```bash
dotnet test app/Bookstore.Domain.Tests/Bookstore.Domain.Tests.csproj --configuration Release --verbosity normal
```

Review the test output for:
- Any failing tests that may indicate behavioral differences between the old and new frameworks.
- Any skipped tests that may need to be re-enabled or updated.

---

## 3. Validate the Data Layer

Since `Bookstore.Data` handles data access, verify the following:

- **Database connectivity**: Confirm that connection strings in configuration files (e.g., `appsettings.json`) are correct and accessible in the new environment.
- **Migrations**: If Entity Framework Core is used, run the following to verify migrations are up to date:

```bash
dotnet ef migrations list --project app/Bookstore.Data/Bookstore.Data.csproj
dotnet ef database update --project app/Bookstore.Data/Bookstore.Data.csproj
```

- **Schema compatibility**: Confirm that the existing database schema matches what the migrated data layer expects.

---

## 4. Run the Web Application Locally

Start the web application to confirm it runs correctly:

```bash
dotnet run --project app/Bookstore.Web/Bookstore.Web.csproj --configuration Release
```

Manually verify the following:
- All pages load without errors.
- Data is read from and written to the database correctly.
- Authentication and authorization flows work as expected, if applicable.
- Any static assets (CSS, JavaScript) are served correctly.

Check the console output and application logs for runtime exceptions or warnings.

---

## 5. Validate the CDK Project

The `Bookstore.Cdk` project likely defines infrastructure. Confirm it compiles and synthesizes correctly:

```bash
dotnet build app/Bookstore.Cdk/Bookstore.Cdk.csproj --configuration Release
```

If this is an AWS CDK project, run the following to validate the synthesized output:

```bash
cdk synth
```

Review the synthesized template for any configuration values that may need to be updated to reflect the new runtime (e.g., Lambda runtime identifiers updated from `dotnetcore3.1` to `dotnet8`).

---

## 6. Check Target Framework Consistency

Open each `.csproj` file and confirm all projects target the same framework version. For example:

```xml
<TargetFramework>net8.0</TargetFramework>
```

Inconsistent target frameworks across projects can cause runtime issues that do not surface as build errors.

---

## 7. Review Configuration Files

- Confirm `appsettings.json` and any environment-specific variants (`appsettings.Development.json`, etc.) contain valid and complete configuration.
- Verify that any configuration keys that were previously stored in `Web.config` or `App.config` have been correctly migrated to the new configuration system.

---

## 8. Publish the Application

Once local validation is complete, publish the application:

```bash
dotnet publish app/Bookstore.Web/Bookstore.Web.csproj --configuration Release --output ./publish
```

Review the contents of the `./publish` directory to confirm all required files are present before deploying to the target environment.