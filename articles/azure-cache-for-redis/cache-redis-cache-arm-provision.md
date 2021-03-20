---
title: Distribuera Azure cache för Redis med hjälp av Azure Resource Manager mall
description: Lär dig hur du använder en Azure Resource Manager mall (ARM-mall) för att distribuera en Azure-cache för Redis-resursen. Mallar finns för vanliga scenarier.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: subject-armqs
ms.date: 08/18/2020
ms.openlocfilehash: 8bd9a45ec7c43d9338dec184afd784d2b163c410
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92735972"
---
# <a name="quickstart-create-an-azure-cache-for-redis-using-an-arm-template"></a>Snabb start: skapa en Azure-cache för Redis med en ARM-mall

Lär dig hur du skapar en Azure Resource Manager mall (ARM-mall) som distribuerar en Azure-cache för Redis. Cachen kan användas med ett befintligt lagrings konto för att behålla diagnostikdata. Du lär dig också hur du definierar vilka resurser som distribueras och hur du definierar parametrar som anges när distributionen körs. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav. Diagnostiska inställningar delas för närvarande för alla cacheminnen i samma region för en prenumeration. Att uppdatera en cache i regionen påverkar alla andra cacheminnen i regionen.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

* **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* **Ett lagrings konto**: om du vill skapa ett, se [skapa ett Azure Storage konto](../storage/common/storage-account-create.md?tabs=azure-portal). Lagrings kontot används för diagnostikdata.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-redis-cache/).

:::code language="json" source="~/quickstart-templates/101-redis-cache/azuredeploy.json":::

Följande resurser definieras i mallen:

* [Microsoft. cache/Redis](/azure/templates/microsoft.cache/redis)
* [Microsoft. Insights/diagnosticsettings](/azure/templates/microsoft.insights/diagnosticsettings)

Resource Manager-mallar för den nya [Premium-nivån](cache-overview.md#service-tiers) är också tillgängliga.

* [Skapa en Premium Azure-cache för Redis med klustring](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
* [Skapa Premium Azure-cache för Redis med data persistence](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
* [Skapa Premium-Redis Cache distribueras till en Virtual Network](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)

Du kan söka efter de senaste mallarna i [Azure snabb starts mallar](https://azure.microsoft.com/documentation/templates/) och söka efter _Azure cache för Redis_.

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna mallen.

    [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)
1. Välj eller ange följande värden:

    * **Prenumeration**: Välj en Azure-prenumeration som används för att skapa data resursen och de andra resurserna.
    * **Resurs grupp**: Välj **Skapa ny** för att skapa en ny resurs grupp eller Välj en befintlig resurs grupp.
    * **Plats**: Välj en plats för resursgruppen. Lagrings kontot och Redis-cachen måste vara i samma region. Som standard använder Redis-cachen samma plats som resurs gruppen. Ange därför samma plats som lagrings kontot.
    * **Redis cache namn**: Ange ett namn för Redis-cachen.
    * **Befintligt diagnostik-lagrings konto**: Ange resurs-ID för ett lagrings konto. Syntax: `/subscriptions/&lt;SUBSCRIPTION ID>/resourceGroups/&lt;RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/&lt;STORAGE ACCOUNT NAME>`.

    Använd standardvärdet för resten av inställningarna.
1. Välj **Jag accepterar de villkor som anges ovan** och välj **köp**.

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Öppna Redis-cachen som du skapade.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort resurs gruppen, som tar bort resurserna i resurs gruppen.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien får du lära dig hur du skapar en Azure Resource Manager-mall som distribuerar ett Azure-cacheminne för Redis. Information om hur du skapar en Azure Resource Manager mall som distribuerar en Azure-webbapp med Azure cache för Redis finns i [skapa en webbapp plus Azure cache för Redis med hjälp av en mall](./cache-web-app-arm-with-redis-cache-provision.md).
