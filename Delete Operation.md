# Deleting a Data Entry in ASP.NET Core MVC

**Prerequisite**: Ensure the Delete Procedure is set up in your database.

### Delete Procedure

Below is the SQL stored procedure for deleting a product from the database:

```sql
CREATE PROCEDURE [dbo].[PR_Product_Delete]
    @ProductID INT
AS
BEGIN
    DELETE FROM [dbo].[Product]
    WHERE [dbo].[Product].[ProductID] = @ProductID
END
```

## Step 1: Implement the `ProductDelete` Action Method in the Controller

Add the following code in your controller to handle the deletion of a product. This method connects to the database, executes the delete procedure, and then redirects to the product list page.

```csharp
public IActionResult ProductDelete(int ProductID)
{
    try
    {
        string connectionString = this._configuration.GetConnectionString("ConnectionString");
        SqlConnection connection = new SqlConnection(connectionString);
        connection.Open();
        SqlCommand command = connection.CreateCommand();
        command.CommandType = CommandType.StoredProcedure;
        command.CommandText = "PR_Product_Delete";
        command.Parameters.Add("@ProductID", SqlDbType.Int).Value = ProductID;
        command.ExecuteNonQuery();
    }
    catch (Exception ex)
    {
        TempData["ErrorMessage"] = ex.Message;
        Console.WriteLine(ex.ToString());
    }
    return RedirectToAction("ProductList");
}
```

## Step 2: Add a Delete Link on the List Page

In the list page, add a delete link/button that calls the `ProductDelete` action method. This link will pass the `ProductID` to the method to identify which product to delete.

```html
<form method="post" asp-controller="Product" asp-action="ProductDelete">
  <input type="hidden" name="ProductID" value="@dataRow["ProductID"]" />
  <button type="submit" class="btn btn-outline-danger btn-xs">
    <i class="bi bi-x"></i>
  </button>
</form>
```

Another way to add a delete link is to use an anchor tag with a `href` attribute that points to the `ProductDelete` action method. This method will be called when the user clicks the link.

```html
<a href="/Product/ProductDelete?ProductID=@dataRow["ProductID"]" class="btn btn-outline-danger btn-xs">
  <i class="bi bi-x"></i>
</a>

```

using the `asp-route-` attribute:

```html
<a asp-controller="Product" asp-action="ProductDelete" asp-route-ProductID="@dataRow["ProductID"]" class="btn btn-outline-danger btn-xs">
  <i class="bi bi-x"></i>
</a>
```

## Step 3: Test the Delete Operation

Ensure that the delete functionality works by testing it in the application. Check that the product is removed from the database and that the list page updates accordingly.
