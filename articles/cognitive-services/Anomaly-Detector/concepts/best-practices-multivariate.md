---
title: 'Metod tips för att använda API: t för avvikelse detektor Multivarierad'
titleSuffix: Azure Cognitive Services
description: 'Metod tips för att använda Multivarierad API: er för avvikelse detektor för att tillämpa avvikelse identifiering för dina tids serie data.'
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: avvikelse identifiering, maskin inlärning, algoritmer
ms.openlocfilehash: 7de25b4a099c706c05b32b52492096923033f822
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315711"
---
# <a name="multivariate-time-series-anomaly-detector-best-practices"></a>Metod tips för multivarierad Time Series-avvikelse

I den här artikeln får du vägledning kring rekommenderade metoder att följa när du använder multivarierad-API: er för avvikelse detektor.

## <a name="how-to-prepare-data-for-training"></a>Förbereda data för utbildning

För att kunna använda multivarierad-API: erna för avvikelse detektor måste vi träna vår egen modell innan identifieringen används. Data som används för utbildning är en batch över tids serier, varje gång serien ska vara i CSV-format med två kolumner, tidsstämpel och värde. Alla tids serier ska vara zippade till en zip-fil och överföras till Azure Blob Storage. Som standard används fil namnet för att representera variabeln för tids serien. Alternativt kan en extra meta.jsi filen inkluderas i zip-filen om du vill att namnet på variabeln ska skilja sig från. zip-filens namn. När vi genererar en [BLOB SAS-URL (Shared Access Signatures)](../../../storage/common/storage-sas-overview.md)kan vi använda den för utbildning.

## <a name="data-quality-and-quantity"></a>Data kvalitet och-kvantitet

Multivarierad-API: t för avvikelse detektor använder avancerade neurala-nätverk för att lära sig vanliga mönster från historiska data och förutsäga om framtida värden är avvikelser. Kvalitet och kvantitet för tränings data är viktiga för att träna en optimal modell. När modellen lär sig vanliga mönster från historiska data bör inlärnings data representera systemets övergripande tillstånd. Det är svårt för modellen att lära sig dessa typer av mönster om tränings data är fulla av avvikelser. Modellen har också miljon tals parametrar och det krävs ett minsta antal data punkter för att lära sig en optimal uppsättning parametrar. Den allmänna regeln är att du måste ange minst 15 000 data punkter per variabel för att träna modellen på rätt sätt. Mer data, desto bättre modell.

Det är vanligt att många tids serier saknar värden, vilket kan påverka prestandan för utbildade modeller. Kvoten för varje tids serie som saknas bör kontrol leras under ett rimligt värde. En tids serie med 90% värden saknas ger lite information om systemets normala mönster. Modellen kan till och med vara sämre än de fyllda värdena som vanliga mönster, som vanligt vis är raka segment eller konstanta värden. När nya data flödar in kan data identifieras som avvikelser.

Ett rekommenderat Max värdes tröskelvärde är 20%, men ett högre tröskelvärde kan vara acceptabelt under vissa omständigheter. Om du till exempel har en tids serie med en-minuters kornig het och en annan tids serie med timkostnad.  Varje timme finns det 60 data punkter per minut med data och 1 data punkt för data varje timme, vilket innebär att den saknade kvoten för Tim data är 98,33%. Det är dock bra att fylla i data för varje timme med det enda värdet om Tim tids serien inte ofta går för mycket.

## <a name="parameters"></a>Parametrar

### <a name="sliding-window"></a>Glidande fönster

Multivarierad avvikelse identifiering tar ett segment med data punkter `slidingWindow` som indata och avgör om nästa data punkt är en avvikelse. Ju större samplings längd, desto mer data kommer att beaktas för ett beslut. Du bör ha två saker att tänka på när du väljer ett korrekt värde för `slidingWindow` : egenskaper för indata och kompromisser mellan utbildning/fördröjning och potentiell prestanda förbättring. `slidingWindow` består av ett heltal mellan 28 och 2880. Du kan bestämma hur många data punkter som används som indata baserat på om dina data är periodiska och samplings frekvensen för dina data.

När dina data är periodiska kan du inkludera 1-3-cykler som indata och när dina data samplas med hög frekvens (liten kornig het) som data på minut-eller andra nivå kan du välja mer data som indata. Ett annat problem är att längre indata kan orsaka längre utbildning eller fördröjning, och det finns ingen garanti för att fler ingångs punkter leder till prestanda vinster. För få data punkter kan det vara svårt att konvergera modellen till en optimal lösning. Det är till exempel svårt att identifiera avvikelser när indata bara innehåller två punkter.

### <a name="align-mode"></a>Justerings läge

