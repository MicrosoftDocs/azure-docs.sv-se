---
title: Skapa Azure Service Bus-resurser med hjälp av mallar
description: Använd Azure Resource Manager mallar för att automatisera skapandet av Service Bus resurser
documentationcenter: .net
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: df8a7fde9114f03521f0e57e072f81a867efcf39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89075264"
---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Skapa Service Bus resurser med Azure Resource Manager-mallar

Den här artikeln beskriver hur du skapar och distribuerar Service Bus-resurser med hjälp av Azure Resource Manager mallar, PowerShell och Service Bus resurs leverantören.

Med Azure Resource Manager mallar kan du definiera de resurser som ska distribueras för en lösning och ange parametrar och variabler som gör att du kan mata in värden för olika miljöer. Mallen är skriven i JSON och består av uttryck som du kan använda för att skapa värden för din distribution. Detaljerad information om hur du skriver Azure Resource Manager mallar och en beskrivning av mallens format finns i [struktur och syntax för Azure Resource Manager mallar](../azure-resource-manager/templates/template-syntax.md).

> [!NOTE]
> I exemplen i den här artikeln visas hur du använder Azure Resource Manager för att skapa en Service Bus namnrymd och meddelande enhet (kö). Andra exempel på mallar finns i [galleriet för Azure snabb starts mallar][Azure Quickstart Templates gallery] och sök efter **Service Bus**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-bus-resource-manager-templates"></a>Service Bus Resource Manager-mallar

Dessa Service Bus Azure Resource Manager mallar är tillgängliga för hämtning och distribution. Klicka på följande länkar om du vill ha mer information om var och en, med länkar till mallarna på GitHub:

* [Skapa ett namnområde för Service Bus](service-bus-resource-manager-namespace.md)
* [Skapa ett Service Bus-namnområde med kö](service-bus-resource-manager-namespace-queue.md)
* [Skapa ett Service Bus-namnområde med ämne och prenumeration](service-bus-resource-manager-namespace-topic.md)
* [Skapa ett Service Bus-namnområde med kö och auktoriseringsregel](service-bus-resource-manager-namespace-auth-rule.md)
* [Skapa ett Service Bus-namnområde med ämne, prenumeration och regel](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>Distribuera med PowerShell

Följande procedur beskriver hur du använder PowerShell för att distribuera en Azure Resource Manager-mall som skapar ett standard-nivå Service Bus-namnområde och en kö inom denna namnrymd. Det här exemplet baseras på [namn området skapa en Service Bus med en ärendekö](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) . Det ungefärliga arbets flödet är följande:

1. Installera PowerShell.
2. Skapa mallen och (valfritt) en parameter fil.
3. I PowerShell loggar du in på ditt Azure-konto.
4. Skapa en ny resurs grupp om det inte finns någon.
5. Testa distributionen.
6. Ange distributions läge om du vill.
7. Distribuera mallen.

Fullständig information om hur du distribuerar Azure Resource Manager-mallar finns i [distribuera resurser med Azure Resource Manager mallar][Deploy resources with Azure Resource Manager templates].

### <a name="install-powershell"></a>Installera PowerShell

Installera Azure PowerShell genom att följa anvisningarna i [komma igång med Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Skapa en mall

Klona lagrings platsen eller kopiera mallen [201-Service Bus-Create-Queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) från GitHub:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
      }
    }
  },
  "variables": {
    "defaultSASKeyName": "RootManageSharedAccessKey",
    "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]",
    "sbVersion": "2017-04-01"
  },
  "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('serviceBusNamespaceName')]",
      "type": "Microsoft.ServiceBus/Namespaces",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "type": "Queues",
          "dependsOn": [
            "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "lockDuration": "PT5M",
            "maxSizeInMegabytes": "1024",
            "requiresDuplicateDetection": "false",
            "requiresSession": "false",
            "defaultMessageTimeToLive": "P10675199DT2H48M5.4775807S",
            "deadLetteringOnMessageExpiration": "false",
            "duplicateDetectionHistoryTimeWindow": "PT10M",
            "maxDeliveryCount": "10",
            "autoDeleteOnIdle": "P10675199DT2H48M5.4775807S",
            "enablePartitioning": "false",
            "enableExpress": "false"
          }
        }
      ]
    }
  ],
  "outputs": {
    "NamespaceConnectionString": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
    },
    "SharedAccessPolicyPrimaryKey": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
    }
  }
}
```

### <a name="create-a-parameters-file-optional"></a>Skapa en parameter fil (valfritt)

Om du vill använda en valfri parameter fil kopierar du filen [201-Service Bus-Create-Queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) . Ersätt värdet för `serviceBusNamespaceName` med namnet på Service Bus namn området som du vill skapa i den här distributionen och Ersätt värdet `serviceBusQueueName` med namnet på kön som du vill skapa.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2017-04-01"
        }
    }
}
```

