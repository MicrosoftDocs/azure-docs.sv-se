---
title: Använd en Azure Resource Manager-mall för att distribuera en privat slut punkt för en webbapp
description: Lär dig hur du använder ARM-mallen för att distribuera en privat slut punkt för din webbapp.
author: ericgre
ms.assetid: 49e460d0-7759-4ceb-b5a4-f1357e4fde56
ms.topic: sample
ms.date: 07/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: acf7198477ce0c55f9321fd3f8e57294a60c0961
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89652025"
---
# <a name="create-an-app-service-app-and-deploy-a-private-endpoint-by-using-an-azure-resource-manager-template"></a>Skapa en App Service-app och distribuera en privat slut punkt med hjälp av en Azure Resource Manager mall

I den här snabb starten använder du en Azure Resource Manager ARM-mall (ARM) för att skapa en webbapp och exponera den med en privat slut punkt.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisite"></a>Förutsättning

Du behöver ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-private-endpoint"></a>Skapa en privat slutpunkt

Den här mallen skapar en privat slut punkt för en Azure-webbapp.

### <a name="review-the-template"></a>Granska mallen

:::code language="json" source="~/quickstart-templates/101-private-endpoint-webapp/azuredeploy.json" :::

### <a name="deploy-the-template"></a>Distribuera mallen

Så här distribuerar du Azure Resource Manager-mallen till Azure:

1. Om du vill logga in på Azure och öppna mallen väljer du den här länken:  [distribuera till Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-webapp%2Fazuredeploy.json). Mallen skapar det virtuella nätverket, webbappen, den privata slut punkten och den privata DNS-zonen.
2. Välj eller skapa en resurs grupp.
3. Ange namnet på din webbapp, Azure App Service plan och privat slut punkt.
5. Läs instruktionen om allmänna villkor. Om du samtycker väljer du **Jag accepterar villkoren som anges ovan**  >  **köpet**. Det kan ta flera minuter att slutföra distributionen.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med den privata slut punkten tar du bort resurs gruppen. Den privata slut punkten och alla relaterade resurser tas bort.

Anropa cmdleten om du vill ta bort resurs gruppen `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Nästa steg

- Du hittar fler Azure Resource Manager mallar för Azure App Service webbappar i arm- [mal exemplen](../samples-resource-manager-templates.md).
