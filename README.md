# mvc-tutorial
Text files for ASP.NET MVC


## Architecture

### Models
  * Class in C# represents table
    - properties of the class file will be the columns of the table
  * Represents
    - shape of the data
    - all the data of the application
    - the data being transferred between views and controllers
    - any business related data that will represent all the tables of the database

### Views
  * User interface
  * Whatever we see on the website
  * Clicking on the button will start interaction between view and model to display some data
    - view does not interact directly with the models
    - view uses controller
    - when it is rendered it will pass into the controller
  * Shared folder
    - used for partial views (similar to user components in WinForms)
    - views we can call within a view in multiple places in our application
    - _Layout.cshtml
      > default master page of the application (styling, header, render body, footer, common js)
      > other views use this layout as master page
    - _ValidationScriptsPartial.cshtml
      > partial view with some scripts for some validations
      > we will include this partial view in other views using validations
    - Error.cshtml
      > included if we encounter errors in the application
  * _ViewsImport.cshtml
    - global namespace
      > using statement makes it visible across all the pages, controllers and pages (no need to type namespace everytime)
    - tag helpers
      > bindings provided by .NET CORE looking like HTML tags
      > special tags for example for ASP controller and ASP action
        = starts with a prefix asp- and then the name
  * _ViewStart.cshtml
    - defines default master page of our application
      > we can overwrite this

### Controllers
  * Interface between the model and the view to process all the business logic and incoming request
  * Manipulates data using model and interacts with the view to render the final view
  * Has a lots of action methods based on which controller will redirect the request to one of the action method
  * Uses model to fetch all the data needed to display inside the view
  * Passes the response send back and user can see the page
  * All the logic of the application
  * HomeController.cs will have all the views and UI pages placed in Views/Home folder
    - controller name should end with Controller.cs
    - action methods
      > IActionResult is an abstraction for multiple return type (View, Action method, Page)
      > for each action method there can be a view with the same name

### Routing
  * https://localhost:5000/{controller}/{action}/{id}
    - id is optional
    - default route controller and action can be defined in Program.cs


## Basics

### Controller
  * CategoryController.cs
    - RC on controller action to create view
  * Add navigation in to view inside _Layout.cshtml
  * To access the database we need to use dbContext object
    - we access the object through dependency injection
    - constructor with dbContext where we populate our private readonly dbContext
  * We can pass for example IEnumerable\<T> to a View constructor

