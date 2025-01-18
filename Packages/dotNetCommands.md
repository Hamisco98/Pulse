# Common .NET Commands and Practices

## Link Project to Solution (SLN)
```bash
dotnet sln add ProjectLocation/projectName.csproj
```

## Get All .NET Templates
```bash
dotnet new list
```

## Get Help / Properties for a Template
```bash
dotnet new **TemplateName** --help
```

## Add Project Reference
```bash
dotnet add reference ProjectLocation/ProjectName.csproj
```

## Fix Namespace Not Found Issue
1. Press **Ctrl+Shift+P**.
2. Select **OmniSharp: Restart OmniSharp**.

## List All Installed Packages
```bash
dotnet list package
```

## Data Annotations
Include the following namespaces for data annotations:
```csharp
using System;
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;
```

## Database Configuration
### Required Packages:
- `Microsoft.EntityFrameworkCore.SqlServer`
- `Microsoft.EntityFrameworkCore.Tools`
- `Microsoft.EntityFrameworkCore.Design`

### Installation:
```bash
dotnet add package **PackageName** --version #.#.#
```

## Entity Framework Commands
### Add New Migration
```bash
dotnet ef migrations add InitialMigration --startup-project ../TEND.API
```

### Update Database
```bash
dotnet ef database update --startup-project ../TEND.API
