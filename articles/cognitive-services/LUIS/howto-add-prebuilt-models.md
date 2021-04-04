---
title: Färdiga modeller för Language Understanding
titleSuffix: Azure Cognitive Services
description: LUIS innehåller en uppsättning förbyggda modeller för att snabbt lägga till vanliga användar scenarier i konversationen.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/17/2020
ms.openlocfilehash: 2080139732d71aa5df9a9d4ad13fcc4545128dff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "91541925"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Lägg till fördefinierade modeller för vanliga användnings scenarier

LUIS innehåller en uppsättning förbyggda modeller för att snabbt lägga till vanliga användar scenarier i konversationen. Det här är ett snabbt och enkelt sätt att lägga till funktioner i ditt samtals klient program utan att behöva utforma modeller för dessa förmågor.

## <a name="add-a-prebuilt-domain"></a>Lägga till en fördefinierad domän

1. Logga in på [Luis-portalen](https://www.luis.ai)och välj din **prenumerations** -och **redigerings resurs** för att se vilka appar som tilldelats den här redigerings resursen.
1. Öppna din app genom att välja namnet på sidan **Mina appar** .

1. Välj **fördefinierade domäner** i det vänstra verktygsfältet.

1. Leta upp den domän som du vill lägga till i appen och välj sedan knappen **Lägg till domän** .

    > [!div class="mx-imgBorder"]
    > ![Lägg till kalender fördefinierad domän](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Lägg till en fördefinierad avsikt

1. Logga in på [Luis-portalen](https://www.luis.ai)och välj din **prenumerations** -och **redigerings resurs** för att se vilka appar som tilldelats den här redigerings resursen.
1. Öppna din app genom att välja namnet på sidan **Mina appar** .

1. På sidan **avsikter** väljer du **Lägg till fördefinierad domän avsikt** från verktygsfältet ovanför listan med intenter.

1. Välj ett avsikts alternativ i popup-dialogrutan.

    > [!div class="mx-imgBorder"]
    > ![Lägg till fördefinierad avsikt](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Klicka på knappen **Slutför** .

## <a name="add-a-prebuilt-entity"></a>Lägg till en fördefinierad entitet
1. Logga in på [Luis-portalen](https://www.luis.ai)och välj din **prenumerations** -och **redigerings resurs** för att se vilka appar som tilldelats den här redigerings resursen.
1. Öppna din app genom att välja namnet på sidan **Mina appar** .
1. Välj **entiteter** på vänster sida.

1. På sidan **entiteter** väljer du **Lägg till fördefinierad entitet**.

1. I dialog rutan **Lägg till fördefinierade entiteter** väljer du den fördefinierade entiteten.

    > [!div class="mx-imgBorder"]
    > ![Dialog rutan Lägg till fördefinierad entitet](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. Välj **Klar**. När entiteten har lagts till behöver du inte träna appen.

## <a name="add-a-prebuilt-domain-entity"></a>Lägg till en fördefinierad domän entitet
1. Logga in på [Luis-portalen](https://www.luis.ai)och välj din **prenumerations** -och **redigerings resurs** för att se vilka appar som tilldelats den här redigerings resursen.
1. Öppna din app genom att välja namnet på sidan **Mina appar** .
1. Välj **entiteter** på vänster sida.

1. På sidan **entiteter** väljer du **Lägg till fördefinierad domän entitet**.

1. I dialog rutan **Lägg till fördefinierade domän modeller** väljer du den fördefinierade domänen entitet.

1. Välj **Klar**. När entiteten har lagts till behöver du inte träna appen.

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publicera för att Visa fördefinierad modell från förutsägelse slut punkt

Det enklaste sättet att visa värdet för en fördefinierad modell är att fråga från den publicerade slut punkten.

## <a name="entities-containing-a-prebuilt-entity-token"></a>Entiteter som innehåller en fördefinierad Entity-token

Om du har en enhet för maskin inlärning som behöver en nödvändig funktion i en fördefinierad entitet, lägger du till en underentitet i enheten för maskin inlärning och lägger sedan till en _nödvändig_ funktion i en fördefinierad entitet.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Bygg en modell från. csv med REST API: er](./luis-tutorial-node-import-utterances-csv.md)
