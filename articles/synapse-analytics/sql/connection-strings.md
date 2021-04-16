---
title: Anslutningssträngar för Synapse SQL
description: Anslutningssträngar för Synapse SQL
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: fb68a15bb64640fd8aa18d2ee01fd4a03d809125
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565278"
---
# <a name="connection-strings-for-synapse-sql"></a>Anslutningssträngar för Synapse SQL

Du kan ansluta till Synapse SQL med flera olika programprotokoll, till exempel, [ADO.NET,](/dotnet/framework/data/adonet/) [ODBC,](/sql/connect/odbc/windows/microsoft-odbc-driver-for-sql-server-on-windows) [PHP](/sql/connect/php/overview-of-the-php-sql-driver?f=255&MSPPError=-2147217396)och [JDBC](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server). Nedan visas några exempel på anslutningssträngar för varje protokoll. 

Du kan också använda Azure Portal för att skapa anslutningssträngen.  Om du vill skapa *anslutningssträngen* med Azure Portal navigerar du till databasbladet. Under Essentials väljer du Visa *databasanslutningssträngar*.

## <a name="sample-adonet-connection-string"></a>Exempel på ADO.NET anslutningssträng

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>Exempel på ODBC-anslutningssträng

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>Exempel på PHP-anslutningssträng

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>Exempel på JDBC-anslutningssträng

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

> [!NOTE]
> Överväg att ställa in tidsgränsen för anslutningen på 300 sekunder så att anslutningen klarar korta perioder av otillgänglighet.

## <a name="recommendations"></a>Rekommendationer

För att köra **serverlösa SQL-poolfrågor** rekommenderas verktyg [Azure Data Studio](get-started-azure-data-studio.md) och Azure Synapse Studio.

## <a name="next-steps"></a>Nästa steg

Information om hur du börjar köra frågor mot din analys Visual Studio program finns i [Fråga med Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).