---
title: Skapa LUIS-resurs
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/20/2020
ms.author: aahi
ms.openlocfilehash: ee7fd384a198c5eff672b14b6cb479aac26cfe54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "95972529"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-the-azure-portal"></a>Skapa LUIS-resurser i Azure Portal

1. Använd [den här länken](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) för att börja skapa Luis-resurser i Azure Portal.

1. Ange alla nödvändiga inställningar:

    |Name|Syfte|
    |--|--|
    |Prenumeration | Den prenumeration som ska faktureras för resursen.|
    |Resursgrupp| Ett namn på en anpassad resurs grupp som du väljer eller skapar. Med resurs grupper kan du gruppera Azure-resurser för åtkomst och hantering.|
    |Name| Ett anpassat namn som du väljer. Den används som din anpassade under domän för dina redigerings-och förutsägelse slut punkt frågor.|
    |Redigerings plats|Den region som är associerad med din modell.|
    |Redigera pris nivå|Fastställer högsta antal transaktioner per sekund och månad.|
    |Förutsägelse plats|Den region som är kopplad till den publicerade slut punkts körningen för förutsägelse.|
    |Pris nivå för förutsägelse|Fastställer högsta antal transaktioner per sekund och månad.|

    > [!div class="mx-imgBorder"]
    > [![Skärm bild som visar fliken grundläggande under skapa.](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. Välj **Granska + skapa** och vänta tills resursen har skapats.
1. När båda resurserna har skapats väljer du den nya redigerings resursen fortfarande i Azure Portal. Välj sedan **nycklar och slut punkt** för att hämta **slut punktens URL** och **nyckel** för redigering program mässigt.

> [!TIP]
> Om du vill använda resurserna går du till LUIS-portalen och [tilldelar resurserna](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps).
