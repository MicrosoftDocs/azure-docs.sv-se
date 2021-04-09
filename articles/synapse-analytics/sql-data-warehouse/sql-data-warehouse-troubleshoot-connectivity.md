---
title: Felsökning av anslutningsmöjligheter
description: Felsöka anslutningar i dedikerad SQL-pool (tidigare SQL DW).
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse, devx-track-csharp
ms.openlocfilehash: 8b23a3634b34277b732d4ba18bef7e71c783ebd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98681194"
---
# <a name="troubleshooting-connectivity-issues-in-dedicated-sql-pool-formerly-sql-dw"></a>Felsöka anslutnings problem i dedikerad SQL-pool (tidigare SQL DW)

Den här artikeln innehåller vanliga fel söknings tekniker för att ansluta till din dedikerade SQL-adresspool (tidigare SQL DW).

## <a name="check-service-availability"></a>Kontrol lera tjänstens tillgänglighet

Kontrol lera om tjänsten är tillgänglig. I Azure Portal går du till den dedikerade SQL-poolen (tidigare SQL DW) som du försöker ansluta. Klicka på **diagnostisera och lös problem** i den vänstra panelen i innehålls förteckningen.

![Välj resurs hälsa](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Status för din dedikerade SQL-pool (tidigare SQL DW) kommer att visas här. Om tjänsten inte visas som **tillgänglig** kontrollerar du ytterligare steg.

![Tjänsten är tillgänglig](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Om din resurs hälsa visar att din dedikerade SQL-pool (tidigare SQL DW) har pausats eller skalas, följer du anvisningarna för att återuppta instansen.

![Skärm bild som visar en instans av en dedikerad SQL-pool som är pausad eller skalning.](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png)
Mer information om Resource Health hittar du här.

## <a name="check-for-paused-or-scaling-operation"></a>Kontrollera om tjänsten är pausad eller håller på att skalas om

Kontrol lera portalen för att se om din dedikerade SQL-pool (tidigare SQL DW) har pausats eller skalats.

![Skärm bild som visar hur du kontrollerar om ett informations lager har pausats.](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Om du ser att tjänsten har pausats eller skalas kan du kontrol lera att den inte finns under ditt underhålls schema. På portalen för din dedikerade SQL-pool (tidigare SQL DW)- *Översikt* visas schemat för valt underhåll.

![Översikt över underhålls schema](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

Annars kan du kontakta IT-administratören för att kontrol lera att det här underhållet inte är en schemalagd händelse. Följ [dessa steg](pause-and-resume-compute-portal.md)om du vill återuppta den DEDIKERADe SQL-poolen (tidigare SQL DW)-instansen.

## <a name="check-your-firewall-settings"></a>Kontrollera brandväggsinställningarna

Den dedikerade SQL-poolen (tidigare SQL DW) kommunicerar via port 1433.Om du försöker ansluta inifrån ett företagsnätverk kan det hända att utgående trafik via port 1433 inte tillåts av nätverkets brandvägg. I så fall kan du inte ansluta till den [logiska servern](../../azure-sql/database/logical-servers.md) om inte din IT-avdelning öppnar port 1433. Ytterligare information om brand Väggs konfigurationer finns [här](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#create-and-manage-ip-firewall-rules).

## <a name="check-your-vnetservice-endpoint-settings"></a>Kontrollera inställningarna för det virtuella nätverket/tjänstslutpunkten

Om du får fel 40914 och 40615, se [fel beskrivning och lösning här](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Kontrollera om det finns nya drivrutiner

### <a name="software"></a>Programvara

Kontrol lera att du använder de senaste verktygen för att ansluta till din dedikerade SQL-pool (tidigare SQL DW):

- SSMS
- Azure Data Studio
- SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Drivrutiner

Kontrol lera att du använder de senaste driv rutins versionerna.Om du använder en äldre version av driv rutinerna kan det leda till oväntade beteenden eftersom de äldre driv rutinerna kanske inte stöder nya funktioner.

- [ODBC](/sql/connect/odbc/download-odbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [JDBC](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [OLE DB](/sql/connect/oledb/download-oledb-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [PHP](/sql/connect/php/download-drivers-php-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="check-your-connection-string"></a>Kontrollera anslutningssträngen

Se till att anslutningssträngarna är inställda på rätt sätt.  Nedan visas några exempel.  Du kan läsa mer om [anslutningssträngar här](sql-data-warehouse-connection-strings.md).

ADO.NET-anslutningssträng

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

ODBC-anslutningssträng

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

PHP-anslutningssträng

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

JDBC-anslutningssträng

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Tillfälliga anslutningsproblem

Kontrollera om servern är hårt belastad med ett stort antal köade förfrågningar. Du kan behöva skala upp din dedikerade SQL-pool (tidigare SQL DW) för ytterligare resurser.

## <a name="common-error-messages"></a>Vanliga felmeddelanden

Fel 40914 och 40615, se [fel beskrivningen och lösningen här](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Har du fortfarande problem med anslutningen?

Skapa ett [support ärende](sql-data-warehouse-get-started-create-support-ticket.md) så att teknik teamet kan stödja dig.
