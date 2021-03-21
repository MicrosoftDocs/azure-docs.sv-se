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
ms.openlocfilehash: 1d25f43ef5a694d8b94710055bf1be72a7fcb45c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97705225"
---
# <a name="quickstart-use-net-core-c-to-query-a-database"></a>Snabb start: använda .NET Core (C#) för att fråga en databas
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

I den här snabb starten använder du [.net Core](https://www.microsoft.com/net/) och C#-kod för att ansluta till en databas. Sedan kör du en Transact-SQL-instruktion för att fråga efter data.

> [!TIP]
> Följande Microsoft Learn modul hjälper dig att lära dig kostnads fritt hur du [utvecklar och konfigurerar ett ASP.NET-program som frågar en databas i Azure SQL Database](/learn/modules/develop-app-that-queries-azure-sql/)

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabbstarten behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [.NET Core för ditt operativsystem](https://www.microsoft.com/net/core).
- En databas där du kan köra din fråga. 

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]
  
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
