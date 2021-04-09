---
title: Fråga Azure-Datautforskaren mellan resurser med Azure Monitor
description: Använd Azure Monitor för att utföra kors produkt frågor mellan Azure Datautforskaren, Log Analytics arbets ytor och klassiska Application Insights program i Azure Monitor.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: a800f78df26ce76144994bb9da2cac6271323eb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419430"
---
# <a name="cross-resource-query-azure-data-explorer-by-using-azure-monitor"></a>Fråga Azure-Datautforskaren mellan resurser med Azure Monitor
Azure Monitor stöder frågor över olika tjänster mellan Azure Datautforskaren, [Application Insights](../app/app-insights-overview.md)och [Log Analytics](../logs/data-platform-logs.md). Du kan sedan fråga ditt Azure Datautforskaren-kluster med Log Analytics/Application Insights-verktyg och se det i en kors tjänst fråga. Artikeln visar hur du skapar en kors tjänst fråga.

Följande diagram visar Azure Monitor över tjänst flödet:

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="Diagram som visar flödet av frågor mellan en användare, Azure Monitor, en proxy och Azure Datautforskaren.":::

>[!NOTE]
> Azure Monitor Cross-service-fråga finns i en offentlig för hands version. Kontakta [tjänst teamet](mailto:ADXProxy@microsoft.com) om du har frågor.

## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Kors fråga din Log Analytics eller Application Insights resurser och Azure Datautforskaren

Du kan köra kors resurs frågor med hjälp av klient verktyg som stöder Kusto-frågor. Exempel på dessa verktyg är Log Analytics webb gränssnitt, arbets böcker, PowerShell och REST API.

Ange ID: t för ett Azure Datautforskaren-kluster i en fråga inom `adx` mönstret, följt av databasens namn och tabell.

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="Skärm bild som visar ett exempel på en kors tjänst fråga.":::

> [!NOTE]
>* Databas namn är Skift läges känsliga.
>* Fråga om kors resurs som en avisering stöds inte.
>* Det finns inte stöd för att identifiera timestamp-kolumnen i klustret, Log Analytics API för frågor skickas inte längs tids filtret

## <a name="combine-azure-data-explorer-cluster-tables-with-a-log-analytics-workspace"></a>Kombinera Azure Datautforskaren Cluster-tabeller med en Log Analytics arbets yta

Använd `union` kommandot för att kombinera kluster tabeller med en Log Analytics-arbetsyta.

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="Skärm bild som visar ett exempel på en cross-service-fråga med kommandot Union.":::

> [!Tip]
> Stenografiska formatet tillåts: *kluster* namn / *InitialCatalog*. Till exempel `adx('help/Samples')` översätts till `adx('help.kusto.windows.net/Samples')` .

## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Koppla data från ett Azure Datautforskaren-kluster i en klient organisation med en Azure Monitor-resurs i en annan

Frågor över flera klienter mellan-tjänsterna stöds inte. Du är inloggad på en enda klient för att köra frågan som omfattar båda resurserna.

Om Azure Datautforskaren-resursen finns i klient organisation A och arbets ytan Log Analytics är i klient B, använder du någon av följande metoder:

*  Med Azure Datautforskaren kan du lägga till roller för huvud konton i olika klienter. Lägg till ditt användar-ID i klient B som en behörig användare i Azure Datautforskaren-klustret. Kontrol lera att egenskapen [TrustedExternalTenant](/powershell/module/az.kusto/update-azkustocluster) i Azure datautforskaren-klustret innehåller klient b. kör kors frågan fullständigt i klient B.
*  Använd [Lighthouse](../../lighthouse/index.yml) för att projicera Azure Monitor resursen i klient organisation A.

## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Ansluta till Azure Datautforskaren-kluster från olika klienter

Kusto Explorer loggar automatiskt in dig på den klient som användar kontot ursprungligen tillhör. För att få åtkomst till resurser i andra klienter med samma användar konto måste du uttryckligen ange `TenantId` anslutnings strängen:

`Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=TenantId`

## <a name="next-steps"></a>Nästa steg
* [Skriva frågor](/azure/data-explorer/write-queries)
* [Fråga efter data i Azure Monitor med Azure Datautforskaren](/azure/data-explorer/query-monitor-data)
* [Utföra kors resurs logg frågor i Azure Monitor](../logs/cross-workspace-query.md)