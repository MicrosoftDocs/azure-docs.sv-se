---
title: Strömma körnings loggar i Azure Functions
description: 115-145 tecken inklusive blank steg. Den här sammanfattningen visas i sökresultatet.
ms.date: 9/1/2020
ms.topic: how-to
ms.custom: contperf-fy21q2, devx-track-azurecli
ms.openlocfilehash: 085849386fce929ceaec4536844cf31b94fe7539
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033059"
---
# <a name="enable-streaming-execution-logs-in-azure-functions"></a>Aktivera strömnings körnings loggar i Azure Functions

När du utvecklar ett program vill du ofta se vad som skrivs till loggarna i nära real tid när de körs i Azure.

Det finns två sätt att visa en ström med loggfiler som genereras av dina funktions körningar.

* **Inbyggd logg strömning**: app Services plattformen gör att du kan visa en ström med dina program loggs-filer. Detta motsvarar de utdata som visas när du felsöker dina funktioner under [lokal utveckling](functions-develop-local.md) och när du använder fliken **test** i portalen. All logg-baserad information visas. Mer information finns i [Stream-loggar](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Den här streaming-metoden har endast stöd för en enda instans och kan inte användas med en app som körs på Linux i en förbruknings plan.

* **Live Metrics Stream**: när din Function-app är [ansluten till Application Insights](configure-monitoring.md#enable-application-insights-integration)kan du Visa loggdata och andra mått i nära real tid i Azure Portal med hjälp av [Live Metrics Stream](../azure-monitor/app/live-stream.md). Använd den här metoden när du övervakar funktioner som körs på flera instanser eller i Linux i en förbruknings plan. Den här metoden använder [exempel data](configure-monitoring.md#configure-sampling).

Logg strömmar kan visas både i portalen och i de flesta lokala utvecklings miljöer. 

## <a name="portal"></a>Portalen

Du kan visa båda typerna av logg strömmar i portalen.

### <a name="built-in-log-streaming"></a>Inbyggd logg strömning

Om du vill visa strömmande loggar i portalen väljer du fliken **plattforms funktioner** i din Function-app. Välj sedan **logg strömning** under **övervakning**.

![Aktivera strömmande loggar i portalen](./media/functions-monitoring/enable-streaming-logs-portal.png)

Detta ansluter din app till logg strömnings tjänsten och program loggar visas i fönstret. Du kan växla mellan **program loggar** och **webb server loggar**.  

![Visa strömmande loggar i portalen](./media/functions-monitoring/streaming-logs-window.png)

### <a name="live-metrics-stream"></a>Live-ström med mätvärden

Om du vill visa Live Metrics Stream för din app väljer du fliken **Översikt** i din Function-app. När du har Application Insights aktiverar kan du se en **Application Insights** länk under **konfigurerade funktioner**. Den här länken leder till Application Insights sidan för din app.

I Application Insights väljer du **Live Metrics Stream**. [Exempel logg poster](configure-monitoring.md#configure-sampling) visas under **exempel telemetri**.

![Visa Live Metrics Stream i portalen](./media/functions-monitoring/live-metrics-stream.png) 

## <a name="visual-studio-code"></a>Visuell Studio-kod

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

## <a name="core-tools"></a>Kärn verktyg

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

## <a name="azure-cli"></a>Azure CLI

Du kan aktivera strömmande loggar med hjälp av [Azure CLI](/cli/azure/install-azure-cli). Använd följande kommandon för att logga in, välja din prenumeration och strömma loggfiler:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

## <a name="azure-powershell"></a>Azure PowerShell

Du kan aktivera strömnings loggar med [Azure PowerShell](/powershell/azure/). För PowerShell använder du kommandot [set-AzWebApp](/powershell/module/az.websites/set-azwebapp) för att aktivera loggning i Function-appen, som du ser i följande kodfragment: 

:::code language="powershell" source="~/powershell_scripts/app-service/monitor-with-logs/monitor-with-logs.ps1" range="19-20":::

Mer information finns i det [fullständiga kod exemplet](../app-service/scripts/powershell-monitor.md#sample-script). 

## <a name="next-steps"></a>Nästa steg

+ [Övervaka Azure Functions](functions-monitoring.md)
+ [Analysera Azure Functions telemetri i Application Insights](analyze-telemetry-data.md)
