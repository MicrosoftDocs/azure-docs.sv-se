---
title: Använd metoden för offline-utvärdering – personanpassa
titleSuffix: Azure Cognitive Services
description: Den här artikeln förklarar hur du använder offline-utvärdering för att mäta appens effektivitet och analysera din inlärnings slinga.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 627f511bb12c16c8f54935d1f782cb7c2c962163
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "87132763"
---
# <a name="offline-evaluation"></a>Offlineutvärdering

Offline-utvärdering är en metod som gör att du kan testa och utvärdera effektiviteten hos tjänsten personanpassa utan att ändra din kod eller påverka användar upplevelsen. Offline-utvärdering använder tidigare data som skickats från ditt program till API: erna rang och belöning, för att jämföra hur olika rankning har utförts.

Offline-utvärdering utförs i ett datum intervall. Intervallet kan sluta så sent som den aktuella tiden. Början av intervallet får inte vara mer än antalet dagar som har angetts för [datakvarhållning](how-to-settings.md).

Offline-utvärdering kan hjälpa dig att besvara följande frågor:

* Hur effektivt är en anpassnings rang för lyckad anpassning?
    * Vilka är de genomsnittliga fördelarna som uppnås av den personliga inlärnings principen för online-datorn?
    * Hur jämförs personanpassa med effektiviteten hos vad programmet skulle ha gjort som standard?
    * Vad skulle ha varit den jämför ande effektiviteten med ett slumpmässigt val för anpassning?
    * Vad skulle ha varit den jämför ande effektiviteten i olika utbildnings principer som anges manuellt?
* Vilka funktioner i kontexten bidrar mer eller mindre för att lyckas med anpassningen?
* Vilka funktioner i åtgärderna bidrar mer eller mindre för att lyckas med anpassningen?

Dessutom kan du använda offline-utvärdering för att upptäcka mer optimerade inlärnings principer som Personanpassaren kan använda för att förbättra resultaten i framtiden.

Offline-utvärdering ger ingen vägledning för hur många procent av händelser som ska användas för utforskning.

## <a name="prerequisites-for-offline-evaluation"></a>Krav för offline-utvärdering

Följande är viktiga överväganden för utvärdering av representativ offline:

* Ha tillräckligt med data. Det rekommenderade minimivärdet är minst 50 000 händelser.
* Samla in data från perioder med representativt användar beteende och trafik.

## <a name="discovering-the-optimized-learning-policy"></a>Identifiera den optimerade inlärnings principen

En personanpassare kan använda processen för offline-utvärdering för att upptäcka en mer optimal inlärnings princip automatiskt.

När du har genomfört offline-utvärderingen kan du se den jämför ande effektiviteten hos Personanpassaren med den nya principen jämfört med den aktuella online-principen. Du kan sedan använda den inlärnings principen för att göra det direkt i Personanpassare, genom att ladda ned den och ladda upp den i modeller och princip panelen. Du kan också ladda ned det för framtida analys eller användning.

Aktuella principer som ingår i utvärderingen:

| Utbildnings inställningar | Syfte|
|--|--|
|**Online-princip**| Den aktuella inlärnings principen som används i personanpassa |
|**Baslinje**|Programmets standard (som fastställs av den första åtgärden som skickas i rang anrop)|
|**Slumpmässig princip**|Ett tänkt rangordnings beteende som alltid returnerar slumpmässiga val av åtgärder från de angivna.|
|**Anpassade principer**|Ytterligare inlärnings principer laddades upp när utvärderingen startades.|
|**Optimerad princip**|Om utvärderingen startades med alternativet för att identifiera en optimerad princip, kommer den också att jämföras och du kommer att kunna ladda ned den eller göra den till en online Learning-princip som ersätter den aktuella.|

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>Förstå relevansen för utvärderings resultat offline

När du kör en offline-utvärdering är det mycket viktigt att analysera _förtroende gränserna_ för resultaten. Om de är breda innebär det att ditt program inte har fått tillräckligt med data för att uppskattningarna ska bli exakta eller betydande. När systemet ackumulerar mer data och du kör offline-utvärderingar över längre perioder, blir konfidens intervallet smalare.

## <a name="how-offline-evaluations-are-done"></a>Hur offline-utvärderingar utförs

Offline-utvärderingar görs med hjälp av en metod som kallas **Counterfactual-utvärdering**.

Personanpassan bygger på antagandet att användarens beteende (och därmed förmåner) inte kan förutsäga retroaktivt (det går inte att förutsäga vad som skulle ha hänt om användaren hade visat något annorlunda än vad de gjorde) och endast för att lära sig från uppmätta förmåner.

Detta är den konceptuella processen som används för utvärderingar:

```
[For a given _learning policy), such as the online learning policy, uploaded learning policies, or optimized candidate policies]:
{
    Initialize a virtual instance of Personalizer with that policy and a blank model;

    [For every chronological event in the logs]
    {
        - Perform a Rank call

        - Compare the reward of the results against the logged user behavior.
            - If they match, train the model on the observed reward in the logs.
            - If they don't match, then what the user would have done is unknown, so the event is discarded and not used for training or measurement.

    }

    Add up the rewards and statistics that were predicted, do some aggregation to aid visualizations, and save the results.
}
```

Offline-utvärderingen använder endast observerat användar beteende. Den här processen tar bort stora mängder data, särskilt om ditt program rangordnar anrop med ett stort antal åtgärder.


## <a name="evaluation-of-features"></a>Utvärdering av funktioner

Offline-utvärderingar kan ge information om hur mycket av de speciella funktionerna för åtgärder eller sammanhang som väger för högre förmåner. Informationen beräknas med hjälp av utvärderingen mot den aktuella tids perioden och data och kan variera med tiden.

Vi rekommenderar att du tittar på funktions utvärderingar och ber:

* Vilka andra, ytterligare funktioner kan ditt program eller system tillhandahålla längs de rader som är mer effektiva?
* Vilka funktioner kan tas bort på grund av låg effektivitet? Funktioner för låg effektivitet lägger till _brus_ i Machine Learning.
* Finns det några funktioner som av misstag ingår? Exempel på dessa är: identifierbar information om användare, dubbla ID: n osv.
* Finns det några oönskade funktioner som inte bör användas för att anpassa sig på grund av regler eller ansvar för att tänka på säkerhet? Finns det funktioner som kan proxy (det vill säga är det som är nära spegling eller korrelera med) oönskade funktioner?


## <a name="next-steps"></a>Nästa steg

[Konfigurera personanpassare](how-to-settings.md) 
 [Köra offline-utvärderingar](how-to-offline-evaluation.md) Förstå [hur personanpassaren fungerar](how-personalizer-works.md)
