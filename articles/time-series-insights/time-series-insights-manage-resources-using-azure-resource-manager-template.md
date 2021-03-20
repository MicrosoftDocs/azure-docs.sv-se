---
title: Hantera din miljö med Azure Resource Manager mallar – Azure Time Series Insights | Microsoft Docs
description: Lär dig hur du hanterar Azure Time Series Insights miljön program mässigt med hjälp av Azure Resource Manager.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: 493750e69b1fdc935b04d6dc705cfd046b6b086e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011667"
---
# <a name="create-azure-time-series-insights-gen-1-resources-using-azure-resource-manager-templates"></a>Skapa Azure Time Series Insights gen 1-resurser med Azure Resource Manager-mallar

> [!CAUTION]
> Det här är en gen1-artikel.

Den här artikeln beskriver hur du skapar och distribuerar Azure Time Series Insights-resurser med hjälp av [Azure Resource Manager mallar](../azure-resource-manager/index.yml), PowerShell och Azure Time Series Insights resurs leverantören.

Azure Time Series Insights stöder följande resurser:

   | Resurs | Beskrivning |
   | --- | --- |
   | Miljö | En Azure Time Series Insightss miljö är en logisk gruppering av händelser som läses från händelse hanterare, lagras och görs tillgängliga för frågor. Mer information finns [i planera din Azure Time Series Insightss miljö](time-series-insights-environment-planning.md) |
   | Händelsekälla | En händelse källa är en anslutning till en händelse hanterare som Azure Time Series Insights läser och matar in händelser i miljön. Händelse källor som stöds för närvarande är IoT Hub och Händelsehubben. |
   | Referens data uppsättning | Referens data uppsättningar innehåller metadata om händelserna i miljön. Metadata i referens data uppsättningar kommer att kopplas till händelser vid ingångar. Referens data uppsättningar definieras som resurser efter deras händelse nyckel egenskaper. De faktiska metadata som utgör referens data uppsättningen överförs eller ändras via API: er för data plan. |
   | Åtkomst princip | Åtkomst principer beviljar behörigheter för att utfärda data frågor, manipulera referens data i miljön och dela sparade frågor och perspektiv som är associerade med miljön. Mer information finns i [bevilja åtkomst till en Azure Time Series Insights-miljö med Azure Portal](./concepts-access-policies.md) |

En Resource Manager-mall är en JSON-fil som definierar infrastrukturen och konfigurationen av resurser i en resurs grupp. I följande dokument beskrivs mallfiler i större detalj:

- [Distribution av Azure Resource Manager-mall](../azure-resource-manager/templates/overview.md)
- [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Resurs typer för Microsoft. TimeSeriesInsights](/azure/templates/microsoft.timeseriesinsights/allversions)

Snabb starts mal len [201-timeseriesinsights-Environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) publiceras på GitHub. Den här mallen skapar en Azure Time Series Insights miljö, en underordnad händelse källa som har kon figurer ATS för att använda händelser från en Händelsehubben och åtkomst principer som beviljar åtkomst till miljöns data. Om ingen befintlig Händelsehubben anges skapas en med distributionen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="specify-deployment-template-and-parameters"></a>Ange distributions mal len och parametrar

Följande procedur beskriver hur du använder PowerShell för att distribuera en Azure Resource Manager-mall som skapar en Azure Time Series Insights miljö, en underordnad händelse källa som har kon figurer ATS för att använda händelser från en Händelsehubben och åtkomst principer som ger åtkomst till miljöns data. Om ingen befintlig Händelsehubben anges skapas en med distributionen.

1. Installera Azure PowerShell genom att följa anvisningarna i [komma igång med Azure PowerShell](/powershell/azure/get-started-azureps).

