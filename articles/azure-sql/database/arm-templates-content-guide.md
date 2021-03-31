---
title: Azure Resource Manager mallar – Azure SQL Database & SQL-hanterad instans
description: Använd Azure Resource Manager mallar för att skapa och konfigurera Azure SQL Database och Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: overview-samples sqldbrb=2
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: 5202145af6f60f34bdce57aa94f4fec92a8b227f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92677262"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database--sql-managed-instance"></a>Azure Resource Manager mallar för Azure SQL Database & SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Med Azure Resource Manager-mallar kan du definiera din infrastruktur som kod och distribuera dina lösningar till Azure-molnet för Azure SQL Database och Azure SQL Managed Instance.

## <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

Följande tabell innehåller länkar till Azure Resource Manager-mallar för Azure SQL Database.

|Länk |Beskrivning|
|---|---|
| [SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Den här Azure Resource Manager-mallen skapar en enda databas i Azure SQL Database och konfigurerar IP-brandvägg på server nivå. |
| [Server](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Den här Azure Resource Manager-mallen skapar en server för Azure SQL Database. |
| [Elastisk pool](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Med den här mallen kan du distribuera en elastisk pool och tilldela databaser till den. |
| [Redundansgrupper](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Den här mallen skapar två servrar, en enda databas och en failover-grupp i Azure SQL Database.|
| [Hot identifiering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Med den här mallen kan du distribuera en server och en uppsättning databaser med hot identifiering aktive rad, med en e-postadress för aviseringar för varje databas. Hot identifiering är en del av erbjudandet för SQL Advanced Threat Protection (ATP) och ger ett säkerhets lager som svarar på potentiella hot över servrar och databaser.|
| [Granskning till Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Med den här mallen kan du distribuera en server med granskning aktiverat för att skriva gransknings loggar till en blob-lagring. Granskning för Azure SQL Database spårar databashändelser och skriver dem till en granskningslogg som kan placeras i ditt Azure Storage-konto, på din OMS-arbetsyta eller i Event Hubs.|
| [Granskning till Azure-händelsehubb](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Med den här mallen kan du distribuera en server med granskning aktiverat för att skriva gransknings loggar till en befintlig händelsehubben. För att kunna skicka gransknings händelser till Event Hubs, anger du gransknings inställningar med `Enabled` `State` och ange `IsAzureMonitorTargetEnabled` som `true` . Konfigurera också diagnostikinställningar med `SQLSecurityAuditEvents` logg kategorin i `master` databasen (för granskning på server nivå). Granskning spårar databas händelser och skriver dem till en Gransknings logg som kan placeras i ditt Azure Storage-konto, OMS-arbetsyta eller Event Hubs.|
| [Azure-webbapp med SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Det här exemplet skapar en kostnads fri Azure-webbapp och en databas i Azure SQL Database på tjänst nivån "Basic".|
| [Azure-webbapp och Redis Cache med SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Den här mallen skapar en webbapp, Redis Cache och databas i samma resurs grupp och skapar två anslutnings strängar i webbappen för-databasen och Redis Cache.|
| [Importera data från Blob Storage med hjälp av ADF v2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Den här Azure Resource Manager-mallen skapar en instans av Azure Data Factory v2 som kopierar data från Azure Blob Storage till SQL Database.|
| [HDInsight-kluster med en databas](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Med den här mallen kan du skapa ett HDInsight-kluster, en logisk SQL-Server, en databas och två tabeller. Den här mallen används av [artikeln Använd Sqoop med Hadoop i HDInsight](../../hdinsight/hadoop/hdinsight-use-sqoop.md). |
| [Azure-logikapp som kör en SQL-lagrad procedur enligt ett schema](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Med den här mallen kan du skapa en logisk app som kör en lagrad SQL-procedur enligt schemat. Argument för proceduren kan placeras i brödtexten i mallen.|

## <a name="azure-sql-managed-instance"></a>[Hanterad Azure SQL-instans](#tab/managed-instance)

Följande tabell innehåller länkar till Azure Resource Manager mallar för Azure SQL-hanterad instans.

|Länk|Beskrivning|
|---|---|
| [SQL-hanterad instans i ett nytt VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Den här Azure Resource Manager-mallen skapar ett nytt konfigurerat virtuellt Azure-nätverk och en hanterad instans i det virtuella nätverket. |
| [Nätverks miljö för SQL-hanterad instans](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Den här distributionen skapar ett konfigurerat virtuellt Azure-nätverk med två undernät, ett som kommer att vara dedikerat till dina hanterade instanser och ett annat där du kan placera andra resurser (till exempel virtuella datorer, App Service miljöer osv.). Den här mallen skapar en korrekt konfigurerad nätverks miljö där du kan distribuera hanterade instanser. |
| [SQL-hanterad instans med P2S-anslutning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Den här distributionen skapar ett virtuellt Azure-nätverk med två undernät `ManagedInstance` och `GatewaySubnet` . SQL-hanterad instans kommer att distribueras i ManagedInstance-undernätet. En virtuell nätverksgateway skapas i `GatewaySubnet` under nätet och konfigureras för punkt-till-plats-VPN-anslutning. |
| [SQL-hanterad instans med en virtuell dator](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Den här distributionen skapar ett virtuellt Azure-nätverk med två undernät `ManagedInstance` och `Management` . SQL-hanterad instans kommer att distribueras i `ManagedInstance` under nätet. En virtuell dator med den senaste versionen av SQL Server Management Studio (SSMS) kommer att distribueras i `Management` under nätet. |

---