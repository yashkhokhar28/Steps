# Implementing a Drop-Down in ASP.NET Core MVC

**Prerequisite**: Ensure the Drop-Down Procedure is created in your database.

### Drop-Down Procedure

Below is the SQL stored procedure for retrieving user data for the drop-down list:

```sql
CREATE PROCEDURE [dbo].[PR_User_DropDown]
AS
BEGIN
    SELECT
        [dbo].[User].[UserID],
        [dbo].[User].[UserName]
    FROM
        [dbo].[User]
END
```

## Step 1: Create a Model for the Drop-Down

In your `ProductModel.cs`, create a model that represents the drop-down data.

```csharp
public class UserDropDownModel
{
    public int UserID { get; set; }
    public string UserName { get; set; }
}
```

## Step 2: Implement Logic to Fetch Data for the Drop-Down in the Controller

In the `ProductAddEdit` action method, add logic to retrieve the user data for the drop-down list from the database.

```csharp
string connectionString = this.configuration.GetConnectionString("ConnectionString");
SqlConnection connection1 = new SqlConnection(connectionString);
connection1.Open();
SqlCommand command1 = connection1.CreateCommand();
command1.CommandType = System.Data.CommandType.StoredProcedure;
command1.CommandText = "PR_User_DropDown";
SqlDataReader reader1 = command1.ExecuteReader();
DataTable dataTable1 = new DataTable();
dataTable1.Load(reader1);
List<UserDropDownModel> users = new List<UserDropDownModel>();
foreach (DataRow dataRow in dataTable1.Rows)
{
    UserDropDownModel userDropDownModel = new UserDropDownModel();
    userDropDownModel.UserID = Convert.ToInt32(dataRow["UserID"]);
    userDropDownModel.UserName = dataRow["UserName"].ToString();
    users.Add(userDropDownModel);
}
ViewBag.UserList = users;
```

## Step 3: Add the Drop-Down in `ProductAddEdit.cshtml`

In the `ProductAddEdit.cshtml` file, add a `<select>` tag for the drop-down list and use the `asp-items` tag helper to bind the data.

```csharp
<select class="form-control" asp-for="UserID" asp-items="@(new SelectList(ViewBag.UserList, "UserID", "UserName"))">
    <option value="">Select User</option>
</select>
```

## Step 4: Test the Drop-Down

Open the Add-Edit page in your application and verify that the drop-down list is populated with the correct data from the database.
