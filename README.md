catch (SqlException ex)
{
    Console.WriteLine("SQL ERROR MESSAGE: " + ex.Message);

    foreach (SqlError err in ex.Errors)
    {
        Console.WriteLine($"Error {err.Number}: {err.Message}");
    }

    return Json(new
    {
        success = false,
        message = ex.Message
    });
}

try
{
    using (var connection = GetRFIDConnectionString())
    {
        Console.WriteLine("Connection object created");

        await connection.OpenAsync();

        Console.WriteLine("Connection opened");
    }
}
catch (Exception ex)
{
    Console.WriteLine("EXCEPTION TYPE: " + ex.GetType().FullName);
    Console.WriteLine("MESSAGE: " + ex.Message);
    Console.WriteLine("INNER: " + ex.InnerException?.Message);

    return Json(new
    {
        success = false,
        message = ex.Message
    });
}


   
   
   
   at Microsoft.Data.SqlClient.SqlConnection.OpenAsync(CancellationToken cancellationToken)
   at System.Data.Common.DbConnection.OpenAsync()
   at TEHP.Controllers.AccountController.Login(AppLogin login, String returnUrl) in D:\Irshad(DotNet Core)\TEHP\TEHP\Controllers\AccountController.cs:line 113
