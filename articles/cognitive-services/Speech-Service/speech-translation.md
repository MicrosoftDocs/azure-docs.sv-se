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
ms.openlocfilehash: 94ddd06068513261b5b73b313877e273c7251d62
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954969"
---
# <a name="what-is-speech-translation"></a>Vad är talöversättning?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

I den här översikten får du lära dig mer om fördelarna och funktionerna i tal översättnings tjänsten, vilket möjliggör real tids [-och tal-till](language-support.md#speech-translation) -text-översättning i real tid av ljud strömmar. Med tal-SDK har dina program, verktyg och enheter åtkomst till käll avskrifter och översättnings utdata för tillhandahållen ljud. Interimistiska avskrifter och översättnings resultat returneras som tal identifieras och slutgiltiga resultat kan konverteras till syntetiskt tal.

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