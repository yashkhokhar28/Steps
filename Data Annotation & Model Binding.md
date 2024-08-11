# Data Annotation & Model Binding in ASP.NET Core MVC

**Prerequisite**: Ensure the Add-Edit Page, Model, `lib` folder, and necessary scripts (available at [tinyurl.com/libfolder](https://tinyurl.com/libfolder)) are in place.

## Step 1: Include Required Scripts

Add the following scripts to your page to enable jQuery and Bootstrap functionalities:

```html
<script src="~/lib/jquery/dist/jquery.min.js"></script>
<script src="~/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
```

## Step 2: Add Data Annotations in the Model

Enhance your model by applying appropriate data annotations. Ensure that each annotation includes a relevant error message to guide users in case of invalid input.

## Step 3: Import the Model in the Product Add-Edit Page

Ensure the model is correctly imported into your Product Add-Edit page to allow for model binding and validation.

## Step 4: Apply Model Binding Using `asp-for` Tag Helper

Use the `asp-for` tag helper in each text field or dropdown to bind the model properties seamlessly.

**Example:**
```html
<input type="text" class="form-control" asp-for="ProductName" placeholder="Enter Product Name"/>
```

## Step 5: Add a Hidden Field for the Primary Key

Include a hidden field in your form to hold the primary key value of the model.

```html
@Html.HiddenFor(x => x.ProductID)
```

## Step 6: Add Validation Summary to Display Error Messages

Place the `asp-validation-summary` tag above all text fields to display model error messages in a single location. Set it to `ModelOnly` to show only the errors related to the model.

**Example:**
```html
<div asp-validation-summary="ModelOnly" class="text-danger"></div>
```

## Step 7: Add `asp-validation-for` for Individual Field Validation

Attach the `asp-validation-for` tag to each text field or dropdown to display specific error messages for individual properties.

**Example:**
```html
<span asp-validation-for="ProductPrice" class="text-danger"></span>
```

## Step 8: Render Partial Scripts for Validation

Include the following code in your Add-Edit page to render the partial scripts required for validation:

```js
@section Scripts{  
    @{ await Html.RenderPartialAsync("_ValidationScriptsPartial"); }
}
```

## Step 9: Optional Script Rendering in `_Layout.cshtml`

Add the following code in `_Layout.cshtml` to allow optional rendering of scripts in your views:

```js
@await RenderSectionAsync("Scripts", required: false)
```

## Step 10: Implement Server-Side Validation in the Controller

In your controller, create an action method that applies server-side validation logic to ensure the data is valid even after the client-side validation.

## Step 11: Test Both Server-Side and Client-Side Validation

Verify that the validation works as expected on both the server-side and client-side to ensure a robust data entry process.
