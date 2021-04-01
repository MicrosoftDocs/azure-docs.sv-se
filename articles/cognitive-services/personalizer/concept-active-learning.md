---
title: Utbildnings princip – Personanpassare
description: Inlärnings inställningarna bestämmer modell utbildningens *Egenskaper* . Två modeller av samma data som har tränats på olika inlärnings inställningar kommer att upphöra annorlunda.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1d2038e3796df843736eb80f7e5645f4141c82f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "91253640"
---
# <a name="learning-policy-and-settings"></a>Utbildnings princip och inställningar

Inlärnings inställningarna bestämmer modell utbildningens *Egenskaper* . Två modeller av samma data som har tränats på olika inlärnings inställningar kommer att upphöra annorlunda.

[Inlärnings policy och inställningar](how-to-settings.md#configure-rewards-for-the-feedback-loop) anges på din personanpassa resurs i Azure Portal.

## <a name="import-and-export-learning-policies"></a>Importera och exportera utbildnings principer

Du kan importera och exportera filer för inlärnings principer från Azure Portal. Använd den här metoden för att spara befintliga principer, testa dem, ersätta dem och arkivera dem i käll kods kontrollen som artefakter för framtida referens och granskning.

Lär dig [hur du](how-to-manage-model.md#import-a-new-learning-policy) importerar och exporterar en utbildnings princip i Azure Portal för din personanpassa resurs.

## <a name="understand-learning-policy-settings"></a>Förstå policy inställningar för inlärning

Inställningarna i inlärnings principen är inte avsedda att ändras. Ändra endast inställningar om du förstår hur de påverkar Personanpassaren. Utan den här kunskapen kan du orsaka problem, t. ex. invaliderar anpassnings modeller.

I personanpassaren används [vowpalwabbit](https://github.com/VowpalWabbit) för att träna och räkna upp händelser. Läs vowpalwabbit- [dokumentationen](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments) om hur du redigerar inlärnings inställningarna med hjälp av vowpalwabbit. När du har rätt kommando rads argument sparar du kommandot till en fil med följande format (Ersätt egenskap svärdet argument med önskat kommando) och laddar upp filen för att importera inlärnings inställningar i fönstret **modell-och inlärnings inställningar** i Azure Portal för din personanpassa resurs.

Följande `.json` är ett exempel på en utbildnings princip.

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

## <a name="compare-learning-policies"></a>Jämför inlärnings principer

Du kan jämföra hur olika inlärnings principer fungerar mot tidigare data i personanpassa loggar genom att göra [offline-utvärderingar](concepts-offline-evaluation.md).

[Ladda upp dina egna utbildnings principer](how-to-manage-model.md) för att jämföra dem med den aktuella inlärnings policyn.

## <a name="optimize-learning-policies"></a>Optimera inlärnings principer

En personanpassare kan skapa en optimerad utbildnings princip i en [offline-utvärdering](how-to-offline-evaluation.md). En optimerad utbildnings princip som har bättre fördelar i en offline-utvärdering ger bättre resultat när den används online i personanpassa företaget.

När du har optimerat en inlärnings princip kan du tillämpa den direkt på en Personanpassare så att den omedelbart ersätter den aktuella principen. Eller så kan du spara den optimerade principen för ytterligare utvärdering och senare bestämma om du vill ignorera, Spara eller tillämpa den.

## <a name="next-steps"></a>Nästa steg

* Lär dig [aktiva och inaktiva händelser](concept-active-inactive-events.md).
