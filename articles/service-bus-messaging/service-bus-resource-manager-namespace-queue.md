---
title: Skapa Azure Service Bus namnrymd och kö med Azure-mall
description: 'Snabb start: skapa en Service Bus namnrymd och en kö med Azure Resource Manager mall'
documentationcenter: .net
author: spelluru
ms.topic: quickstart
ms.custom: subject-armqs
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: 69cffb6000df9d8b058d92231c130ea8a601e6d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88660639"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-arm-template"></a>Snabb start: skapa en Service Bus namnrymd och en kö med en ARM-mall

Den här artikeln visar hur du använder en Azure Resource Manager mall (ARM-mall) som skapar ett Service Bus-namnområde och en kö inom denna namnrymd. Artikeln förklarar hur du anger vilka resurser som distribueras och hur du definierar parametrar som anges när distributionen körs. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/201-servicebus-create-queue).

:::code language="json" source="~/quickstart-templates/201-servicebus-create-queue/azuredeploy.json":::

De resurser som definieras i mallen omfattar:

- [**Microsoft. Service Bus/namnrymder**](/azure/templates/microsoft.servicebus/namespaces)
- [**Microsoft. Service Bus/namnrymder/köer**](/azure/templates/microsoft.servicebus/namespaces/queues)

> [!NOTE]
> Följande ARM-mallar är tillgängliga för hämtning och distribution.
>
> * [Skapa ett Service Bus-namnområde med kö och auktoriseringsregel](service-bus-resource-manager-namespace-auth-rule.md)
> * [Skapa ett Service Bus-namnområde med ämne och prenumeration](service-bus-resource-manager-namespace-topic.md)
> * [Skapa ett namnområde för Service Bus](service-bus-resource-manager-namespace.md)
> * [Skapa ett Service Bus-namnområde med ämne, prenumeration och regel](service-bus-resource-manager-namespace-topic-with-rule.md)

Du kan hitta fler mallar från [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>Distribuera mallen

Med den här mallen distribuerar du en Service Bus namnrymd med en kö.

[Service Bus köer](service-bus-queues-topics-subscriptions.md#queues) ger en eller flera konkurrerande konsumenter först in, först ut-meddelande (FIFO).

Om du vill köra distributionen automatiskt klickar du på följande knapp: skapa en ny resurs grupp för distributionen så att du enkelt kan rensa senare.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="validate-the-deployment"></a>Verifiera distributionen

1. Välj **meddelanden** högst upp för att se status för distributionen. Vänta tills distributionen har slutförts. Välj sedan **gå till resurs grupp** i meddelandet för att gå till sidan för resurs gruppen som innehåller Service Bus namn området. 

    ![Meddelande från distribution](./media/service-bus-resource-manager-namespace-queue/notification.png)
2. Bekräfta att du ser Service Bus namn området i listan över resurser. 

    ![Resurs grupp – namnrymd](./media/service-bus-resource-manager-namespace-queue/resource-group-namespace.png)
3. Välj namn området i listan om du vill se sidan **Service Bus namn område** . 

## <a name="clean-up-resources"></a>Rensa resurser

1. I Azure Portal går du till **resurs** grupp sidan för resurs gruppen.
2. Välj **Ta bort resursgrupp** från verktygsfältet. 
3. Skriv namnet på resurs gruppen och välj **ta bort**. 

    ![Resurs grupp-ta bort](./media/service-bus-resource-manager-namespace-queue/resource-group-delete.png)

## <a name="next-steps"></a>Nästa steg

Se följande avsnitt som visar hur du skapar en auktoriseringsregel för namn området/kön:

[Skapa en auktoriseringsregel för Service Bus för namnrymd och kö med en ARM-mall](service-bus-resource-manager-namespace-auth-rule.md)

Lär dig hur du hanterar dessa resurser genom att läsa följande artiklar:

* [Hantera Service Bus med PowerShell](service-bus-manage-with-ps.md)
* [Hantera Service Bus-resurser med Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md