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
<link  href="assets/img/favicon.png"  rel="icon">
<link  href="assets/img/apple-touch-icon.png"  rel="apple-touch-icon">

<!-- Google Fonts -->
<link  href="https://fonts.gstatic.com"  rel="preconnect">
<link  href="https://fonts.googleapis.com/css?family=Open+Sans:300,300i,400,400i,600,600i,700,700i|Nunito:300,300i,400,400i,600,600i,700,700i|Poppins:300,300i,400,400i,500,500i,600,600i,700,700i"  rel="stylesheet">
<!-- Vendor CSS Files -->
<link  href="assets/vendor/bootstrap/css/bootstrap.min.css"  rel="stylesheet">
<link  href="assets/vendor/bootstrap-icons/bootstrap-icons.css"  rel="stylesheet">
<link  href="assets/vendor/boxicons/css/boxicons.min.css"  rel="stylesheet">
<link  href="assets/vendor/quill/quill.snow.css"  rel="stylesheet">
<link  href="assets/vendor/quill/quill.bubble.css"  rel="stylesheet">
<link  href="assets/vendor/remixicon/remixicon.css"  rel="stylesheet">
<link  href="assets/vendor/simple-datatables/style.css"  rel="stylesheet">
<!-- Template Main CSS File -->
<link  href="assets/css/style.css"  rel="stylesheet">
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
<script  src="assets/vendor/apexcharts/apexcharts.min.js"></script>
<script  src="assets/vendor/bootstrap/js/bootstrap.bundle.min.js"></script>
<script  src="assets/vendor/chart.js/chart.umd.js"></script>
<script  src="assets/vendor/echarts/echarts.min.js"></script>
<script  src="assets/vendor/quill/quill.min.js"></script>
<script  src="assets/vendor/simple-datatables/simple-datatables.js"></script>
<script  src="assets/vendor/tinymce/tinymce.min.js"></script>
<script  src="assets/vendor/php-email-form/validate.js"></script>
<!-- Template Main JS File -->
<script  src="assets/js/main.js"></script>
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
[HttpPost]
public IActionResult Login(UserLoginModel userLoginModel)
{
    if (ModelState.IsValid)
    {
        SqlConnection conn = new SqlConnection(this.Configuration.GetConnectionString("ConnectionString")))
        {
            conn.Open();

            SqlCommand objCmd = conn.CreateCommand();
            objCmd.CommandType = System.Data.CommandType.StoredProcedure;
            objCmd.CommandText = "PR_SEC_User_Login";
            objCmd.Parameters.AddWithValue("@UserName", userLoginModel.UserName);
            objCmd.Parameters.AddWithValue("@Password", userLoginModel.Password);

            SqlDataReader objSDR = objCmd.ExecuteReader();
            DataTable dtLogin = new DataTable();

            // If no user found
            if (!objSDR.HasRows)
            {
                TempData["ErrorMessage"] = "Invalid Username or Password.";
                return RedirectToAction("Login");
            }
            else
            {
                dtLogin.Load(objSDR);
                foreach (DataRow dr in dtLogin.Rows)
                {
                    HttpContext.Session.SetString("UserID", dr["UserID"].ToString());
                    HttpContext.Session.SetString("UserName", dr["UserName"].ToString());
                }

                return RedirectToAction("Index", "Home");
            }
        }
    }

    TempData["ErrorMessage"] = ErrorMsg;
    return RedirectToAction("Login");
}
```

## Step 3: Create the Login Page (`Login.cshtml`)
Set up your login page to accept the username and password. Use `asp-for` to bind the model properties and `asp-validation-for` to display validation errors.

### Code:

```html
@{
    Layout = "_LoginLayout";
}
@model UserLoginModel

<div class="container-login100">
    <div class="wrap-login100">
        <div class="login100-form-title" style="background-image: url('~/Login/images/bg-01.jpg');">
            <span class="login100-form-title-1">Sign In</span>
        </div>
        
        @if(TempData["ErrorMessage"] != null)
        {
            <div class="text-danger">
                @Html.Raw(TempData["ErrorMessage"])
            </div>
        }
        
        <form class="login100-form validate-form" asp-action="Login">
            <div class="wrap-input100" data-validate="Username is required">
                <span class="label-input100">Username</span>
                <input class="input100" type="text" asp-for="UserName" placeholder="Enter username">
                <span asp-validation-for="UserName" class="text-danger"></span>
            </div>

            <div class="wrap-input100" data-validate="Password is required">
                <span class="label-input100">Password</span>
                <input class="input100" type="password" asp-for="Password" placeholder="Enter password">
                <span asp-validation-for="Password" class="text-danger"></span>
            </div>

            <div class="container-login100-form-btn">
                <button type="submit" class="login100-form-btn">Login</button>
            </div>
        </form>
    </div>
</div>
```

## Step 5: Implement Logout in `SEC_UserController.cs`
Create an action to clear the session when the user logs out.

### Code:

```csharp
[HttpPost]
public IActionResult Logout()
{
    HttpContext.Session.Clear();
    return RedirectToAction("Login");
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