Mer information finns i artikeln om [parametrar](../azure-resource-manager/templates/parameter-files.md) .

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Logga in på Azure och ange Azure-prenumerationen

Kör följande kommando från en PowerShell-kommandotolk:

```powershell
Connect-AzAccount
```

Du uppmanas att logga in på ditt Azure-konto. När du har loggat in kör du följande kommando för att visa dina tillgängliga prenumerationer:

```powershell
Get-AzSubscription
```

Det här kommandot returnerar en lista med tillgängliga Azure-prenumerationer. Välj en prenumeration för den aktuella sessionen genom att köra följande kommando. Ersätt `<YourSubscriptionId>` med GUID för den Azure-prenumeration som du vill använda:

```powershell
Set-AzContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Ange resurs grupp

Om du inte har en befintlig resurs grupp skapar du en ny resurs grupp med kommandot **New-AzResourceGroup** . Ange namnet på den resurs grupp och plats som du vill använda. Exempel:

```powershell
New-AzResourceGroup -Name MyDemoRG -Location "West US"
```

Om det lyckas visas en sammanfattning av den nya resurs gruppen.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Testa distributionen

Verifiera distributionen genom att köra `Test-AzResourceGroupDeployment` cmdleten. När du testar distributionen ska du ange parametrar exakt som du skulle göra när du utför distributionen.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Skapa distributionen

Skapa den nya distributionen genom att köra `New-AzResourceGroupDeployment` cmdleten och ange nödvändiga parametrar när du uppmanas att göra det. Parametrarna innehåller ett namn för din distribution, namnet på din resurs grupp och sökvägen eller URL: en till mallfilen. Om parametern **mode** inte anges används standardvärdet **incremental** . Mer information finns i [stegvisa och fullständiga distributioner](../azure-resource-manager/templates/deployment-modes.md).

Följande kommando efterfrågar de tre parametrarna i PowerShell-fönstret:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Använd följande kommando för att ange en parameter fil i stället:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Du kan också använda infogade parametrar när du kör distributions-cmdleten. Kommandot är följande:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Om du vill köra en [fullständig](../azure-resource-manager/templates/deployment-modes.md) distribution anger du att parametern **läge** ska **slutföras**:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>Verifiera distributionen
Om resurserna har distribuerats visas en sammanfattning av distributionen i PowerShell-fönstret:

```powershell
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2017 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2017-04-01

```

## <a name="next-steps"></a>Nästa steg
Du har nu sett det grundläggande arbets flödet och kommandon för att distribuera en Azure Resource Manager-mall. Mer detaljerad information finns på följande länkar:

* [Översikt över Azure Resource Manager][Azure Resource Manager overview]
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell][Deploy resources with Azure Resource Manager templates]
* [Skapa Azure Resource Manager-mallar](../azure-resource-manager/templates/template-syntax.md)
* [Resurs typer för Microsoft. Service Bus](/azure/templates/microsoft.servicebus/allversions)

[Azure Resource Manager overview]: ../azure-resource-manager/management/overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/templates/deploy-powershell.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus
