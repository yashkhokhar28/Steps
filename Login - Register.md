
# Implementing Login Functionality in ASP.NET Core

## Prerequisite

- **Stored Procedure: `PR_User_Login`**

### Stored Procedure Code:

```sql
CREATE PROCEDURE [dbo].[PR_User_Login]
    @UserName NVARCHAR(50),
    @Password NVARCHAR(50)
AS
BEGIN
    SELECT 
        [dbo].[User].[UserID], 
        [dbo].[User].[UserName], 
        [dbo].[User].[MobileNo], 
        [dbo].[User].[Email], 
        [dbo].[User].[Password],
        [dbo].[User].[Address]
    FROM 
        [dbo].[User] 
    WHERE 
        [dbo].[User].[UserName] = @UserName 
        AND [dbo].[User].[Password] = @Password;
END
```

- **Stored Procedure: `PR_User_Register`**

### Stored Procedure Code:

```sql
CREATE PROCEDURE [dbo].[PR_User_Register]
    @UserName NVARCHAR(50),
    @Password NVARCHAR(50),
    @Email NVARCHAR(500),
    @MobileNo VARCHAR(50),
    @Address VARCHAR(50)
AS
BEGIN
    INSERT INTO [dbo].[User]
    (
        [UserName],
        [Password],
        [Email],
        [MobileNo],
        [Address]
    )
    VALUES
    (
        @UserName,
        @Password,
        @Email,
        @MobileNo,
        @Address
    );
END
```

## Step 1: Layout Setup in `_LoginLayout.cshtml`
Ensure that your layout file includes the necessary CSS and JS libraries to support the login page.

### Code:

```html
<!DOCTYPE  html>
<html  lang="en">
<head>
    <meta  charset="utf-8">
    <meta  content="width=device-width, initial-scale=1.0"  name="viewport">
    <title>Pages / Login - NiceAdmin Bootstrap Template</title>
    <meta  content=""  name="description">
    <meta  content=""  name="keywords">

    <!-- Favicons -->
    <link  href="~/img/favicon.png"  rel="icon">
    <link  href="~/img/apple-touch-icon.png"  rel="apple-touch-icon">

    <!-- Google Fonts -->
    <link  href="https://fonts.gstatic.com"  rel="preconnect">
    <link  href="https://fonts.googleapis.com/css?family=Open+Sans:300,300i,400,400i,600,600i,700,700i|Nunito:300,300i,400,400i,600,600i,700,700i|Poppins:300,300i,400,400i,500,500i,600,600i,700,700i"  rel="stylesheet">
    <!-- Vendor CSS Files -->
    <link  href="~/vendor/bootstrap/css/bootstrap.min.css"  rel="stylesheet">
    <link  href="~/vendor/bootstrap-icons/bootstrap-icons.css"  rel="stylesheet">
    <link  href="~/vendor/boxicons/css/boxicons.min.css"  rel="stylesheet">
    <link  href="~/vendor/quill/quill.snow.css"  rel="stylesheet">
    <link  href="~/vendor/quill/quill.bubble.css"  rel="stylesheet">
    <link  href="~/vendor/remixicon/remixicon.css"  rel="stylesheet">
    <link  href="~/vendor/simple-datatables/style.css"  rel="stylesheet">
    <!-- Template Main CSS File -->
    <link  href="~/css/style.css"  rel="stylesheet">
    <!-- =======================================================
    * Template Name: NiceAdmin
    * Updated: May 30 2023 with Bootstrap v5.3.0
    * Template URL: https://bootstrapmade.com/nice-admin-bootstrap-admin-html-template/
    * Author: BootstrapMade.com
    * License: https://bootstrapmade.com/license/
    ======================================================== -->
</head>
<body>
@RenderBody()
<a  href="#"  class="back-to-top d-flex align-items-center justify-content-center"><i  class="bi bi-arrow-up-short"></i></a>

<!-- Vendor JS Files -->
<script  src="~/vendor/apexcharts/apexcharts.min.js"></script>
<script  src="~/vendor/bootstrap/js/bootstrap.bundle.min.js"></script>
<script  src="~/vendor/chart.js/chart.umd.js"></script>
<script  src="~/vendor/echarts/echarts.min.js"></script>
<script  src="~/vendor/quill/quill.min.js"></script>
<script  src="~/vendor/simple-datatables/simple-datatables.js"></script>
<script  src="~/vendor/tinymce/tinymce.min.js"></script>
<script  src="~/vendor/php-email-form/validate.js"></script>
<!-- Template Main JS File -->
<script src="~/js/main.js"></script>
<script src="~/lib/jquery/dist/jquery.min.js"></script>
<script src="~/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
@await RenderSectionAsync("Scripts", required: false)
</body>
</html>
```

