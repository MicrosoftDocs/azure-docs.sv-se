---
title: Ansluta till Synapse SQL
description: Anslut till Synapse SQL.
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: f5682302ea0fa83c04a8560ba3f0f98ea075e072
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565550"
---
# <a name="connect-to-synapse-sql"></a>Ansluta till Synapse SQL
Anslut till Synapse SQL i Azure Synapse Analytics.

## <a name="supported-tools-for-serverless-sql-pool"></a>Verktyg som stöds för serverlös SQL-pool

[Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) stöds fullt ut från och med version 1.18.0. SSMS stöds delvis från och med version 18.5. Du kan använda det för att ansluta och endast fråga.

> [!NOTE]
> Om en AAD-inloggning har en anslutning öppen i mer än en timme vid tidpunkten för frågekörningen misslyckas alla frågor som förlitar sig på AAD. Detta inkluderar frågor om lagring med hjälp av AAD-genomsnidering och instruktioner som interagerar med AAD (t.ex. CREATE EXTERNAL PROVIDER). Detta påverkar alla verktyg som håller anslutningar öppna, som i frågeredigeraren i SSMS och ADS. Verktyg som öppnar nya anslutningar för att köra en fråga, Synapse Studio, påverkas inte.

> Du kan starta om SSMS eller ansluta och koppla från ADS för att åtgärda det här problemet. 

## <a name="find-your-server-name"></a>Hitta servernamnet

Servernamnet för den dedikerade SQL-poolen i följande exempel är: showdemoweu.sql.azuresynapse.net.
Servernamnet för den serverlösa SQL-poolen i följande exempel är: showdemoweu-ondemand.sql.azuresynapse.net.

För att hitta det fullständigt kvalificerade servernamnet:

1. Gå till [Azure-portalen](https://portal.azure.com).
2. Välj på **Synapse-arbetsytor**.
3. Välj den arbetsyta som du vill ansluta till.
4. Gå till översikt.
5. Leta upp det fullständiga servernamnet.

## <a name="sql-pool"></a>**SQL-pool**

![Fullständigt servernamn](./media/connect-overview/server-connect-example.png)

## <a name="serverless-sql-pool"></a>**serverlös SQL-pool**

![Fullständigt servernamn för serverlös SQL-pool](./media/connect-overview/server-connect-example-sqlod.png)

## <a name="supported-drivers-and-connection-strings"></a>Drivrutiner och anslutningssträngar som stöds
Synapse SQL stöder [ADO.NET,](/dotnet/framework/data/adonet/) [ODBC,](/sql/connect/odbc/windows/microsoft-odbc-driver-for-sql-server-on-windows) [PHP](/sql/connect/php/overview-of-the-php-sql-driver?f=255&MSPPError=-2147217396)och [JDBC](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server). Om du vill hitta den senaste versionen och dokumentationen väljer du någon av de föregående drivrutinerna. Om du vill generera anslutningssträngen för den drivrutin som du använder från Azure Portal väljer du **Visa databasanslutningssträngar** i föregående exempel. Nedan visas några exempel på hur en anslutningssträng kan se ut för respektive drivrutin.

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
Synapse SQL standardiserar vissa inställningar när anslutningen och objektet skapas. De här inställningarna kan inte åsidosättas och omfattar:

| Databasinställning | Värde |
|:--- |:--- |
| [ANSI_NULLS](/sql/t-sql/statements/set-ansi-nulls-transact-sql?view=azure-sqldw-latest&preserve-view=true) |ON |
| [QUOTED_IDENTIFIERS](/sql/t-sql/statements/set-quoted-identifier-transact-sql?view=azure-sqldw-latest&preserve-view=true) |ON |
| [DATEFORMAT](/sql/t-sql/statements/set-dateformat-transact-sql?view=azure-sqldw-latest&preserve-view=true) |mdy |
| [DATEFIRST](/sql/t-sql/statements/set-datefirst-transact-sql?view=azure-sqldw-latest&preserve-view=true) |7 |

## <a name="recommendations"></a>Rekommendationer

För att köra **serverlösa SQL-poolfrågor** rekommenderas verktyg [Azure Data Studio](get-started-azure-data-studio.md) och Azure Synapse Studio.

## <a name="next-steps"></a>Nästa steg
Information om hur du ansluter och ställer frågor med Visual Studio finns i [Fråga med Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Mer information om autentiseringsalternativ finns i [Autentisering för att Synapse SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).