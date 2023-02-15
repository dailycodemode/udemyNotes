<h1>Section 2</h1>
cmd: .net list

1. Creating the .Net project
see powershell setup script

2. Change the port mapping in launchSettings.json

3. Add an Activity class in proj:Domain

4. Create Entity relationship mapper

install nuget
microsoft.entityframeworkcore.sqlite
    install version that matches the .Net version

Add DataContext
```
public DbSet<Value> Values { get; set; }
``

5. Add services
builder.Services.AddDbContext to Program.cs

```
services.AddDbContext<DataContext>(opt =>
            {
                opt.UseLazyLoadingProxies();
                opt.UseSqlite(Configuration.GetConnectionString("DefaultConnection"));
            });
```

```
Add ConnectionStrings to appsettings.dev json
```
afterwards, 
dotnet restore

6. entity migration code first migration
dotnet tool list -g

dotnet-ef not available
dotnet tool install --global dotnet-ef --version 7.0.0

dotnet ef    <--unicorn>
dotnet ef migrations add InitialCreate -s API -p Persistence

install nuget, into API project
Microsoft.EntityFrameworkCore.Design

rerun
dotnet ef migrations add InitialCreate -s API -p Persistence

Will create a project within Persistence project

7. Creating the database
add code in startup to update the database



OR
```
dotnet ef database update
```

<h1>Section 4</h1>
See CleanArchitecture.jpg
Entities = Domain layer
Use Cases = Application & Business logic. Each use class will have its own Class.
Controllers = Interface between use cases and the external client/DB
Frameworks/Drivers = DB, user interface
