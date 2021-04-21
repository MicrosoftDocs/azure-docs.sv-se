---
title: Använda en Azure Resource Manager mall för att distribuera en privat slutpunkt för en webbapp
description: Lär dig hur du använder ARM-mallen för att distribuera en privat slutpunkt för din webbapp.
author: ericgre
ms.assetid: 49e460d0-7759-4ceb-b5a4-f1357e4fde56
ms.topic: sample
ms.date: 07/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: df71c1a92840ae0e7fa263e2ababcf5b3e059789
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832544"
---
# <a name="create-an-app-service-app-and-deploy-a-private-endpoint-by-using-an-azure-resource-manager-template"></a>Skapa en App Service app och distribuera en privat slutpunkt med hjälp av en Azure Resource Manager mall

I den här snabbstarten använder du en arm Azure Resource Manager mall för att skapa en webbapp och exponera den med en privat slutpunkt.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisite"></a>Förutsättning

Du behöver ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-private-endpoint"></a>Skapa en privat slutpunkt

Den här mallen skapar en privat slutpunkt för en Azure-webbapp.

### <a name="review-the-template"></a>Granska mallen

:::code language="json" source="~/quickstart-templates/101-private-endpoint-webapp/azuredeploy.json" :::

### <a name="deploy-the-template"></a>Distribuera mallen

Så här distribuerar du Azure Resource Manager till Azure:

1. Om du vill logga in på Azure och öppna mallen väljer du den här länken:  [Distribuera till Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-webapp%2Fazuredeploy.json). Mallen skapar det virtuella nätverket, webbappen, den privata slutpunkten och den privata DNS-zonen.
2. Välj eller skapa din resursgrupp.
3. Ange namnet på webbappen, Azure App Service plan och privat slutpunkt.
5. Läs instruktionen om allmänna villkor. Om du godkänner väljer **du Jag godkänner villkoren som anges ovan**  >  **Köp.** Distributionen kan ta flera minuter att slutföra.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med den privata slutpunkten tar du bort resursgruppen. Detta tar bort den privata slutpunkten och alla relaterade resurser.

Om du vill ta bort resursgruppen anropar du `Remove-AzResourceGroup` cmdleten :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Nästa steg

- Du hittar fler Azure Resource Manager för Azure App Service-webbappar i [ARM-mallexempel](../samples-resource-manager-templates.md).
