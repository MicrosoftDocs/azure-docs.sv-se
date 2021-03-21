---
title: Text förskjutningar i API för textanalys
titleSuffix: Azure Cognitive Services
description: Lär dig mer om förskjutningar som orsakas av flerspråkiga och emoji-kodningar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/09/2020
ms.author: aahi
ms.reviewer: jdesousa
ms.openlocfilehash: f5b63503792b13e089568004ba67e5be8a3d0c7f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98932370"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>Text förskjutningar i API för textanalys utdata

Stöd för flerspråkig och emoji har lett till Unicode-kodningar som använder mer än en [kod punkt](https://wikipedia.org/wiki/Code_point) för att representera ett enda visat tecken, som kallas en Grapheme. Exempel: emojis som 🌷 och 👍 kan använda flera tecken för att skapa formen med ytterligare tecken för visuella attribut, till exempel hudton. På samma sätt kodas hindi-ordet `अनुच्छेद` som fem bokstäver och tre kombinations märken.

På grund av olika längd på möjliga flerspråkiga och emoji-kodningar kan API för textanalys returnera förskjutningar i svaret.

## <a name="offsets-in-the-api-response"></a>Förskjutningar i API-svaret. 

Tänk på följande när offsets returnerade API-svar, till exempel [igenkänning av namngivna enheter](../how-tos/text-analytics-how-to-entity-linking.md) eller [Attitydanalys](../how-tos/text-analytics-how-to-sentiment-analysis.md):

* Element i svaret kan vara särskilt för den slut punkt som anropades. 
* HTTP POST/Hämta nytto laster kodas i [UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp), vilket kan vara standard tecken kodningen på klient sidans kompilator eller operativ system.
* Offsets avser antalet Grapheme som baseras på [Unicode-8.0.0](https://unicode.org/versions/Unicode8.0.0) standard, inte tecken antal.

## <a name="extracting-substrings-from-text-with-offsets"></a>Extrahera del strängar från text med förskjutningar

Förskjutningar kan orsaka problem när du använder teckenbaserade under sträng metoder, till exempel metoden .NET [substring ()](/dotnet/api/system.string.substring) . Ett problem är att en förskjutning kan leda till att en del Strängs metod avslutas i mitten av en Grapheme kodning i flera tecken i stället för slutet.

I .NET bör du överväga att använda klassen [StringInfo](/dotnet/api/system.globalization.stringinfo) , som gör att du kan arbeta med en sträng som en serie text element, i stället för enskilda tecken objekt. Du kan också söka efter Grapheme delarer bibliotek i din önskade program miljö. 

API för textanalys returnerar även dessa text element, för bekvämlighet.

## <a name="offsets-in-api-version-31-preview"></a>Förskjutningar i API-version 3,1-för hands version

Från och med API version 3,1 – för hands version. 1, kommer alla API för textanalys slut punkter som returnerar en förskjutning att stödja `stringIndexType` parametern. Den här parametern justerar `offset` `length` attributen och i API-utdata för att matcha det begärda sträng upprepnings schemat. För närvarande stöder vi tre typer:

1. `textElement_v8` (standard): iterer över graphemes enligt definitionen i [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0) -standarden
2. `unicodeCodePoint`: iterer över [Unicode-kodfragment](http://www.unicode.org/versions/Unicode13.0.0/ch02.pdf#G25564), standard schema för python 3
3. `utf16CodeUnit`: iterer över [UTF-16-kodfragment](https://unicode.org/faq/utf_bom.html#UTF16), standard schema för Java Script, Java och .net

Om det `stringIndexType` begärda matchar den programmerings miljö du väljer kan del sträng extrahering göras med hjälp av standard under sträng eller sektor metoder. 

## <a name="see-also"></a>Se även

* [Översikt över Textanalys](../overview.md)
* [Attitydanalys](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Enhets igenkänning](../how-tos/text-analytics-how-to-entity-linking.md)
* [Identifiera språk](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Språk igenkänning](../how-tos/text-analytics-how-to-language-detection.md)
