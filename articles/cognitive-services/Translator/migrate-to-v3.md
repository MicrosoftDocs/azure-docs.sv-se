---
title: Migrera till v3-Translator
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller de steg som hjälper dig att migrera från v2 till v3 i Azure Cognitive Services Translator.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 13c4d39284fad293c945f8b7e31076dccee84fda
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896841"
---
# <a name="translator-v2-to-v3-migration"></a>Translator v2 till v3-migrering

> [!NOTE]
> V2 föråldrades den 30 april 2018. Migrera dina program till v3 för att kunna dra nytta av nya funktioner som är tillgängliga exklusivt i v3. V2 kommer att dras tillbaka den 24 maj 2021. 

Microsoft Translator-teamet har publicerat version 3 (v3) av Translator. Den här versionen innehåller nya funktioner, föråldrade metoder och ett nytt format för att skicka till och ta emot data från tjänsten Microsoft Translator. Det här dokumentet innehåller information om hur du ändrar program till att använda v3. 

Slutet av det här dokumentet innehåller användbara länkar som hjälper dig att lära dig mer.

## <a name="summary-of-features"></a>Sammanfattning av funktioner

* Ingen spårnings-i v3-No-Trace gäller för alla pris nivåer i Azure Portal. Den här funktionen innebär att ingen text som skickas till v3-API: t sparas av Microsoft.
* JSON-XML har ersatts av JSON. Alla data som skickas till tjänsten och tas emot från tjänsten är i JSON-format.
* Flera mål språk i en enskild begäran – översättnings metoden accepterar flera till-språk för översättning i en enskild begäran. Till exempel kan en enskild begäran vara från engelska och till tyska, spanska och japanska eller någon annan grupp av språk.
* Tvåspråkig ord lista – en tvåspråkig ordboks metod har lagts till i API: et. Den här metoden inkluderar "Lookup" och "exempel".
* Translittererad till en translittererad-metod har lagts till i API: et. Med den här metoden konverteras ord och meningar i ett skript (t. ex. Arabic) i ett annat skript (t. ex. Latin).
* Språk – en ny "språk"-metod levererar språk information i JSON-format för användning med metoderna "Översätt", "dictionary" och "translittererad".
* Nytt att översätta – nya funktioner har lagts till i "Översätt"-metoden för att stödja vissa av funktionerna i v2-API: et som separata metoder. Ett exempel är TranslateArray.
* Speak-metoden – text till tal-funktionen stöds inte längre i Microsoft Translator. Text till tal-funktionen finns i [Microsoft Speech service](../speech-service/text-to-speech.md).

I följande lista med v2-och v3-metoder identifieras de v3-metoder och API: er som tillhandahåller de funktioner som medföljde v2.

