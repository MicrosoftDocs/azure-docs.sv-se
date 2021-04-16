---
title: Alla fördefinierade entiteter – LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller listor över de fördefinierade entiteter som ingår i Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/13/2021
ms.openlocfilehash: 7155a829655645e13e0485ed7d51305ec50e5b0a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502767"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Entiteter per kultur i din LUIS-modell

Language Understanding (LUIS) tillhandahåller fördefinierade entiteter.

## <a name="entity-resolution"></a>Entitetsmatchning
När en förbyggd entitet ingår i ditt program inkluderar LUIS motsvarande entitetsmatchning i slutpunktssvaret. Alla exempelyttranden är också märkta med entiteten.

Beteendet för fördefinierade entiteter kan inte ändras, men du kan förbättra lösningen genom att lägga till den fördefinierade entiteten som en funktion i en maskininlärningsentitet eller [underentitet](luis-concept-entity-types.md#prebuilt-entity).

## <a name="availability"></a>Tillgänglighet
Om inget annat anges är fördefinierade entiteter tillgängliga i alla språk för LUIS-program (kulturer). I följande tabell visas de fördefinierade entiteter som stöds för varje kultur.

|Kultur|Subkulturer|Kommentarer|
|--|--|--|
|Kinesiska|[zh-CN](#chinese-entity-support)||
|Nederländska|[nl-NL](#dutch-entity-support)||
|Engelska|[en-US (amerikansk)](#english-american-entity-support)||
|Franska|[fr-CA (Kanada),](#french-canadian-entity-support) [fr-FR (Frankrike)](#french-france-entity-support), ||
|Tyska|[de-DE](#german-entity-support)||
|Italienska|[it-IT](#italian-entity-support)||
|Japanska|[ja-JP](#japanese-entity-support)||
|Koreanska|[ko-KR](#korean-entity-support)||
|Portugisiska|[pt-BR (Brasilien)](#portuguese-brazil-entity-support)||
|Spanska|[es-ES (Spanien)](#spanish-spain-entity-support), [es-MX (Mexiko)](#spanish-mexico-entity-support)||
|Turkiska|[Turkiska](#turkish-entity-support)||

## <a name="prediction-endpoint-runtime"></a>Körning av förutsägelseslutpunkt

Tillgängligheten för en förbyggd entitet på ett visst språk bestäms av körningsversionen för förutsägelseslutpunkten.

## <a name="chinese-entity-support"></a>Stöd för kinesiska entiteter

Följande entiteter stöds:

| Förbyggd entitet | zh-CN |
| --------------- | :---: |
[Ålder:](luis-reference-prebuilt-age.md)<br>år<br>månad<br>Vecka<br>day   |    V2, V3   |
[Valuta (pengar)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (t.ex. )  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>datumintervall<br>time<br>timerange   |    V2, V3   |
[Dimension:](luis-reference-prebuilt-dimension.md)<br>volym<br>område<br>vikt<br>information (t.ex. bit/byte)<br>längd (t.ex. mätare)<br>hastighet (t.ex. mil per timme)  |    V2, V3   |
[E-post](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordningstal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdningstalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatur:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="dutch-entity-support"></a>Stöd för nederländska entiteter

Följande entiteter stöds:

| Förbyggd entitet | nl-NL |
| --------------- | :---: |
[Ålder:](luis-reference-prebuilt-age.md)<br>år<br>månad<br>Vecka<br>day   |    V2, V3   |
[Valuta (pengar)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (t.ex. )  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimension:](luis-reference-prebuilt-dimension.md)<br>volym<br>område<br>vikt<br>information (t.ex. bit/byte)<br>length (t.ex. mätare)<br>hastighet (t.ex. mil per timme)  |    V2, V3   |
[E-post](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordningstal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdningstalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatur:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="english-american-entity-support"></a>Stöd för entiteter på engelska (amerikansk)

Följande entiteter stöds:

| Förbyggd entitet | sv-SE |
| --------------- | :---: |
[Ålder:](luis-reference-prebuilt-age.md)<br>år<br>månad<br>Vecka<br>day   |    V2, V3   |
[Valuta (pengar)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (t.ex. )  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>datumintervall<br>time<br>timerange   |    V2, V3   |
[Dimension:](luis-reference-prebuilt-dimension.md)<br>volym<br>område<br>vikt<br>information (t.ex. bit/byte)<br>length (t.ex. mätare)<br>hastighet (t.ex. mil per timme)  |    V2, V3   |
[E-post](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    V2, V3   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordningstal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdningstalV2](luis-reference-prebuilt-ordinal-v2.md)   |    V2, V3   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatur:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-france-entity-support"></a>Stöd för franska (Frankrike)

Följande entiteter stöds:

| Förbyggd entitet | fr-FR |
| --------------- | :---: |
[Ålder:](luis-reference-prebuilt-age.md)<br>år<br>månad<br>Vecka<br>day   |    V2, V3   |
[Valuta (pengar)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (t.ex. )  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>datumintervall<br>time<br>timerange   |    V2, V3   |
[Dimension:](luis-reference-prebuilt-dimension.md)<br>volym<br>område<br>vikt<br>information (t.ex. bit/byte)<br>length (t.ex. mätare)<br>hastighet (t.ex. mil per timme)  |    V2, V3   |
[E-post](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordningstal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdningstalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |   -   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatur:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-canadian-entity-support"></a>Stöd för franska (italienska) entiteter

Följande entiteter stöds:

| Förbyggd entitet | fr-CA |
| --------------- | :---: |
[Ålder:](luis-reference-prebuilt-age.md)<br>år<br>månad<br>Vecka<br>day   |    V2, V3   |
[Valuta (pengar)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (t.ex. )  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>datumintervall<br>time<br>timerange   |    V2, V3   |
[Dimension:](luis-reference-prebuilt-dimension.md)<br>volym<br>område<br>vikt<br>information (t.ex. bit/byte)<br>length (t.ex. mätare)<br>hastighet (t.ex. mil per timme)  |    V2, V3   |
[E-post](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordningstal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdningstalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatur:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="german-entity-support"></a>Stöd för tyska entiteter

Följande entiteter stöds:

|Förbyggd entitet | de-DE |
| -------------- | :---: |
[Ålder:](luis-reference-prebuilt-age.md)<br>år<br>månad<br>Vecka<br>day   |    V2, V3   |
[Valuta (pengar)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (t.ex. )  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>datumintervall<br>time<br>timerange   |    V2, V3   |
[Dimension:](luis-reference-prebuilt-dimension.md)<br>volym<br>område<br>vikt<br>information (t.ex. bit/byte)<br>length (t.ex. mätare)<br>hastighet (t.ex. mil per timme)  |    V2, V3   |
[E-post](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordningstal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdningstalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatur:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="italian-entity-support"></a>Stöd för italienska entiteter

Italienska fördefinierade ålder, valuta, dimension, nummer, procentmatchning har _ändrats_ från V2 och V3 förhandsversion.

Följande entiteter stöds:

| Förbyggd entitet | it-IT |
| --------------- | :---: |
[Ålder:](luis-reference-prebuilt-age.md)<br>år<br>månad<br>Vecka<br>day   |    V2, V3   |
[Valuta (pengar)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (t.ex. )  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>område<br>vikt<br>information (t.ex. bit/byte)<br>length (t.ex. mätare)<br>hastighet (t.ex. mil per timme)  |    V2, V3   |
[E-post](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordningstal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdningstalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatur:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="japanese-entity-support"></a>Stöd för japanska entiteter

Följande entiteter stöds:

|Förbyggd entitet | ja-JP |
| -------------- | :---: |
[Ålder:](luis-reference-prebuilt-age.md)<br>år<br>månad<br>Vecka<br>day   |    V2, -   |
[Valuta (pengar)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (t.ex. )  |    V2, -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>område<br>vikt<br>information (t.ex. bit/byte)<br>längd (t.ex. mätare)<br>hastighet (t.ex. mil per timme)  |    V2, -   |
[E-post](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, -   |
[Ordningstal](luis-reference-prebuilt-ordinal.md)   |    V2, -   |
[OrdningstalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatur:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, -   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="korean-entity-support"></a>Stöd för koreanska entiteter

Följande entiteter stöds:

| Förbyggd entitet | ko-KR |
| --------------- | :---: |
[Ålder:](luis-reference-prebuilt-age.md)<br>år<br>månad<br>Vecka<br>day   |    -   |
[Valuta (pengar)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (t.ex. )  |    -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>område<br>vikt<br>information (t.ex. bit/byte)<br>längd (t.ex. mätare)<br>hastighet (t.ex. mil per timme)  |    -   |
[E-post](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    -   |
[Ordningstal](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdningstalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatur:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="portuguese-brazil-entity-support"></a>Stöd för portugisiska (Brasilien)

Följande entiteter stöds:

| Förbyggd entitet | pt-BR |
| --------------- | :---: |
[Ålder:](luis-reference-prebuilt-age.md)<br>år<br>månad<br>Vecka<br>day   |    V2, V3   |
[Valuta (pengar)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (t.ex. )  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>datumintervall<br>time<br>timerange   |    V2, V3   |
[Dimension:](luis-reference-prebuilt-dimension.md)<br>volym<br>område<br>vikt<br>information (t.ex. bit/byte)<br>length (t.ex. mätare)<br>hastighet (t.ex. mil per timme)  |    V2, V3   |
[E-post](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordningstal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdningstalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatur:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

KeyPhrase är inte tillgängligt i alla underkulturer i portugisiska (Brasilien) – ```pt-BR``` .

## <a name="spanish-spain-entity-support"></a>Stöd för spanska entiteter (Spanien)

Följande entiteter stöds:

| Förbyggd entitet | es-ES |
| --------------- | :---: |
[Ålder:](luis-reference-prebuilt-age.md)<br>år<br>månad<br>Vecka<br>day   |    V2, V3   |
[Valuta (pengar)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (t.ex. )  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>datumintervall<br>time<br>timerange   |    V2, V3   |
[Dimension:](luis-reference-prebuilt-dimension.md)<br>volym<br>område<br>vikt<br>information (t.ex. bit/byte)<br>length (t.ex. mätare)<br>hastighet (t.ex. mil per timme)  |    V2, V3   |
[E-post](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordningstal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdningstalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatur:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-mexico-entity-support"></a>Stöd för spanska entiteter (Mexiko)

Följande entiteter stöds:

| Förbyggd entitet | es-MX |
| --------------- | :---: |
[Ålder:](luis-reference-prebuilt-age.md)<br>år<br>månad<br>Vecka<br>day   |    -   |
[Valuta (pengar)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (t.ex. )  |    -   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>datumintervall<br>time<br>timerange   |    -   |
[Dimension:](luis-reference-prebuilt-dimension.md)<br>volym<br>område<br>vikt<br>information (t.ex. bit/byte)<br>length (t.ex. mätare)<br>hastighet (t.ex. mil per timme)  |    -   |
[E-post](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordningstal](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdningstalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatur:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

Se information om [inaktuella fördefinierade entiteter](luis-reference-prebuilt-deprecated.md)

## <a name="turkish-entity-support"></a>Stöd för turkiska entiteter

| Förbyggd entitet | tr-tr |
| --------------- | :---: |
[Ålder:](luis-reference-prebuilt-age.md)<br>år<br>månad<br>Vecka<br>day   |    -   |
[Valuta (pengar)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (t.ex. )  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>datumintervall<br>time<br>timerange   |    -   |
[Dimension:](luis-reference-prebuilt-dimension.md)<br>volym<br>område<br>vikt<br>information (t.ex. bit/byte)<br>length (t.ex. mätare)<br>hastighet (t.ex. mil per timme)  |    -   |
[E-post](luis-reference-prebuilt-email.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Nummer](luis-reference-prebuilt-number.md)   |    -   |
[Ordningstal](luis-reference-prebuilt-ordinal.md)   |    -   |
[Procent](luis-reference-prebuilt-percentage.md)   |    -   |
[Telefonnummer](luis-reference-prebuilt-phonenumber.md)   |    -   |
[Temperatur:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    -   |

<!---
See notes on [Deprecated prebuilt entities](luis-reference-prebuilt-deprecated.md)
KeyPhrase is not available.
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>Bidra till fördefinierade entitetskulturer
De fördefinierade entiteterna utvecklas i Recognizers-Text med öppen källkod. [Bidra](https://github.com/Microsoft/Recognizers-Text) till projektet. Det här projektet innehåller exempel på valuta per kultur.

GeographyV2 och PersonName ingår inte i Recognizers-Text projekt. För problem med dessa fördefinierade entiteter öppnar du en [supportbegäran.](../../azure-portal/supportability/how-to-create-azure-support-request.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [tal,](luis-reference-prebuilt-number.md) [datetimeV2 och](luis-reference-prebuilt-datetimev2.md) [valutaentiteter.](luis-reference-prebuilt-currency.md)
