# Drop-Down

**Prerequisite**: Drop-Down Procedure

**Drop-Down Procedure**

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

## Step 1

Create Model For Drop-Down in ProductModel.cs

```csharp
public class UserDropDownModel
{
  public int UserID { get; set; }
  public string UserName { get; set; }
}
```

## Step 2

Add a Logic of Getting Data For Drop-Down in ProductAddEdit Page Action method in Controller

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

## Step 3

Add Select Tag in ProductAddEdit.cshtml file For Drop-Down and use asp-items Tag Helper

```csharp
<select class="form-control" asp-for="UserID" asp-items="@(new SelectList(ViewBag.UserList, "UserID", "UserName"))">
 <option value="">Select User</option>
</select>
```

## Step 4

Open Add-Edit Page and Check Data Of Drop-Down
