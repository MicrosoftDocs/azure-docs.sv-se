---
title: Distribuera Azure Cache for Redis med hjälp Azure Resource Manager mall
description: Lär dig hur du använder en Azure Resource Manager mall (ARM-mall) för att distribuera Azure Cache for Redis resurs. Mallar tillhandahålls för vanliga scenarier.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: subject-armqs, devx-track-azurepowershell
ms.date: 08/18/2020
ms.openlocfilehash: 81940d23ebfcc017455974981649023351b6eeb3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835064"
---
# <a name="quickstart-create-an-azure-cache-for-redis-using-an-arm-template"></a>Snabbstart: Skapa en Azure Cache for Redis med hjälp av en ARM-mall

Lär dig hur du skapar Azure Resource Manager mall (ARM-mall) som distribuerar en Azure Cache for Redis. Cacheminnet kan användas med ett befintligt lagringskonto för att behålla diagnostikdata. Du får också lära dig hur du definierar vilka resurser som distribueras och hur du definierar parametrar som anges när distributionen körs. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav. Diagnostikinställningar delas för närvarande för alla cacheminnen i samma region för en prenumeration. Uppdatering av ett cacheminne i regionen påverkar alla andra cacheminnen i regionen.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

* **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* **Ett lagringskonto:** Information om hur du skapar ett finns [i Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md?tabs=azure-portal). Lagringskontot används för diagnostikdata.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-redis-cache/).

:::code language="json" source="~/quickstart-templates/101-redis-cache/azuredeploy.json":::

Följande resurser definieras i mallen:

* [Microsoft.Cache/Redis](/azure/templates/microsoft.cache/redis)
* [Microsoft.Insights/diagnosticsettings](/azure/templates/microsoft.insights/diagnosticsettings)

Resource Manager mallar för den nya [Premium-nivån](cache-overview.md#service-tiers) är också tillgängliga.

* [Skapa en Premium Azure Cache for Redis med klustring](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
* [Skapa Premium Azure Cache for Redis med datapersistence](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
* [Skapa Premium Redis Cache distribueras till en Virtual Network](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)

Om du vill söka efter de senaste mallarna kan du [läsa Azure-snabbstartsmallar](https://azure.microsoft.com/documentation/templates/) och _söka efter Azure Cache for Redis_.

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna mallen.

    [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)
1. Välj eller ange följande värden:

    * **Prenumeration:** Välj en Azure-prenumeration som används för att skapa dataresursen och de andra resurserna.
    * **Resursgrupp:** Välj **Skapa ny för** att skapa en ny resursgrupp eller välj en befintlig resursgrupp.
    * **Plats**: Välj en plats för resursgruppen. Lagringskontot och Redis-cachen måste finnas i samma region. Som standard använder Redis-cachen samma plats som resursgruppen. Ange därför samma plats som lagringskontot.
    * **Redis Cache namn:** ange ett namn för Redis-cachen.
    * **Befintligt diagnostiklagringskonto:** Ange resurs-ID för ett lagringskonto. Syntax: `/subscriptions/&lt;SUBSCRIPTION ID>/resourceGroups/&lt;RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/&lt;STORAGE ACCOUNT NAME>`.

    Använd standardvärdet för resten av inställningarna.
1. välj **Jag godkänner villkoren som anges ovan** och välj **Köp.**

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Öppna Redis-cachen som du skapade.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen när den inte längre behövs, vilket tar bort resurserna i resursgruppen.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar en Azure Resource Manager som distribuerar en Azure Cache for Redis. Information om hur du skapar en Azure Resource Manager-mall som distribuerar en Azure-webbapp med Azure Cache for Redis finns i Skapa en [webbapp plus Azure Cache for Redis med hjälp](./cache-web-app-arm-with-redis-cache-provision.md)av en mall .
