---
title: Skapa Service Bus ämnes prenumeration och regel med Azure-mall
description: Skapa ett Service Bus-namnområde med ämne, prenumeration och regel med hjälp av Azure Resource Manager mall
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 3f3287dd67f89f678a9875ddce93e2d0d26b2209
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89077632"
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Skapa ett Service Bus-namnområde med ämne, prenumeration och regel med hjälp av en Azure Resource Manager mall

Den här artikeln visar hur du använder en Azure Resource Manager-mall som skapar ett Service Bus-namnområde med ett ämne, en prenumeration och regel (filter). Artikeln förklarar hur du anger vilka resurser som distribueras och hur du definierar parametrar som anges när distributionen körs. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav

Mer information om att skapa mallar finns i [Redigera Azure Resource Manager-mallar][Authoring Azure Resource Manager templates].

Mer information om praxis och mönster på namngivnings konventioner för Azure-resurser finns i [rekommenderade namn konventioner för Azure-resurser][Recommended naming conventions for Azure resources].

En fullständig mall finns i [Service Bus namnrymd med ämne, prenumeration och regel][Service Bus namespace with topic, subscription, and rule] mall.

> [!NOTE]
> Följande Azure Resource Manager mallar är tillgängliga för hämtning och distribution.
> 
> * [Skapa ett Service Bus-namnområde med kö och auktoriseringsregel](service-bus-resource-manager-namespace-auth-rule.md)
> * [Skapa ett Service Bus-namnområde med kö](service-bus-resource-manager-namespace-queue.md)
> * [Skapa ett namnområde för Service Bus](service-bus-resource-manager-namespace.md)
> * [Skapa ett Service Bus-namnområde med ämne och prenumeration](service-bus-resource-manager-namespace-topic.md)
> 
> Om du vill söka efter de senaste mallarna går du till galleriet för [Azure snabb starts mallar][Azure Quickstart Templates] och söker efter Service Bus.

## <a name="what-do-you-deploy"></a>Vad distribuerar du?

Med den här mallen distribuerar du ett Service Bus-namnområde med ämne, prenumeration och regel (filter).

[Service Bus ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) ger en en-till-många-form av kommunikation i ett mönster för *publicering/prenumeration* . När du använder ämnen och prenumerationer kommunicerar komponenter i ett distribuerat program inte direkt med varandra, i stället Exchange-meddelanden via ämne som fungerar som en mellanhand. En prenumeration på ett ämne liknar en virtuell kö som tar emot kopior av meddelanden som har skickats till ämnet. Ett filter för prenumerationen gör att du kan ange vilka meddelanden som skickas till ett ämne som ska visas i en speciell ämnes prenumeration.

## <a name="what-are-rules-filters"></a>Vad är regler (filter)?

I många fall måste meddelanden med särskilda egenskaper bearbetas på olika sätt. Om du vill aktivera den här anpassade bearbetningen kan du konfigurera prenumerationer för att hitta meddelanden som har vissa egenskaper och sedan utföra ändringar i dessa egenskaper. Även om Service Bus prenumerationer visar alla meddelanden som skickas till ämnet, kan du bara kopiera en delmängd av dessa meddelanden till kön för virtuella prenumerationer. Det utförs med hjälp av prenumerations filter. Mer information om regler (filter) finns i [regler och åtgärder](service-bus-queues-topics-subscriptions.md#rules-and-actions).

Klicka på följande knapp för att köra distributionen automatiskt:

[![Distribuera till Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Parametrar

Med Azure Resource Manager definierar du parametrar för värden som du vill ange när mallen distribueras. Mallen innehåller ett avsnitt som heter `Parameters` och som innehåller alla parametervärden. Definiera en parameter för de värden som varierar beroende på vilket projekt du distribuerar eller baserat på den miljö som du distribuerar till. Definiera inte parametrar för värden som aldrig ändras. Varje parametervärde används i mallen för att definiera de resurser som distribueras.

Mallen definierar följande parametrar:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

Namnet på Service Bus namn området som ska skapas.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName

Namnet på ämnet som skapats i Service Bus namn området.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName

Namnet på prenumerationen som skapades i Service Bus namn området.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusrulename"></a>serviceBusRuleName

Namnet på regeln (filtret) som skapats i namn området Service Bus.

```json
   "serviceBusRuleName": {
   "type": "string",
  }
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion

Mallens Service Bus-API-version.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```

## <a name="resources-to-deploy"></a>Resurser som ska distribueras

Skapar ett standard Service Bus-namnområde av typ **meddelanden**, med ämne och prenumeration och regler.

```json
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "sku": {
            "name": "Standard",
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filterType": "SqlFilter",
                        "sqlFilter": {
                            "sqlExpression": "StoreName = 'Store1'",
                            "requiresPreprocessing": "false"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

Information om JSON-syntax och egenskaper finns i [namn områden](/azure/templates/microsoft.servicebus/namespaces), [ämnen](/azure/templates/microsoft.servicebus/namespaces/topics), [prenumerationer](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions)och [regler](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions/rules).

## <a name="commands-to-run-deployment"></a>Kommandon för att köra distributionen

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du hanterar dessa resurser genom att läsa följande artiklar:

* [Hantera Azure Service Bus](service-bus-management-libraries.md)
* [Hantera Service Bus med PowerShell](service-bus-manage-with-ps.md)
* [Hantera Service Bus-resurser med Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md
[Recommended naming conventions for Azure resources]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
