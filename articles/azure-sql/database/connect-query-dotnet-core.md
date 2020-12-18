---
title: Använd .NET Core för att ansluta och fråga en databas
description: Det här avsnittet visar hur du använder .NET Core för att skapa ett program som ansluter till en databas i Azure SQL Database eller en Azure SQL-hanterad instans och frågar den med hjälp av Transact-SQL-uttryck.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2, devx-track-csharp
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: c1443dcefb12a063a287e89f1c292ba39bd38dc6
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680346"
---
# <a name="quickstart-use-net-core-c-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Snabb start: använda .NET Core (C#) för att fråga en databas i Azure SQL Database eller Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

I den här snabb starten använder du [.net Core](https://www.microsoft.com/net/) och C#-kod för att ansluta till en databas. Sedan kör du en Transact-SQL-instruktion för att fråga efter data.

> [!TIP]
> Följande Microsoft Learn modul hjälper dig att lära dig kostnads fritt hur du [utvecklar och konfigurerar ett ASP.NET-program som frågar en databas i Azure SQL Database](/learn/modules/develop-app-that-queries-azure-sql/)

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabbstarten behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- En databas där du kan köra din fråga. 

[!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

- [.NET Core för ditt operativsystem](https://www.microsoft.com/net/core).

## <a name="get-server-connection-information"></a>Hämta information om Server anslutning

Hämta anslutnings informationen du behöver för att ansluta till databasen i Azure SQL Database. Du behöver det fullständiga servernamnet eller värdnamnet, databasnamnet och inloggningsinformationen för de kommande procedurerna.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Gå till sidan **SQL-databaser**  eller **SQL-hanterade instanser** .

3. På sidan **Översikt** granskar du det fullständigt kvalificerade Server namnet bredvid **Server namnet** för databasen i Azure SQL Database eller det fullständigt kvalificerade Server namnet (eller IP-adressen) bredvid **värd** för en Azure SQL-hanterad instans eller SQL Server på Azure VM. Om du vill kopiera servernamnet eller värdnamnet hovrar du över det och väljer ikonen **Kopiera**.

> [!NOTE]
> Anslutnings information för SQL Server på den virtuella Azure-datorn finns i [Anslut till en SQL Server instans](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="get-adonet-connection-information-optional---sql-database-only"></a>Hämta anslutnings information för ADO.NET (valfritt-SQL Database endast)

1. Gå till bladet databas i Azure Portal och välj **anslutnings strängar** under **Inställningar**.

2. Granska den fullständiga **ADO.NET**-anslutningssträngen.

    ![ADO.NET-anslutningssträng](./media/connect-query-dotnet-core/adonet-connection-string2.png)

3. Kopiera anslutningssträngen **ADO.NET** om du planerar att använda den.
  
## <a name="create-a-new-net-core-project"></a>Skapa ett nytt .NET Core-projekt

1. Öppna en kommandotolk och skapa en mapp med namnet **sqltest**. Gå till mappen och kör detta kommando.

    ```cmd
    dotnet new console
    ```

    Detta kommando skapar nya app-projektfiler, inklusive en inledande C#-kodfil (**Program.cs**), en XML-konfigurationsfil (**sqltest.csproj**) och de binärfiler som behövs.

2. Öppna **sqltest.csproj** i ett redigeringsprogram och klistra in följande XML mellan `<Project>`-taggarna. Detta XML lägger till `System.Data.SqlClient` som ett beroende.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-the-database-in-azure-sql-database"></a>Infoga kod för att fråga databasen i Azure SQL Database

1. Öppna **Program.cs** i ett redigeringsprogram.

2. Ersätt innehållet med nedanstående kod och lägg till lämpliga värden för server, databas, användarnamn och lösenord.

> [!NOTE]
> Om du vill använda en ADO.NET-anslutningssträng ersätter du de 4 raderna i kodinställningen för server, databas, användarnamn och lösenord med raden nedan. Ange ditt användarnamn och lösenord i strängen.
>
>    `builder.ConnectionString="<your_ado_net_connection_string>";`

```csharp
using System;
using System.Data.SqlClient;
using System.Text;

namespace sqltest
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();

                builder.DataSource = "<your_server.database.windows.net>"; 
                builder.UserID = "<your_username>";            
                builder.Password = "<your_password>";     
                builder.InitialCatalog = "<your_database>";
         
                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nQuery data example:");
                    Console.WriteLine("=========================================\n");
                    
                    connection.Open();       

                    String sql = "SELECT name, collation_name FROM sys.databases";

                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        using (SqlDataReader reader = command.ExecuteReader())
                        {
                            while (reader.Read())
                            {
                                Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                            }
                        }
                    }                    
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
            Console.WriteLine("\nDone. Press enter.");
            Console.ReadLine(); 
        }
    }
}
```

## <a name="run-the-code"></a>Kör koden

1. Kör följande kommandon när du uppmanas till det.

   ```cmd
   dotnet restore
   dotnet run
   ```

2. Kontrol lera att raderna returneras.

   ```text
   Query data example:
   =========================================

   master   SQL_Latin1_General_CP1_CI_AS
   tempdb   SQL_Latin1_General_CP1_CI_AS
   WideWorldImporters   Latin1_General_100_CI_AS

   Done. Press enter.
   ```

3. Välj **Retur** för att stänga programfönstret.

## <a name="next-steps"></a>Nästa steg

- [Komma igång med .NET Core för Windows/Linux/macOS med hjälp av kommandoraden](/dotnet/core/tutorials/using-with-xplat-cli).
- Lär dig hur du [ansluter och frågar Azure SQL Database eller en Azure SQL-hanterad instans med hjälp av .NET Framework och Visual Studio](connect-query-dotnet-visual-studio.md).  
- Lär dig hur du [utformar din första databas med SSMS](design-first-database-tutorial.md) eller [utformar en databas och ansluter med C# och ADO.net](design-first-database-csharp-tutorial.md).
- Mer information om .NET finns i [.NET-dokumentationen](/dotnet/).
