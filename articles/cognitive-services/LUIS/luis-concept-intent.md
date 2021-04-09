---
title: Avsikter och entiteter – LUIS
titleSuffix: Azure Cognitive Services
description: En enda avsikt representerar en uppgift eller åtgärd som användaren vill utföra. Det är ett syfte eller mål som uttrycks i en användares uttryck. Definiera en uppsättning syften som motsvarar åtgärder som användare vill vidta i ditt program.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: bf5ab7361c84fb787366c7c361829e52362fe427
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "95025916"
---
# <a name="intents-in-your-luis-app"></a>Avsikter i din LUIS-app

En avsikt representerar en uppgift eller åtgärd som användaren vill utföra. Det är ett syfte eller mål som uttrycks i en användares [uttryck](luis-concept-utterance.md).

Definiera en uppsättning syften som motsvarar åtgärder som användare vill vidta i ditt program. Till exempel definierar en rese app flera avsikter:

Rese programs avsikter   |   Exempel på yttranden   |
------|------|
 BookFlight (Boka flyg)     |   "Boka mig en flygning till Rio nästa vecka" <br/> "Lägg till Rio på 24" <br/> "Jag behöver en plan biljett nästa söndag för att Rio de Janeiro"    |
 Hälsning     |   ”Hallå” <br/>”Hej” <br/>”God morgon”  |
 CheckWeather | "Vad är väder som i Boston?" <br/> "Visa en prognos för den här helgen" |
 Inget         | "Hämta en cookie-recept"<br>"Gjorde The The Lakers Win?" |

Alla program levereras med det fördefinierade syftet "[ingen](#none-intent)", vilket är återställnings avsikten.

## <a name="prebuilt-domains-provide-intents"></a>Fördefinierade domäner innehåller avsikter
Förutom de som du definierar kan du använda färdiga syften från en av de [fördefinierade domänerna](./howto-add-prebuilt-models.md).

## <a name="return-all-intents-scores"></a>Returnera alla avsikter
Du tilldelar en uttryck till ett enda avsikt. När LUIS tar emot en uttryck på slut punkten returnerar den som standard den främsta avsikten för uttryck.

Om du vill ha poängen för alla avsikter för uttryck kan du ange en flagga i frågesträngen för förutsägelse-API: et.

|Förutsägelse-API-version|Flagga|
|--|--|
|V2|`verbose=true`|
|V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Avsikt jämfört med entitet
Avsikten representerar en åtgärd som programmet ska vidta för användaren och som baseras på hela uttryck. En uttryck kan bara ha en Top-bedömnings avsikt, men den kan ha många entiteter.

<a name="how-do-intents-relate-to-entities"></a>

Skapa en avsikt när användarens _avsikt_ skulle utlösa en åtgärd i klient programmet, t. ex. ett anrop till funktionen checkweather (). Skapa sedan entiteter som representerar de parametrar som krävs för att utföra åtgärden.

|Avsikt   | Entitet | Exempel på yttrande   |
|------------------|------------------------------|------------------------------|
| CheckWeather | {"typ": "plats", "entitet": "Seattle"}<br>{"typ": "Builtin. datetimeV2. date", "entitet": "imorgon", "lösning": "2018-05-23"} | Vad är det som är väder i `Seattle` `tomorrow` ? |
| CheckWeather | {"typ": "date_range", "entitet": "den här helgen"} | Visa prognosen för `this weekend` |
||||

## <a name="prebuilt-domain-intents"></a>Fördefinierade domän avsikter

[Fördefinierade domäner](./howto-add-prebuilt-models.md) innehåller metoder med yttranden.

## <a name="none-intent"></a>Avsikten Ingen

**None** -avsikten skapas men lämnas tomt i syfte. **Ingen** avsikt är en obligatorisk avsikt och kan inte tas bort eller byta namn. Fyll i med talindata som ligger utanför din domän.

**Ingen** avsikt är till gång till återställnings avsikten, viktigt i varje app och bör ha 10% av den totala yttranden. Den används för att lära LUIS-yttranden som inte är viktiga i app-domänen (ämnes område). Om du inte lägger till några yttranden för **none** -LUIS tvingar en uttryck som ligger utanför domänen i en av domän syftena. Detta kommer att skeva förutsägelse poängen genom att lära LUIS fel avsikt för uttryck.

När en uttryck förutsägs som none-avsikt kan klient programmet ställa frågor till fler frågor eller ange en meny för att dirigera användaren till giltiga val.

## <a name="negative-intentions"></a>Negativa avsikter
Om du vill fastställa negativa och positiva avsikter, till exempel "Jag **vill ha** en bil" och "Jag vill **inte** ha en bil", kan du skapa två avsikter (ett positivt och ett negativt) och lägga till lämpliga yttranden för var och en. Eller så kan du skapa en enda avsikt och markera de två olika positiva och negativa villkoren som en entitet.

## <a name="intents-and-patterns"></a>Avsikter och mönster

Om du har ett exempel på yttranden, som kan definieras delvis eller helt som ett reguljärt uttryck, bör du överväga att använda [entiteten för reguljära uttryck](luis-concept-entity-types.md#regular-expression-entity) med ett [mönster](luis-concept-patterns.md).

Om du använder en entitet med reguljära uttryck garanteras data extraheringen så att mönstret matchas. Mönster matchningen garanterar att en exakt avsikt returneras.

## <a name="intent-balance"></a>Kvarhållande-saldo
Appens domän avsikter bör ha en balansering av yttranden för varje avsikt. Det finns ingen avsikt med 10 yttranden och en annan avsikt med 500 yttranden. Detta är inte balanserade. Om du har den här situationen kan du granska avsikten med 500 yttranden för att se om många av syftet kan omorganiseras i ett [mönster](luis-concept-patterns.md).

**None** -avsikten ingår inte i saldot. Avsikten bör innehålla 10% av den totala yttranden i appen.

## <a name="intent-limits"></a>Begränsningar för avsikt
Granska [gränser](luis-limits.md#model-boundaries) för att förstå hur många avsikter du kan lägga till i en modell.

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Om du behöver fler än det högsta antalet syften
Överväg först om systemet använder för många avsikter.

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Kan flera avsikter kombineras till en enda avsikt med entiteter
Avsikter som är för likt kan göra det svårare för LUIS att skilja mellan dem. Avsikter bör vara tillräckligt stort för att fånga de huvudsakliga uppgifter som användaren efterfrågar, men de behöver inte samla in alla sökvägar som koden tar. Till exempel kan BookFlight och FlightCustomerService vara separata intentor i en rese app, men BookInternationalFlight och BookDomesticFlight är för likartade. Om systemet behöver särskilja dem använder du entiteter eller annan logik i stället för avsikter.

### <a name="dispatcher-model"></a>Dispatcher-modell
Lär dig mer om att kombinera LUIS-och QnA Maker-appar med [sändnings modellen](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps).

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Be om hjälp för appar med ett stort antal avsikter
Kontakta supporten om du minskar antalet syften eller delar upp dina avsikter i flera appar. Om din Azure-prenumeration innehåller support tjänster kontaktar du [teknisk support för Azure](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [entiteter](luis-concept-entity-types.md)som är viktiga ord som är relevanta för avsikter
* Lär dig hur du [lägger till och hanterar avsikter](luis-how-to-add-intents.md) i din Luis-app.
* Granska [metod tips](luis-concept-best-practices.md)