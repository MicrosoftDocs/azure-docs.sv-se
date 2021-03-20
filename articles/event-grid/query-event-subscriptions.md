---
title: Fråga Azure Event Grid prenumerationer
description: Den här artikeln beskriver hur du visar Event Grid prenumerationer i din Azure-prenumeration. Du anger olika parametrar utifrån typ av prenumeration.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 3d700f543bc5e3c7add2a346c10acf975e1c2462
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86120457"
---
# <a name="query-event-grid-subscriptions"></a>Fråga Event Grid prenumerationer 

Den här artikeln beskriver hur du visar Event Grid prenumerationer i din Azure-prenumeration. När du frågar befintliga Event Grid prenumerationer är det viktigt att förstå de olika typerna av prenumerationer. Du anger olika parametrar beroende på vilken typ av prenumeration du vill hämta.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-groups-and-azure-subscriptions"></a>Resurs grupper och Azure-prenumerationer

Azure-prenumerationer och resurs grupper är inte Azure-resurser. Därför har Event Grid-prenumerationer till resurs grupper eller Azure-prenumerationer inte samma egenskaper som Event Grid-prenumerationer på Azure-resurser. Event Grid-prenumerationer till resurs grupper eller Azure-prenumerationer betraktas som globala.

Om du vill hämta Event Grid-prenumerationer för en Azure-prenumeration och dess resurs grupper behöver du inte ange några parametrar. Kontrol lera att du har valt den Azure-prenumeration som du vill fråga. I följande exempel får du inga event Grid-prenumerationer för anpassade ämnen eller Azure-resurser.

Om du använder Azure CLI använder du:

```azurecli-interactive
az account set -s "My Azure Subscription"
az eventgrid event-subscription list
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Set-AzContext -Subscription "My Azure Subscription"
Get-AzEventGridSubscription
```

Om du vill hämta Event Grid-prenumerationer för en Azure-prenumeration anger du ämnes typen **Microsoft. Resources. Subscriptions**.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions" --location global
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

Om du vill hämta Event Grid-prenumerationer för alla resurs grupper i en Azure-prenumeration anger du ämnes typen **Microsoft. Resources. ResourceGroups**.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups" --location global
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

Om du vill hämta Event Grid-prenumerationer för en angiven resurs grupp anger du namnet på resurs gruppen som en parameter.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --resource-group myResourceGroup --location global
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>Anpassade ämnen och Azure-resurser

Anpassade Event Grid-ämnen är Azure-resurser. Därför kan du fråga Event Grid-prenumerationer efter anpassade ämnen och andra resurser, t. ex. Blob Storage-konto, på samma sätt. Om du vill hämta Event Grid-prenumerationer för anpassade ämnen måste du ange parametrar som identifierar resursen eller identifierar platsen för resursen. Det går inte att utföra stora frågor om Event Grid-prenumerationer för resurser i din Azure-prenumeration.

Om du vill hämta Event Grid-prenumerationer för anpassade ämnen och andra resurser på en plats anger du namnet på platsen.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzEventGridSubscription -Location westus2
```

Om du vill få prenumerationer på anpassade ämnen för en plats anger du platsen och ämnes typen **Microsoft. EventGrid. ämnen**.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

Om du vill hämta prenumerationer på lagrings konton för en plats anger du platsen och ämnes typen **Microsoft. Storage. StorageAccounts**.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location westus2
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location westus2
```

Om du vill hämta Event Grid-prenumerationer för ett anpassat ämne anger du namnet på det anpassade ämnet och namnet på dess resurs grupp.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

Ange resurs-ID för att hämta Event Grid-prenumerationer för en viss resurs.

Om du använder Azure CLI använder du:

```azurecli-interactive
resourceid=$(az resource show -n mystorage -g myResourceGroup --resource-type "Microsoft.Storage/storageaccounts" --query id --output tsv)
az eventgrid event-subscription list --resource-id $resourceid
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
$resourceid = (Get-AzResource -Name mystorage -ResourceGroupName myResourceGroup).ResourceId
Get-AzEventGridSubscription -ResourceId $resourceid
```

## <a name="next-steps"></a>Nästa steg

* Om du vill ha information om händelse leverans och försök kan du [Event Grid meddelande leverans och försöka igen](delivery-and-retry.md).
* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Information om hur du snabbt kommer igång med Event Grid finns i [skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