### View
  * To capture what is being passed from controller use @model IEnumerable\<T>
  * To display list of items we can use \<table>
    ``` html
    <table class="table-bordered table-striped" style="width:100%">
        <thead>
          <tr>
            <th>
              Category Name
            </th>
            <th>
              Display Order
            </th>
          </tr>
        </thead>
        <tbody>
          @foreach (var obj in Model)
          {
            <tr>
              <td width="50%">
                @obj.Name
              </td>
              <td width="30%">
                @obj.DisplayOrder
              </td>
            </tr>
          }
        </tbody>
      ```
  * bootswatch.com has free themes for bootstrap
    - download bootstrap.css
    - create name.css file inside wwwroot/css
      > paste the content of bootstrap.css inside
    - remove .btn-primary and a section from site.css
      > we will use what is inside bootswatch theme
    - go to _Layout.cshtmp
      > replace ~/lib/bootstrap/dist/css/bootstrap.min.css for ~/css/name.css
      > replace ~/lib/bootstrap/dist/js/bootstrap.bundle.min.css for bundle from getbootstrap.com/docs/5.0/getting-started/introduction
    - go to preview and copy navbar html
      > paste it right before navbar in _Layout.cshtml
        = add builder.Services.AddRazorPages().AddRazorRuntimeCompilation(); to enable hot reload for razor pages
      > adjust navigation and remove class="text-dark"
  * Footer styling in \<footer class="">
  * To add buttons we can use \<div>
    - by default bootstrap divides each row into 12 parts
    ``` html
    <div class="container p-3">
        <div class="row pt-4">
          <div class="col-6">
            <h2 class="text-primary">Category List</h2>
          </div>
          <div class="col-6 text-end">
            <a asp-controller="Category" asp-action="Create" class="btn btn-primary">
              Create New Category
            </a>
          </div>
        </div>
        <br /> <br />
      ... table ...
      </div>
  * Use icons from icons.getbootstrap.com
    - click install and copy CDN
      > paste it to _Layout.cshtml in \<head>
    - search for the icon in webpage
    - click the icon and copy the icon font
      > paste it before 'Create New Category' text with &nbsp; space

## Create

### Controller

  ``` csharp
  public IActionResult Create() // GET
  {
    return View();
  }
  
  [HttpPost]
  [ValidateAntiForgeryToken]
  public IActionResult Create(Category obj) // POST
  {
      _db.Categories.Add(obj);
      _db.SaveChanges();
      return RedirectToAction("Index"); // same controller
  }
  ```
  
  * anti forgery token to help and prevent cross site request forgery attack
     - automatically inject a key in a form, which will be validated

### View
  * Create.cshtml view
  * Fetch Category model properties
    - @model Category
    
  ``` html
  <form method="post">
      <div class="border p-3 mt-4">
        <div class="row pb-2">
          <h2 class="text-primary">Create Category</h2>
          <hr />
        </div>
        <div class="mb-3">
          <label asp-for="Name"></label>
          <input asp-for="Name" class="form-control"/>
        </div>
        <div class="mb-3">
          <label asp-for="DisplayOrder"></label>
          <input asp-for="DisplayOrder" class="form-control"/>
        </div>
        <button type="submit" class="btn btn-primary" style="width:150px">Create</button>
        <a asp-controller="Category" asp-action="Index" class="btn btn-secondary" style="width:150px">
          Back To List
        </a>
      </div>
    </form>
 ```

## Data

### Model

  ``` csharp
  using System.ComponentModel.DataAnnotations;

  [Key]
  public int Id { get; set; }
  [Required]
  public string Name { get; set; }
  public int DisplayOrder { get; set; }
  public DateTime CreatedDateTime { get; set; } = DateTime.Now;
 ```


### Database
  * Put connection strings inside appsettings.json
    ```  
    "ConnectionStrings" : {
      "DefaultConnection" : "Server=(localDB)\\MSSQLLocalDB;Database=DatabaseName;TrustedConnection=True;"
    }
    ```
      - TrustedConnection is true, so we can use just Windows Authentication

### DBContext
  * Inside Data folder create NameDBContext.cs file
    - NameDbContext : DBContext
      > we need nuget package Microsoft.EntityFrameworkCore
        = wait for red squigly line
        = RC project, manage nuget packages
        = Tools -> Nuget package manager -> Manage Nuget packages for solution -> EntityFrameworkCore -> Select project and install
      > using Microsoft.EntityFrameworkCore;
    - ctor is code snippet for contructor
    ``` csharp  
    public NameDBContext(DBContextOptions<NameDBContext> options) : base(options)
    {
    }

    public DBSet<ModelName> TableName { get; set; }
    ```
    
  * Inside Program.cs file
    ``` csharp
    builder.Services.AddDBContext<NameDBContext>(options => options.UseSqlServer(
      builder.Configuration.GetConnectionString("DefaultConnection") // only looks inside "ConnectionStrings"
    ));
    ```
    - we need nuget package Microsoft.EntityFrameworkCore.SqlServer same version
    
### Migration folder
  * Nuget package Microsoft.EntityFrameworkCore.Tools
  * Tools -> Nuget package manager -> Package manager console
    - need to run migrations using Entity Framework to push changes to database
      > create the database and the table
    - add a migration
      > add-migraton AddCategoryToDatabase
    - push the migrations to the database
      > update-database
  * Migration files
    - up method is what needs to happen inside the migration
      > CreateTable with name, columns and contraints
    - down method is when something goes down and we need to rollback the changes
  * Based on these migrations, EF creates optimized version of SQL queries

## Delete

### Controller
  * GET is the same as Edit GET
  * POST can use only id
    ``` csharp 
    public IActionResult DeletePOST(int id?)
    {
      var obj = _db.Categories.Find(id);
      if (obj == null)
      {
        return NotFound();
      }
      _db.Categories.Remove(obj);
      _db.SaveChanges();
      return RedirectToAction("Index");
    }
    ```
  * We can name action as Delete and add [ActionName("Delete")] annotation


### View
  * Similar to Edit View
    - add \<input asp-for="Id" hidden />
    - different namings and styling
      > btn-danger
    - we dont need any validations
    - inputs are disabled
      > keyword disabled inside input tag
  * Add delete button inside the index view


## Files

### Project file
  * ProjectName.csproj
  * Configuration file
    - target framework
    - item group
      > used nuget packages

### Dependencies
  * Packages
    - used nuget packages

### Properties folder
  * launchSettings.json
  * Profiles we can use to run application
    - port number
    - default - ProjectNameWeb
    - run dotnet command and trigger application

### wwwroot folder
  * Project static files
  * css
  * js
  * lib
  * Root folder of the application

### App settings
  * appsettings.json
  * appsettings.Development.json | appsettings.Production.json
    - different environment variables
  * All connection strings and secrets

### App running
  * Program.cs
  * builder variable with built-in arguments
  * register anything with dependency injection container
    - between the builder and app=builder.Build() call
  * Startup.cs
    - builder and registering prior to .NET 6.0
    - services added to container in method configure services
    - everything onward in configure method
  * HTTP request pipeline
    - how application should respond to a web request
      > request goes through the pipeline
      > we can add items/middleware to the pipeline
        = Auth, MVC, static files
      > request gets modified by each of the middleware
      > after the last middleware the response is send back to server
    - app.UseStaticFiles();
      > for wwwroot folder
    - app.MapControllerRoute(name: "default", pattern: "{controller=Home}/{action=Index}/{id?}");
      > maps different pattern - it will be able to redirect a request to the corresponding controllers and action
    - order of pipeline matters
      > request will be passed in that order (UseAuthentication(), UseAuthorization())
  * Responsible for running the application

## Other

### Tag helpers
  * Similar to Angular directives
    - tag helpers are for server-side rendering of HTML elements in Razor files
  * Focused around html elements
  * Natural to use

### IActionResult
  * Generic type implementing all of the other return types
    - we could use ViewResult and PageResult instead
  * Result of action methods/pages or return types of action methods/pages handlers
  * Appropriate when multiple ActionResult return types are possible in an action
  * Examples of ActionResult in Razor pages (helper method):
    - ContentResult (Content)
    - FileContentResult (File)
    - NotFoundResult (NotFound)
    - PageResult (Page)
    - PartialResult (Partial)
    - RedirectToPageResult (RedirectToPage, RedirectToPagePermanent, RedirectToPagePerserveMethod, RedirectToPagePerserveMethodPermanent)
    - ViewComponentResult
  * Examples of ActionResult in MVC (helper method):
    - ViewResult (View)
    - PartialViewResult (PartialView)
    - RedirectResult (Redirect)
    - RedirectToRouteResult (RedirectToRoute, RedirectToAction)
    - ContentResult (Content)
    - JsonResult (Json)
    - JavaScriptResult (JavaScript)
    - FileResult (File)
    - EmptyResult (Empty)
  * Useful when returning multiple ActionResult types based on some conditions

### Hot Reload
  * Added to .NET 6.0
  * In older versions install MVC Razor Runtime compilation nuget package
    - in Program.cs add builder.Services.AddRazorPages().AddRazorRuntimeCompilation() before build() method

### Temp data
  * What we store in temp data stays there for only one request
  * Perfect for displaying alerts
  * In controllers
      ``` csharp
      TempData["success"] = "Created successfully";
      ```
  * In Views
      ``` html
      @if (TempData["success"] != null)
      {
        <h2>TempData["success"]</h2>
      }
      ```

### New partial view
  * Partial views are in Views/Shared
  * Add some code to view
    ``` html
    @if (TempData["success"] != null)
    {
      <h2>TempData["success"]</h2>
    }
    @if (TempData["error"] != null)
    {
      <h2>TempData["error"]</h2>
    }
    ```
  * Link partial view
    ``` html
    <partial name="_PartialView" />
    ```


## Update

### Controller
  ``` csharp
  public IActionResult Edit(int id?) // GET
  {
    if (id == null || id == 0)
    {
      return NotFound();
    }
    var category = _db.Categories.Find(id);
    if (category == null)
    {
      return NotFound();
    }
    return View(category);
  }
  ```
  * POST is similar to Create POST
    - Update instead of Add

### View
  * Basically the same as in Create
    - different text values
    - we can add redirection to different action and controller
    ``` html
    <form method="post" asp-controller="x" asp-action="y"> ...
    ```
    - by default stays the same

### Other
  * Add a link to edit view
    ``` html
    <div class="w-75 btn-group" role="group">
      <a asp-controller="Category" asp-action="Edit" asp-route-id=@obj.Id
       class="btn btn-primary mx-2">
        <i class="bi bi-pencil-square"> </i> Edit
      </a>
    </div>
   ```
   markdown bug?
   ```

## Validation

### Server side
  * Inside a controller
  * Check a valid model
    - validations inside model class
    ``` csharp
    if (ModelState.IsValid)
    {
      // normal actions
    }
    return View(obj);
    ```
  * Display errors inside view for each property
    ``` html
    <span asp-validation-for="Name" class="text-danger"></span>
    ```
      - default error message will be displayed
  * Display validation summary before fields
    ``` html
    <div asp-validation-summary="All"></div>
    ```
  * Custom validation inside controller
    ``` csharp
    if (obj.Name == obj.DisplayOrder.ToString())
    {
      ModelState.AddModelError("CustomError", "This cannot match that")
    }
    ```
    - error will be displayed in the summary
    - we can display it under field if we set name to property name

### Client side
  * Inside a view
  * Include partial view in our view
  ``` html
  @section Scripts{
    @{
      <partial name="_ValidationScriptsPartial.cshtml" />
    }
  }
  ```

### Other
  * We can change display name of property in validation and fields
    - add [DisplayName("The Name")] inside model before property
  * There are more annotation available in the documentation
    - [Range(1, 100, ErrorMessage="What is this")]
      > value range of a model field
