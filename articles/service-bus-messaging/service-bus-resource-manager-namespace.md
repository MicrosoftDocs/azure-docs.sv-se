---
title: Skapa ett Azure Service Bus-namnområde med mall
description: Använd Azure Resource Manager mall för att skapa ett namn område för Service Bus meddelanden
documentationcenter: .net
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: 1b7aafca331170100ce99c084a11c96c97df7781
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88067400"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Skapa ett Service Bus-namnområde med hjälp av en Azure Resource Manager mall

Lär dig hur du distribuerar en Azure Resource Manager-mall för att skapa ett Service Bus-namnområde. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav. Mer information om hur du skapar mallar finns i [Azure Resource Manager-dokumentationen](../azure-resource-manager/index.yml).

Följande mallar är också tillgängliga för att skapa Service Bus namnrum:

* [Skapa ett Service Bus-namnområde med kö](./service-bus-resource-manager-namespace-queue.md)
* [Skapa ett Service Bus-namnområde med ämne och prenumeration](./service-bus-resource-manager-namespace-topic.md)
* [Skapa ett Service Bus-namnområde med kö och auktoriseringsregel](./service-bus-resource-manager-namespace-auth-rule.md)
* [Skapa ett Service Bus-namnområde med ämne, prenumeration och regel](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-a-service-bus-namespace"></a>Skapa ett Service Bus-namnområde

I den här snabb starten använder du en [befintlig Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json) från [Azure snabb starts-mallar](https://azure.microsoft.com/resources/templates/):

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

Mer information om mallar finns i [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular).

Så här skapar du ett Service Bus-namnområde genom att distribuera en mall:

1. Välj **prova** med följande kodblock och följ sedan anvisningarna för att logga in på Azure Cloud Shell.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    Resurs gruppens namn är namnet på Service Bus-namnområdet med **RG** tillagt.

2. Välj **Kopiera** för att kopiera PowerShell-skriptet.
3. Högerklicka på Shell-konsolen och välj sedan **Klistra in**.

Det tar en stund att skapa en Event Hub.

## <a name="verify-the-deployment"></a>Verifiera distributionen

Om du vill se det distribuerade Service Bus-namnområdet kan du antingen öppna resurs gruppen från Azure Portal eller använda följande Azure PowerShell-skript. Om Cloud Shell fortfarande är öppet behöver du inte kopiera/köra den första och andra raden i följande skript.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

Azure PowerShell används för att distribuera mallen i den här självstudien. För andra metoder för mall distribution, se:

* Med [hjälp av Azure Portal](../azure-resource-manager/templates/deploy-portal.md).
* Med [hjälp av Azure CLI](../azure-resource-manager/templates/deploy-cli.md).
* Med [hjälp av REST API](../azure-resource-manager/templates/deploy-rest.md).

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen. Om Cloud Shell fortfarande är öppet behöver du inte kopiera/köra den första och andra raden i följande skript.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat ett Service Bus-namnområde. Se de andra snabb starterna för att lära dig hur du skapar köer, ämnen/prenumerationer och använder dem:

* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Kom igång med Service Bus-ämnen](service-bus-dotnet-how-to-use-topics-subscriptions.md)