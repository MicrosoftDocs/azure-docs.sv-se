---
title: Vad är Azure Content Moderator?
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder Content Moderator för att spåra, flagga, utvärdera och filtrera olämpligt material i användargenererat innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 04/16/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: content moderator, azure content moderator, online moderator, content filtering software, content moderation service, content moderation
ms.openlocfilehash: b0ff5a241a76cd49d104e4145df37515b43c4e27
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726204"
---
# <a name="what-is-azure-content-moderator"></a>Vad är Azure Content Moderator?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Content Moderator är en AI-tjänst som gör att du kan hantera innehåll som är potentiellt stötande, riskabelt eller på annat sätt oönskat. Den innehåller den AI-baserade innehållsmodereringstjänsten som söker igenom text, bilder och videor och tillämpar innehållsflaggor automatiskt, samt granskningsverktyget, en moderatormiljö online för ett team med mänskliga granskare.

Du kanske vill skapa programvara för innehållsfiltrering i din app för att uppfylla kraven eller upprätthålla den avsedda miljön för dina användare.

Den här dokumentationen innehåller följande artikeltyper:  

* [**Snabbstarter**](client-libraries.md) är komma igång-instruktioner som vägleder dig genom att göra begäranden till tjänsten.  
* [**Instruktionsguider**](try-text-api.md) innehåller instruktioner för att använda tjänsten på mer specifika eller anpassade sätt.  
* [**Begrepp**](text-moderation-api.md) ger djupgående förklaringar av tjänstens funktioner och funktioner.  
* [**Självstudier**](ecommerce-retail-catalog-moderation.md) är längre guider som visar hur du använder tjänsten som en komponent i bredare affärslösningar.  

## <a name="where-its-used"></a>Var används det

Följande är några scenarier där en programutvecklare eller ett team behöver en tjänst för innehållsmoderering:

- Marknadsplatser online som modererar produktkataloger och annat användargenererat innehåll.
- Spelföretag som modererar användargenererade spelartefakter och chattrum.
- Plattformar för sociala meddelanden som modererar bilder, text och videor som läggs till av användarna.
- Medieföretag som implementerar centraliserad moderering för sitt innehåll.
- K-12-utbildningslösningsleverantörer filtrerar bort innehåll som är olämpligt för elever och lärare.

> [!IMPORTANT]
> Du kan inte använda Content Moderator för att identifiera avbildningar av otillåtet utnyttjande av barn. Kvalificerade organisationer kan dock använda [PhotoDNA-molntjänsten för att](https://www.microsoft.com/photodna "Microsoft PhotoDNA Cloud Service") skärma för den här typen av innehåll.

## <a name="what-it-includes"></a>Vad verktyget innehåller

Content Moderator-tjänsten består av flera webbtjänst-API:er som är tillgängliga via både REST-anrop och en .NET-SDK. Den innehåller även granskningsverktyget, som gör att mänskliga granskare kan hjälpa tjänsten och förbättra eller finjustera dess modereringsfunktion.

## <a name="moderation-apis"></a>API:er för moderering

Tjänsten Content Moderator innehåller API:er för moderering, som kontrollerar om det finns material som kan vara olämpligt eller stötande.

![blockdiagram för Content Moderator-API:er för moderering](images/content-moderator-mod-api.png)

I följande tabell beskrivs de olika typerna av modererings-API:er.

| API-grupp | Description |
| ------ | ----------- |
|[**Textmoderering**](text-moderation-api.md)| Söker igenom text efter stötande innehåll, sexuellt explicit eller suggestivt innehåll, svordomar och personliga data.|
|[**Anpassade termlistor**](try-terms-list-api.md)| Söker igenom text mot en anpassad lista med termer tillsammans med de inbyggda termerna. Använd anpassade listor för att blockera eller tillåta innehåll baserat på dina egna innehållsprinciper.|  
|[**Bildmoderering**](image-moderation-api.md)| Söker igenom bilder efter vuxet eller olämpligt innehåll, identifierar text i bilder med funktionen för optisk teckenläsning (OCR) och identifierar ansikten.|
|[**Anpassade bildlistor**](try-image-list-api.md)| Söker igenom bilder mot en anpassad lista med bilder. Använd anpassade bildlistor för att filtrera bort instanser av ofta återkommande innehåll som du inte vill klassificera igen.|
|[**Videomoderering**](video-moderation-api.md)| Söker igenom videor efter vuxet eller olämpligt innehåll och returnerar tidsmarkörer för sådant innehåll.|

## <a name="review-apis"></a>Omdömes-API:er

Med gransknings-API:erna kan du integrera din modereringspipeline med mänskliga granskare. Använd åtgärderna [Jobb,](review-api.md#jobs) [Granskningar](review-api.md#reviews)och [Arbetsflöde](review-api.md#workflows) för att skapa och automatisera human-in-the-loop-arbetsflöden med [granskningsverktyget](#review-tool) (nedan).

> [!NOTE]
> Arbetsflödes-API:et är ännu inte tillgängligt i .NET SDK men kan användas med REST-slutpunkten.

![blockdiagram för Content Moderator granska API:er](images/content-moderator-rev-api.png)

## <a name="review-tool"></a>Granskningsverktyg

Tjänsten Content Moderator även det webbaserade granskningsverktyget [,](Review-Tool-User-Guide/human-in-the-loop.md)som är värd för innehållsgranskningar som mänskliga moderatorer kan bearbeta. Mänskliga indata tränar inte tjänsten, men det kombinerade arbetet från tjänsten och mänskliga granskningsteam gör det möjligt för utvecklare att hitta rätt balans mellan effektivitet och noggrannhet. Granskningsverktyget tillhandahåller också en användarvänlig frontend för flera Content Moderator resurser.

![Content Moderator startsidan för granskningsverktyget](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Som med alla Cognitive Services bör utvecklare som använder Content Moderator-tjänsten känna till Microsofts policyer gällande kunddata. Läs mer på [Cognitive Services-sidan](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Kom igång med Content Moderator på webbportalen genom att [följa Prova Content Moderator på webben](quick-start.md). Du kan också slutföra [ett klientbibliotek eller REST API snabbstart för](client-libraries.md) att implementera de grundläggande scenarierna i kod.