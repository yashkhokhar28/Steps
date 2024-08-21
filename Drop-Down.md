# Implementing a Drop-Down in ASP.NET Core MVC

**Prerequisite**: Ensure the Drop-Down Procedure is created in your database.

### Drop-Down Procedure

Below is the SQL stored procedure for retrieving user data for the drop-down list:

```sql
CREATE PROCEDURE [dbo].[PR_Customer_DropDown]
AS
BEGIN
    SELECT
	[dbo].[Customer].[CustomerID],
        [dbo].[Customer].[CustomerName]
    FROM
        [dbo].[Customer]
END
```

## Step 1: Create a Model for the Drop-Down

In your `OrderModel.cs`, create a model that represents the drop-down data.


example: inside UserModel create a UserDropDownModel

```csharp
public class CustomerDropDownModel
{
    public int CustomerID { get; set; }
    public string CustomerName { get; set; }
}
```

## Step 2: Implement Logic to Fetch Data for the Drop-Down in the Controller

Create New Method `CustomerDropDown` add logic to retrieve the user data for the drop-down list from the database.

```csharp
public void CustomerDropDown()
{
    string connectionString = this._configuration.GetConnectionString("ConnectionString");
    SqlConnection connection1 = new SqlConnection(connectionString);
    connection1.Open();
    SqlCommand command1 = connection1.CreateCommand();
    command1.CommandType = System.Data.CommandType.StoredProcedure;
    command1.CommandText = "PR_Customer_DropDown";
    SqlDataReader reader1 = command1.ExecuteReader();
    DataTable dataTable1 = new DataTable();
    dataTable1.Load(reader1);
    List<CustomerDropDownModel> customerList = new List<CustomerDropDownModel>();
    foreach (DataRow data in dataTable1.Rows)
    {
        CustomerDropDownModel customerDropDownModel = new CustomerDropDownModel();
        customerDropDownModel.CustomerID = Convert.ToInt32(data["CustomerID"]);
        customerDropDownModel.CustomerName = data["CustomerName"].ToString();
        customerList.Add(customerDropDownModel);
    }
    ViewBag.CustomerList = customerList;
}
```

## Step 3: Call This Method in `OrderAddEdit` Action Method and `OrderSave` method where `ModelState.IsValid` gets False

In the `OrderAddEdit.cshtml` file, add a `<select>` tag for the drop-down list and use the `asp-items` tag helper to bind the data.

```csharp
public IActionResult OrderAddEdit()
{
    CustomerDropDown();
    return View();
}
```

```csharp
public IActionResult OrderSave(OrderModel orderModel)
{
    if (ModelState.IsValid)
    {
        return RedirectToAction("OrderList");
    }
    CustomerDropDown();
    return View("OrderAddEdit", orderModel);
}
```

## Step 4: Add the Drop-Down in `OrderAddEdit.cshtml`

In the `OrderAddEdit.cshtml` file, add a `<select>` tag for the drop-down list and use the `asp-items` tag helper to bind the data.

```csharp
<select class="form-control" asp-for="CustomerID" asp-items="@(new SelectList(ViewBag.CustomerList, "CustomerID", "CustomerName"))">
    <option value="">Select Customer</option>
</select>
```

another way to add the drop-down list is to use the `foreach` loop in the view file.

```csharp
<select class="form-control" asp-for="CustomerID">
    <option value="">Select Customer</option>
    @foreach (var customer in ViewBag.CustomerList)
    {
        <option value="@customer.CustomerID">@customer.CustomerName</option>
    }
</select>
```

## Step 5: Test the Drop-Down

Open the Add-Edit page in your application and verify that the drop-down list is populated with the correct data from the database.

```

```
