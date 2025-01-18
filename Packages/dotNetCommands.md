*Link project to SLN:*
dotnet sln add ProjectLocation/projectName.csproj

*Get All Dot Net Templates*
dotnet new list

*Get Help / Properties for a template*
dotnet new **TemplateName** --help

*Add Project Reference*
dotnet add reference ProjectLocation/ProjectName.csproj

*The Issus For Namespace Not Found*
**Ctrl+Shift+P** => OmniSharp: Restart OmniSharp

*To List All Packages Installed*
dotnet list package

*Data Anotation*
using System;
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

*Database*
Microsoft.EntityFrameworkCore.SqlServer
Microsoft.EntityFrameworkCore.Tools
Microsoft.EntityFrameworkCore.Design

**Instalation** dotnet add package **PackageName** --version #.#.#



*Add New Migration*
dotnet ef migrations add InitialMigration --startup-project ../TEND.API

*Update Database*
dotnet ef database update --startup-project ../TEND.API
