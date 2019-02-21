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
- Change connection string in web.config from `(LocalDb)\v11.0` (for SQL 2012) to use newer LocalDB: `(LocalDb)\MSSQLLocalDB`
- Change LocalDbConnectionFactory `localDbVersion` to `mssqllocaldb`
- Created an empty database (using SQL Management Studio)
- Run migrations in Package Manager Console: `update-database`

### Upgrade .NET Framework
- Change project framework from .NET 4.5 to .NET 4.6.1

### Migrate to ASP.NET Core (on .NET Framework)
- Create empty ASP.NET Core Web Application project (targeting .NET Framework) in the same solution (requires different project name)
- From properties, change default namespace to match existing code namepsace (`ContosoUniversity`)
- Refactor code to match this namespace throughout the empty scaffolded project
- Create the first controller (`HomeController`) and view for `Views/Home/Index.cshtml` and test app works
- Migrate Controllers and Views and supporting Models, Migrations, etc.

#### Explicit / Additional References

In the CSProj file:

    <!-- Entity Framework 6 -->
    <PackageReference Include="EntityFramework" Version="6.1.1" />

    <!-- Fix view errors using PagedList -->
    <PackageReference Include="PagedList.Mvc" Version="4.5.0" />

#### Misc

* Don't copy `Global.asax` over to new project

#### Startup

- In `ConfigureServices`:
    - Add MVC: `services.AddMvc();`

- In `Configure`:
    - Add `app.UseExceptionHandler("/Home/Error");` when not in `Development` environment

#### AppSettings

- Move conection strings from `web.config` to `appsettings.json`


#### Controllers

- Create `Controllers` directory
- Copy over controllers from the old project
- Changed any direct DbContext object instantiations to use Dependency injection instead via the controller contructor

- Configure default routing in `Configure` method of `Startup.cs`:

        app.UseMvc(routes =>
        {
            routes.MapRoute(
                name: "default",
                template: "{controller=Home}/{action=Index}/{id?}");
        });

- Replace `return new HttpStatusCodeResult(HttpStatusCode.BadRequest);` with `return new BadRequestResult();`
- Replace `return HttpNotFound();` with `return new NotFoundResult();`
- Change controller action signatures from:

```c#
public ActionResult Create([Bind(Include = "CourseID,Title,Credits,DepartmentID")]Course course)
```

to

```C#
public ActionResult Create([Bind("CourseID","Title","Credits","DepartmentID")]Course course)
```

#### Views

- Create a `Views` directory
- Copy over views from the old project
- Leave default `_ViewStart.cshtml`
- Create/update `_ViewImports.cshtml` with this content:

```
@using ContosoUniversity
@using ContosoUniversity.Models
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
```

#### Static Content

* Add `app.UseStaticFiles();` to `Configure` method in `Startup.cs` to handle static files
* Copy `/favicon.ico` from old project to `wwwroot` directory in new project
* Copy any required JS scripts from the old project `/Scripts` directory to `wwwroot/lib` directory in new project
    * `bootstrap` already present
    * `jquery`, `jquery-validation`, `jquery-validation-unobtrusive` already present
* Copy `/fonts` directory from old project to `wwwroot/fonts` in new project
* Copy CSS files in `/Content` directory from old project to `wwwroot/css` in new project:
    * Ignore `bootstrap.css`, `bootstrap.min.css`
    * Copy `Site.css` and `PagedList.css` to `wwwroot/css`

#### App_Data

Ignore this directory -- don't copy to new project

#### App_Start

Much of this functionality has moved over to `Startup.cs`:

* Move routes from `RouteConfig.cs` to `StartUp.cs`
* Ignore `BundleConfig.cs`
* Ignore `FilterConfig.cs`
* Do not copy the `App_Start` directory over


# TODO:
- Finish documenting changes to Views based on this [guide](https://docs.microsoft.com/en-us/aspnet/core/migration/mvc?view=aspnetcore-2.2)
- In controllers, figure out how to use or replace `TryUpdateModel`
- Examine `packages.config` and add any required packages to the `.csproj` file (e.g. `PagedList.Mvc`)
- Change Logging over to use .NET Core Logging (then retire `Logging` folder)
- Change configuration of app to use .NET Core Configuration
- Change controllers to use `IActionResult` as return types
- Figure out if `modernizer.js` and `respond.js` are needed
- Refer to [completed sample .NET Core app](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final) for hints on how to migrate
