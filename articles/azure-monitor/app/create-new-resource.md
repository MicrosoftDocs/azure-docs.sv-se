---
title: Skapa en ny Azure Application Insights-| Microsoft Docs
description: Konfigurera manuellt Application Insights för ett nytt liveprogram.
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: 6158b5604046897e20053c67321f26d650c21b7f
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566230"
---
# <a name="create-an-application-insights-resource"></a>Skapa en Application Insights-resurs

Azure Application Insights visar data om ditt program i en Microsoft Azure *resurs*. Att skapa en ny resurs är därför en del [av att konfigurera Application Insights att övervaka ett nytt program][start]. När du har skapat den nya resursen kan du hämta dess instrumentationsnyckel och använda den för att konfigurera Application Insights SDK. Instrumenteringsnyckeln länkar din telemetri till resursen.

> [!IMPORTANT]
> [Klassisk Application Insights har gjorts inaktuell.](https://azure.microsoft.com/updates/we-re-retiring-classic-application-insights-on-29-february-2024/) Följ de här [anvisningarna om hur du uppgraderar till arbetsytebaserade Application Insights](convert-classic-resource.md).

## <a name="sign-in-to-microsoft-azure"></a>Logga in på Microsoft Azure

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="create-an-application-insights-resource"></a>Skapa en Application Insights-resurs

Logga in på [Azure Portal](https://portal.azure.com)och skapa en Application Insights resurs:

![Klicka på tecknet "+" i det övre vänstra hörnet. Välj Utvecklarverktyg följt av Application Insights](./media/create-new-resource/new-app-insights.png)

   | Inställningar        |  Värde           | Beskrivning  |
   | ------------- |:-------------|:-----|
   | **Namn**      | `Unique value` | Namn som identifierar den app som du övervakar. |
   | **Resursgrupp**     | `myResourceGroup`      | Namnet på den nya eller befintliga resursgruppen som ska vara värd för App Insights-data. |
   | **Region** | `East US` | Välj en plats nära dig eller nära den plats där appen finns. |
   | **Resursläge** | `Classic` eller `Workspace-based` | Med arbetsytebaserade resurser kan du skicka Application Insights telemetri till en gemensam Log Analytics-arbetsyta. Mer information finns i artikeln [om arbetsytebaserade resurser.](create-workspace-resource.md)

> [!NOTE]
> Du kan använda samma resursnamn i olika resursgrupper, men det kan vara bra att använda ett globalt unikt namn. Detta kan vara användbart om du planerar [att köra frågor mellan resurser](../logs/cross-workspace-query.md#identifying-an-application) eftersom det förenklar den nödvändiga syntaxen.

Ange lämpliga värden i de obligatoriska fälten och välj sedan **Granska + skapa.**

> [!div class="mx-imgBorder"]
> ![Ange värden i obligatoriska fält och välj sedan "granska + skapa".](./media/create-new-resource/review-create.png)

När appen har skapats öppnas ett nytt fönster. I det här fönstret ser du prestanda- och användningsdata om det övervakade programmet. 

## <a name="copy-the-instrumentation-key"></a>Kopiera instrumenteringsnyckeln

Instrumenteringsnyckeln identifierar den resurs som du vill associera dina telemetridata med. Du måste kopiera instrumenteringsnyckeln och lägga till den i programmets kod.

> [!IMPORTANT]
> Nya **Azure-regioner kräver** användning av anslutningssträngar i stället för instrumentationsnycklar. [Anslutningssträngen](./sdk-connection-string.md?tabs=net) identifierar den resurs som du vill associera dina telemetridata med. Du kan också ändra de slutpunkter som din resurs ska använda som mål för din telemetri. Du måste kopiera anslutningssträngen och lägga till den i programmets kod eller till en miljövariabel.

## <a name="install-the-sdk-in-your-app"></a>Installera SDK i din app

Installera Application Insights SDK i din app. Det här steget beror mycket på typen av program.

Använd instrumenteringsnyckeln för att [konfigurera SDK:n som du installerar i ditt program][start].

SDK:n innehåller standardmoduler som skickar telemetri utan att du behöver skriva någon ytterligare kod. Om du vill spåra användaråtgärder eller diagnostisera problem i detalj använder [du API:et][api] för att skicka din egen telemetri.

## <a name="creating-a-resource-automatically"></a>Skapa en resurs automatiskt

### <a name="powershell"></a>PowerShell

Skapa en ny Application Insights resurs

```powershell
New-AzApplicationInsights [-ResourceGroupName] <String> [-Name] <String> [-Location] <String> [-Kind <String>]
 [-Tag <Hashtable>] [-DefaultProfile <IAzureContextContainer>] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="example"></a>Exempel

```powershell
New-AzApplicationInsights -Kind java -ResourceGroupName testgroup -Name test1027 -location eastus
```
#### <a name="results"></a>Resultat

```powershell
Id                 : /subscriptions/{subid}/resourceGroups/testgroup/providers/microsoft.insights/components/test1027
ResourceGroupName  : testgroup
Name               : test1027
Kind               : web
Location           : eastus
Type               : microsoft.insights/components
AppId              : 8323fb13-32aa-46af-b467-8355cf4f8f98
ApplicationType    : web
Tags               : {}
CreationDate       : 10/27/2017 4:56:40 PM
FlowType           :
HockeyAppId        :
HockeyAppToken     :
InstrumentationKey : 00000000-aaaa-bbbb-cccc-dddddddddddd
ProvisioningState  : Succeeded
RequestSource      : AzurePowerShell
SamplingPercentage :
TenantId           : {subid}
```

Den fullständiga PowerShell-dokumentationen för den här cmdleten och information om hur du hämtar instrumenteringsnyckeln finns [i Azure PowerShell dokumentationen](/powershell/module/az.applicationinsights/new-azapplicationinsights).

### <a name="azure-cli-preview"></a>Azure CLI (förhandsversion)

För att få åtkomst Application Insights azure CLI-kommandon måste du först köra:

```azurecli
 az extension add -n application-insights
```

Om du inte kör `az extension add` kommandot visas ett felmeddelande som säger: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Nu kan du köra följande för att skapa Application Insights resurs:

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--kind]
                                         [--tags]
```

#### <a name="example"></a>Exempel

```azurecli
az monitor app-insights component create --app demoApp --location westus2 --kind web -g demoRg --application-type web
```

#### <a name="results"></a>Resultat

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g demoApp  --application-type web
{
  "appId": "87ba512c-e8c9-48d7-b6eb-118d4aee2697",
  "applicationId": "demoApp",
  "applicationType": "web",
  "creationDate": "2019-08-16T18:15:59.740014+00:00",
  "etag": "\"0300edb9-0000-0100-0000-5d56f2e00000\"",
  "flowType": "Bluefield",
  "hockeyAppId": null,
  "hockeyAppToken": null,
  "id": "/subscriptions/{subid}/resourceGroups/demoApp/providers/microsoft.insights/components/demoApp",
  "instrumentationKey": "00000000-aaaa-bbbb-cccc-dddddddddddd",
  "kind": "web",
  "location": "eastus",
  "name": "demoApp",
  "provisioningState": "Succeeded",
  "requestSource": "rest",
  "resourceGroup": "demoApp",
  "samplingPercentage": null,
  "tags": {},
  "tenantId": {tenantID},
  "type": "microsoft.insights/components"
}
```

Den fullständiga Azure CLI-dokumentationen för det här kommandot och information om hur du hämtar instrumenteringsnyckeln finns i [Azure CLI-dokumentationen.](/cli/azure/ext/application-insights/monitor/app-insights/component#ext-application-insights-az-monitor-app-insights-component-create)

## <a name="next-steps"></a>Nästa steg
* [Diagnostiksökning](./diagnostic-search.md)
* [Utforska mått](../essentials/metrics-charts.md)
* [Skriv analysfrågor](../logs/log-query-overview.md)

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[diagnostic]: ./diagnostic-search.md
[metrics]: ../essentials/metrics-charts.md
[start]: ./app-insights-overview.md