| V2 API-metod   | V3 API-kompatibilitet |
|:----------- |:-------------|
| `Translate`     | [Översätta](reference/v3-0-translate.md)          |
| `TranslateArray`      | [Översätta](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [Språk](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [Språk](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Microsoft Speech service](../speech-service/language-support.md#text-to-speech)         |
| `Speak`     | [Microsoft Speech service](../speech-service/text-to-speech.md)          |
| `Detect`     | [Identifiering](reference/v3-0-detect.md)         |
| `DetectArray`     | [Identifiering](reference/v3-0-detect.md)         |
| `AddTranslation`     | Funktionen stöds inte längre       |
| `AddTranslationArray`    | Funktionen stöds inte längre          |
| `BreakSentences`      | [BreakSentence](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | Funktionen stöds inte längre         |
| `GetTranslationsArray`      | Funktionen stöds inte längre         |

## <a name="move-to-json-format"></a>Flytta till JSON-format

Microsoft Translator översättning v2 accepterade och returnerade data i XML-format. I v3 är alla data som skickas och tas emot med API: t i JSON-format. XML kommer inte längre att accepteras eller returneras i v3.

Den här ändringen påverkar flera delar av ett program som är skrivet för API: et för text översättning i v2. Exempel: språk-API: t returnerar språk information för text översättning, transkriberingsspråk och de två lexikon metoderna. Du kan begära all språk information för alla metoder i ett anrop eller begära dem individuellt.

Språk metoden kräver inte autentisering. genom att klicka på följande länk kan du se all språk information för v3 i JSON:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation, ord lista, transkriberingsspråk](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Autentiseringsnyckel

Den autentiseringsnyckel som du använder för v2 kommer att accepteras för v3. Du behöver inte skaffa någon ny prenumeration. Du kommer att kunna blanda v2 och v3 i dina appar under migreringen av yearlong, vilket gör det enklare för dig att frigöra nya versioner medan du fortfarande migrerar från v2-XML till v3-JSON.

## <a name="pricing-model"></a>Pris modell

Microsoft Translator v3 är priss ätts på samma sätt som v2. per tecken, inklusive blank steg. De nya funktionerna i v3 gör vissa ändringar i vilka tecken som räknas för fakturering.

| V3-metod   | Antal tecken för fakturering |
|:----------- |:-------------|
| `Languages`     | Inga tecken har skickats, inget antal, ingen avgift.          |
| `Translate`     | Count baseras på hur många tecken som skickas för översättning och hur många språk som tecknen översätts till. 50 tecken har skickats och 5 efterfrågade språk kommer att vara 50x5.           |
| `Transliterate`     | Antalet tecken som skickats för transkriberingsspråk räknas.         |
| `Dictionary lookup & example`     | Antalet tecken som har skickats för sökning efter ord lista och exempel räknas.         |
| `BreakSentence`     | Ingen avgift.       |
| `Detect`     | Ingen avgift.      |

## <a name="v3-end-points"></a>V3-slut punkter

Global

* api.cognitive.microsofttranslator.com

## <a name="v3-api-text-translations-methods"></a>Text översättnings metoder för v3-API

[`Languages`](reference/v3-0-languages.md)

[`Translate`](reference/v3-0-translate.md)

[`Transliterate`](reference/v3-0-transliterate.md)

[`BreakSentence`](reference/v3-0-break-sentence.md)

[`Detect`](reference/v3-0-detect.md)

[`Dictionary/lookup`](reference/v3-0-dictionary-lookup.md)

[`Dictionary/example`](reference/v3-0-dictionary-examples.md)

## <a name="compatibility-and-customization"></a>Kompatibilitet och anpassning

> [!NOTE]
> 
> Microsoft Translator-hubben kommer att dras tillbaka den 17 maj 2019. [Visa viktig information och datum för migreringen](https://www.microsoft.com/translator/business/hub/).   

Microsoft Translator v3 använder neurala maskin översättning som standard. Den kan därför inte användas med Microsoft Translator Hub. Translator-navet stöder endast äldre statistik för maskin översättning. Anpassningen av neurala-översättning är nu tillgänglig med hjälp av den anpassade översättare. [Lär dig mer om att anpassa neurala Machine Translation](custom-translator/overview.md)

Neurala-översättning med v3-text-API: t stöder inte användning av standard kategorier (SMT, tal, Tech, generalnn).

| Version | Slutpunkt | Kompatibilitet för GDPR-processor | Använd Translator Hub | Använd anpassad översättare (förhands granskning) |
| :------ | :------- | :------------------------ | :----------------- | :------------------------------ |
|Översättare, version 2|    api.microsofttranslator.com|    Inga    |Ja    |Inga|
|Översättare, version 3|    api.cognitive.microsofttranslator.com|    Ja|    Inga|    Ja|

**Översättare, version 3**
* Är allmänt tillgänglig och stöds fullt ut.
* Är GDPR-kompatibel som en processor och uppfyller alla ISO 20001 och 20018 samt SOC 3-certifierings krav. 
* Gör att du kan anropa de neurala-nätverks översättnings system som du har anpassat med anpassad översättare (för hands version), den nya funktionen Translator NMT anpassning. 
* Ger inte åtkomst till anpassade översättnings system som skapats med Microsoft Translator Hub.

Du använder version 3 av Translator om du använder api.cognitive.microsofttranslator.com-slutpunkten.

**Översättare, version 2**
* Uppfyller inte alla certifierings krav för ISO-20001, 20018 och SOC 3. 
* Tillåter inte att du anropar de neurala-nätverks översättnings system som du har anpassat med funktionen Translator-anpassning.
* Ger åtkomst till anpassade översättnings system som skapats med Microsoft Translator Hub.
* Du använder version 2 av Translator om du använder api.microsofttranslator.com-slutpunkten.

Ingen version av Translator skapar en post med dina översättningar. Dina översättningar delas aldrig med någon. Mer information på webb sidan [Translator-trace](https://www.aka.ms/NoTrace) .

## <a name="links"></a>Länkar

* [Microsoft sekretesspolicy](https://privacy.microsoft.com/privacystatement)
* [Microsoft Azure juridisk information](https://azure.microsoft.com/support/legal)
* [Villkor för onlinetjänster](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Visa V 3.0-dokumentation](reference/v3-0-reference.md)