Parametern `alignMode` används för att ange hur du vill justera flera tids serier i tidsstämplar. Detta beror på att många tids serier saknar värden och vi behöver justera dem på samma tidsstämplar innan vidare bearbetning. Det finns två alternativ för den här parametern `inner join` och `outer join` . `inner join` innebär att vi rapporterar identifierings resultat för tidsstämplar som **varje tids serie** har ett värde, medan `outer join` innebär att vi rapporterar identifierings resultat för tidsstämplar för **alla tids serier** som har ett värde.  **`alignMode` Påverkar också modellens aktivitetssekvens**, så välj en lämplig `alignMode` för ditt scenario eftersom resultatet kan vara betydligt annorlunda.

Här visas ett exempel på hur du förklarar olika `alignModel` värden.

#### <a name="series1"></a>Series1

|timestamp | värde|
----------| -----|
|`2020-11-01`| 1  
|`2020-11-02`| 2  
|`2020-11-04`| 4  
|`2020-11-05`| 5

#### <a name="series2"></a>Series2

timestamp | värde  
--------- | -
`2020-11-01`| 1  
`2020-11-02`| 2  
`2020-11-03`| 3  
`2020-11-04`| 4

#### <a name="inner-join-two-series"></a>Inre koppling av två serier
  
timestamp | Series1 | Series2
----------| - | -
`2020-11-01`| 1 | 1
`2020-11-02`| 2 | 2
`2020-11-04`| 4 | 4

#### <a name="outer-join-two-series"></a>Yttre koppling av två serier

timestamp | series1 | series2
--------- | - | -
`2020-11-01`| 1 | 1
`2020-11-02`| 2 | 2
`2020-11-03`| NA | 3
`2020-11-04`| 4 | 4
`2020-11-05`| 5 | NA

### <a name="fill-not-available-na"></a>Fyllning är inte tillgänglig (NA)

När variablerna har justerats på tidsstämpeln av OUTER JOIN kan det finnas ett `Not Available` `NA` värde () i några av variablerna. Du kan ange metod för att fylla i detta NA-värde. Alternativen för `fillNAMethod` är,, `Linear` , `Previous` `Subsequent`  `Zero` och `Fixed` .

| Alternativ     | Metod                                                                                           |
| ---------- | -------------------------------------------------------------------------------------------------|
| Linjär     | Fill NA-värden efter linjär interpolation                                                           |
| Föregående   | Sprid det senaste giltiga värdet till Fill-luckor. Exempel: `[1, 2, nan, 3, nan, 4]` -> `[1, 2, 2, 3, 3, 4]` |
| Ytterligare | Använd nästa giltiga värde för att fylla luckor. Exempel: `[1, 2, nan, 3, nan, 4]` -> `[1, 2, 3, 3, 4, 4]`       |
| Noll       | Fyll NA-värden med 0.                                                                           |
| Fast      | Fill NA-värden med ett angivet giltigt värde som ska anges i `paddingValue` .          |

## <a name="model-analysis"></a>Modell analys

### <a name="training-latency"></a>Svars tid för utbildning

Multivarierad för avvikelse identifiering kan vara tids krävande. Särskilt när du har en stor mängd tidsstämplar som används för utbildning. Därför kan en del av inlärnings processen vara asynkron. Vanligt vis skickar användarna träna uppgifter genom träna modell-API. Hämta sedan modell status via `Get Multivariate Model API` . Här visar vi hur du extraherar återstående tid innan inlärningen är slutförd. Det finns ett objekt med namnet i get Multivarierad Model API Response `diagnosticsInfo` . Det finns ett-element i det här objektet `modelState` . För att beräkna den återstående tiden måste vi använda `epochIds` och `latenciesInSeconds` . En epok representerar en komplett cykel genom tränings data. Var 10 epoker kommer vi att returnera statusinformation. Som en del kommer vi att träna för 100 epoker, men svars tiden anger hur lång tid det tar. Med den här informationen vet vi hur lång tid som återstår för att träna modellen.

### <a name="model-performance"></a>Modellprestanda

Multivarierad avvikelse identifiering, som en oövervakad modell. Det bästa sättet att utvärdera det är att kontrol lera de avvikande resultaten manuellt. I Multivarierad-modellens svar tillhandahåller vi grundläggande information för att vi ska kunna analysera modell prestanda. I det `modelState` element som returneras av Get Multivarierad Model-API: et kan vi använda `trainLosses` och `validationLosses` för att utvärdera om modellen har tränats som förväntat. I de flesta fall kommer de två förlusterna att minska gradvis. En annan information för oss att analysera modell prestanda mot är i `variableStates` . Listan med variabla tillstånd rangordnas enligt `filledNARatio` fallande ordning. Ju större desto sämre prestanda, desto ofta behöver vi minska det `NA ratio` så mycket som möjligt. `NA` kan ha orsakats av saknade värden eller ej justerade variabler från ett tidsstämpel-perspektiv.

## <a name="next-steps"></a>Nästa steg

- [Snabb starter](../quickstarts/client-libraries-multivariate.md).
- [Lär dig mer om de underliggande algoritmerna som Power avvikelse detektor Multivarierad](https://arxiv.org/abs/2009.02040)