---
title: Starta en redundansväxling manuellt på SQL-hanterad instans
description: Lär dig hur du manuellt växlar mellan primära och sekundära repliker på Azure SQL Managed instance.
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 08/18/2020
ms.openlocfilehash: 1833f0343aa3e41119e215e7ce022f122d13489b
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589511"
---
# <a name="user-initiated-manual-failover-on-sql-managed-instance"></a>Användarinitierade manuell redundans på SQL-hanterad instans

Den här artikeln förklarar hur du manuellt redundansväxlas en primär nod på SQL-hanterad instans Generell användning (GP) och Affärskritisk (BC) service nivåer och hur du manuellt redundansväxlas en sekundär skrivskyddad replik-nod på BC-tjänstnivå.

## <a name="when-to-use-manual-failover"></a>När du ska använda manuell redundans

[Hög tillgänglighet](../database/high-availability-sla.md) är en grundläggande del av SQL-hanterad instans plattform som fungerar transparent för dina databas program. Redundans från primära till sekundära noder vid nod-försämring eller fel identifiering, eller under regelbundna månatliga program uppdateringar, är en förväntad förekomst för alla program som använder SQL-hanterad instans i Azure.

Du kan överväga att köra en [manuell redundansväxling](../database/high-availability-sla.md#testing-application-fault-resiliency) på SQL-hanterad instans av någon av följande anledningar:
- Testa program för redundansväxling av redundans innan du distribuerar till produktion
- Testa slutpunkt-till-slutpunkt-system för fel återhämtning vid automatisk redundans
- Testa hur redundansväxlingen påverkar befintliga Databassessioner
- Kontrol lera om redundansen ändras från slut punkt till slut punkt på grund av ändringar i nätverks fördröjningen
- I vissa fall av prestanda försämringar för frågor kan manuella redundans hjälpa till att minska prestanda problemet.

> [!NOTE]
> Att se till att dina program är redundanta innan du distribuerar till produktion bidrar till att minska risken för program fel i produktionen och kommer att bidra till programmets tillgänglighet för dina kunder.

## <a name="initiate-manual-failover-on-sql-managed-instance"></a>Initiera manuell redundans på SQL-hanterad instans

### <a name="using-powershell"></a>Använda PowerShell

Den lägsta versionen av AZ. SQL måste vara [v 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0). Överväg att använda [Azure Cloud Shell](../../cloud-shell/overview.md) från Azure Portal som alltid har den senaste versionen av PowerShell. 

Som ett krav använder du följande PowerShell-skript för att installera de Azure-moduler som krävs. Välj dessutom den prenumeration där den hanterade instans du vill redundansväxla finns.

```powershell
$subscription = 'enter your subscription ID here'
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subscription
```

Använd PowerShell [-kommandot Invoke-AzSqlInstanceFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlinstancefailover) med följande exempel för att initiera redundansväxlingen av den primära noden, som är tillämpligt på både BC-och GP-tjänstenivå.

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName
```

Använd följande PS-kommando för att redundansväxla sekundär nod, som endast gäller för BC-tjänstnivå.

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName -ReadableSecondary
```

### <a name="using-cli"></a>Använda CLI

Se till att de senaste CLI-skripten är installerade.

Använd AZ SQL mi redundans-kommando med följande exempel för att initiera redundansväxlingen av den primära noden, som är tillämpligt på både BC-och GP-tjänstenivå.

```cli
az sql mi failover -g myresourcegroup -n myinstancename
```

Använd följande CLI-kommando för att läsa den sekundära noden för redundans, som endast gäller för BC-tjänstnivå.

```cli
az sql mi failover -g myresourcegroup -n myinstancename --replica-type ReadableSecondary
```

### <a name="using-rest-api"></a>Använda REST API

För avancerade användare som kanske behöver automatisera redundans för sina SQL-hanterade instanser för att implementera kontinuerliga test pipelines, eller automatiska prestanda dämpare, kan den här funktionen utföras genom initiering av redundans via ett API-anrop. Mer information finns i [hanterade instanser – redundansväxla REST API](https://docs.microsoft.com/rest/api/sql/managed%20instances%20-%20failover/failover) .

Om du vill initiera redundans med REST API-anrop ska du först skapa en autentiseringstoken med valfri API-klient. Den genererade autentiseringstoken används som Auktoriseringspost i rubriken för API-begäran och är obligatorisk.

Följande kod är ett exempel på API: n för API: et som ska anropas:

```HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/managedInstances/{managedInstanceName}/failover?api-version=2019-06-01-preview
```

Följande egenskaper måste skickas i API-anropet:

| **API-egenskap** | **Parameter** |
| --- | --- |
| subscriptionId | Prenumerations-ID som den hanterade instansen distribueras till |
| resourceGroupName | Resurs grupp som innehåller en hanterad instans |
| managedInstanceName | Namn på hanterad instans |
| replicaType | Valfritt (Primär eller ReadableSecondary). Dessa parametrar representerar den typ av replik som ska växlas över: primär eller läsbar sekundär. Om inget värde anges kommer redundansväxlingen att initieras på den primära repliken som standard. |
| api-version | Statiskt värde och måste vara "2019-06-01-för hands version" |

API-svar är något av följande två:

- 202 accepterad
- Ett av 400-fel i begäran.

Åtgärds status kan spåras genom att granska API-svar i svarshuvuden. Mer information finns i [status för asynkrona Azure-åtgärder](../../azure-resource-manager/management/async-operations.md).

## <a name="monitor-the-failover"></a>Övervaka redundansväxlingen

Om du vill övervaka förloppet för manuell redundansväxling kör du följande T-SQL-fråga i din favorit klient (t. ex. SSMS) på SQL-hanterad instans. Den läser system View sys. dm_hadr_fabric_replica_states och rapport repliker som är tillgängliga på instansen. Uppdatera samma fråga när du har initierat den manuella redundansväxlingen.

```T-SQL
SELECT DISTINCT replication_endpoint_url, fabric_replica_role_desc FROM sys.dm_hadr_fabric_replica_states
```

Innan redundansväxlingen initieras anger utdata den aktuella primära repliken på BC-tjänstnivå som innehåller en primär och tre sekundära servrar i AlwaysOn-tillgänglighetsgruppen. Vid körning av en redundansväxling måste du köra frågan igen för att indikera en ändring av den primära noden.

Du kommer inte att kunna se samma utdata med GP-tjänstens nivå som den som anges ovan för BC. Detta beror på att GP-tjänstens nivå endast baseras på en enda nod. Utdata från T-SQL-frågan för GP-tjänstnivå visar bara en enskild nod före och efter redundansväxlingen. Förlust av anslutning från klienten under redundansväxlingen, vanligt vis bestående av en minut, är en indikation på redundansväxlingen.

> [!NOTE]
> Det kan ta flera minuter att slutföra redundansväxlingen (inte den faktiska kort otillgängligheten) i taget vid arbets belastningar med **hög intensitet** . Detta beror på att instans motorn tar hand om alla aktuella transaktioner på den primära och den sekundära noden, innan de kan redundansväxla.

> [!IMPORTANT]
> Funktionella begränsningar för manuell redundansväxling i användaren är:
> - En (1) redundansväxling kan initieras på samma hanterade instans var **30: e minut**.
> - För BC-instanser måste det finnas kvorum med repliker för att redundansväxlingen ska godkännas.
> - För BC-instanser går det inte att ange vilken läsbar sekundär replik som ska initiera redundansväxlingen.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om hög tillgänglighet för hanterad instans med [hög tillgänglighet för Azure SQL-hanterad instans](../database/high-availability-sla.md).
- En översikt finns i [Vad är en Azure SQL-hanterad instans?](sql-managed-instance-paas-overview.md).
