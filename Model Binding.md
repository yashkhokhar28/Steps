
# Model Binding in ASP.NET Core MVC

**Prerequisite**: Ensure the Add-Edit Page, Model, `lib`

## Step 1 : Import the Model in the Product Add-Edit Page

Ensure the model is correctly imported into your Product Add-Edit page to allow for model binding and validation.

## Step 2: Apply Model Binding Using `asp-for` Tag Helper

Use the `asp-for` tag helper in each text field or dropdown to bind the model properties seamlessly.

**Example:**
```html
<input type="text" class="form-control" asp-for="ProductName" placeholder="Enter Product Name"/>
```

## Step 3: Add a Hidden Field for the Primary Key

Include a hidden field in your form to hold the primary key value of the model.

```html
@Html.HiddenFor(x => x.ProductID)
```
