---
title: Anslut och fråga
description: Länkar till Azure SQL Database snabb starter som visar hur du ansluter till och frågar Azure SQL Database och Azure SQL-hanterad instans.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 7dccce597dcfbcedd5083befafa79b8ba6f3adac
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2020
ms.locfileid: "97693475"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-connect-and-query-articles"></a>Azure SQL Database och Azure SQL-hanterad instans Anslut och skicka frågor till artiklar
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Följande dokument innehåller länkar till Azure-exempel som visar hur du ansluter och frågar Azure SQL Database och Azure SQL-hanterad instans. Vissa relaterade rekommendationer för säkerhet på transport nivå finns i [TLS-överväganden för databas anslutning](#tls-considerations-for-database-connectivity).

## <a name="quickstarts"></a>Snabbstarter

| Snabbstart | Beskrivning |
|---|---|
|[SQL Server Management Studio](connect-query-ssms.md)|Den här snabb starten visar hur du använder SSMS för att ansluta till en databas och sedan använder Transact-SQL-uttryck för att fråga, infoga, uppdatera och ta bort data i databasen.|
|[Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=%2fazure%2fsql-database%2ftoc.json)|Den här snabb starten visar hur du använder Azure Data Studio för att ansluta till en databas och sedan använder Transact-SQL-uttryck (T-SQL) för att skapa de TutorialDB som används i Azure Data Studio självstudier.|
|[Azure-portalen](connect-query-portal.md)|Den här snabb starten visar hur du använder Frågeredigeraren för att ansluta till en databas (endast Azure SQL Database) och sedan använda Transact-SQL-uttryck för att fråga, infoga, uppdatera och ta bort data i databasen.|
|[Visual Studio Code](connect-query-vscode.md)|Den här snabb starten visar hur du använder Visual Studio Code för att ansluta till en databas och sedan använder Transact-SQL-uttryck för att fråga, infoga, uppdatera och ta bort data i databasen.|
|[.NET med Visual Studio](connect-query-dotnet-visual-studio.md)|Den här snabb starten visar hur du använder .NET Framework för att skapa ett C#-program med Visual Studio för att ansluta till en databas och använda Transact-SQL-uttryck för att fråga data.|
|[.NET Core](connect-query-dotnet-core.md)|Den här snabb starten visar hur du använder .NET Core på Windows/Linux/macOS för att skapa ett C#-program för att ansluta till en databas och använda Transact-SQL-uttryck för att fråga data.|
|[Kör](connect-query-go.md)|Den här snabb starten visar hur du använder Go för att ansluta till en databas. Här visas också hur du använder Transact-SQL-uttryck för att köra frågor mot och ändra data.|
|[Java](connect-query-java.md)|Den här snabb starten visar hur du använder Java för att ansluta till en databas och sedan använder Transact-SQL-uttryck för att fråga data.|
|[Node.js](connect-query-nodejs.md)|Den här snabb starten visar hur du använder Node.js för att skapa ett program för att ansluta till en databas och använda Transact-SQL-uttryck för att fråga data.|
|[PHP](connect-query-php.md)|Den här snabb starten visar hur du använder PHP för att skapa ett program för att ansluta till en databas och använda Transact-SQL-uttryck för att fråga data.|
|[Python](connect-query-python.md)|Den här snabb starten visar hur du använder python för att ansluta till en databas och använder Transact-SQL-uttryck för att fråga data. |
|[Ruby](connect-query-ruby.md)|Den här snabb starten visar hur du använder ruby för att skapa ett program för att ansluta till en databas och använda Transact-SQL-uttryck för att fråga data.|
|[R](connect-query-r.md)|Den här snabb starten visar hur du använder R med Azure SQL Database Machine Learning Services för att skapa ett program för att ansluta till en databas i Azure SQL Database och använda Transact-SQL-uttryck för att fråga data.|
|||

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

## <a name="tls-considerations-for-database-connectivity"></a>TLS-överväganden för databas anslutning

Transport Layer Security (TLS) används av alla driv rutiner som Microsoft tillhandahåller eller stöder för att ansluta till databaser i Azure SQL Database eller Azure SQL-hanterad instans. Ingen särskild konfiguration krävs. För alla anslutningar till en SQL Server instans, en databas i Azure SQL Database eller en instans av Azure SQL-hanterad instans, rekommenderar vi att alla program ställer in följande konfigurationer eller deras motsvarigheter:

- **Kryptera = På**
- **TrustServerCertificate = Av**

Vissa system använder andra men likvärdiga nyckelord för dessa konfigurationsnyckelord. Dessa konfigurationer ser till att klientdrivrutinen bekräftar identiteten för TLS-certifikatet som togs emot från servern.

Vi rekommenderar också att du inaktiverar TLS 1.1 och 1.0 på klienten om du behöver följa datasäkerhetsstandarden PCI-DSS (Payment Card Industry – Data Security Standard).

Drivrutiner som inte kommer från Microsoft kanske inte använder TLS som standard. Detta kan vara en faktor vid anslutning till Azure SQL Database eller Azure SQL-hanterad instans. Du kanske inte kan ändra dessa anslutningsinställningar i program med inbäddade drivrutiner. Vi rekommenderar att du kontrollerar säkerheten i sådana drivrutiner och program innan du använder dem i system som hanterar känsliga data.

## <a name="libraries"></a>Bibliotek

Du kan använda olika bibliotek och ramverk för att ansluta till Azure SQL Database eller Azure SQL-hanterad instans. Kolla in våra [inledande självstudier](https://aka.ms/sqldev) om du snabbt vill komma igång med programmeringsspråk som C#, Java, Node.js, PHP och Python. Skapa sedan en app med hjälp av SQL Server i Linux eller Windows, eller Docker i macOS.

I följande tabell ser du vilka anslutningsbibliotek eller *drivrutiner* som klientprogram kan använda i olika språk för att ansluta till och använda SQL Server lokalt eller i molnet. Du kan använda dem på Linux, Windows eller Docker och använda dem för att ansluta till Azure SQL Database, Azure SQL-hanterad instans och Azure Synapse Analytics.

| Språk | Plattform | Ytterligare resurser | Ladda ned | Kom igång |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET för SQL Server](/sql/connect/ado-net/microsoft-ado-net-sql-server) | [Ladda ned](https://www.microsoft.com/net/download/) | [Komma igång](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Microsoft JDBC-drivrutin för SQL Server](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server/) | [Ladda ned](https://go.microsoft.com/fwlink/?linkid=852460) |  [Komma igång](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [PHP SQL-drivrutin för SQL Server](/sql/connect/php/microsoft-php-driver-for-sql-server) | [Ladda ned](/sql/connect/php/download-drivers-php-sql-server) | [Komma igång](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Node.js-drivrutin för SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server/) | [Installera](/sql/connect/node-js/step-1-configure-development-environment-for-node-js-development/) |  [Komma igång](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Python SQL-drivrutin](/sql/connect/python/python-driver-for-sql-server/) | Installationsval: <br/> \* [pymssql](/sql/connect/python/pymssql/step-1-configure-development-environment-for-pymssql-python-development/) <br/> \* [pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development/) |  [Komma igång](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Ruby-drivrutin för SQL Server](/sql/connect/ruby/ruby-driver-for-sql-server/) | [Installera](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development/) | [Komma igång](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Microsoft ODBC-drivrutin för SQL Server](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server/) | [Ladda ned](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server/) |  

I följande tabell visas exempel på ramverk för objekt Relations mappning (ORM) och webb ramverk som klient program kan använda med SQL Server, Azure SQL Database, Azure SQL-hanterad instans eller Azure Synapse Analytics. Du kan använda ramverken på Linux, Windows eller Docker.

| Språk | Plattform | ORM |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](/ef)<br>[Entity Framework Core](/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernate ORM](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Eloquent)](https://laravel.com/docs/eloquent)<br>[Doctrine](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](https://sequelize.org/) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>Nästa steg

- Information om anslutningsarkitekturen finns i [Azure SQL Database Connectivity Architecture](connectivity-architecture.md) (Anslutningsarkitektur för Azure SQL Database).
- Hitta [SQL Server driv rutiner](/sql/connect/sql-connection-libraries/) som används för att ansluta från klient program.
- Anslut till Azure SQL Database eller Azure SQL-hanterad instans:
  - [Anslut och fråga med .NET (C#)](connect-query-dotnet-core.md)
  - [Anslut och fråga med PHP](connect-query-php.md)
  - [Anslut och fråga med Node.js](connect-query-nodejs.md)
  - [Anslut och fråga med Java](connect-query-java.md)
  - [Anslut och fråga med python](connect-query-python.md)
  - [Anslut och fråga med ruby](connect-query-ruby.md)
- Kodexempel för omprövningslogik:
  - [Anslut elastiskt med ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [Ansluta elastiskt med PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: /sql/connect/php/step-4-connect-resiliently-to-sql-with-php
