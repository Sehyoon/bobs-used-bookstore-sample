# Next Steps

## Issues resolved
- Transformed Bookstore.Domain.csproj to net8.0
- Transformed Bookstore.Data.csproj to net8.0
- Transformed Bookstore.Web.csproj to net8.0
- Transformed Bookstore.Cdk.csproj to net8.0
- Transformed Bookstore.Domain.Tests.csproj to net8.0

## Summary

The transformation appears to have completed successfully. No build errors were detected across any of the projects in the solution:

- `Bookstore.Data`
- `Bookstore.Domain.Tests`
- `Bookstore.Cdk`
- `Bookstore.Web`
- `Bookstore.Domain`

The following steps outline how to validate, test, and deploy the migrated solution.

---

## 1. Restore Dependencies

Run a NuGet package restore to ensure all dependencies are resolved correctly before building:

```bash
dotnet restore
```

Review the output for any warnings about deprecated or unlisted packages that may need to be updated.

---

## 2. Build the Solution

Perform a full solution build to confirm there are no compilation issues:

```bash
dotnet build --configuration Release
```

Ensure the build completes with zero errors and review any warnings, particularly those related to nullable reference types, obsolete APIs, or platform compatibility.

---

## 3. Run the Test Project

Execute the tests in `Bookstore.Domain.Tests` to verify that domain logic behaves correctly after migration:

```bash
dotnet test app/Bookstore.Domain.Tests/Bookstore.Domain.Tests.csproj --configuration Release --verbosity normal
```

Review the test results for any failures or skipped tests. If tests are failing, compare the test output against the expected behavior from the original legacy project to identify regressions.

---

## 4. Verify Runtime Behavior of the Web Project

Run the `Bookstore.Web` project locally to confirm it starts and serves requests as expected:

```bash
dotnet run --project app/Bookstore.Web/Bookstore.Web.csproj --configuration Release
```

Manually verify the following:
- Application starts without runtime exceptions.
- Key pages and endpoints load correctly.
- Any database connections configured in `Bookstore.Data` are functioning (check connection strings in `appsettings.json` or environment variables).

---

## 5. Validate the Data Layer

Confirm that `Bookstore.Data` is connecting to the correct database and that any Entity Framework Core migrations (if applicable) are up to date:

```bash
dotnet ef migrations list --project app/Bookstore.Data/Bookstore.Data.csproj --startup-project app/Bookstore.Web/Bookstore.Web.csproj
```

If migrations are pending, apply them to the target database:

```bash
dotnet ef database update --project app/Bookstore.Data/Bookstore.Data.csproj --startup-project app/Bookstore.Web/Bookstore.Web.csproj
```

---

## 6. Review the CDK Project

If `Bookstore.Cdk` is used for infrastructure provisioning, verify that the CDK stack definitions are still valid and that any environment-specific configuration (such as AWS account IDs, regions, or resource names) is correctly set for the target environment.

Synthesize the CDK stack to check for configuration issues:

```bash
cd app/Bookstore.Cdk
cdk synth
```

Review the synthesized output for any unexpected changes compared to the previous infrastructure definition.

---

## 7. Perform a Final Smoke Test

After completing the above steps, perform an end-to-end smoke test of the application against a staging or pre-production environment to confirm that all layers — web, domain, and data — are functioning correctly together.