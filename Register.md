
# Implementing Login Functionality in ASP.NET Core

## Prerequisite

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

## Step 1: Layout Setup in `_Layout_Register.cshtml`
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

## Step 2: Create the `UserRegisterModel` in the UserModel

### Code:

```csharp
public class UserRegisterModel  
{  
  public int? UserID { get; set; }  
  
 [Required(ErrorMessage = "Username is required.")]  
  public string UserName { get; set; }  
  
 [Required(ErrorMessage = "Password is required.")]  
  public string Password { get; set; }  
  
 [Required(ErrorMessage = "Email is required.")]  
  public string Email { get; set; }  
  
 [Required(ErrorMessage = "Mobile Number is required.")]  
  public string MobileNo { get; set; }  
  
 [Required(ErrorMessage = "Address is required.")]  
  public string Address { get; set; }  
}
```

## Step 3: Implement the Register Logic in `UserController.cs`

### Code:

```csharp
public IActionResult UserRegister(UserRegisterModel userRegisterModel)  
{  
  try  
  {  
  if (ModelState.IsValid)  
 {  string connectionString = this.configuration.GetConnectionString("ConnectionString");  
  SqlConnection sqlConnection = new SqlConnection(connectionString);  
 sqlConnection.Open();  
  SqlCommand sqlCommand = sqlConnection.CreateCommand();  
 sqlCommand.CommandType = System.Data.CommandType.StoredProcedure;  
 sqlCommand.CommandText = "PR_User_Register";  
 sqlCommand.Parameters.Add("@UserName", SqlDbType.VarChar).Value = userRegisterModel.UserName;  
 sqlCommand.Parameters.Add("@Password", SqlDbType.VarChar).Value = userRegisterModel.Password;  
 sqlCommand.Parameters.Add("@Email", SqlDbType.VarChar).Value = userRegisterModel.Email;  
 sqlCommand.Parameters.Add("@MobileNo", SqlDbType.VarChar).Value = userRegisterModel.MobileNo;  
 sqlCommand.Parameters.Add("@Address", SqlDbType.VarChar).Value = userRegisterModel.Address;  
 sqlCommand.ExecuteNonQuery();  
  return RedirectToAction("Login", "User");  
 } }  catch (Exception e)  
 {  TempData["ErrorMessage"] = e.Message;  
  return RedirectToAction("Register");  
 }  
  return RedirectToAction("Register");  
}
```

## Step 3: Create the Register Page (`Register.cshtml`)
Set up your register page. Use `asp-for` to bind the model properties and `asp-validation-for` to display validation errors.

### Code:

```html
@{
    Layout = "~/Views/Shared/_Layout_Register.cshtml";
}

@model CoffeeShop.Models.UserRegisterModel


<main>
    <div class="container">
        @if (TempData["ErrorMessage"] != null)
        {
            <div class="alert alert-danger">
                @TempData["ErrorMessage"]
            </div>
        }
        <section class="section register min-vh-100 d-flex flex-column align-items-center justify-content-center py-4">
            <div class="container">
                <div class="row justify-content-center">
                    <div class="col-lg-4 col-md-6 d-flex flex-column align-items-center justify-content-center">

                        <div class="d-flex justify-content-center py-4">
                            <a class="logo d-flex align-items-center w-auto">
                                <img src="~/img/logo.png" alt="">
                                <span class="d-none d-lg-block">NiceAdmin</span>
                            </a>
                        </div><!-- End Logo -->

                        <div class="card mb-3">

                            <div class="card-body">

                                <div class="pt-4 pb-2">
                                    <h5 class="card-title text-center pb-0 fs-4">Create an Account</h5>
                                    <p class="text-center small">Enter your personal details to create account</p>
                                </div>

                                <form class="row g-3 needs-validation" asp-controller="User" asp-action="UserRegister" novalidate>
                                    <div class="col-12">
                                        <label for="yourName" class="form-label">UserName</label>
                                        <input type="text" asp-for="UserName" class="form-control" id="yourName">
                                        <span class="text-danger" asp-validation-for="UserName"></span>
                                    </div>

                                    <div class="col-12">
                                        <label for="yourEmail" class="form-label">Password</label>
                                        <input type="password" asp-for="Password" class="form-control" id="yourEmail">
                                        <span class="text-danger" asp-validation-for="Password"></span>
                                    </div>

                                    <div class="col-12">
                                        <label for="yourEmail" class="form-label">Email</label>
                                        <input type="email" asp-for="Email" class="form-control" id="yourEmail">
                                        <span class="text-danger" asp-validation-for="Email"></span>
                                    </div>

                                    <div class="col-12">
                                        <label for="yourEmail" class="form-label">Mobile Number</label>
                                        <input type="number" asp-for="MobileNo" class="form-control" id="yourEmail">
                                        <span class="text-danger" asp-validation-for="MobileNo"></span>
                                    </div>

                                    <div class="col-12">
                                        <label for="yourEmail" class="form-label">Address</label>
                                        <input type="text" asp-for="Address" class="form-control" id="yourEmail">
                                        <span class="text-danger" asp-validation-for="Address"></span>
                                    </div>

                                    <div class="col-12">
                                        <div class="form-check">
                                            <input class="form-check-input" name="terms" type="checkbox" value="" id="acceptTerms" required>
                                            <label class="form-check-label" for="acceptTerms">I agree and accept the <a href="#">terms and conditions</a></label>
                                            <div class="invalid-feedback">You must agree before submitting.</div>
                                        </div>
                                    </div>
                                    <div class="col-12">
                                        <button class="btn btn-primary w-100" type="submit">Create Account</button>
                                    </div>
                                    <div class="col-12">
                                        <p class="small mb-0">Already have an account? <a asp-controller="User" asp-action="Login">Log in</a></p>
                                    </div>
                                </form>

                            </div>
                        </div>


                    </div>
                </div>
            </div>

        </section>

    </div>
</main><!-- End #main -->

@section Scripts{
    @{
        await Html.RenderPartialAsync("_ValidationScriptsPartial");
    }
}
```
