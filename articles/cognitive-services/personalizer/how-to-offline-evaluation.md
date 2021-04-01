---
title: Så här gör du en offline-utvärdering – Personanpassare
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du använder offline-utvärdering för att mäta appens effektivitet och analysera din inlärnings slinga.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: a473085f9c94ca42a75d01b342d60cc33836b096
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "88244847"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>Analysera din inlärnings slinga med en offline-utvärdering

Lär dig hur du slutför en offline-utvärdering och förstår resultatet.

Med offline-utvärdering kan du mäta hur effektiv Personanpassare jämförs med programmets standard beteende, se vilka funktioner som bidrar mest till anpassning och identifiera nya maskin inlärnings värden automatiskt.

Läs om [offline-utvärderingar](concepts-offline-evaluation.md) för mer information.

## <a name="prerequisites"></a>Förutsättningar

* En konfigurerad personanpassa slinga
* Den personliga slingan måste ha en representativ mängd data – som en ungefärligt rekommenderar vi minst 50 000 händelser i sina loggar för meningsfulla utvärderings resultat. Om du vill kan du också ha tidigare exporterade _Learning-principfiler_ som du kan jämföra och testa i samma utvärdering.

## <a name="run-an-offline-evaluation"></a>Köra en offline-utvärdering

1. Leta upp din personanpassa resurs i [Azure Portal](https://azure.microsoft.com/free/cognitive-services).
1. I Azure Portal går du till avsnittet **utvärderingar** och väljer **Skapa utvärdering**.
    ![I Azure Portal går du till avsnittet * * utvärdering * * och väljer * * Skapa utvärdering * *.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Konfigurera följande värden:

    * Ett namn på utvärderingen.
    * Start-och slutdatum – dessa är datum som anger det data intervall som ska användas i utvärderingen. Dessa data måste finnas i loggarna, enligt vad som anges i [datakvarhållning](how-to-settings.md) svärdet.
    * Optimerings identifieringen är inställd på **Ja**.

    > [!div class="mx-imgBorder"]
    > ![Välj inställningar för utvärdering av offline](./media/offline-evaluation/create-an-evaluation-form.png)

1. Starta utvärderingen genom att välja **OK**.

## <a name="review-the-evaluation-results"></a>Granska utvärderings resultaten

Utvärderingen kan ta lång tid att köra, beroende på mängden data som ska bearbetas, antalet inlärnings principer som ska jämföras och om en optimering har begärts.

När du är klar kan du välja utvärderingen i listan över utvärderingar och sedan välja **Jämför poängen för ditt program med andra potentiella inlärnings inställningar**. Välj den här funktionen om du vill se hur din aktuella utbildnings princip fungerar jämfört med en ny princip.

1. Granska prestanda för [utbildnings principerna](concepts-offline-evaluation.md#discovering-the-optimized-learning-policy).

    > [!div class="mx-imgBorder"]
    > [![Granska utvärderings resultat](./media/offline-evaluation/evaluation-results.png)](./media/offline-evaluation/evaluation-results.png#lightbox)

1. Välj **tillämpa** för att tillämpa principen som förbättrar modellen för dina data.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [hur offline-utvärdering fungerar](concepts-offline-evaluation.md).
