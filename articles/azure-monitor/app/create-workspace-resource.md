---
title: Skapa en ny Azure Monitor Application Insights arbets ytans baserade resurs | Microsoft Docs
description: Lär dig mer om de steg som krävs för att aktivera de nya Azure Monitor Application Insights arbets ytans baserade resurser.
ms.topic: conceptual
ms.date: 10/06/2020
ms.openlocfilehash: 3ec0b25a24af13b29a3568165009f8a6d66e0218
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578348"
---
# <a name="workspace-based-application-insights-resources"></a>Arbets yta-baserade Application Insights resurser

Arbets ytans resurser stöder fullständig integrering mellan Application Insights och Log Analytics. Nu kan du välja att skicka din Application Insights telemetri till en gemensam Log Analytics arbets yta, vilket ger dig fullständig åtkomst till alla funktioner i Log Analytics samtidigt som du behåller program-, infrastruktur-och plattforms loggar på en enda konsol IDE rad plats.

Detta möjliggör även vanlig rollbaserad åtkomst kontroll i Azure (Azure RBAC) i dina resurser och eliminerar behovet av frågor mellan appar/arbets ytor.

> [!NOTE]
> Data inmatning och kvarhållning för arbets ytans baserade Application Insights resurser faktureras via arbets ytan Log Analytics där data finns. [Läs mer]( ./pricing.md#workspace-based-application-insights) om fakturering för arbets ytans baserade Application Insights-resurser.

## <a name="new-capabilities"></a>Nya funktioner

Med arbets ytans baserade Application Insights kan du dra nytta av de senaste funktionerna i Azure Monitor och Log Analytics inklusive:

* [Kundhanterade nycklar (CMK)](../logs/customer-managed-keys.md) tillhandahåller kryptering i vila för dina data med krypterings nycklar som bara du har åtkomst till.
* Med [Azures privata länk](../logs/private-link-security.md) kan du på ett säkert sätt länka Azure PaaS-tjänster till ditt virtuella nätverk med hjälp av privata slut punkter.
* [Ta med din egen lagring (BYOS) för profiler och Snapshot debugger](./profiler-bring-your-own-storage.md) ger dig fullständig kontroll över principen för kryptering vid vila, livs längds hanterings principen och nätverks åtkomst för alla data som är kopplade till Application Insights Profiler och Snapshot debugger. 
* Med [kapacitet reservations nivåer](../logs/manage-cost-storage.md#pricing-model) kan du spara så mycket som 25% jämfört med priset betala per användning. 
* Snabbare data inmatning via Log Analytics strömning.

## <a name="create-workspace-based-resource"></a>Skapa arbets yta-baserad resurs

Logga in på [Azure Portal](https://portal.azure.com)och skapa en Application Insights resurs:

> [!div class="mx-imgBorder"]
> ![Arbets yta-baserad Application Insights resurs](./media/create-workspace-resource/create-workspace-based.png)

Om du inte redan har en befintlig Log Analytics arbets yta kan du [läsa dokumentationen för att skapa Log Analytics arbets ytor](../logs/quick-create-workspace.md).

**Arbets ytans baserade resurser är för närvarande tillgängliga i alla kommersiella regioner och Azure Government**

När din resurs har skapats visas motsvarande information om arbets ytan i **översikts** fönstret:

![Namn på arbetsyta](./media/create-workspace-resource/workspace-name.png)

Om du klickar på den blå länk texten tas du till den associerade Log Analytics arbets ytan där du kan dra nytta av den nya frågan i enhetlig arbets yta.

> [!NOTE]
> Vi ger fortfarande fullständig bakåtkompatibilitet för dina Application Insights klassiska resurs frågor, arbets böcker och loggbaserade aviseringar inom Application Insightss upplevelsen. Om du vill fråga/Visa mot den [nya arbetsytebaserade tabell strukturen/schemat](apm-tables.md) måste du först gå till din Log Analytics-arbetsyta. Genom att välja **loggar (analys)** i Application Insightss fönstret får du till gång till den klassiska Application Insights fråge upplevelsen.

## <a name="copy-the-connection-string"></a>Kopiera anslutningssträngen

[Anslutnings strängen](./sdk-connection-string.md?tabs=net) identifierar den resurs som du vill associera dina telemetridata med. Du kan också ändra de slut punkter som resursen kommer att använda som mål för din telemetri. Du måste kopiera anslutnings strängen och lägga till den i programmets kod eller till en miljö variabel.

## <a name="monitoring-configuration"></a>Övervaknings konfiguration

När en arbets yta-baserad Application Insights resurs har skapats är det relativt enkelt att konfigurera övervakning.

### <a name="code-based-application-monitoring"></a>Kod baserad program övervakning

För kodbaserade program övervakning installerar du bara lämplig Application Insights SDK och pekar på Instrumentation-nyckeln eller anslutnings strängen till den nya resursen.  

Detaljerad dokumentation om hur du konfigurerar en Application Insights SDK för kodbaserade övervakning finns i språk-och Framework-specifik dokumentation:

- [ASP.NET](./asp-net.md)
- [ASP.NET Core ](./asp-net-core.md)
- [Bakgrunds aktiviteter & moderna konsol program (.NET/.NET Core)](./worker-service.md)
- [Klassiska konsol program (.NET)](./console.md) 
- [Programmeringsspråket ](./java-get-started.md?tabs=maven)
- [JavaScript](./javascript.md)
- [Node.js](./nodejs.md)
- [Python](./opencensus-python.md)

### <a name="codeless-monitoring-and-visual-studio-resource-creation"></a>Övervakning av kod och Visual Studio-resurs skapas

För kod lös övervakning av tjänster som Azure Functions-och Azure App-tjänster måste du också först skapa din arbets yta-baserade Application Insights-resurs och sedan peka på resursen under övervaknings konfigurations fasen.

Dessa tjänster erbjuder alternativet att skapa en ny Application Insights-resurs i en egen resurs skapande process, men resurser som skapats via dessa GRÄNSSNITTs alternativ är för närvarande begränsade till den klassiska Application Insightss upplevelsen.

Samma sak gäller för Application Insights att skapa resurser i Visual Studio för ASP.NET och ASP.NET Core. Du måste välja en befintlig arbets yta baserad resurs från med gränssnittet för Visual Studio Monitoring-aktivering. Om du väljer Skapa ny resurs i Visual Studio begränsas du till att skapa en klassisk Application Insights-resurs.

## <a name="creating-a-resource-automatically"></a>Skapa en resurs automatiskt

### <a name="azure-cli"></a>Azure CLI

För att få åtkomst till för hands versionen Application Insights Azure CLI-kommandon måste du först köra:

```azurecli
 az extension add -n application-insights
```

Om du inte kör `az extension add` kommandot visas ett fel meddelande som säger: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Nu kan du köra följande för att skapa din Application Insights-resurs:

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--only-show-errors]
                                         [--query-access {Disabled, Enabled}]
                                         [--tags]
                                         [--workspace]
```

#### <a name="example"></a>Exempel

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g my_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

Fullständig Azure CLI-dokumentation för det här kommandot finns i [Azure CLI-dokumentationen](/cli/azure/ext/application-insights/monitor/app-insights/component#ext-application-insights-az-monitor-app-insights-component-create).

### <a name="azure-powershell"></a>Azure PowerShell

`New-AzApplicationInsights`PowerShell-kommandot har för närvarande inte stöd för att skapa en arbets yta baserad Application Insights resurs. Om du vill skapa en arbets yta baserad resurs med PowerShell kan du använda Azure Resource Manager-mallarna nedan och distribuera med PowerShell.

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

#### <a name="template-file"></a>Mallfil

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>Parameter fil

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>Ändra den associerade arbets ytan

När en arbets yta-baserad Application Insights resurs har skapats kan du ändra den associerade Log Analytics-arbetsytan.

I fönstret Application Insights resurs väljer du **Egenskaper**  >  **ändra arbets yta**  >  **Log Analytics arbets ytor**

## <a name="export-telemetry"></a>Exportera telemetri

Äldre funktioner för kontinuerlig export stöds inte för arbets ytans resurser. I stället väljer du **diagnostikinställningar**  >  **Lägg till diagnostisk inställning** inifrån din Application Insights-resurs. Du kan välja alla tabeller eller en delmängd av tabeller för att arkivera till ett lagrings konto eller strömma till en Azure Event Hub.

> [!NOTE]
> Det finns för närvarande inga ytterligare avgifter för exporten av telemetri. Pris information för den här funktionen kommer att finnas på [sidan Azure Monitor priser](https://azure.microsoft.com/pricing/details/monitor/).  Innan faktureringen kommer att skickas meddelanden. Om du väljer att fortsätta använda <feature name> efter meddelande perioden debiteras du enligt tillämplig taxa. 
 

## <a name="next-steps"></a>Nästa steg

* [Utforska mått](../essentials/metrics-charts.md)
* [Skriv analysfrågor](../logs/log-query-overview.md)
