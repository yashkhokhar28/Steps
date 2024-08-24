
# Validation in ASP.NET Core MVC

Validation in ASP.NET Core MVC can be performed using the following methods:

- **Using ModelState Object (Server Side)**
- **Using Data Annotations (Server Side)**
- **Using jQuery (Client Side)**

**Prerequisite:** Ensure that the Add-Edit Page, Model, `lib` folder, and necessary scripts (available at [tinyurl.com/libfolder](https://tinyurl.com/libfolder)) are properly set up.

## Using jQuery (Client Side)

### Step 1: Include Required Scripts

To enable jQuery and Bootstrap functionalities, add the following scripts to your page:

```html
<script src="~/lib/jquery/dist/jquery.min.js"></script>
<script src="~/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
```

### Step 2: Import the Model into the Product Add-Edit Page

Ensure that the model is correctly imported into your Product Add-Edit page to allow for model binding and validation.

### Step 3: Apply Model Binding Using the `asp-for` Tag Helper

Use the `asp-for` tag helper in each text field or dropdown to seamlessly bind the model properties.

**Example:**

```html
<input type="text" class="form-control" asp-for="ProductName" placeholder="Enter Product Name"/>
```

### Step 4: Add `asp-validation-for` for Individual Field Validation

Attach the `asp-validation-for` tag to each text field or dropdown to display specific error messages for individual properties.

**Example:**

```html
<span asp-validation-for="ProductPrice" class="text-danger"></span>
```

### Step 5: Render Partial Scripts for Validation

Include the following code in your Add-Edit page to render the partial scripts required for validation:

```csharp
@section Scripts {
    @{ await Html.RenderPartialAsync("_ValidationScriptsPartial"); }
}
```

### Step 6: Optional Script Rendering in `_Layout.cshtml`

To allow optional rendering of scripts in your views, add the following code to `_Layout.cshtml`:

```csharp
@await RenderSectionAsync("Scripts", required: false)
```

## Using Data Annotations (Server Side)

### Step 1: Add Data Annotations in the Model

Enhance your model by applying appropriate data annotations. Ensure that each annotation includes a relevant error message to guide users in case of invalid input.

**Example:**

```csharp
public class ProductModel  
{  
    public int? ProductID { get; set; }  
    
    [Required(ErrorMessage = "Product Name is required.")]  
    public string ProductName { get; set; }  
    
    [Required(ErrorMessage = "Product Code is required.")]  
    public string ProductCode { get; set; }  
    
    [Required(ErrorMessage = "Product Price is required.")]  
    public double ProductPrice { get; set; }  
    
    public string Description { get; set; }  
    
    [Required(ErrorMessage = "User is required.")]  
    public int UserID { get; set; }  
}
```

### Step 2: Create an Action Method in the Controller

Implement the following action method to handle the save operation in your controller:

```csharp
public IActionResult ProductSave(ProductModel productModel)  
{  
    if (ModelState.IsValid)  
    {  
        return RedirectToAction("ProductList");  
    }  
    return View("ProductAddEdit", productModel);  
}
```

## Using ModelState Object (Server Side)

### Step 1: Modify the `ProductSave` Method

To inspect ModelState errors before validation, modify the `ProductSave` method as follows:

```csharp
public IActionResult ProductSave(ProductModel productModel)  
{
    foreach (var key in ModelState.Keys)  
    {  
        var state = ModelState[key];  
        foreach (var error in state.Errors)  
        {
            Console.WriteLine($"Key: {key}, Error: {error.ErrorMessage}");  
        }
    }  
    if (ModelState.IsValid)  
    {  
        return RedirectToAction("ProductList");  
    }  
    return View("ProductAddEdit", productModel);  
}
```

This allows you to see the error messages associated with each key in the console, such as:

```csharp
Key: UserID, Error: The value '' is invalid.
Key: Description, Error: The Description field is required.
Key: ProductCode, Error: Product Code is required.
Key: ProductName, Error: Product Name is required.
Key: ProductPrice, Error: The value '' is invalid.
```

The error message **“The value ‘’ is invalid.”** occurs because of the default model binding behavior in ASP.NET Core. When the form is submitted and the **UserID** field is left empty, the model binder tries to bind an empty string to an integer property (UserID), which is invalid.

### Step 2: Remove Default ModelState Errors

To replace the default error messages with custom ones, remove the existing ModelState entries:

```csharp
ModelState.Remove("ProductName");  
ModelState.Remove("ProductCode");  
ModelState.Remove("ProductPrice");  
ModelState.Remove("Description");  
ModelState.Remove("UserID");
```

### Step 3: Add Custom Error Messages

Add custom error messages to the ModelState object:

```csharp
public IActionResult ProductSave(ProductModel productModel)  
{  
    ModelState.Remove("ProductName");  
    ModelState.Remove("ProductCode");  
    ModelState.Remove("ProductPrice");  
    ModelState.Remove("Description");  
    ModelState.Remove("UserID");  

    if (string.IsNullOrEmpty(productModel.ProductName))  
    {
        ModelState.AddModelError("ProductName", "Product Name can't be null or empty.");  
    }  
    if (string.IsNullOrEmpty(productModel.ProductCode))  
    {
        ModelState.AddModelError("ProductCode", "Product Code can't be null or empty.");  
    }  
    if (productModel.ProductPrice <= 0)  
    {
        ModelState.AddModelError("ProductPrice", "Product Price can't be zero or negative.");  
    }  
    if (string.IsNullOrEmpty(productModel.Description))  
    {
        ModelState.AddModelError("Description", "Product Description can't be null or empty.");  
    }  
    if (productModel.UserID <= 0)  
    {
        ModelState.AddModelError("UserID", "UserID can't be zero or negative.");  
    }  

    if (ModelState.IsValid)  
    {
        return RedirectToAction("ProductList");  
    }   
    return View("ProductAddEdit", productModel);  
}
```

With these steps, you will implement both client-side and server-side validation in ASP.NET Core MVC, ensuring that your application handles user input robustly and effectively.