## Step 2: Create the `UserLoginModel` in the UserModel
Create a model that represents the login form data (Username and Password), and add data annotations for validation.

### Code:

```csharp
public class UserLoginModel
{
    [Required(ErrorMessage = "Username is required.")]
    public string UserName { get; set; }

    [Required(ErrorMessage = "Password is required.")]
    public string Password { get; set; }
}
```

## Step 3: Implement the Login Logic in `UserController.cs`
Handle the login process in the `UserController`. It uses the stored procedure `PR_User_Login` to validate user credentials. If the credentials are correct, it stores the user data in the session.

### Code:

```csharp
public IActionResult Login(UserLoginModel userLoginModel)
    {
        try
        {
            if (ModelState.IsValid)
            {
                string connectionString = this.configuration.GetConnectionString("ConnectionString");
                SqlConnection sqlConnection = new SqlConnection(connectionString);
                sqlConnection.Open();
                SqlCommand sqlCommand = sqlConnection.CreateCommand();
                sqlCommand.CommandType = System.Data.CommandType.StoredProcedure;
                sqlCommand.CommandText = "PR_User_Login";
                sqlCommand.Parameters.Add("@UserName", SqlDbType.VarChar).Value = userLoginModel.UserName;
                sqlCommand.Parameters.Add("@Password", SqlDbType.VarChar).Value = userLoginModel.Password;
                SqlDataReader sqlDataReader = sqlCommand.ExecuteReader();
                DataTable dataTable = new DataTable();
                dataTable.Load(sqlDataReader);
                foreach (DataRow dr in dataTable.Rows)
                {
                    HttpContext.Session.SetString("UserID", dr["UserID"].ToString());
                    HttpContext.Session.SetString("UserName", dr["UserName"].ToString());
                }

                return RedirectToAction("ProductList", "Product");
            }
        }
        catch (Exception e)
        {
            TempData["ErrorMessage"] = e.Message;
        }

        return RedirectToAction("Login");
    }
```

## Step 3: Create the Login Page (`Login.cshtml`)
Set up your login page to accept the username and password. Use `asp-for` to bind the model properties and `asp-validation-for` to display validation errors.

### Code:

```html
@{
    Layout = "~/Views/Shared/_Layout_Login.cshtml";
}

@model CoffeeShop.Models.UserLoginModel

<main class="main" id="main">
    <section class="section register min-vh-100 d-flex flex-column align-items-center justify-content-center py-4">
        <div class="container">
            <div class="row justify-content-center">
                <div class="col-lg-4 col-md-6 d-flex flex-column align-items-center justify-content-center">

                    <div class="card mb-3">

                        <div class="card-body">

                            <div class="pt-4 pb-2">
                                <h5 class="card-title text-center pb-0 fs-4">Login to Your Account</h5>
                                @if (TempData["ErrorMessage"] != null)
                                {
                                    <div class="text-danger">@Html.Raw(TempData["Error"]) </div>
                                }
                                <p class="text-center small">Enter your username & password to login</p>
                            </div>

                            <form class="row g-3 needs-validation" asp-action="Login" asp-controller="User">
                                <div asp-validation-summary="All" class="text-danger"></div>
                                <div class="col-12">
                                    <label for="yourUsername" class="form-label">Username</label>
                                    <div class="input-group has-validation">
                                        <span class="input-group-text" id="inputGroupPrepend"></span>
                                        <input type="text" class="form-control" asp-for="UserName" id="yourUsername">
                                        <span asp-validation-for="UserName" class="text-danger"></span>
                                    </div>
                                </div>

                                <div class="col-12">
                                    <label for="yourPassword" class="form-label">Password</label>
                                    <input type="password" class="form-control" asp-for="Password" id="yourPassword">
                                    <span asp-validation-for="Password" class="text-danger"></span>
                                </div>

                                <div class="col-12">
                                    <button class="btn btn-primary w-100" type="submit">Login</button>
                                </div>
                            </form>
                        </div>
                    </div>
                </div>
            </div>
        </div>

    </section>
</main>

@section Scripts{
    @{
        await Html.RenderPartialAsync("_ValidationScriptsPartial");
    }
}
```

