# SQL Connection & Data List

**Prerequisite**: SQL Server Installation, SelectAll Procedure

## Step 1

Add Static Data in SQL Server [here](https://codeshare.io/ar)

## Step 2

Add Connection String in `appsettings.json` file

### For Windows Users:

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

```json
"ConnectionStrings": {
    "ConnectionString": "Data Source=SQL Server Name;Initial Catalog=DatabaseName;User id=userID ; password=Password;"
}
```

**Example:**

```json
"ConnectionStrings": {
    "ConnectionString": "Data Source=localhost;Initial Catalog=Practice;User id=SA ; password=MyStrongPass123;"
}
```

## Step 3

Go to Controller and make Configuration variable and set it using constructor
```js
private IConfiguration configuration;

        public ProductController(IConfiguration _configuration)
        {
            configuration = _configuration;
        }
```

## Step 4

install `System.Data.SqlClient` from Nuget Package Manager

## Step 5

In List Page Action Method Write Logic to Fetch Data:

```csharp
string connectionString = this.Configuration.GetConnectionString("ConnectionString");
SqlConnection connection = new SqlConnection(connectionString);
connection.Open();
SqlCommand command = connection.CreateCommand();
command.CommandType = CommandType.StoredProcedure;
command.CommandText = "PR_Country_SelectAll";
SqlDataReader reader = command.ExecuteReader();
DataTable table = new DataTable();
table.Load(reader);
return View(table);
```

## Step 6

In View Page import Data Table and use `foreach` loop (iterate through data table) and display the data
