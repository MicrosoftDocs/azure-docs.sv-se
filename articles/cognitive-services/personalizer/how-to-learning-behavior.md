---
title: Konfigurera inlärnings beteende
description: Lärlings läget ger dig förtroende för tjänsten för anpassnings tjänsten och dess maskin inlärnings funktioner, och tillhandahåller mått för att tjänsten ska skicka information som kan inlärts från – utan att riskera online-trafik.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 05/01/2020
ms.openlocfilehash: 57a03b107678f83200b11f408784f6455cbceffd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "94579299"
---
# <a name="configure-the-personalizer-learning-behavior"></a>Konfigurera inlärnings beteendet för personanpassa

[Lärlings läget](concept-apprentice-mode.md) ger dig förtroende och tillförlitlighet i anpassnings tjänsten och dess funktioner för maskin inlärning, och garanterar att tjänsten har skickat information som kan inlärts från – utan att riskera online-trafik.

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="configure-apprentice-mode"></a>Konfigurera lärlings läge

1. Logga in på [Azure Portal](https://portal.azure.com)för din personanpassa resurs.

1. På sidan **konfiguration** på fliken **inlärnings beteende** väljer du **RETUR bas linje åtgärd, lär dig som en lärling** och väljer sedan **Spara**.

> [!div class="mx-imgBorder"]
> ![Skärm bild av hur du konfigurerar inlärnings beteende i lärlings läge i Azure Portal](media/settings/configure-learning-behavior-azure-portal.png)

## <a name="changes-to-the-existing-application"></a>Ändringar i det befintliga programmet

Ditt befintliga program bör inte ändra hur det för närvarande väljer åtgärder att visa eller hur programmet bestämmer värdet, **belöningen** för den åtgärden. Den enda ändringen av programmet kan vara ordningen på de åtgärder som skickas till personens rang-API. Åtgärden som programmet visar visas som _första åtgärd_ i åtgärds listan. [Ranknings-API: et](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) använder den här första åtgärden för att träna din personliga modell.

### <a name="configure-your-application-to-call-the-rank-api"></a>Konfigurera ditt program så att det anropar rang-API: et

För att kunna lägga till personanpassa till ditt program måste du anropa API: er för rang och belöning.

1. Lägg till [rang-API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) -anropet efter punkten i din befintliga program logik där du bestämmer listan över åtgärder och deras funktioner. Den första åtgärden i åtgärds listan måste vara den åtgärd som valts av din befintliga logik.

1. Konfigurera din kod för att visa åtgärden som associeras med ID: t för ranknings-API-svarets **belönings åtgärd**.

### <a name="configure-your-application-to-call-reward-api"></a>Konfigurera ditt program för att anropa belönings-API: et

1. Använd din befintliga affärs logik för att beräkna **belöningen** för den visade åtgärden. Värdet måste ligga i intervallet från 0 till 1. Skicka den här belöningen till Personanpassare med hjälp av [belönings-API: et](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward). Belöning svärdet förväntas inte omedelbart och kan förskjutas över en tids period – beroende på din affärs logik.

1. Om du inte returnerar belöningen inom den konfigurerade svars **tiden för belöningen** kommer standard belöningen att användas i stället.

## <a name="evaluate-apprentice-mode"></a>Utvärdera lärlings läge

I Azure Portal går du till **utvärderings** sidan för din personanpassa resurs och granskar den **aktuella prestandan för inlärnings beteendet**.

> [!div class="mx-imgBorder"]
> ![Skärm bild av granskning av inlärnings beteende i lärlings läge i Azure Portal](media/settings/evaluate-apprentice-mode.png)

Lärlings läget tillhandahåller följande **utvärderings mått**:
* **Bas linje – genomsnittlig belöning**: Genomsnittligt antal förmåner för programmets standard (bas linje).
* **Personanpassa – genomsnittlig belöning**: genomsnitt av den totala belönings anpassningen kan ha uppnåtts.
* **Utmärkelse för utmärkelse över de senaste 1000 händelserna**: förhållandet mellan bas linje och personanpassa belöning – normaliserade över de senaste 1000-händelserna.

## <a name="switch-behavior-to-online-mode"></a>Växla beteende till onlineläge

När du bestämmer att en Personanpassare tränas med ett medelvärde på 75-85% rullande medelvärde, är modellen redo att växla till onlineläge.

I Azure Portal för din personanpassa resurs går du till sidan **konfiguration** , på fliken **inlärnings beteende** , och väljer **återgå till den bästa åtgärden** och väljer **Spara**.

Du behöver inte göra några ändringar i rang-och belönings-API-anrop.

## <a name="next-steps"></a>Nästa steg

* [Hantera inställningar för modell och inlärning](how-to-manage-model.md)
