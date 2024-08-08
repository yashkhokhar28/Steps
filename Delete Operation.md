# Delete a Data

**Prerequisite**: Delete Procedure

## Step 1

Add a ProductDelete Action method in Controller

```csharp
string connectionString = this.Configuration.GetConnectionString("ConnectionString");
            SqlConnection connection = new SqlConnection(connectionString);
            connection.Open();
            SqlCommand command = connection.CreateCommand();
            command.CommandType = CommandType.StoredProcedure;
            command.CommandText = "PR_Product_DeleteByPK";
            command.Parameters.AddWithValue("@ProductID", CountryID);
            command.ExecuteNonQuery();
            return RedirectToAction("ProductList");
```

## Step 2

Add Link to List Page

```csharp
<a class="btn btn-outline-danger btn-xs" asp-controller="Product" asp-action="ProductDelete" asp-route-ProductID="@dataRow["ProductID"]">
 <i class="bi bi-x"></i>
</a>
```

## Step 3

Check Delete Operation
