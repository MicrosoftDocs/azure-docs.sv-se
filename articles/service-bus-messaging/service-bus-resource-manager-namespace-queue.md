---
title: Skapa Azure Service Bus namnområde och kö med hjälp av En Azure-mall
description: 'Snabbstart: Skapa en Service Bus namnrymd och en kö med hjälp Azure Resource Manager mall'
documentationcenter: .net
author: spelluru
ms.author: spelluru
ms.date: 06/23/2020
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 6577abf1900a166087ff63c331d7c0390fab1342
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535022"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-arm-template"></a>Snabbstart: Skapa en Service Bus namnrymd och en kö med hjälp av en ARM-mall

Den här artikeln visar hur du använder en Azure Resource Manager (ARM-mall) som skapar ett Service Bus namnområde och en kö inom det namnområdet. Artikeln förklarar hur du anger vilka resurser som distribueras och hur du definierar parametrar som anges när distributionen körs. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/201-servicebus-create-queue).

:::code language="json" source="~/quickstart-templates/201-servicebus-create-queue/azuredeploy.json":::

De resurser som definieras i mallen är:

- [**Microsoft.ServiceBus/namespaces**](/azure/templates/microsoft.servicebus/namespaces)
- [**Microsoft.ServiceBus/namespaces/queues**](/azure/templates/microsoft.servicebus/namespaces/queues)

> [!NOTE]
> Följande ARM-mallar är tillgängliga för nedladdning och distribution.
>
> * [Skapa ett Service Bus med kö och auktoriseringsregel](service-bus-resource-manager-namespace-auth-rule.md)
> * [Skapa ett Service Bus-namnområde med ämne och prenumeration](service-bus-resource-manager-namespace-topic.md)
> * [Skapa ett namnområde för Service Bus](service-bus-resource-manager-namespace.md)
> * [Skapa en Service Bus med ämne, prenumeration och regel](service-bus-resource-manager-namespace-topic-with-rule.md)

Du hittar fler mallar i [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>Distribuera mallen

Med den här mallen distribuerar du Service Bus en namnrymd med en kö.

[Service Bus köer erbjuder](service-bus-queues-topics-subscriptions.md#queues) FIFO-meddelandeleverans (First In, First Out) till en eller flera konkurrerande konsumenter.

Om du vill köra distributionen automatiskt klickar du på följande knapp: Skapa en ny resursgrupp för distributionen så att du enkelt kan rensa senare.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="validate-the-deployment"></a>Verifiera distributionen

1. Välj **Meddelanden** längst upp för att se status för distributionen. Vänta tills distributionen lyckas. Välj sedan **Gå till resursgrupp i** aviseringsmeddelandet för att gå till sidan för den resursgrupp som innehåller Service Bus namnområdet. 

    ![Meddelande från distribution](./media/service-bus-resource-manager-namespace-queue/notification.png)
2. Bekräfta att du ser Service Bus namnområdet i listan över resurser. 

    ![Resursgrupp – namnområde](./media/service-bus-resource-manager-namespace-queue/resource-group-namespace.png)
3. Välj namnområdet i listan för att se Service Bus **namnområde.** 

## <a name="clean-up-resources"></a>Rensa resurser

1. I Azure Portal du till sidan **Resursgrupp** för din resursgrupp.
2. Välj **Ta bort resursgrupp** från verktygsfältet. 
3. Skriv namnet på resursgruppen och välj Ta **bort.** 

    ![Resursgrupp – ta bort](./media/service-bus-resource-manager-namespace-queue/resource-group-delete.png)

## <a name="next-steps"></a>Nästa steg

Se följande avsnitt som visar hur du skapar en auktoriseringsregel för namnområdet/kön:

[Skapa en Service Bus auktoriseringsregel för namnområde och kö med hjälp av en ARM-mall](service-bus-resource-manager-namespace-auth-rule.md)

Lär dig hur du hanterar dessa resurser genom att läsa följande artiklar:

* [Hantera Service Bus med PowerShell](service-bus-manage-with-ps.md)
* [Hantera Service Bus resurser med Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md
