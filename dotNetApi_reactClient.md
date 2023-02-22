<h1>Complete guide to building an app with .Net Core and React</h1>
https://www.udemy.com/course/complete-guide-to-building-an-app-with-net-core-and-react/

<h2>Intro</h2>
This walkthrough is for the strucutral setup of the .net side of this course. The main repo can be found here.

https://github.com/TryCatchLearn/Reactivities

<h2>Folder structure.</h2>

1. Client => react app
2. API => Controllers & starting point
3. Application => business logic
4. Domain => object classes
5. Persistence => DB connection, seeding + query commands

<h2>Section 2</h2>
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
```

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

<h2>Section 4 - CQRS+Mediator</h2>
See CleanArchitecture.jpg
Entities = Domain layer
Use Cases = Application & Business logic. Each use class will have its own Class.
Controllers = Interface between use cases and the external client/DB
Frameworks/Drivers = DB, user interface

Nuget
mediatR.Extensions.Microsoft.DependencyInjection into Application

1. In application i.e. business logic layer, create Activites folder .

Create.vs, Delete.cs, Details.cs, Edit.cs, List.cs

2. Types of additions.cs .

IRequest is a MediatR object. This will be where you set the paramters
IRequestHandler is a MediatR interface. Where the DB sending will be done e.g
```
    public async Task<List<Activity>> Handle(Query request, CancellationToken token)
    {
        return await _context.Activities.ToListAsync();
    }
```

3. Activities controller.

Inject IMediator into Base controller
```
    private IMediator _mediator;

    protected IMediator Mediator => _mediator ??= 
        HttpContext.RequestServices.GetService<IMediator>();
```
 
 Update the Activities controller to use the Mediator rather than the dbContext
```
    [HttpGet]
    public async Task<ActionResult<List<Activity>>> GetActivities()
    {
        return await Mediator.Send(new List.Query());
    }
```

4. Register MediatR as a service.
```
services.AddMediatR(typeof(List.Handler)); // ---- any handler could have been chosen
```
Check if working: dotnet watch --no-hot-reload


<h2>Section 7 - Mobx</h2>

```
npm install mobx mobx-react-lite
```

Stores folder in /src/tores

touch ActivtyStore.ts

touch store.ts

Add code

1. Wrap App in StoreContext.Provider value={store}.

2. Where useStates are being used, add activityStore. 
```
{activityStore} = useActivityStore
```
3. Add the setters in the activityStore.ts  .
4. Make App.tsx an observer.
```
export default observer(App);
```

5. Update the code base to use store. Note, to use Store then function components need to be made observers

<h2>Section 8 - reactRouter</h2>

```
npm install react-router-dom  
```
add Routes folder



<h2>Section 10 - validation</h2>

1. install fluent validation.

2. Wrap the Validation around the Creat.cs, where the Handler is creating the connecitons


3. Add Fluent Configuration to the AppServiceExtensions.cs
Below AutoMapper. Any other validators will be registered alongside.

4. Add a ActitiyValidator.cs in applications/activities/

5. Derive from AbstractValidator<Activity>. Add all rules.

6. Update the ApplicationsServiceExtensions.cs 

7. Update other Command commands e.g. Edit.


<h4>Section 10 - validation - api error response</h4>

Add a Result.cs to application/core/ to check if api call was a success.
Update Details Query class to return a {Result{Activity}}



<h2>Section 14 - ef relationships</h2>

<h4>Section 14 - ef relationships - configuring the relationship</h4>

1. Create many to many relationship. In the /domain/ , add a reference to the realted class in the other classes.
Create new class ActivityAttenddee.cs  
Update the two classes to reference this class instead.

2. Open DataContext.cs

add dbSet{ActitivyAttendee}

3. Override the class to create a new builder. Add both way relationships.


