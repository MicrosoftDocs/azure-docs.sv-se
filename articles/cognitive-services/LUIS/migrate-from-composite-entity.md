---
title: Uppgradera sammansatt entitet – LUIS
description: Uppgradera den sammansatta entiteten till enheten för maskin inlärning med uppgraderings processen i LUIS-portalen.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 46e9ece70d9f980065c719ee1205eb46591b45c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "95025250"
---
# <a name="upgrade-composite-entity-to-machine-learning-entity"></a>Uppgradera sammansatt entitet till enhet för maskin inlärning

Uppgradera den sammansatta entiteten till en enhet för maskin inlärning för att bygga en entitet som tar emot mer kompletta förutsägelser med bättre dekomposition för fel sökning av entiteten.

## <a name="current-version-model-restrictions"></a>Aktuella versions modell begränsningar

Uppgraderings processen skapar enheter för maskin inlärning, baserat på de befintliga sammansatta entiteter som finns i din app, i en ny version av din app. Detta omfattar underordnade och roller för sammansatta entiteter. Processen växlar också etiketterna i yttranden till exempel för att använda den nya entiteten.

## <a name="upgrade-process"></a>Uppgraderings process

Uppgraderings processen:
* Skapar ny maskin inlärnings enhet för varje sammansatt entitet.
* Underordnade entiteter:
    * Om den underordnade entiteten endast används i den sammansatta entiteten läggs den bara till i enheten för maskin inlärning.
    * Om den underordnade entiteten används i sammansatt form _och_ som en separat entitet (med etikett i exemplet yttranden), kommer den att läggas till i versionen som en entitet och som en underordnad enhet till den nya enheten för maskin inlärning.
    * Om den underordnade entiteten använder en roll, kommer varje roll att konverteras till en underordnad till en underordnad till samma namn.
    * Om den underordnade entiteten är en icke-arbetsentitet (reguljärt uttryck, listen het eller en fördefinierad entitet) skapas en ny underordnad enhet med samma namn och den nya underentiteten har en funktion som använder entiteten icke-maskin inlärning med den nödvändiga funktionen tillagd.
* Namnen bevaras men måste vara unika på samma underentiteter/samma nivå. Referera till [unika namngivnings gränser](./luis-limits.md#name-uniqueness).
* Etiketter i exempel yttranden växlas till den nya enheten för maskin inlärning med underentiteter.

Använd följande diagram för att förstå hur din modell ändras:

|Gammalt objekt|Nytt objekt|Kommentarer|
|--|--|--|
|Sammansatt entitet|enhet för maskin inlärning med struktur|Båda objekten är överordnade objekt.|
|Sammansatt entitets underordnade entitet är **enkel entitet**|underentitet|Båda objekten är underordnade objekt.|
|Sammansatt entitets underordnade entitet är en **inbyggd entitet** , till exempel Number|underentiteten med namnet på den fördefinierade entiteten, till exempel Number, och underentiteten har _funktionen_ för fördefinierad nummer entitet med begränsnings alternativet inställt på _True_.|underentiteten innehåller en funktion med begränsning på underenhets nivå.|
|Sammansatt entitets underordnade entitet är en **inbyggd entitet** , till exempel tal, och en fördefinierad entitet har en **roll**|underentitet med namnet på rollen och underentiteten har funktionen för fördefinierad nummer entitet med begränsnings alternativ inställt på True.|underentiteten innehåller en funktion med begränsning på underenhets nivå.|
|Roll|underentitet|Roll namnet blir namnet på underentiteten. Underentiteten är en direkt underordnad för enheten för maskin inlärning.|

## <a name="begin-upgrade-process"></a>Påbörja uppgraderings processen

Innan du uppdaterar bör du se till att:

* Ändra versioner om du inte har rätt version för att uppgradera


1. Påbörja uppgraderings processen från meddelandet eller vänta till nästa schemalagda prompt.

    > [!div class="mx-imgBorder"]
    > ![Påbörja uppgradering från meddelanden](./media/update-composite-entity/notification-begin-update.png)

1. I popup-fönstret väljer du **Uppgradera nu**.

1. Granska **vad som händer när du uppgraderar** informationen och välj sedan **Fortsätt**.

1. Välj de sammansatta entiteter som ska uppgraderas i listan och välj sedan **Fortsätt**.

1. Du kan flytta eventuella intränade ändringar från den aktuella versionen till den uppgraderade versionen genom att markera kryss rutan.

1. Välj **Fortsätt** för att påbörja uppgraderings processen.

1. Förlopps indikatorn visar status för uppgraderings processen.

1. När processen är färdig finns du med i en ny utbildad version med de nya enheterna för maskin inlärning. Välj **testa dina nya entiteter** för att se den nya entiteten.

    Om uppgraderingen eller träningen misslyckades för den nya entiteten ger ett meddelande mer information.

1. På sidan entitets lista markeras de nya entiteterna med **ny** bredvid typnamn.

## <a name="next-steps"></a>Nästa steg

* [Författare och medarbetare](luis-how-to-collaborate.md)