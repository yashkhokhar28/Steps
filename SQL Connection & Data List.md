# Establishing SQL Connection & Displaying Data in ASP.NET Core MVC

**Prerequisite**: Ensure SQL Server is installed and the `SelectAll` stored procedure is created.

```sql
CREATE PROCEDURE [dbo].[PR_Product_SelectAll]
AS
BEGIN
    SELECT 
        [dbo].[Product].[ProductID],
        [dbo].[Product].[ProductName],
        [dbo].[Product].[ProductPrice],
        [dbo].[Product].[ProductCode],
        [dbo].[Product].[Description],
        [dbo].[Product].[UserID],
        [dbo].[User].[UserName]
    FROM
        [dbo].[Product]
    INNER JOIN
        [dbo].[User] ON [dbo].[Product].[UserID] = [dbo].[User].[UserID]
END
```

## Step 1: Add Static Data to SQL Server

Insert static data into your SQL Server database using the provided script [here](https://codeshare.io/ar).

## Step 2: Configure the Connection String in `appsettings.json`

### For Windows Users:

Add the connection string for your SQL Server database in the `appsettings.json` file as shown below:

```json
"ConnectionStrings": {
    "ConnectionString": "Data Source=SQL Server Name;Initial Catalog=DatabaseName;Integrated Security=true;"
}
```

**Example:**

```json
"ConnectionStrings": {
    "ConnectionString": "Data Source=LAPTOP-LBMAFD6U\\SQLEXPRESS;Initial Catalog=StudentMaster;Integrated Security=true;"
}
```

### For Mac Users:

For Mac users, the connection string should include the user ID and password:

```json
"ConnectionStrings": {
    "ConnectionString": "Data Source=SQL Server Name;Initial Catalog=DatabaseName;User id=userID; password=Password;"
}
```

**Example:**

```json
"ConnectionStrings": {
    "ConnectionString": "Data Source=localhost;Initial Catalog=Practice;User id=SA; password=MyStrongPass123;"
}
```

## Step 3: Set Up the Configuration Variable in the Controller

In your controller, declare a configuration variable and initialize it using the constructor. This will allow you to access the connection string from the configuration.

```csharp
private IConfiguration configuration;

public ProductController(IConfiguration _configuration)
{
    configuration = _configuration;
}
```

## Step 4: Install the `System.Data.SqlClient` Package

Install the `System.Data.SqlClient` package from the NuGet Package Manager to enable SQL Server connectivity.

- Navigate to **Tools** -> **NuGet Package Manager** -> **Manage NuGet Packages for Solution**.
- Ensure the project is not running during the installation.

## Step 5: Write Logic to Fetch Data in the List Page Action Method

In the action method for your list page, add the following logic to fetch data from the SQL Server database:

```csharp
string connectionString = this.Configuration.GetConnectionString("ConnectionString");
SqlConnection connection = new SqlConnection(connectionString);
connection.Open();
SqlCommand command = connection.CreateCommand();
command.CommandType = CommandType.StoredProcedure;
command.CommandText = "PR_Product_SelectAll";
SqlDataReader reader = command.ExecuteReader();
DataTable table = new DataTable();
table.Load(reader);
return View(table);
```

## Step 6: Display the Data in the View

In your view page, import the `DataTable` and use a `foreach` loop to iterate through the data and display it:

```csharp
@model DataTable
@using System.Data

<table class="table">
    <thead>
        <tr>
            <th>ProductID</th>
            <th>ProductName</th>
            <th>ProductPrice</th>
            <th>ProductCode</th>
            <th>Description</th>
            <th>UserName</th>
        </tr>
    </thead>
    <tbody>
        @foreach (DataRow row in Model.Rows)
        {
            <tr>
                <td>@row["ProductID"]</td>
                <td>@row["ProductName"]</td>
                <td>@row["ProductPrice"]</td>
                <td>@row["ProductCode"]</td>
                <td>@row["Description"]</td>
                <td>@row["UserName"]</td>
            </tr>
        }
    </tbody>
</table>
```
