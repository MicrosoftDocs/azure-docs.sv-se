---
title: Använda Visual Studio med .NET och C# för att fråga
description: Använd Visual Studio för att skapa en C#-app som ansluter till en databas i Azure SQL Database eller Azure SQL Managed Instance och kör frågor.
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
ms.openlocfilehash: 1fe39e0ff9acc0c092199ab2dd199cf396f67d01
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874773"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-database"></a>Snabbstart: Använda .NET och C# i Visual Studio för att ansluta till och fråga en databas
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

Den här snabbstarten visar hur du [använder .NET Framework-](https://dotnet.microsoft.com) och C#-koden i Visual Studio för att fråga en databas i Azure SQL eller Synapse SQL med Transact-SQL-uttryck.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabbstarten behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Visual Studio 2019](https://www.visualstudio.com/downloads/) Community, Professional eller Enterprise Edition.
- En databas där du kan köra en fråga.

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

## <a name="create-code-to-query-the-database-in-azure-sql-database"></a>Skapa kod för att fråga databasen i Azure SQL Database

1. Skapa ett nytt projekt i Visual Studio. 
   
1. I dialogrutan **Nytt projekt** väljer du **Visual C#**, **Konsolapp (.NET Framework).**
   
1. Ange *sqltest* som projektnamn och välj sedan **OK**. Det nya projektet skapas. 
   
1. Välj **Project**  >  **Manage NuGet Packages (Projekthantera NuGet-paket).** 
   
1. I **NuGet Package Manager** väljer du **fliken** Bläddra och söker sedan efter och väljer **Microsoft.Data.SqlClient.**
   
1. På sidan **Microsoft.Data.SqlClient** väljer du **Installera**. 
   - Välj **OK** för att fortsätta med installationen om du uppmanas att göra det. 
   - Om ett fönster för **godkännande av licensen** visas väljer du **Jag accepterar**.
   
1. När installationen har slutförts kan du stänga **NuGet-pakethanteraren**. 
   
1. Ersätt innehållet i **Program.cs** med följande kod i kodredigeraren. Ersätt värdena för `<your_server>` , `<your_username>` , och `<your_password>` `<your_database>` .
   
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

1. Om du vill köra appen **väljer du**  >  **Felsök Starta felsökning,** eller väljer **Starta** i verktygsfältet eller trycker på **F5.**
1. Kontrollera att databasnamnen och sorteringarna returneras och stäng sedan appfönstret.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur [du ansluter och frågar en databas i Azure SQL Database med hjälp av .NET Core](connect-query-dotnet-core.md) i Windows/Linux/macOS.  
- Lär dig mer om att [Komma igång med .NET Core för Windows/Linux/macOS med hjälp av kommandoraden](/dotnet/core/tutorials/using-with-xplat-cli).
- Lär dig hur du utformar din första databas i Azure SQL Database med [hjälp av SSMS](design-first-database-tutorial.md) eller utformar din [första databas i Azure SQL Database med hjälp av .NET](design-first-database-csharp-tutorial.md).
- Mer information om .NET finns i [.NET-dokumentationen](/dotnet/).
- Exempel på logik för omprövning: [Anslut elastiskt till Azure SQL med ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net
