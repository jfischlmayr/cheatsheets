
# EF/ASP.NET Cheat Sheet

## Install EF

* Install EF Tools globally
  * `dotnet tool install --global dotnet-ef`
 * Update with `dotnet tool update --global dotnet-ef` ( `--version ...` to install a prerelease)

## Most important NuGet-Packages
|       Package        |                    Purpose                    |
| ----------------- | ----------------------------------------------- |
| Microsoft.EntityFrameworkCore | To be able to use EF                                   |
| Microsoft.EntityFrameworkCore.Design | For creating migrations		|
| Microsoft.EntityFrameworkCore.SqlServer | Database connection ([Choose DB provider](https://docs.microsoft.com/en-us/ef/core/providers/)) |
| Microsoft.AspNetCore.OData | OData |
| Microsoft.Extensions.Logging | Displaying generated sql statements |
| AutoMapper | For mapping DTO's into Objects |

## Create *Model*
* Consider *conventions*
  * Property named `Id` or `<type name>Id` -> *key* of an entity

* Configure your model using:
  * [Fluent API](https://docs.microsoft.com/en-us/ef/core/modeling/#use-fluent-api-to-configure-a-model) in `DbContext.OnModelCreating`
  * *Data Annotations*

```csharp
// Fluent API Example
// Add to DbContext:
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.ApplyConfigurationsFromAssembly(Assembly.GetExecutingAssembly());
}
```
```csharp
// Add to Model:
public class CategoryEntityImageTypeConfiguration : IEntityTypeConfiguration<Category>
{
    public void Configure(EntityTypeBuilder<Category> builder)
    {
        builder.Property(c => c.Title).HasMaxLength(100);
        builder.HasMany(c => c.Subcategories)
            .WithOne(s => s.Category)
            .OnDelete(DeleteBehavior.Cascade)
            .IsRequired(false);
    }
}
```

## Connection String in `appsettings.json`

* Create *appsettings.json* file:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\MSSQLLocalDB;Database=AddressBook;Trusted_Connection=True"
  }
}
```

## Create a *DbContext*

* `DbContextOptions` in Constructor:

```csharp
public class ShareDbContext: DbContext
{
    public ShareDbContext(DbContextOptions<ShareDbContext> options)
        : base(options)
        { }

    public DbSet<Share> Shares{ get; set; }
}
```

* Add `DbContext` to `Program.cs`:

```csharp
builder.Services.AddDbContext<FundraisingContext>(
    options => options.UseSqlServer(builder.Configuration["ConnectionStrings:DefaultConnection"]));
```
* Add `DbContext` to Controller:
```csharp
private readonly ShareDbContext context;

public SearchController(ShareDbContext context)
{
    this.context = context;
}
```
## Manage DB Schema
* Add Migration: `dotnet ef migrations add <MigrationName>`
* Update target database: `dotnet ef database update`
* Remove last Migration: `dotnet ef migrations remove`
* Generate SQL script from Migrations: `dotnet ef migrations script`

## Query Data

* [Documentation](https://docs.microsoft.com/en-us/ef/core/querying/)

|       Task        |                    Operation                    |
| ----------------- | ----------------------------------------------- |
| Load all data     | `ToListAsync`                                   |
| Load single row   | `Single`                                        |
| Filter            | `Where`                                         |
| Load related data | `Include`, `ThenInclude`, `Entry`, `Collection` |
| No-tracking query | `AsNoTracking`                                  |
| Raw SQL queries   | `FromSql`                                       |
| Sorting           | `OrderBy`, `OrderByDescending`                  |

## Transactions:

```csharp
using (var transaction = context.Database.BeginTransaction())
{
    try
    {
        ...
        context.SaveChanges();
        ...
        context.SaveChanges();
        transaction.Commit();
    }
    catch (Exception)
    {
        // TODO: Handle failure
    }
}
```

* Set state of external object to *modified*: `context.Entry(obj).State = EntityState.Modified;`

## OData
* In `Program.cs`
	* Before `var app = builder.Build();`:
		```csharp
		builder.Services.AddControllers()
		       .AddOData(opt => opt.Filter()
		                           .Expand()
		                           .Select()
		                           .OrderBy()
		       .AddRouteComponents("odata", GetEdmModel()));
		```
	* After `app.Run()`:
		```csharp
		static IEdmModel GetEdmModel()
		{
		    var builder = new ODataConventionModelBuilder();
		    builder.EntitySet<Share>("Share");
		    return builder.GetEdmModel();
		}
		```
* In the model class
	```csharp
	public class SearchController : ODataController
	```
	```csharp
	[EnableQuery]
    public IActionResult Get() => Ok(context.Shares);
	```
	
## Fixed Ports

Add the following code to `Program.cs`:
```builder.WebHost.UseUrls("http://localhost:5000", "https://localhost:5001");```