1. Klona eller kopiera mallen [201-timeseriesinsights-Environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) från GitHub.

   - Skapa en parameter fil

     Om du vill skapa en parameter fil kopierar du filen [201-timeseriesinsights-Environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json) .

      [!code-json[deployment-parameters](~/quickstart-templates/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)]

    <div id="required-parameters"></div>

   - Obligatoriska parametrar

     | Parameter | Beskrivning |
     | --- | --- |
     | eventHubNamespaceName | Namn området för käll händelse navet. |
     | eventHubName | Namnet på käll händelse navet. |
     | consumerGroupName | Namnet på den konsument grupp som Azure Time Series Insights tjänsten använder för att läsa data från händelsehubben. **Obs:** För att undvika resurs konkurrens måste den här konsument gruppen vara dedikerad till Azure Time Series Insights tjänsten och inte delas med andra läsare. |
     | environmentName | Namnet på miljön. Namnet får inte innehålla:,,,,,,   `<` `>` `%` `&` `:` `\\` `?` `/` , och eventuella kontroll tecken. Alla andra tecken tillåts.|
     | eventSourceName | Namnet på den underordnade resursen för händelse källan. Namnet får inte innehålla:,,,,,,   `<` `>` `%` `&` `:` `\\` `?` `/` , och eventuella kontroll tecken. Alla andra tecken tillåts. |

    <div id="optional-parameters"></div>

   - Valfria parametrar

     | Parameter | Beskrivning |
     | --- | --- |
     | existingEventHubResourceId | Ett valfritt resurs-ID för en befintlig händelsehubben som ska anslutas till Azure Time Series Insightss miljön via händelse källan. **Obs:** Användaren som distribuerar mallen måste ha behörighet att utföra åtgärden listnycklar i Händelsehubben. Om inget värde skickas skapas en ny händelsehubben av mallen. |
     | environmentDisplayName | Ett valfritt eget namn som ska visas i verktygs-eller användar gränssnitt i stället för miljö namnet. |
     | environmentSkuName | Namnet på SKU:n. Mer information finns på sidan med [priser för Azure Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
     | environmentSkuCapacity | Enhets kapaciteten för SKU: n. Mer information finns på sidan med [priser för Azure Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).|
     | environmentDataRetentionTime | Det minsta TimeSpan som miljöns händelser kommer att vara tillgängliga för fråga. Värdet måste anges i ISO 8601-format, till exempel `P30D` för en bevarande princip på 30 dagar. |
     | eventSourceDisplayName | Ett valfritt eget namn som ska visas i verktygs-eller användar gränssnitt i stället för händelse källans namn. |
     | eventSourceTimestampPropertyName | Händelse egenskapen som ska användas som händelse källans tidstämpel. Om ett värde inte har angetts för timestampPropertyName, eller om null eller tom-sträng har angetts, används händelse skapande tiden. |
     | eventSourceKeyName | Namnet på den delade åtkomst nyckel som Azure Time Series Insightss tjänsten använder för att ansluta till händelsehubben. |
     | accessPolicyReaderObjectIds | En lista med objekt-ID: n för de användare eller program i Azure AD som ska ha läsar åtkomst till miljön. Tjänstens huvud-ID kan erhållas genom att anropa cmdletarna **Get-AzADUser** eller **Get-AzADServicePrincipal** . Det finns ännu inte stöd för att skapa en åtkomst princip för Azure AD-grupper. |
     | accessPolicyContributorObjectIds | En lista med objekt-ID: n för de användare eller program i Azure AD som ska ha deltagar åtkomst till miljön. Tjänstens huvud-ID kan erhållas genom att anropa cmdletarna **Get-AzADUser** eller **Get-AzADServicePrincipal** . Det finns ännu inte stöd för att skapa en åtkomst princip för Azure AD-grupper. |

   - Som exempel används följande parameter fil för att skapa en miljö och en händelse källa som läser händelser från en befintlig händelsehubben. Dessutom skapas två åtkomst principer som ger deltagar åtkomst till miljön.

     ```JSON
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "eventHubNamespaceName": {
                 "value": "tsiTemplateTestNamespace"
             },
             "eventHubName": {
                 "value": "tsiTemplateTestEventHub"
             },
             "consumerGroupName": {
                 "value": "tsiTemplateTestConsumerGroup"
             },
             "environmentName": {
                 "value": "tsiTemplateTestEnvironment"
             },
             "eventSourceName": {
                 "value": "tsiTemplateTestEventSource"
             },
             "existingEventHubResourceId": {
                 "value": "/subscriptions/{yourSubscription}/resourceGroups/MyDemoRG/providers/Microsoft.EventHub/namespaces/tsiTemplateTestNamespace/eventhubs/tsiTemplateTestEventHub"
             },
             "accessPolicyContributorObjectIds": {
                 "value": [
                     "AGUID001-0000-0000-0000-000000000000",
                     "AGUID002-0000-0000-0000-000000000000"
                 ]
             }
         }
     }
     ```

   - Mer information finns i artikeln om [parametrar](../azure-resource-manager/templates/parameter-files.md) .

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Distribuera snabb starts mal len lokalt med PowerShell

> [!IMPORTANT]
> De kommando rads åtgärder som visas nedan beskriver [AZ PowerShell-modulen](/powershell/azure/).

1. I PowerShell loggar du in på ditt Azure-konto.

    - Kör följande kommando från en PowerShell-kommandotolk:

      ```powershell
      Connect-AzAccount
      ```

    - Du uppmanas att logga in på ditt Azure-konto. När du har loggat in kör du följande kommando för att visa dina tillgängliga prenumerationer:

      ```powershell
      Get-AzSubscription
      ```

    - Det här kommandot returnerar en lista med tillgängliga Azure-prenumerationer. Välj en prenumeration för den aktuella sessionen genom att köra följande kommando. Ersätt `<YourSubscriptionId>` med GUID för den Azure-prenumeration som du vill använda:

      ```powershell
      Set-AzContext -SubscriptionID <YourSubscriptionId>
      ```

1. Skapa en ny resurs grupp om det inte finns någon.

   - Om du inte har en befintlig resurs grupp skapar du en ny resurs grupp med kommandot **New-AzResourceGroup** . Ange namnet på den resurs grupp och plats som du vill använda. Exempel:

     ```powershell
     New-AzResourceGroup -Name MyDemoRG -Location "West US"
     ```

   - Om det lyckas visas en sammanfattning av den nya resurs gruppen.

     ```powershell
     ResourceGroupName : MyDemoRG
     Location          : westus
     ProvisioningState : Succeeded
     Tags              :
     ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
     ```

1. Testa distributionen.

   - Verifiera distributionen genom att köra `Test-AzResourceGroupDeployment` cmdleten. När du testar distributionen ska du ange parametrar exakt som du skulle göra när du utför distributionen.

     ```powershell
     Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
     ```

1. Skapa distributionen

    - Skapa den nya distributionen genom att köra `New-AzResourceGroupDeployment` cmdleten och ange nödvändiga parametrar när du uppmanas att göra det. Parametrarna innehåller ett namn för din distribution, namnet på din resurs grupp och sökvägen eller URL: en till mallfilen. Om parametern **mode** inte anges används standardvärdet **incremental** . Mer information finns i [stegvisa och fullständiga distributioner](../azure-resource-manager/templates/deployment-modes.md).

    - Följande kommando efterfrågar de fem obligatoriska parametrarna i PowerShell-fönstret:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

    - Använd följande kommando för att ange en parameter fil i stället:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
      ```

    - Du kan också använda infogade parametrar när du kör distributions-cmdleten. Kommandot är följande:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
      ```

    - Om du vill köra en [fullständig](../azure-resource-manager/templates/deployment-modes.md) distribution anger du att parametern **läge** ska **slutföras**:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

1. Verifiera distributionen

    - Om resurserna har distribuerats visas en sammanfattning av distributionen i PowerShell-fönstret:

      ```powershell
       DeploymentName          : MyDemoDeployment
       ResourceGroupName       : MyDemoRG
       ProvisioningState       : Succeeded
       Timestamp               : 10/11/2019 3:20:37 AM
       Mode                    : Incremental
       TemplateLink            :
       Parameters              :
                                 Name                                Type                       Value
                                 ==================================  =========================  ==========
                                 eventHubNewOrExisting               String                     new
                                 eventHubResourceGroup               String                     MyDemoRG
                                 eventHubNamespaceName               String                     tsiquickstartns
                                 eventHubName                        String                     tsiquickstarteh
                                 consumerGroupName                   String                     tsiquickstart
                                 environmentName                     String                     tsiquickstart
                                 environmentDisplayName              String                     tsiquickstart
                                 environmentSkuName                  String                     S1
                                 environmentSkuCapacity              Int                        1
                                 environmentDataRetentionTime        String                     P30D
                                 eventSourceName                     String                     tsiquickstart
                                 eventSourceDisplayName              String                     tsiquickstart
                                 eventSourceTimestampPropertyName    String
                                 eventSourceKeyName                  String                     manage
                                 accessPolicyReaderObjectIds         Array                      []
                                 accessPolicyContributorObjectIds    Array                      []
                                 location                            String                     westus

       Outputs                 :
                                  Name              Type                       Value
                                  ================  =========================  ==========
                                  dataAccessFQDN    String
                                  11aa1aa1-a1aa-1a1a-a11a-aa111a111a11.env.timeseries.azure.com

       DeploymentDebugLogLevel :
      ```

1. Distribuera snabb starts mal len via Azure Portal

   - Start sidan för snabb starts mal len på GitHub innehåller också knappen **distribuera till Azure** . När du klickar på den öppnas en anpassad distributions sida i Azure Portal. Från den här sidan kan du ange eller välja värden för var och en av parametrarna från de [obligatoriska parametrarna](#required-parameters) eller [valfria parameter](#optional-parameters) tabeller. När du har fyllt i inställningarna kommer du att initiera mallen genom att klicka på knappen **köp** .
    </br>
    </br>
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
       <img src="https://azuredeploy.net/deploybutton.png" alt="The Deploy to Azure button."/>
    </a>

## <a name="next-steps"></a>Nästa steg

- Information om hur du hanterar Azure Time Series Insights-resurser via programmering med hjälp av REST API: er finns i [Azure Time Series Insights hantering](/rest/api/time-series-insights-management/).