---
title: Skapa ett Azure Attestation certifikat med hjälp Azure Resource Manager mall
description: Lär dig hur du skapar Azure Attestation certifikat med hjälp Azure Resource Manager mall.
services: azure-resource-manager
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.author: mbaldwin
ms.date: 10/16/2020
ms.openlocfilehash: 7d70f9ebd071d6699412f56e9dca1abcebb38105
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834254"
---
# <a name="quickstart-create-an-azure-attestation-provider-with-an-arm-template"></a>Snabbstart: Skapa en Azure Attestation provider med en ARM-mall

[Microsoft Azure attestering](overview.md) är en lösning för attestera betrodda körningsmiljöer (TEE). Den här snabbstarten fokuserar på hur du distribuerar en Azure Resource Manager (ARM-mall) för att skapa en Microsoft Azure attestation-princip.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-attestation-provider-create).

:::code language="json" source="~/quickstart-templates/101-attestation-provider-create/azuredeploy.json":::

Azure-resurser som definieras i mallen:

- Microsoft.Attestation/attestationProviders

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna mallen.

    [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

1. Välj eller ange följande värden.

    Om det inte anges använder du standardvärdet för att skapa attestationsprovidern.

    - **Attestation providernamn:** Välj ett namn för din Azure Attestation providern.
    - **Plats:** Välj en plats. Välj till exempel **USA, centrala**.
    - **Taggar:** Välj en plats. Välj till exempel **USA, centrala**.

1. Välj **Köp**. När attestationsresursen har distribuerats får du ett meddelande.

Azure-portalen används för att distribuera mallen. Förutom de Azure Portal kan du även använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributionsmetoder finns i [Distribuera mallar.](../azure-resource-manager/templates/deploy-powershell.md)

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Du kan använda Azure Portal för att kontrollera attestationsresursen.

## <a name="clean-up-resources"></a>Rensa resurser

Andra Azure Attestation bygger på den här snabbstarten. Om du planerar att fortsätta med efterföljande snabbstarter och självstudier kan du lämna kvar de här resurserna.

Ta bort resursgruppen när den inte längre behövs, vilket tar bort attestationsresursen. Så här tar du bort resursgruppen med hjälp av Azure CLI eller Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en attestationsresurs med hjälp av en ARM-mall och verifierade distributionen. Mer information om Azure Attestation finns i [Översikt över Azure Attestation](overview.md).
