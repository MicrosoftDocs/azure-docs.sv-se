---
title: Avsikter och entiteter – LUIS
titleSuffix: Azure Cognitive Services
description: En enda avsikt representerar en uppgift eller åtgärd som användaren vill utföra. Det är ett syfte eller mål som uttrycks i en användares uttryck. Definiera en uppsättning avsikter som motsvarar de åtgärder som användarna vill utföra i ditt program.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: 8e76e3e7683d43a7a39bc0c168a29016a988c705
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499418"
---
# <a name="intents-in-your-luis-app"></a>Avsikter i LUIS-appen

En avsikt representerar en uppgift eller åtgärd som användaren vill utföra. Det är ett syfte eller mål som uttrycks i en [användares ta upp .](luis-concept-utterance.md)

Definiera en uppsättning avsikter som motsvarar de åtgärder som användarna vill utföra i ditt program. En reseapp definierar till exempel flera avsikter:

Avsikter för reseappen   |   Exempel på yttranden   |
------|------|
 BookFlight (Boka flyg)     |   "Boka ett flyg till Rio nästa vecka" <br/> "Flyg mig till Rio den 24:e" <br/> "Jag behöver en planbiljett nästa söndag till Rio de Rio de Rio"    |
 Hälsning     |   ”Hallå” <br/>”Hej” <br/>”God morgon”  |
 CheckWeather | "Hur är vädret i Boston?" <br/> "Visa mig prognosen för den här helg" |
 Inget         | "Skaffa ett cookie-recept"<br>"Vann Lakers?" |

Alla program har den fördefinierade avsikten "[None](#none-intent)", vilket är reserv avsikt.

## <a name="prebuilt-domains-provide-intents"></a>Fördefinierade domäner tillhandahåller avsikter
Förutom avsikter som du definierar kan du använda fördefinierade avsikter från en av de [fördefinierade domänerna](./howto-add-prebuilt-models.md).

## <a name="return-all-intents-scores"></a>Returnera alla avsikters poäng
Du tilldelar ett uttryck till en enda avsikt. När LUIS tar emot ett uttryck på slutpunkten returnerar den som standard den främsta avsikten för det yttrandena.

Om du vill ha poäng för alla avsikter för yttrandena kan du ange en flagga i frågesträngen för förutsägelse-API:et.

|Förutsägelse-API-version|Flagga|
|--|--|
|V2|`verbose=true`|
|V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Avsikt jämfört med entitet
Avsikten representerar åtgärden som programmet ska vidta för användaren och baseras på hela targränssnittet. Ett uttryck kan bara ha en avsikt med högst poäng, men det kan ha många entiteter.

<a name="how-do-intents-relate-to-entities"></a>

Skapa en avsikt när användarens _avsikt_ utlöser en åtgärd i klientprogrammet, till exempel ett anrop till funktionen checkweather(). Skapa sedan entiteter som representerar de parametrar som krävs för att utföra åtgärden.

|Avsikt   | Entitet | Exempel på yttrande   |
|------------------|------------------------------|------------------------------|
| CheckWeather | { "type": "location", "entity": "Seattle" }<br>{ "type": "builtin.datetimeV2.date","entity": "tomorrow","resolution":"2018-05-23" } | Hur är vädret i `Seattle` `tomorrow` ? |
| CheckWeather | { "type": "date_range", "entity": "this weekend" } | Visa mig prognosen för `this weekend` |
||||

## <a name="prebuilt-domain-intents"></a>Fördefinierade domän intents

[Fördefinierade domäner](./howto-add-prebuilt-models.md) tillhandahåller avsikter med yttranden.

## <a name="none-intent"></a>Avsikten Ingen

Avsikten **None** skapas men lämnas tom för avsikt. Avsikten **None** är en obligatorisk avsikt och kan inte tas bort eller byta namn. Fyll i med talindata som ligger utanför din domän.

Avsikten **None** är reserv avsikt, viktig i varje app, och bör ha 10 % av de totala yttrandena. Den används för att lära LUIS-yttranden som inte är viktiga i appdomänen (ämnesområdet). Om du inte lägger till några yttranden för avsikten **None** (Ingen) tvingar LUIS ett uttryck som ligger utanför domänen till en av domän avsikterna. Detta förvränger prediktionspoängen genom att lära LUIS att använda fel avsikt för yttrandena.

När ett uttryck förutsägs som avsikten None (Ingen) kan klientprogrammet ställa fler frågor eller ange en meny för att dirigera användaren till giltiga val.

## <a name="negative-intentions"></a>Negativa avsikter
Om du vill fastställa negativa och positiva avsikter, till exempel  **"Jag** vill ha en bil" och "Jag vill inte ha en bil", kan du skapa två avsikter (en positiv och en negativ) och lägga till lämpliga yttranden för var och en. Eller så kan du skapa en enda avsikt och markera de två olika positiva och negativa termerna som en entitet.

## <a name="intents-and-patterns"></a>Avsikter och mönster

Om du har exempelyttranden, som kan definieras helt eller [](luis-concept-entity-types.md#regex-entity) delvis som ett reguljärt uttryck, kan du överväga att använda entiteten för reguljära uttryck som är parad med [ett mönster](luis-concept-patterns.md).

Om du använder en entitet för reguljära uttryck garanteras extrahering av data så att mönstret matchas. Mönstermatchningen garanterar att en exakt avsikt returneras.

## <a name="intent-balance"></a>Avsiktssaldo
Appdomänens avsikter bör ha en balans mellan yttranden för varje avsikt. Ha inte en avsikt med 10 yttranden och en annan avsikt med 500 yttranden. Detta är inte balanserat. Om du har den här situationen granskar du avsikten med 500 yttranden för att se om många av avsikterna kan ordnas om i ett [mönster](luis-concept-patterns.md).

Avsikten **None** ingår inte i saldot. Avsikten ska innehålla 10 % av de totala yttrandena i appen.

## <a name="intent-limits"></a>Avsiktsbegränsningar
Granska [gränserna](luis-limits.md#model-boundaries) för att förstå hur många avsikter du kan lägga till i en modell.

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Om du behöver mer än det maximala antalet avsikter
Börja med att fundera över om systemet använder för många avsikter.

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Flera avsikter kan kombineras till en enda avsikt med entiteter
Avsikter som är för lika kan göra det svårare för LUIS att skilja mellan dem. Avsikter bör variera tillräckligt för att fånga de viktigaste uppgifterna som användaren frågar efter, men de behöver inte avbilda alla sökvägar som koden tar. Till exempel kan BookFlight och FlightCustomerService vara separata avsikter i en reseapp, men BookInternationalFlight och BookDomesticFlight är för lika. Om systemet behöver särskilja dem använder du entiteter eller annan logik i stället för avsikter.

### <a name="dispatcher-model"></a>Dispatcher-modell
Läs mer om att kombinera LUIS- och QnA Maker-appar med [dispatch-modellen](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps).

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Begära hjälp för appar med ett stort antal avsikter
Kontakta supporten om det inte fungerar att minska antalet avsikter eller dela upp dina avsikter i flera appar. Om din Azure-prenumeration innehåller supporttjänster kontaktar du [den tekniska supporten för Azure.](https://azure.microsoft.com/support/options/)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [entiteter](luis-concept-entity-types.md), som är viktiga ord som är relevanta för avsikter
* Lär dig hur [du lägger till och hanterar avsikter](luis-how-to-add-intents.md) i din LUIS-app.
* Granska [metodtips för avsikter](luis-concept-best-practices.md)