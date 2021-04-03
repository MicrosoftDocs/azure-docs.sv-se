---
title: Terminologi – Personanpassare
description: 'Personanpassare använder terminologin från förstärkt inlärning. Dessa villkor används i Azure Portal och API: erna.'
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: cd0d85be5447aad0f2a3c37041e7d5d5d047a468
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "91777300"
---
# <a name="personalizer-terminology"></a>Terminologi för anpassning

Personanpassare använder terminologin från förstärkt inlärning. Dessa villkor används i Azure Portal och API: erna.

## <a name="conceptual-terminology"></a>Konceptuell terminologi

* **Inlärnings slinga**: du skapar en personanpassa resurs, som kallas _inlärnings slinga_, för alla delar av ditt program som kan dra nytta av anpassning. Om du har mer än en upplevelse att anpassa, skapar du en slinga för var och en.

* **Modell**: en personanpassa modell som samlar in alla data som lärts om användarens beteende, hämtar tränings data från kombinationen av argument som du skickar till ranknings-och belönings samtal och med ett inlärnings beteende som fastställs av inlärnings principen.

* **Onlineläge**: standard [inlärnings beteendet](#learning-behavior) för en personanpassare där din inlärnings slinga, använder Machine Learning för att bygga modellen som förutsäger den **främsta åtgärden** för ditt innehåll.

* **Lärlings läge**: ett [inlärnings beteende](#learning-behavior) som hjälper till att varmen starta en personanpassa modell för att träna utan att påverka programmens resultat och åtgärder.

## <a name="learning-behavior"></a>Inlärnings beteende:

* **Online-läge**: returnera den bästa åtgärden. Din modell kommer att svara på ranka samtal med bästa möjliga åtgärd och kommer att använda belönings anrop för att lära sig och förbättra sina val över tid.
* **[Lärlings läge](concept-apprentice-mode.md)**: Lär dig som lärling. Din modell kommer att lära sig genom att observera hur det befintliga systemet fungerar. Ranknings anrop returnerar alltid programmets **standard åtgärd** (bas linje).

## <a name="personalizer-configuration"></a>Personanpassa konfiguration

Personanpassaren har kon figurer ATS från [Azure Portal](https://portal.azure.com).

* **Fördelar**: konfigurera standardvärden för belönings vänte tid, standard belöning och agg regerings princip för belöning.

* **Undersökning**: Konfigurera procent andelen rang anrop som ska användas för utforskning

* **Modell uppdaterings frekvens**: hur ofta modellen omtränas.

* **Datakvarhållning**: hur många dagars data som ska lagras. Detta kan påverka utvärderingar offline, som används för att förbättra din inlärnings slinga.

## <a name="use-rank-and-reward-apis"></a>Använd rang-och belönings-API: er

* **Rangordning**: med hjälp av åtgärder med funktioner och kontext funktioner kan du använda utforska eller utnyttja för att returnera den översta åtgärden (innehålls objekt).

    * **Åtgärder**: åtgärder är innehålls objekt, till exempel produkter eller kampanjer, för att välja bland. Personanpassa väljer den främsta åtgärden (returnerat åtgärds-ID för belöning) som ska visas för användarna via rang-API: et.

    * **Kontext**: Ange en mer exakt rangordning genom att ange information om din kontext, till exempel:
        * Användaren.
        * Enheten som de är på.
        * Aktuell tid.
        * Andra data om den aktuella situationen.
        * Historiska data om användaren eller kontexten.

        Ditt specifika program kan ha annan Sammanhangs information.

    * **[Funktioner](concepts-features.md)**: en enhet med information om ett innehålls objekt eller en användar kontext. Se till att endast använda funktioner som är aggregerade. Använd inte vissa tider, användar-ID: n eller andra icke-aggregerade data som funktioner.

        * En _Åtgärds funktion_ är metadata om innehållet.
        * En _kontext funktion_ är metadata om kontexten där innehållet visas.

* **Utforskning**: tjänsten personanpassa är en utforskande av när du i stället för att returnera den bästa åtgärden väljer en annan åtgärd för användaren. Tjänsten personanpassa förhindrar drift, stagnation och kan anpassas till pågående användar beteende genom att utforska.

* **Utnyttjande**: tjänsten personanpassa använder den aktuella modellen för att avgöra den bästa åtgärden baserat på tidigare data.

* **Experimentets varaktighet**: den tid som tjänsten personanpassa förväntar sig en belöning, med början från det ögonblick då rang samtalet inträffade för händelsen.

* **Inaktiva händelser**: en inaktiv händelse är en där du anropade rang, men du är inte säker på att användaren någonsin kommer att se resultatet, på grund av klient programs beslut. Inaktiva händelser gör att du kan skapa och lagra anpassnings resultat och sedan välja att ta bort dem senare utan att påverka Machine Learning-modellen.


* **Belöning**: ett mått på hur användaren svarade på rang-API: et returnerade belönings åtgärds-ID, som ett resultat mellan 0 och 1. Värdet 0 till 1 anges av din affärs logik, baserat på hur valet hjälpte dig att uppnå dina affärs mål för anpassning. Inlärnings slingan lagrar inte den här belöningen som enskild användar historik.

## <a name="evaluations"></a>Utvärderingar

### <a name="offline-evaluations"></a>Offlineutvärderingar

* **Utvärdering**: en offline-utvärdering avgör den bästa inlärnings principen för din loop baserat på programmets data.

* **Inlärnings princip**: hur personanpassare en modell i varje händelse kommer att fastställas av vissa parametrar som påverkar hur Machine Learning-algoritmen fungerar. En ny inlärnings slinga börjar med en standard **inlärnings princip** som kan ge måttliga prestanda. När du kör [utvärderingarna](concepts-offline-evaluation.md)skapar personanpassaren nya inlärnings principer som är specifikt optimerade för användnings fall i slingan. Personanpassaren kommer att utföra avsevärt bättre med principer som är optimerade för varje enskild slinga, som genereras under utvärderingen. Inlärnings policyn heter _utbildnings inställningar_ på **modell-och utbildnings inställningarna** för personanpassa-resursen i Azure Portal.

### <a name="apprentice-mode-evaluations"></a>Utvärdering av lärlings läge

Lärlings läget tillhandahåller följande **utvärderings mått**:
* **Bas linje – genomsnittlig belöning**: Genomsnittligt antal förmåner för programmets standard (bas linje).
* **Personanpassa – genomsnittlig belöning**: genomsnitt av den totala belönings anpassningen kan ha uppnåtts.
* **Genomsnittlig rullande belöning**: förhållandet mellan bas linje och personanpassa belöning – normaliserat under de senaste 1000 händelserna.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [etik och ansvarig användning](ethics-responsible-use.md)