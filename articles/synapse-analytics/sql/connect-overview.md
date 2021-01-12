---
title: Anslut till Synapse SQL
description: Anslut till Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: f0c762d0f12f11cb4eef23b55fccb5b7c2a274d3
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118225"
---
# <a name="connect-to-synapse-sql"></a>Anslut till Synapse SQL
Anslut till SQL-funktionen Synapse i Azure Synapse Analytics.

## <a name="supported-tools-for-serverless-sql-pool"></a>Verktyg som stöds för Server lös SQL-pool

[Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) stöds fullt ut från version 1.18.0. SSMS stöds delvis från och med version 18,5. du kan bara använda den för att ansluta och fråga.

> [!NOTE]
> Om en AAD-inloggning har en anslutning som är öppen i mer än 1 timme vid frågekörningen, kommer alla frågor som förlitar sig på AAD att Miss förväntas. Detta omfattar att fråga lagring med AAD-vidarekoppling och-instruktioner som interagerar med AAD (t. ex. skapa extern PROVIDER). Detta påverkar alla verktyg som håller igång anslutningar, som i Frågeredigeraren i SSMS och ADS. Verktyg som öppnar nya anslutningar för att köra en fråga, t. ex. Synapse Studio, påverkas inte.

> Du kan åtgärda problemet genom att starta om SSMS eller ansluta och frånkoppla i ADS. 

## <a name="find-your-server-name"></a>Hitta servernamnet

Server namnet för den dedikerade SQL-poolen i följande exempel är: showdemoweu.sql.azuresynapse.net.
Server namnet för den serverbaserade SQL-poolen i följande exempel är: showdemoweu-ondemand.sql.azuresynapse.net.

För att hitta det fullständigt kvalificerade servernamnet:

1. Gå till [Azure-portalen](https://portal.azure.com).
2. Välj på **Synapse-arbetsytor**.
3. Välj den arbets yta som du vill ansluta till.
4. Gå till översikt.
5. Leta upp det fullständiga servernamnet.

## <a name="sql-pool"></a>**SQL-pool**

![Fullständigt servernamn](./media/connect-overview/server-connect-example.png)

## <a name="serverless-sql-pool"></a>**serverlös SQL-pool**

![Fullständigt Server namn utan server utan SQL-pool](./media/connect-overview/server-connect-example-sqlod.png)

## <a name="supported-drivers-and-connection-strings"></a>Drivrutiner och anslutningssträngar som stöds
Synapse SQL stöder [ADO.net](/dotnet/framework/data/adonet/), [ODBC](/sql/connect/odbc/windows/microsoft-odbc-driver-for-sql-server-on-windows), [php](/sql/connect/php/overview-of-the-php-sql-driver?f=255&MSPPError=-2147217396)och [JDBC](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server). Välj en av de föregående driv rutinerna för att hitta den senaste versionen och dokumentationen. Om du automatiskt vill generera anslutnings strängen för den driv rutin som du använder från Azure Portal väljer du **Visa databas anslutnings strängar** från föregående exempel. Nedan visas några exempel på hur en anslutningssträng kan se ut för respektive drivrutin.

> [!NOTE]
> Det kan vara bra att ange en tidsgräns på 300 sekunder för anslutningen så att den inte bryts vid korta perioder av inaktivitet.

### <a name="adonet-connection-string-example"></a>Exempel på ADO.NET-anslutningssträng

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Exempel på ODBC-anslutningssträng

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Exempel på PHP-anslutningssträng

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Exempel på JDBC-anslutningssträng

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Inställningar för anslutning
Synapse SQL standardiserar vissa inställningar under anslutning och objekt skapande. De här inställningarna kan inte åsidosättas och inkludera:

| Databasinställning | Värde |
|:--- |:--- |
| [ANSI_NULLS](/sql/t-sql/statements/set-ansi-nulls-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |ON |
| [QUOTED_IDENTIFIERS](/sql/t-sql/statements/set-quoted-identifier-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |ON |
| [DATEFORMAT](/sql/t-sql/statements/set-dateformat-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |mdy |
| [DATEFIRST](/sql/t-sql/statements/set-datefirst-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |7 |

## <a name="recommendations"></a>Rekommendationer

Rekommenderade verktyg är [Azure Data Studio](get-started-azure-data-studio.md) och Azure Synapse Studio för att köra frågor utan **Server utan SQL-pool** .

## <a name="next-steps"></a>Nästa steg
Information om hur du ansluter och ställer frågor med Visual Studio finns i [Fråga med Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Mer information om autentiseringsalternativ finns i [autentisering till SYNAPSE SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).