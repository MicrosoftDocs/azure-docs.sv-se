---
title: Skapa en regel för Service Bus auktoriseringsregler med en Azure-mall
description: Skapa en Service Bus auktoriseringsregel för namn område och kö med Azure Resource Manager-mall
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurecli
ms.openlocfilehash: b0ffe022f6c61cf7d06c510c4f8fa5c764d72b42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88067196"
---
# <a name="create-a-service-bus-authorization-rule-for-namespace-and-queue-using-an-azure-resource-manager-template"></a>Skapa en Service Bus auktoriseringsregel för namn område och kö med hjälp av en Azure Resource Manager mall

Den här artikeln visar hur du använder en Azure Resource Manager-mall som skapar en [auktoriseringsregel](service-bus-authentication-and-authorization.md#shared-access-signature) för ett Service Bus namn område och en kö. Artikeln förklarar hur du anger vilka resurser som distribueras och hur du definierar parametrar som anges när distributionen körs. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav.

Mer information om hur du skapar mallar finns i [redigera Azure Resource Manager mallar][Authoring Azure Resource Manager templates].

Den fullständiga mallen finns i [mallen Service Bus autentiseringsregel][Service Bus auth rule template] på GitHub.

> [!NOTE]
> Följande Azure Resource Manager mallar är tillgängliga för hämtning och distribution.
> 
> * [Skapa ett namnområde för Service Bus](service-bus-resource-manager-namespace.md)
> * [Skapa ett Service Bus-namnområde med kö](service-bus-resource-manager-namespace-queue.md)
> * [Skapa ett Service Bus-namnområde med ämne och prenumeration](service-bus-resource-manager-namespace-topic.md)
> * [Skapa ett Service Bus-namnområde med ämne, prenumeration och regel](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Om du vill söka efter de senaste mallarna går du till galleriet för [Azure snabb starts mallar][Azure Quickstart Templates] och söker efter **Service Bus**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-will-you-deploy"></a>Vad vill du distribuera?

Med den här mallen distribuerar du en Service Bus auktoriseringsregel för en namnrymd och meddelande enhet (i det här fallet en kö).

Den här mallen använder [signaturen för delad åtkomst (SAS)](service-bus-sas.md) för autentisering. SAS gör det möjligt för program att autentisera till Service Bus att använda en åtkomst nyckel som kon figurer ATS i namn området eller i meddelande enheten (kö eller ämne) med vilka vissa rättigheter är associerade. Du kan sedan använda den här nyckeln för att generera en SAS-token som klienter kan använda för att autentisera till Service Bus.

Klicka på följande knapp för att köra distributionen automatiskt:

[![Distribuera till Azure](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parametrar

Med Azure Resource Manager kan du definiera parametrar för värden som du vill ange när mallen distribueras. Mallen innehåller ett avsnitt som kallas `Parameters` som innehåller alla parameter värden. Du bör definiera en parameter för de värden som varierar beroende på vilket projekt du distribuerar eller baserat på den miljö som du distribuerar till. Definiera inte parametrar för värden som alltid ska vara desamma. Varje parametervärde används i mallen för att definiera de resurser som distribueras.

Mallen definierar följande parametrar.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

Namnet på Service Bus namn området som ska skapas.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="namespaceauthorizationrulename"></a>namespaceAuthorizationRuleName

Namnet på auktoriseringsregeln för namn området.

```json
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName

Köns namn i Service Bus namn området.

```json
"serviceBusQueueName": {
"type": "string"
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

Skapar ett standard Service Bus-namnområde av typ **meddelanden** och en Service Bus auktoriseringsregel för namn område och entitet.

```json
"resources": [
        {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[variables('location')]",
            "kind": "Messaging",
            "sku": {
                "name": "Standard",
            },
            "resources": [
                {
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusQueueName')]",
                    "type": "Queues",
                    "dependsOn": [
                        "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('serviceBusQueueName')]"
                    },
                    "resources": [
                        {
                            "apiVersion": "[variables('sbVersion')]",
                            "name": "[parameters('queueAuthorizationRuleName')]",
                            "type": "authorizationRules",
                            "dependsOn": [
                                "[parameters('serviceBusQueueName')]"
                            ],
                            "properties": {
                                "Rights": ["Listen"]
                            }
                        }
                    ]
                }
            ]
        }, {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[variables('namespaceAuthRuleName')]",
            "type": "Microsoft.ServiceBus/namespaces/authorizationRules",
            "dependsOn": ["[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"],
            "location": "[resourceGroup().location]",
            "properties": {
                "Rights": ["Send"]
            }
        }
    ]
```

Information om JSON-syntax och egenskaper finns i [namnrymder](/azure/templates/microsoft.servicebus/namespaces), [köer](/azure/templates/microsoft.servicebus/namespaces/queues)och [AuthorizationRules](/azure/templates/microsoft.servicebus/namespaces/authorizationrules).

## <a name="commands-to-run-deployment"></a>Kommandon för att köra distributionen

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat och distribuerat resurser med Azure Resource Manager kan du läsa mer om hur du hanterar resurserna genom att läsa följande artiklar:

* [Hantera Service Bus med PowerShell](./service-bus-manage-with-ps.md)
* [Hantera Service Bus-resurser med Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)
* [Service Bus, autentisering och auktorisering](service-bus-authentication-and-authorization.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md
[Service Bus auth rule template]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/