## Step 5: Implement Logout in `SEC_UserController.cs`
Create an action to clear the session when the user logs out.

### Code:

```csharp
[HttpPost]
public IActionResult Logout()
{
    HttpContext.Session.Clear();
    return RedirectToAction("Login", "User");
}
```

## Step 6: Add Session and Authorization Logic with `CheckAccess.cs`
To restrict access based on the session, implement `CheckAccess` to ensure that a user cannot access pages without logging in.

### Code:

```csharp
public class CheckAccess : ActionFilterAttribute, IAuthorizationFilter
{
    public void OnAuthorization(AuthorizationFilterContext filterContext)
    {
        if (filterContext.HttpContext.Session.GetString("UserID") == null)
        {
            filterContext.Result = new RedirectResult("~/SEC_User/Login");
        }
    }

    public override void OnResultExecuting(ResultExecutingContext context)
    {
        context.HttpContext.Response.Headers["Cache-Control"] = "no-cache, no-store, must-revalidate";
        context.HttpContext.Response.Headers["Expires"] = "-1";
        context.HttpContext.Response.Headers["Pragma"] = "no-cache";
        base.OnResultExecuting(context);
    }
}
```

## Step 7: Add Method For Accessing UserName,Address From Session in  `CommonVariable.cs`
To restrict access based on the session, implement `CheckAccess` to ensure that a user cannot access pages without logging in.

### Code:

```csharp
public static class CommonVariable  
{  
  private static IHttpContextAccessor _httpContextAccessor;  
  
  static CommonVariable()  
 {  _httpContextAccessor = new HttpContextAccessor();  
 }  
  public static int? UserID()  
 {  int? UserID = null;  
  if (_httpContextAccessor.HttpContext.Session.GetString("UserID") != null)  
 { UserID =  Convert.ToInt32(_httpContextAccessor.HttpContext.Session.GetString("UserID").ToString());  
 }  
  return UserID;  
 }  
  public static string? UserName()  
 {  string? UserName = null;  
  if (_httpContextAccessor.HttpContext.Session.GetString("UserName") != null)  
 { UserName =  _httpContextAccessor.HttpContext.Session.GetString("UserName").ToString();  
 }  
  return UserName;  
 }}
```


## Step 8: **Configure Session in Startup.cs or Program.cs**
**In Program.cs (for ASP.NET Core 6.0 or later)**

If you are using ASP.NET Core 6.0 or later, the configuration is done in Program.cs.

1. **Add Session Services:**

Add session configuration inside the builder.Services section.

### Code:

```csharp
var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddDistributedMemoryCache();
builder.Services.AddSession(options =>
{
    options.IdleTimeout = TimeSpan.FromMinutes(30); // Set session timeout
    options.Cookie.HttpOnly = true; // Secure the session cookie
    options.Cookie.IsEssential = true; // Ensure cookie is essential
});

builder.Services.AddControllersWithViews();

var app = builder.Build();
```

**Enable Session in the Pipeline:**

After configuring the services, make sure the session middleware is enabled in the request pipeline.

```csharp
// Enable session middleware
app.UseSession(); // Must be added before app.MapControllerRoute

app.UseHttpsRedirection();
app.UseStaticFiles();
app.UseRouting();

// enables the authentication middleware in ASP.NET Core to handle user authentication for securing endpoints.​
app.UseAuthentication();

app.UseAuthorization();

app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Home}/{action=Index}/{id?}");

app.Run();
```

