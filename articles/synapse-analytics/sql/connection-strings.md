---
title: Anslutnings strängar för Synapse-SQL
description: Anslutnings strängar för Synapse-SQL
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 5ce411f3a2f3be162f9af16422d20b3e8b536fee
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98120928"
---
# <a name="connection-strings-for-synapse-sql"></a>Anslutnings strängar för Synapse-SQL

Du kan ansluta till Synapse SQL med flera olika program protokoll som, [ADO.net](/dotnet/framework/data/adonet/), [ODBC](/sql/connect/odbc/windows/microsoft-odbc-driver-for-sql-server-on-windows), [php](/sql/connect/php/overview-of-the-php-sql-driver?f=255&MSPPError=-2147217396)och [JDBC](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server). Nedan visas några exempel på anslutnings strängar för varje protokoll. 

Du kan också använda Azure Portal för att bygga anslutnings strängen.  Om du vill bygga anslutnings strängen med hjälp av Azure Portal navigerar du till bladet databas under *Essentials* . Välj *Visa databas anslutnings strängar*.

## <a name="sample-adonet-connection-string"></a>Exempel på ADO.NET-anslutningssträng

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
> Överväg att ange tids gränsen för anslutningen till 300 sekunder så att anslutningen kan överleva korta perioder med otillgänglighet.

## <a name="recommendations"></a>Rekommendationer

Rekommenderade verktyg är [Azure Data Studio](get-started-azure-data-studio.md) och Azure Synapse Studio för att köra frågor utan **Server utan SQL-pool** .

## <a name="next-steps"></a>Nästa steg

Information om hur du börjar fråga din analys med Visual Studio och andra program finns i [fråga med Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).