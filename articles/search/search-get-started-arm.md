---
title: Använd en Azure Resource Manager-mall för att distribuera tjänsten
titleSuffix: Azure Cognitive Search
description: Du kan snabbt distribuera en Azure Kognitiv sökning service-instans med hjälp av Azure Resource Manager-mallen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 11/17/2020
ms.openlocfilehash: 88a081f1ebbda52653302384c06403e223128c60
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668392"
---
# <a name="quickstart-deploy-cognitive-search-using-an-arm-template"></a>Snabb start: Distribuera Kognitiv sökning med en ARM-mall

Den här artikeln vägleder dig genom processen för att använda en Azure Resource Manager-mall (ARM-mall) för att distribuera en Azure Kognitiv sökning-resurs i Azure Portal.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-azure-search-create/).

:::code language="json" source="~/quickstart-templates/101-azure-search-create/azuredeploy.json":::

Den Azure-resurs som definierats i den här mallen:

- [Microsoft. search/searchServices](/azure/templates/Microsoft.Search/searchServices): skapa en Azure kognitiv sökning-tjänst

## <a name="deploy-the-template"></a>Distribuera mallen

Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar en Azure Kognitiv sökning-resurs.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

Portalen visar ett formulär som gör att du enkelt kan ange parameter värden. Vissa parametrar fylls i automatiskt med standardvärdena från mallen. Du måste ange din prenumeration, resurs grupp, plats och tjänst namn. Om du vill använda Cognitive Services i en pipeline för [AI-anrikning](cognitive-search-concept-intro.md) , till exempel för att analysera binära bildfiler för text, väljer du en plats som erbjuder både Kognitiv sökning och Cognitive Services. Båda tjänsterna måste finnas i samma region för AI-arbetsbelastningar. När du har fyllt i formuläret måste du godkänna de allmänna villkoren och sedan välja knappen köp för att slutföra distributionen.

> [!div class="mx-imgBorder"]
> ![Azure Portal visning av mall](./media/search-get-started-arm/arm-portalscrnsht.png)

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

När distributionen är klar kan du komma åt den nya resurs gruppen och den nya Sök tjänsten i portalen.

## <a name="clean-up-resources"></a>Rensa resurser

Andra Kognitiv sökning snabb starter och självstudier som bygger på den här snabb starten. Om du planerar att fortsätta att arbeta med efterföljande snabb starter och självstudier, kanske du vill lämna den här resursen på plats. När de inte längre behövs kan du ta bort resurs gruppen, som tar bort Kognitiv sökning tjänsten och relaterade resurser.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en Kognitiv sökning tjänst med en ARM-mall och validerade distributionen. Om du vill veta mer om Kognitiv sökning och Azure Resource Manager fortsätter du till artiklarna nedan.

- Läs en [Översikt över Azure kognitiv sökning](search-what-is-azure-search.md).
- [Skapa ett index](search-get-started-portal.md) för Sök tjänsten.
- [Skapa en demo-app](search-create-app-portal.md) med hjälp av Portal guiden.
- [Skapa en färdigheter](cognitive-search-quickstart-blob.md) för att extrahera information från dina data.
