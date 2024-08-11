# Add-Edit Operation in ASP.NET Core MVC

**Prerequisite**: Procedures for Insert, Update, and Select by Primary Key Operations

### Insert Procedure

```sql
CREATE PROCEDURE [dbo].[PR_Product_Insert]
    @ProductName VARCHAR(100),
    @ProductPrice DECIMAL(10,2),
    @ProductCode VARCHAR(100),
    @Description VARCHAR(100),
    @UserID INT
AS
BEGIN
    INSERT INTO [dbo].[Product]
    (
        [dbo].[Product].[ProductName],
        [dbo].[Product].[ProductPrice],
        [dbo].[Product].[ProductCode],
        [dbo].[Product].[Description],
        [dbo].[Product].[UserID]
    )
    VALUES
    (
        @ProductName,
        @ProductPrice,
        @ProductCode,
        @Description,
        @UserID
    )
END
```

### Update Procedure

```sql
CREATE PROCEDURE [dbo].[PR_Product_Update]
    @ProductID INT,
    @ProductName VARCHAR(100),
    @ProductPrice DECIMAL(10,2),
    @ProductCode VARCHAR(100),
    @Description VARCHAR(100),
    @UserID INT
AS
BEGIN
    UPDATE [dbo].[Product]
    SET
        [dbo].[Product].[ProductName] = @ProductName,
        [dbo].[Product].[ProductPrice] = @ProductPrice,
        [dbo].[Product].[ProductCode] = @ProductCode,
        [dbo].[Product].[Description] = @Description,
        [dbo].[Product].[UserID] = @UserID
    WHERE
        [dbo].[Product].[ProductID] = @ProductID
END
```

### Select By Primary Key Procedure

```sql
CREATE PROCEDURE [dbo].[PR_Product_SelectByPK]
    @ProductID INT
AS
BEGIN
    SELECT
        [dbo].[Product].[ProductID],
        [dbo].[Product].[ProductName],
        [dbo].[Product].[ProductPrice],
        [dbo].[Product].[ProductCode],
        [dbo].[Product].[Description],
        [dbo].[Product].[UserID]
    FROM
        [dbo].[Product]
    WHERE
        [dbo].[Product].[ProductID] = @ProductID
END
```

## Step 1: Implement Logic for Insert-Update Operations in the `ProductSave` Action Method

```csharp
public IActionResult ProductSave(ProductModel productModel)
        {
            string connectionString = this.configuration.GetConnectionString("ConnectionString");
            SqlConnection connection = new SqlConnection(connectionString);
            connection.Open();
            SqlCommand command = connection.CreateCommand();
            command.CommandType = CommandType.StoredProcedure;
            if (ModelState.IsValid)
            {
                if (productModel.ProductID == null)
                {
                    command.CommandText = "PR_Product_Insert";
                    command.Parameters.Add("@ProductName", SqlDbType.VarChar).Value = productModel.ProductName;
                    command.Parameters.Add("@ProductCode", SqlDbType.VarChar).Value = productModel.ProductCode;
                    command.Parameters.Add("@ProductPrice", SqlDbType.Decimal).Value = productModel.ProductPrice;
                    command.Parameters.Add("@Description", SqlDbType.VarChar).Value = productModel.Description;
                    command.Parameters.Add("@UserID", SqlDbType.Int).Value = productModel.UserID;
                }
                else
                {
                    command.CommandText = "PR_Product_Update";
                    command.Parameters.Add("@ProductID", SqlDbType.Int).Value = productModel.ProductID;
                    command.Parameters.Add("@ProductName", SqlDbType.VarChar).Value = productModel.ProductName;
                    command.Parameters.Add("@ProductCode", SqlDbType.VarChar).Value = productModel.ProductCode;
                    command.Parameters.Add("@ProductPrice", SqlDbType.Decimal).Value = productModel.ProductPrice;
                    command.Parameters.Add("@Description", SqlDbType.VarChar).Value = productModel.Description;
                    command.Parameters.Add("@UserID", SqlDbType.Int).Value = productModel.UserID;
                }

                command.ExecuteNonQuery();
                return View("ProductList");
            }

            return View("ProductAddEdit", productModel);
        }
```

## Step 2: Verify Insert Operation

## Step 3: Update Operation (Two Parts)

### Part 1: Fetch and Populate Existing Data in the Text Fields

To retrieve the existing product data and display it in the form for editing, update the `ProductAddEdit` method:

```csharp
public IActionResult ProductAddEdit(int ProductID)
        {
            string connectionString = this.configuration.GetConnectionString("ConnectionString");

            #region User Drop-Down

            SqlConnection connection1 = new SqlConnection(connectionString);
            connection1.Open();
            SqlCommand command1 = connection1.CreateCommand();
            command1.CommandType = System.Data.CommandType.StoredProcedure;
            command1.CommandText = "PR_User_DropDown";
            SqlDataReader reader1 = command1.ExecuteReader();
            DataTable dataTable1 = new DataTable();
            dataTable1.Load(reader1);
            connection1.Close();

            List<UserDropDownModel> users = new List<UserDropDownModel>();

            foreach (DataRow dataRow in dataTable1.Rows)
            {
                UserDropDownModel userDropDownModel = new UserDropDownModel();
                userDropDownModel.UserID = Convert.ToInt32(dataRow["UserID"]);
                userDropDownModel.UserName = dataRow["UserName"].ToString();
                users.Add(userDropDownModel);
            }

            ViewBag.UserList = users;

            #endregion

            #region ProductByID

            SqlConnection connection = new SqlConnection(connectionString);
            connection.Open();
            SqlCommand command = connection.CreateCommand();
            command.CommandType = CommandType.StoredProcedure;
            command.CommandText = "PR_Product_SelectByPK";
            command.Parameters.AddWithValue("@ProductID", ProductID);
            SqlDataReader reader = command.ExecuteReader();
            DataTable table = new DataTable();
            table.Load(reader);
            ProductModel productModel = new ProductModel();

            foreach (DataRow dataRow in table.Rows)
            {
                productModel.ProductID = Convert.ToInt32(@dataRow["ProductID"]);
                productModel.ProductName = @dataRow["ProductName"].ToString();
                productModel.ProductCode = @dataRow["ProductCode"].ToString();
                productModel.ProductPrice = Convert.ToDouble(@dataRow["ProductPrice"]);
                productModel.Description = @dataRow["Description"].ToString();
                productModel.UserID = Convert.ToInt32(@dataRow["UserID"]);
            }

            #endregion

            return View("ProductAddEdit", productModel);
        }
```

### Part 2: Update the Product Data in the Database

The logic for updating the product data has already been implemented in the `ProductSave` method.

## Step 4: Add Edit Button with Link in the List Page

```csharp
<a class="btn btn-outline-success btn-xs" asp-controller="Product" asp-action="ProductAddEdit" asp-route-ProductID="@dataRow["ProductID"]">
    <i class="bi bi-pencil-fill"></i>
</a>
```

## Step 5: Verify Update Operation
