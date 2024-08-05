
# Data Annotation & Model Binding

**Prerequisite**: Add-Edit Page, Model, lib folder, script (tinyurl.com/libfolder)

## Step 1
Add Appropriate Data Annotation in Model With Error Message

## Step 2
Import Model in Product Add Edit Page

## Step 3
Apply Model Binding in Each TextField / DropDown Using `asp-for` tag helper

## Step 4
Add Hidden Field (Primary Key)

## Step 5
Add `asp-validation-summary` above all the text field and set it to model only to show proper error message of model 
(use case is to show all the error messages at one place)

## Step 6
Add `asp-validation-for` to each TextField / DropDown to show particular property’s error message

## Step 7
Add RenderPartialAsync section Script in add-edit page

## Step 8
Add RenderSectionAsync in `_Layout.cshtml` (for optionally allow script)

## Step 9
In controller create one action method to apply server side validation

## Step 10
Check server side and client side validation
