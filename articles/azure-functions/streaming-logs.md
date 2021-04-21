---
title: Strömma körningsloggar i Azure Functions
description: 115–145 tecken, inklusive blanksteg. Den här sammanfattningen visas i sökresultatet.
ms.date: 9/1/2020
ms.topic: how-to
ms.custom: contperf-fy21q2, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 4afb1068acda69c9dd65a423d887abea80c695cd
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830906"
---
# <a name="enable-streaming-execution-logs-in-azure-functions"></a>Aktivera direktuppspelningskörningsloggar i Azure Functions

När du utvecklar ett program vill du ofta se vad som skrivs till loggarna nästan i realtid när du kör i Azure.

Det finns två sätt att visa en ström av loggfiler som genereras av funktionskörningarna.

* **Inbyggd loggströmning:** Med App Service-plattformen kan du visa en ström av dina programloggfiler. Detta motsvarar de utdata som visas när [](functions-develop-local.md) du felsöker dina funktioner under lokal utveckling och när du använder **fliken** Test i portalen. All loggbaserad information visas. Mer information finns i [Stream-loggar.](../app-service/troubleshoot-diagnostic-logs.md#stream-logs) Den här strömningsmetoden stöder bara en enda instans och kan inte användas med en app som körs på Linux i en förbrukningsplan.

* **Live Metrics Stream:** när funktionsappen är ansluten [till Application Insights](configure-monitoring.md#enable-application-insights-integration)kan du visa loggdata och andra mått nästan i realtid i Azure Portal med hjälp [av Live Metrics Stream](../azure-monitor/app/live-stream.md). Använd den här metoden när du övervakar funktioner som körs på flera instanser eller på Linux i en förbrukningsplan. Den här metoden [använder exempeldata](configure-monitoring.md#configure-sampling).

Loggströmmar kan visas både i portalen och i de flesta lokala utvecklingsmiljöer. 

## <a name="portal"></a>Portalen

Du kan visa båda typerna av loggströmmar i portalen.

### <a name="built-in-log-streaming"></a>Inbyggd loggströmning

Om du vill visa strömningsloggar i portalen väljer **du fliken Plattformsfunktioner** i funktionsappen. Under Övervakning **väljer du** **loggströmning.**

![Aktivera strömningsloggar i portalen](./media/functions-monitoring/enable-streaming-logs-portal.png)

Detta ansluter din app till loggströmningstjänsten och programloggar visas i fönstret. Du kan växla mellan **Programloggar och** **Webbserverloggar.**  

![Visa strömningsloggar i portalen](./media/functions-monitoring/streaming-logs-window.png)

### <a name="live-metrics-stream"></a>Live-ström med mätvärden

Om du vill Live Metrics Stream för din app väljer du **fliken Översikt** för din funktionsapp. När du har Application Insights aktiveras visas en länk **Application Insights** under **Konfigurerade funktioner**. Den här länken tar dig Application Insights sidan för din app.

I Application Insights väljer du **Live Metrics Stream**. [Exempelloggposter visas](configure-monitoring.md#configure-sampling) under **Exempeltelemetri**.

![Visa Live Metrics Stream i portalen](./media/functions-monitoring/live-metrics-stream.png) 

## <a name="visual-studio-code"></a>Visuell Studio-kod

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

## <a name="core-tools"></a>Core Tools

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

## <a name="azure-cli"></a>Azure CLI

Du kan aktivera strömmande loggar med hjälp av [Azure CLI.](/cli/azure/install-azure-cli) Använd följande kommandon för att logga in, välja din prenumeration och strömma loggfiler:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

## <a name="azure-powershell"></a>Azure PowerShell

Du kan aktivera strömmande loggar med hjälp [av Azure PowerShell](/powershell/azure/). För PowerShell använder du [kommandot Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) för att aktivera loggning i funktionsappen, som du ser i följande kodavsnitt: 

:::code language="powershell" source="~/powershell_scripts/app-service/monitor-with-logs/monitor-with-logs.ps1" range="19-20":::

Mer information finns i det [fullständiga kodexe exemplet](../app-service/scripts/powershell-monitor.md#sample-script). 

## <a name="next-steps"></a>Nästa steg

+ [Övervaka Azure Functions](functions-monitoring.md)
+ [Analysera Azure Functions telemetri i Application Insights](analyze-telemetry-data.md)
