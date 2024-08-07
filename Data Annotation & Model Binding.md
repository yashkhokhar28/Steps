
Here's the updated guideline for Data Annotation & Model Binding with the added scripts and the first step clarified:

# Data Annotation & Model Binding

**Prerequisite**: Add-Edit Page, Model, lib folder, script (tinyurl.com/libfolder)

## Step 1
Add the following scripts to _Layout.cshtml page below your main JavaScript File:

```html
<script src="~/lib/jquery/dist/jquery.min.js"></script>
<script src="~/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
```

## Step 2
Add Appropriate Data Annotation in Model With Error Message

## Step 3
Import Model in Product Add Edit Page

## Step 4
Apply Model Binding in Each TextField / DropDown Using `asp-for` tag helper

## Step 5
Add Hidden Field (Primary Key)

## Step 6
Add `asp-validation-summary` above all the text fields and set it to model only to show proper error messages of the model (use case is to show all the error messages in one place)

## Step 7
Add `asp-validation-for` to each TextField / DropDown to show the particular property’s error message

## Step 8
Add RenderPartialAsync section Script in the add-edit page

## Step 9
Add RenderSectionAsync in `_Layout.cshtml` (for optionally allowing scripts)

## Step 10
In the controller, create one action method to apply server-side validation

## Step 11
Check server-side and client-side validation
