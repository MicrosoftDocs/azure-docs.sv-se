---
title: Azure Functions utlösare för Cosmos DB Avancerad konfiguration
description: Lär dig hur du konfigurerar loggning och anslutnings princip som används av Azure Functions utlösare för Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/19/2020
ms.author: maquaran
ms.openlocfilehash: f753010eb994c9f3c286ad6eca6392ca7b643075
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97932919"
---
# <a name="how-to-configure-logging-and-connectivity-with-the-azure-functions-trigger-for-cosmos-db"></a>Så här konfigurerar du loggning och anslutningar med Azure Functions utlösare för Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

I den här artikeln beskrivs avancerade konfigurations alternativ som du kan ange när du använder Azure Functions utlösare för Cosmos DB.

## <a name="enabling-trigger-specific-logs"></a>Aktivera Utlös ande loggar

Azure Functions-utlösaren för Cosmos DB använder [processor biblioteket för ändrings flöden](./change-feed-processor.md) internt och biblioteket genererar en uppsättning hälso loggar som kan användas för att övervaka interna åtgärder i [fel söknings syfte](./troubleshoot-changefeed-functions.md).

Hälso loggarna beskriver hur Azure Functions utlösare för Cosmos DB fungerar vid försök att utföra åtgärder vid belastnings Utjämnings scenarier eller initiering.

### <a name="enabling-logging"></a>Aktivera loggning

Om du vill aktivera loggning när du använder Azure Functions utlösare för Cosmos DB, letar du upp `host.json` filen i Azure Functions-projektet eller Azure Functions app och [konfigurerar önskad loggnings nivå](../azure-functions/functions-monitoring.md#log-levels-and-categories). Aktivera spårningarna  `Host.Triggers.CosmosDB` som visas i följande exempel:

```js
{
  "version": "2.0",
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "Host.Triggers.CosmosDB": "Trace"
    }
  }
}
```

När Azure-funktionen har distribuerats med den uppdaterade konfigurationen visas Azure Functions utlösare för Cosmos DB loggar som en del av dina spår. Du kan visa loggarna i den konfigurerade Logging-providern under *kategorin* `Host.Triggers.CosmosDB` .

### <a name="query-the-logs"></a>Skicka frågor till loggarna

Kör följande fråga för att skicka frågor till loggarna som genereras av Azure Functions utlösare för Cosmos DB i [Azure Application insikter-analys](../azure-monitor/log-query/log-query-overview.md):

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="configuring-the-connection-policy"></a>Konfigurera anslutnings principen

Det finns två anslutnings lägen – direkt läge och gateway-läge. Mer information om dessa anslutnings lägen finns i artikeln [anslutnings lägen](sql-sdk-connection-modes.md) . Som standard används **gatewayen** för att upprätta alla anslutningar i Azure Functions-utlösaren för Cosmos dB. Men det kanske inte är det bästa alternativet för prestanda drivna scenarier.

### <a name="changing-the-connection-mode-and-protocol"></a>Ändra anslutnings läge och protokoll

Det finns två nyckel konfigurations inställningar som är tillgängliga för att konfigurera klient anslutnings principen – **anslutnings läge** och **anslutnings protokoll**. Du kan ändra standard anslutnings läge och protokoll som används av Azure Functions-utlösaren för Cosmos DB och alla [Azure Cosmos DB bindningar](../azure-functions/functions-bindings-cosmosdb-v2-output.md)). Om du vill ändra standardinställningarna måste du leta upp `host.json` filen i Azure Functions-projektet eller Azure Functions-appen och lägga till följande [extra inställning](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings):

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Där `connectionMode` måste du ha önskat anslutnings läge (direkt eller Gateway) och `protocol` önskat anslutnings protokoll (TCP eller https). 

Om Azure Functions-projektet arbetar med Azure Functions v1-körningen har konfigurationen en liten namn skillnad, du bör använda `documentDB` i stället för `cosmosDB` :

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> När du är värd för din Function-app i en förbruknings plan har varje instans en gräns för antalet socketanslutningar som den kan underhålla. När du arbetar med direkt-/TCP-läge skapas fler anslutningar och du kan nå [gränsen för förbruknings planen](../azure-functions/manage-connections.md#connection-limit), i så fall kan du antingen använda Gateway-läge eller vara värd för din Function-app antingen i en [Premium-plan](../azure-functions/functions-premium-plan.md) eller en [dedikerad (App Service) plan](../azure-functions/dedicated-plan.md).

## <a name="next-steps"></a>Nästa steg

* [Anslutnings gränser i Azure Functions](../azure-functions/manage-connections.md#connection-limit)
* [Aktivera övervakning](../azure-functions/functions-monitoring.md) i Azure Functions-program.
* Lär dig hur du [diagnostiserar och felsöker vanliga problem](./troubleshoot-changefeed-functions.md) när du använder Azure Functions-utlösare för Cosmos dB.