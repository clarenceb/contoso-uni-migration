Migration of app from .NET Framework to .NET Core
=================================================

## Contoso University

* Tutorial: Get Started with Entity Framework 6 Code First using MVC 5 - [Microsoft Docs Tutorial](https://docs.microsoft.com/en-us/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application), [Download Completed Project](https://webpifeed.blob.core.windows.net/webpifeed/Partners/ASP.NET%20MVC%20Application%20Using%20Entity%20Framework%20Code%20First.zip) -- use this code as the starting point for the migration
* Already migrated code to EF Core and ASP.NET Core MVC - [GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final) -- use this code as a guide and reference if you get stuck migrating the code yourself

## Technology:

### Starting app

- .NET Framework 4.5
- ASP.NET Framework MVC 5
- Entity Framework 6 (Code First Migrations)
- VS2017 Solution / Project
- SQL Server LocalDB (v11.0 - SQL Server 2012 LocalDB)

## Migration steps:

### SQL database:
- Change connection string in web.config to use newer LocalDB: (LocalDb)\MSSQLLocalDB
- Change LocalDbConnectionFactory localDbVersion to mssqllocaldb
- Create database (using SQL Management Studio)
- Run migrations in Package Manager Console: update-database

### Upgrade .NET Framework
- .NET 4.5 to .NET 4.6.1

### Migrate to ASP.NET Core (on .NET Framework)
- Create empty ASP.NET Core Web Application (targeting .NET Framework) in the same solution (requires different name)
- From properties, change default namespace to match (ContosoUniversity)
- Refactor code to match this namespace throughout the empty scaffolded project
- Create Controllers directory with a HomeController, Create a Views/Home directory with a basic Index.cshtml page
