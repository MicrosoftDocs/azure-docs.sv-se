---
title: Migrera från Taligenkänning i Bing till tal-tjänsten
titleSuffix: Azure Cognitive Services
description: Lär dig hur du migrerar från en befintlig Taligenkänning i Bing-prenumeration till tal tjänsten från Azure Cognitive Services.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: nitinme
ms.openlocfilehash: d6b7b298e7e4d18e68985ec3cf536c90ef773608
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644389"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migrera från Taligenkänning i Bing till tal tjänsten

Använd den här artikeln för att migrera dina program från API för Bing-taligenkänning till tal tjänsten.

Den här artikeln beskriver skillnaderna mellan Taligenkänning i Bing-API: er och tal tjänsten och ger förslag på strategier för att migrera dina program. Din API för Bing-taligenkänning prenumerations nyckel fungerar inte med tal tjänsten. du behöver en ny röst tjänst prenumeration.

En prenumerations nyckel för en enskild röst tjänst ger åtkomst till följande funktioner. Varje funktion mäts separat, så att du bara debiteras för de funktioner du använder.

* [Tal till text](speech-to-text.md)
* [Anpassat tal till text](/azure/cognitive-services/speech-service/custom-speech-overview)
* [Text till tal](text-to-speech.md)
* [Anpassad text till talade röster](./how-to-custom-voice-create-voice.md)
* [Talöversättning](speech-translation.md) (omfattar inte [textöversättning](../translator/translator-info-overview.md))

[Talet SDK](speech-sdk.md) är en funktionell ersättning för taligenkänning i Bing klient bibliotek, men använder ett annat API.

## <a name="comparison-of-features"></a>Jämförelse av funktioner

Tal tjänsten liknar Taligenkänning i Bing, med följande skillnader.

| Funktion | Bing-taligenkänning | Tjänst för taligenkänning | Information |
|--|--|--|--|
| C#-SDK | :heavy_check_mark: | :heavy_check_mark: | Speech service stöder Windows 10, Universell Windows-plattform (UWP) och .NET standard 2,0. |
| C++ SDK | : heavy_minus_sign: | :heavy_check_mark: | Speech service stöder Windows och Linux. |
| Java SDK | :heavy_check_mark: | :heavy_check_mark: | Tal tjänsten stöder Android-och tal enheter. |
| Kontinuerlig tal igenkänning | 10 minuter | Obegränsat | Tal-SDK: n stöder obegränsad kontinuerlig igenkänning och återansluter automatiskt vid timeout eller från koppling. |
| Partiella eller interimistiska resultat | :heavy_check_mark: | :heavy_check_mark: | Stöds med talet SDK. |
| Anpassade tal modeller | :heavy_check_mark: | :heavy_check_mark: | Taligenkänning i Bing kräver en separat Custom Speech prenumeration. |
| Anpassade röst teckensnitt | :heavy_check_mark: | :heavy_check_mark: | Taligenkänning i Bing kräver en separat anpassad röst prenumeration. |
| 24-kHz-röster | : heavy_minus_sign: | :heavy_check_mark: |
| Igenkänning av tal avsikt | Kräver separat LUIS-API-anrop | Integrerad (med SDK) | Du kan använda en LUIS-nyckel med tal tjänsten. |
| Enkel avsikts igenkänning | : heavy_minus_sign: | :heavy_check_mark: |
| Batch-avskrift av långa ljudfiler | : heavy_minus_sign: | :heavy_check_mark: |
| Igenkänningsläge | Manuell via slut punkts-URI | Automatiskt | Igenkännings läget är inte tillgängligt i tal tjänsten. |
| Slut punkts plats | Global | Regional | Regionala slut punkter förbättrar svars tiden. |
| REST API:er | :heavy_check_mark: | :heavy_check_mark: | Tal service REST-API: er är kompatibla med Taligenkänning i Bing (annan slut punkt). REST-API: er stöder text-till-tal och begränsade tal-till-text-funktioner. |
| WebSockets-protokoll | :heavy_check_mark: | : heavy_minus_sign: | Talet SDK sammanfattar WebSocket-anslutningar för funktioner som kräver en konstant anslutning till tjänsten, så det finns inte längre stöd för att prenumerera på dem manuellt. |
| API-anrop från tjänst till tjänst | :heavy_check_mark: | : heavy_minus_sign: | Tillhandahålls i Taligenkänning i Bing via C#-tjänst biblioteket. |
| SDK för öppen källkod | :heavy_check_mark: | : heavy_minus_sign: |

Tal tjänsten använder en tidsbaserad pris modell (i stället för en transaktions baserad modell). Mer information finns i pris information om [Speech service](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

## <a name="migration-strategies"></a>Migreringsstrategier

Om du eller din organisation har program som är i utvecklings-eller produktions miljön som använder en API för Bing-taligenkänning bör du uppdatera dem så att de använder röst tjänsten så snart som möjligt. I [dokumentationen för tal tjänsten](index.yml) finns information om tillgängliga SDK: er, kod exempel och självstudier.

Tal service [REST-API: er](./overview.md#reference-docs) är kompatibla med taligenkänning i Bing-API: er. Om du för närvarande använder Taligenkänning i Bing REST-API: er behöver du bara ändra REST-slutpunkten och byta till en prenumerations nyckel för röst tjänst.

Om du använder ett Taligenkänning i Bing klient bibliotek för ett särskilt programmeringsspråk, kräver migrering till tal- [SDK](speech-sdk.md) ändringar i programmet, eftersom API: et skiljer sig. Talet SDK kan göra din kod enklare, samtidigt som du ger dig till gång till nya funktioner. Talet SDK är tillgängligt i många olika programmeringsspråk. API: er på alla plattformar liknar varandra, med flera plattformar.

Tal tjänsten erbjuder inte en global slut punkt. Ta reda på om programmet fungerar effektivt när det använder en enda regional slut punkt för all trafik. Om inte, Använd geolokalisering för att fastställa den effektiva slut punkten. Du behöver en separat röst tjänst prenumeration i varje region som du använder.

Kom igång med talet SDK:

1. Hämta [tal-SDK: n](speech-sdk.md).
1. Arbeta via [Start guiderna](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) för tal service och [självstudier](how-to-recognize-intents-from-speech-csharp.md). Titta även på [kod exemplen](./speech-sdk.md#sample-source-code) för att få erfarenhet av de nya API: erna.
1. Uppdatera programmet för att använda tal tjänsten.

## <a name="support"></a>Support

Taligenkänning i Bing kunder kontaktar kund support genom att öppna ett [support ärende](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Du kan också kontakta oss om support behovet kräver en [teknisk Support plan](https://azure.microsoft.com/support/plans/).

För tal service, SDK och API-stöd går du till [support Sidan](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)för Speech service.

## <a name="next-steps"></a>Nästa steg

* [Prova röst tjänsten kostnads fritt](overview.md#try-the-speech-service-for-free)
* [Komma igång med tal-till-text](get-started-speech-to-text.md)
* [Komma igång med text till tal](get-started-text-to-speech.md)

## <a name="see-also"></a>Se även

* [Viktig information om Speech service](releasenotes.md)
* [Vad är tal tjänsten](overview.md)
* [Dokumentation om Speech service och Speech SDK](speech-sdk.md#get-the-speech-sdk)
