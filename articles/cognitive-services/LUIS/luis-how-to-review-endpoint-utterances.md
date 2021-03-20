---
title: Granska användarens yttranden-LUIS
titleSuffix: Azure Cognitive Services
description: Granska yttranden som har registrerats av aktiv inlärning och välj avsikt och markera entiteter för Read-World yttranden; acceptera ändringar, träna och publicera.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/08/2020
ms.openlocfilehash: ea2b44d05d25756a16b6b84f0734966b1f579848
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97007610"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>Förbättra LUIS-appen genom att granska slut punkts yttranden

Processen för att granska slut punkts yttranden för korrekta förutsägelser kallas [aktiv inlärning](luis-concept-review-endpoint-utterances.md). Active Learning samlar in slut punkts frågor och väljer användarens slut punkt yttranden att det är osäkert. Du kan granska dessa yttranden för att välja avsikten och markera entiteter för dessa verkliga yttranden. Acceptera ändringarna till dina exempeltalindata samt träna och publicera. LUIS identifierar sedan yttranden mer noggrant.

## <a name="log-user-queries-to-enable-active-learning"></a>Logga användar frågor för att aktivera aktiv inlärning

Om du vill aktivera aktiv inlärning måste du logga användar frågor. Detta åstadkommer du genom att anropa [slut punkts frågan](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) med `log=true` parametern QueryString och värdet.

Använd LUIS-portalen för att skapa rätt slut punkts fråga.

1. Logga in på [Luis-portalen](https://www.luis.ai)och välj din **prenumerations** -och **redigerings resurs** för att se vilka appar som tilldelats den här redigerings resursen.
1. Öppna din app genom att välja namnet på sidan **Mina appar** .
1. Gå till avsnittet **Hantera** och välj sedan **Azure-resurser**.
1. För den tilldelade förutsägelse resursen väljer du **ändra** frågeparametrar.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar länken Ändra frågeparametrar.](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. Växla **Spara loggar** och spara genom att välja **färdig**.

    > [!div class="mx-imgBorder"]
    > ![Använd LUIS-portalen för att spara loggar, vilket krävs för aktiv inlärning.](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     Den här åtgärden ändrar exempel-URL genom att lägga till `log=true` parametern QueryString. Kopiera och Använd fråge-URL: en för den ändrade exemplet när du gör förutsägelse frågor till körnings slut punkten.

## <a name="correct-intent-predictions-to-align-utterances"></a>Korrigera yttranden genom att korrigera avsikts förutsägelser

Varje uttryck har en föreslagen avsikt som visas i kolumnen **justerad avsikt** .

> [!div class="mx-imgBorder"]
> [![Granska slut punkts yttranden som LUIS är osäker på](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

Om du samtycker till detta väljer du kryss rutan. Om du inte stämmer överens med förslaget väljer du rätt avsikt från List rutan justerad avsikt och markerar sedan kryss rutan till höger om det justerade avsikts alternativet. När du har markerat kryss rutan flyttas uttryck till avsikten och tas bort från listan **yttranden för gransknings slut punkt** .

> [!TIP]
> Det är viktigt att gå till sidan med information om avsikt att granska och korrigera enhets förutsägelserna från alla exempel yttranden från **yttranden för gransknings slut punkt** .

## <a name="delete-utterance"></a>Ta bort uttryck

Varje uttryck kan tas bort från gransknings listan. När du har tagit bort den visas den inte i listan igen. Detta gäller även om användaren anger samma uttryck från slut punkten.

Om du är osäker på om du ska ta bort uttryck, antingen flyttar du den till none-avsikten eller skapar en ny avsikt, till exempel `miscellaneous` och flyttar uttryck till den avsikten.

## <a name="disable-active-learning"></a>Inaktivera aktiv inlärning

Du inaktiverar aktiv inlärning genom att inte logga användar frågor. Detta åstadkommer du genom att ställa in [slut punkts frågan](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) med `log=false` parametern QueryString och värdet eller inte använda QueryString-värdet eftersom standardvärdet är false.

## <a name="next-steps"></a>Nästa steg

Om du vill testa hur prestanda förbättras när du har märkt föreslagen yttranden kan du komma åt test konsolen genom att välja **test** i den övre panelen. Anvisningar om hur du testar din app med hjälp av test konsolen finns i [träna och testa din app](luis-interactive-test.md).
