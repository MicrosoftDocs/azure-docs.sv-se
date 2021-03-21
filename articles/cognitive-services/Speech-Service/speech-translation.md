---
title: Översikt över tal översättning – tal tjänst
titleSuffix: Azure Cognitive Services
description: Tal översättning gör att du kan lägga till data översättning från slut punkt till slut punkt, stöd för flera språk i real tid till dina program, verktyg och enheter. Samma API kan användas för både tal till tal- och tal till text-översättning. Den här artikeln är en översikt över fördelarna och funktionerna i röst översättnings tjänsten.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: erhopf
ms.custom: devx-track-csharp, cog-serv-seo-aug-2020
keywords: tal Översättning
ms.openlocfilehash: 36c2943d7cd5ce2c5c7c4a8501dc0033aa8c9cbc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95015213"
---
# <a name="what-is-speech-translation"></a>Vad är talöversättning?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

I den här översikten får du lära dig mer om fördelarna och funktionerna i tal översättnings tjänsten, vilket möjliggör real tids-och tal-till-text-översättning i real tid av ljud strömmar. Med tal-SDK har dina program, verktyg och enheter åtkomst till käll avskrifter och översättnings utdata för tillhandahållen ljud. Interimistiska avskrifter och översättnings resultat returneras som tal identifieras och slutgiltiga resultat kan konverteras till syntetiskt tal.

Microsofts översättnings motor drivs av två olika metoder: statistisk maskin översättning (SMT) och neurala Machine Translation (NMT). SMT använder avancerad statistisk analys för att uppskatta de bästa möjliga översättningarna med några få ord. Med NMT används neurala-nätverk för att tillhandahålla mer exakta, naturligt förekommande översättningar genom att använda den fullständiga kontexten för meningar för att översätta ord.

Idag använder Microsoft NMT för översättning till de flesta populära språken. Alla [språk som är tillgängliga för översättning av tal till tal](language-support.md#speech-translation) drivs av NMT. Översättning av tal till text kan använda SMT-eller NMT beroende på språk paret. När mål språket stöds av NMT, är den fullständiga översättningen NMT. När mål språket inte stöds av NMT, är översättningen en hybrid av NMT och SMT, med engelska som en "Pivot"-lösning mellan de två språken.

## <a name="core-features"></a>Kärn funktioner

* Översättning av tal till text med igenkännings resultat.
* Översättning av tal till tal.
* Stöd för översättning till flera mål språk.
* Resultat av löpande igenkänning och översättning.

## <a name="get-started"></a>Kom igång 

Se [snabb start](get-started-speech-translation.md) för att komma igång med tal översättning. Röst översättnings tjänsten är tillgänglig via [talet SDK](speech-sdk.md) och tal- [CLI](spx-overview.md).

## <a name="sample-code"></a>Exempelkod

Exempel koden för talet SDK finns på GitHub. De här exemplen beskriver vanliga scenarier som att läsa ljud från en fil eller ström, kontinuerlig och enkel igenkänning/översättning och arbeta med anpassade modeller.

* [Exempel på tal-till-text-och översättning (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Migreringsguider

Om dina program, verktyg eller produkter använder [Translator Speech API](./how-to-migrate-from-translator-speech-api.md)har vi skapat guider som hjälper dig att migrera till tal tjänsten.

* [Migrera från Translator Speech API till tal tjänsten](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Referens dokument

* [Speech SDK](./speech-sdk.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: tal till text](rest-speech-to-text.md)
* [REST API: text till tal](rest-text-to-speech.md)
* [REST API: batch-avskrift och anpassning](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)

## <a name="next-steps"></a>Nästa steg

* Slutför [snabb](get-started-speech-translation.md) starten för tal omvandling
* [Hämta en prenumerations nyckel för Speech service kostnads fritt](overview.md#try-the-speech-service-for-free)
* [Hämta tal-SDK](speech-sdk.md)