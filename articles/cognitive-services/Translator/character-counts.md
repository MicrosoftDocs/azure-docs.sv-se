---
title: Antal tecken – Translator
titleSuffix: Azure Cognitive Services
description: Den här artikeln förklarar hur Azure Cognitive Services Translator räknar tecken så att du förstår hur det matar in innehåll.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 53fc22e1dbdac3240f72e8d64fbaee690597950f
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107373935"
---
# <a name="how-the-translator-counts-characters"></a>Så räknar Translator tecken

Translator räknar varje Unicode-kodpunkt i indatatext som ett tecken. Varje översättning av en text till ett språk räknas som en separat översättning, även om begäran gjordes i ett enda API-anrop som översätter till flera språk. Längden på svaret spelar ingen roll.

Vad är antalet:

* Text som skickas till Translator i brödtexten i begäran
   * `Text` när du använder metoderna Translate, Transliterate och Dictionary Lookup
   * `Text` och `Translation` när du använder metoden Ordlisteexempel
* All kod: HTML, XML-taggar osv. i textfältet i begärandetexten. JSON-notation som används för att skapa begäran (till exempel "Text:") räknas inte.
* En enskild bokstav
* Skiljetecken
* Blanksteg, tabbar, markeringar och alla typer av blanksteg
* Varje kodpunkt som definieras i Unicode
* En upprepad översättning, även om du har översatt samma text tidigare

För skript som baseras på ideogram som kinesiska och japanska Kanji räknar Translator-tjänsten fortfarande antalet Unicode-kodpunkter, ett tecken per ideogram. Undantag: Unicode-surrogat räknas som två tecken.

Antalet begäranden, ord, byte eller meningar är irrelevant i antalet tecken.

Anrop till metoderna Detect och BreakSentence räknas inte i teckenförbrukningen. Vi förväntar oss dock att anropen till metoderna Detect och BreakSentence är i en rimlig proportion till användningen av andra funktioner som räknas. Om antalet identifierings- eller BreakSentence-anrop du gör överskrider antalet andra räknade metoder med 100 gånger, förbehåller sig Microsoft rätten att begränsa din användning av metoderna Detect och BreakSentence.

Alla tecken som skickas till translate-funktionen räknas även om innehållet inte ändras eller när käll- och målspråket är samma.

Mer information om antal tecken finns i Vanliga frågor [och svar om Translator.](https://www.microsoft.com/en-us/translator/faq.aspx)
