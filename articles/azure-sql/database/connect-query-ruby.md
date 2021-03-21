---
title: Använd ruby för att fråga
description: Det här avsnittet visar hur du använder ruby för att skapa ett program som ansluter till en databas och frågar den med hjälp av Transact-SQL-uttryck.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ruby
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 0434a99d28b4f71594e0ca9ce312087dee5b0ee2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92672543"
---
# <a name="quickstart-use-ruby-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Snabb start: Använd ruby för att fråga en databas i Azure SQL Database eller Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Den här snabb starten visar hur du använder [ruby](https://www.ruby-lang.org) för att ansluta till en databas och fråga efter data med Transact-SQL-uttryck.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabbstarten, behöver du följande förhandskrav:

- En databas. Du kan använda någon av följande snabb starter för att skapa och konfigurera databasen:

  | Action | SQL Database | SQL-hanterad instans | SQL Server på virtuella Azure-datorer |
  |:--- |:--- |:---|:---|
  | Skapa| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Konfigurera | [IP-brandväggsregel på servernivå](firewall-create-server-level-portal-quickstart.md)| [Anslutning från en virtuell dator](../managed-instance/connect-vm-instance-configure.md)|
  |||[Anslutning från lokal plats](../managed-instance/point-to-site-p2s-configure.md) | [Ansluta till en SQL Server-instans](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Läsa in data|AdventureWorks som lästs in per snabbstart|[Återställa Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [Återställa Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  |||Återställa eller importera Adventure Works från en [BACPAC](database-import.md) -fil från [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Återställa eller importera Adventure Works från en [BACPAC](database-import.md) -fil från [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skripten i den här artikeln skrivs för att använda Adventure Works-databasen. Med en SQL-hanterad instans måste du antingen importera Adventure Works-databasen till en instans databas eller ändra skripten i den här artikeln för att använda Wide World imports-databasen.
  
- Ruby och relaterad programvara för ditt operativsystem:
  
  - **MacOS**: installera homebrew, rbenv och ruby-build, ruby, freetds och TinyTDS. Se steg 1.2, 1.3, 1.4, 1.5 och 2.1 i [Skapa Ruby-appar med SQL Server på macOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
  
  - **Ubuntu**: installera förutsättningar för ruby, rbenv och ruby-build, ruby, freetds och TinyTDS. Se steg 1.2, 1.3, 1.4, 1.5 och 2.1 i [Skapa Ruby-appar med SQL Server på Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).
  
  - **Windows**: installera ruby, ruby devkit och TinyTDS. Se [Konfigurera utvecklingsmiljön för Ruby-utveckling](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development).

## <a name="get-server-connection-information"></a>Hämta information om Server anslutning

Hämta anslutnings informationen du behöver för att ansluta till en databas i Azure SQL Database. Du behöver det fullständiga servernamnet eller värdnamnet, databasnamnet och inloggningsinformationen för de kommande procedurerna.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Gå till sidan **SQL-databaser**  eller **SQL-hanterade instanser** .

3. På sidan **Översikt** granskar du det fullständigt kvalificerade Server namnet bredvid **Server namnet** för en databas i Azure SQL Database eller det fullständigt kvalificerade Server namnet (eller IP-adressen) bredvid **värd** för en Azure SQL-hanterad instans eller SQL Server på Azure VM. Om du vill kopiera servernamnet eller värdnamnet hovrar du över det och väljer ikonen **Kopiera**.

> [!NOTE]
> Anslutnings information för SQL Server på den virtuella Azure-datorn finns i [Anslut till en SQL Server instans](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="create-code-to-query-your-database-in-azure-sql-database"></a>Skapa en kod för att köra frågor mot databasen i Azure SQL Database

1. Skapa en ny fil med namnet *sqltest.rb* i en text- eller kodredigerare.

1. Lägg till följande kod. Ersätt värdena från databasen i Azure SQL Database för `<server>` , `<database>` , `<username>` och `<password>` .

   >[!IMPORTANT]
   >Koden i det här exemplet använder AdventureWorksLT-exempeldata, som du kan välja som källa när du skapar din databas. Om din databas har andra data använder du tabeller från din egen databas i SELECT-frågan. 

   ```ruby
   require 'tiny_tds'
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   client = TinyTds::Client.new username: username, password: password, 
       host: server, port: 1433, database: database, azure: true
   
   puts "Reading data from table"
   tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
           FROM [SalesLT].[ProductCategory] pc
           JOIN [SalesLT].[Product] p
           ON pc.productcategoryid = p.productcategoryid"
   result = client.execute(tsql)
   result.each do |row|
       puts row
   end
   ```

## <a name="run-the-code"></a>Kör koden

1. Kör följande kommando i en kommandotolk:

   ```bash
   ruby sqltest.rb
   ```
   
1. Kontrollera att de 20 främsta raderna för kategori/produkt från databasen returneras. 

## <a name="next-steps"></a>Nästa steg
- [Utforma din första databas i Azure SQL Database](design-first-database-tutorial.md)
- [GitHub-lagringsplatsen för TinyTDS](https://github.com/rails-sqlserver/tiny_tds)
- [Rapportera problem eller ställ frågor om TinyTDS](https://github.com/rails-sqlserver/tiny_tds/issues)
- [Ruby-drivrutin för SQL Server](/sql/connect/ruby/ruby-driver-for-sql-server/)