---
title: Fråga med hjälp av Visual Studio med .NET och C#
description: Använd Visual Studio för att skapa en C#-app som ansluter till en databas i Azure SQL Database eller Azure SQL-hanterad instans och kör frågor.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: devx-track-csharp, sqldbrb=2
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/10/2020
ms.openlocfilehash: 1d8859f4790610e72ad517f74bbbbf0cf77d9316
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705216"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-database"></a>Snabb start: använda .NET och C# i Visual Studio för att ansluta till och fråga en databas
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

Den här snabb starten visar hur du använder [.NET Framework](https://www.microsoft.com/net/) och C#-koden i Visual Studio för att fråga en databas i Azure SQL eller Synapse SQL med Transact-SQL-uttryck.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabbstarten behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Visual Studio 2019](https://www.visualstudio.com/downloads/) Community, Professional eller Enterprise Edition.
- En databas där du kan köra en fråga.

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

## <a name="create-code-to-query-the-database-in-azure-sql-database"></a>Skapa kod för att köra frågor mot databasen i Azure SQL Database

1. Skapa ett nytt projekt i Visual Studio. 
   
1. I dialog rutan **nytt projekt** väljer du **Visual C#**, **konsol program (.NET Framework)**.
   
1. Ange *sqltest* som projektnamn och välj sedan **OK**. Det nya projektet skapas. 
   
1. Välj **projekt**  >  **Hantera NuGet-paket**. 
   
1. I **NuGet Package Manager** väljer du fliken **Bläddra** och sedan söker du efter och väljer **Microsoft. data. SqlClient**.
   
1. På sidan **Microsoft. data. SqlClient** väljer du **Installera**. 
   - Välj **OK** för att fortsätta med installationen om du uppmanas att göra det. 
   - Om ett fönster för **godkännande av licensen** visas väljer du **Jag accepterar**.
   
1. När installationen har slutförts kan du stänga **NuGet-pakethanteraren**. 
   
1. Ersätt innehållet i **Program.cs** med följande kod i kodredigeraren. Ersätt värdena för `<your_server>` , `<your_username>` , `<your_password>` och `<your_database>` .
   
   ```csharp
   using System;
   using Microsoft.Data.SqlClient;
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
                   builder.DataSource = "<your_server>.database.windows.net"; 
                   builder.UserID = "<your_username>";            
                   builder.Password = "<your_password>";     
                   builder.InitialCatalog = "<your_database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       String sql = "SELECT name, collation_name FROM sys.databases";
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           connection.Open();
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
               Console.ReadLine();
           }
       }
   }
   ```

## <a name="run-the-code"></a>Kör koden

1. Om du vill köra appen väljer du **Felsök**  >  **Starta fel sökning**, eller så väljer du **Starta** i verktygsfältet eller trycker på **F5**.
1. Kontrol lera att databas namnen och sorteringarna returneras och stäng sedan app-fönstret.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [ansluter och frågar en databas i Azure SQL Database med .net Core](connect-query-dotnet-core.md) på Windows/Linux/MacOS.  
- Lär dig mer om att [Komma igång med .NET Core för Windows/Linux/macOS med hjälp av kommandoraden](/dotnet/core/tutorials/using-with-xplat-cli).
- Lär dig hur du [utformar din första databas i Azure SQL Database genom att använda SSMS](design-first-database-tutorial.md) eller [utforma din första databas i Azure SQL Database med hjälp av .net](design-first-database-csharp-tutorial.md).
- Mer information om .NET finns i [.NET-dokumentationen](/dotnet/).
- Exempel på logik för omprövning: [ansluta elastiskt till Azure SQL med ADO.net][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net
