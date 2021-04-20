---
title: Begärandegränser – Translator
titleSuffix: Azure Cognitive Services
description: Den här artikeln listar begärandebegränsningar för Translator. Avgifter debiteras baserat på antal tecken, inte begärandefrekvens med en gräns på 5 000 tecken per begäran. Teckenbegränsningar är prenumerationsbaserade, med F0 begränsat till 2 miljoner tecken per timme.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: lajanuar
ms.openlocfilehash: b5beb222ec20b1e7941f9438c0aacf98879a567a
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727950"
---
# <a name="request-limits-for-translator"></a>Begärandegränser för Translator

Den här artikeln innehåller begränsningar för översättning, transkribering, meningslängdsidentifiering, språkidentifiering och alternativa översättningar.

## <a name="character-and-array-limits-per-request"></a>Tecken- och matrisgränser per begäran

Varje översättningsbegäran är begränsad till 10 000 tecken, över alla målspråk som du översätter till. Om du till exempel skickar en översättningsbegäran på 3 000 tecken för att översätta till tre olika språk resulterar det i en begärandestorlek på 3 000 x 3 = 9 000 tecken, vilket uppfyller begärandegränsen. Du debiteras per tecken, inte efter antalet begäranden. Vi rekommenderar att du skickar kortare begäranden.

I följande tabell visas matriselement och teckenbegränsningar för varje åtgärd i Translator.

| Åtgärd | Maximal storlek för matriselement |    Maximalt antal matriselement |    Maximal begärandestorlek (tecken) |
|:----|:----|:----|:----|
| Översätt | 10 000| 100| 10 000 |
| Transkribera | 5 000| 10| 5 000 |
| Upptäcka | 50 000 |100 |50 000 |
| BreakSentence | 50 000| 100 |50 000 |
| Slå upp i ordlista| 100 |10| 1 000 |
| Ordlisteexempel | 100 för text och 100 för översättning (200 totalt)| 10|2 000 |

## <a name="character-limits-per-hour"></a>Teckenbegränsningar per timme

Teckengränsen per timme baseras på din Translator-prenumerationsnivå.

Timkvoten ska förbrukas jämnt under timmen. Till exempel bör tecken med en gräns på 2 miljoner tecken per timme på F0-nivån inte förbrukas snabbare än cirka 33 300 tecken per minut (2 miljoner tecken dividerat med 60 minuter).

Om du når eller överskrider dessa gränser eller skickar för stor andel av kvoten under en kort tidsperiod får du troligen ett svar om att kvoten är slut. Det finns inga begränsningar för samtidiga begäranden.

| Nivå | Teckengräns |
|------|-----------------|
| F0 | 2 miljoner tecken per timme |
| S1 | 40 miljoner tecken per timme |
| S2/C2 | 40 miljoner tecken per timme |
| S3/C3 | 120 miljoner tecken per timme |
| S4/C4 | 200 miljoner tecken per timme |

Gränserna för [prenumerationer med flera tjänster](./reference/v3-0-reference.md#authentication) är desamma som för S1-nivån.

Dessa gränser är begränsade till Microsofts standardöversättningsmodeller. Anpassade översättningsmodeller som använder Custom Translator är begränsade till 1 800 tecken per sekund och per modell.

## <a name="latency"></a>Svarstid

Translator har en maximal svarstid på 15 sekunder med standardmodeller och 120 sekunder när du använder anpassade modeller. Normalt returneras svar för text inom *100* tecken på 150 millisekunder till 300 millisekunder. De anpassade översättningsmodellerna har liknande svarstidsegenskaper för varaktig begärandefrekvens och kan ha en högre svarstid när din begärandefrekvens är tillfällig. Svarstiderna varierar beroende på storleken på begäran och språkparet. Om du inte får någon [](./reference/v3-0-reference.md#errors) översättning eller ett felsvar inom den tidsramen kontrollerar du koden, nätverksanslutningen och försöker igen.

## <a name="sentence-length-limits"></a>Meningslängdsbegränsningar

När du använder [funktionen BreakSentence](./reference/v3-0-break-sentence.md) är meningslängden begränsad till 275 tecken. Det finns undantag för dessa språk:

| Språk | Kod | Teckengräns |
|----------|------|-----------------|
| Kinesiska | Zh | 166 |
| Tyska | de | 800 |
| Italienska | it | 800 |
| Japanska | ja | 166 |
| Portugisiska | pt | 800 |
| Spanska | es | 800 |
| Thailändska | Th | 180 |

> [!NOTE]
> Den här gränsen gäller inte för översättningar.

## <a name="next-steps"></a>Nästa steg

* [Prissättning](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Regional tillgänglighet](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [Referens för Translator v3](./reference/v3-0-reference.md)