---
title: Vad är Translator? -Translator
titlesuffix: Azure Cognitive Services
description: Integrera översättaren i dina program, webbplatser, verktyg och andra lösningar för att tillhandahålla användar upplevelser med flera språk.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 05/26/2020
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: 5da9e5b38b88a5a148bd3d85664daa523153086b
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589460"
---
# <a name="what-is-the-translator"></a>Vad är Translator?

Översättningen är enkel att integrera i dina program, webbplatser, verktyg och lösningar. Det gör att du kan lägga till användar upplevelser med flera språk på [över 70 språk](languages.md)och kan användas på alla maskin varu plattformar med valfritt operativ system för översättning av text till text-språk.

Översättaren är en del av [Azure Cognitive Services](https://docs.microsoft.com/azure/?pivot=products&panel=ai) -samlingen av Machine Learning-och AI-algoritmer i molnet och är lätt att använda i dina utvecklings projekt.

## <a name="about-microsoft-translator"></a>Om Microsoft Translator

Translator är en molnbaserad maskin översättnings tjänst. Kärn tjänsten är översättaren, vilket ger en mängd olika Microsoft-produkter och-tjänster och används av tusentals företag i hela världen i sina program och arbets flöden, vilket ger sitt innehåll möjlighet att komma åt en global publik.

Tal översättning, som drivs av Translator, är också tillgängligt via [Microsoft Speech service](https://docs.microsoft.com/azure/cognitive-services/speech-service/). Den kombinerar funktioner från Translator Speech API och Custom Speech Service till en enhetlig och helt anpassningsbar tjänst.Speech Service ersätter Translator Speech API, som tas ur drift 15 oktober 2019.

## <a name="language-support"></a>Stöd för språk

Microsoft Translator ger flerspråksstöd för översättning, translitteration, språkidentifiering och ordlistor. Se [språkstöd](language-support.md) för en fullständig lista, eller kom åt listan programmatiskt med [REST API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).  

## <a name="microsoft-translator-neural-machine-translation"></a>Neural maskinöversättning med Microsoft Translator

Neural maskinöversättning är den nya standarden för högkvalitativ AI-driven maskinöversättning. Den ersätter den äldre statistiska maskinöversättningen, som nådde en kvalitetsplatå i mitten av 2010-talet.

Neural maskinöversättning ger bättre översättningar än den statistiska varianten. Den ger bättre överensstämmelse betydelsemässigt och låter dessutom mer mänsklig. Den viktigaste orsaken till det är att hela meningskontexten används när ord översätts. I den statistiska översättningen användes bara direkta samband med några få ord före och efter varje ord.

Neurala modeller är kärnan i API:et och visas inte för slutanvändaren. Den enda märkbara skillnaden är förbättrad översättning, och särskilt för språk som arabiska, kinesiska och japanska.

Läs mer om [hur neural maskinöversättning fungerar](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="language-customization"></a>Språkanpassning

En utökning av tjänsten Microsoft Translator, anpassad översättare kan användas tillsammans med Translator för att anpassa neurala översättnings systemet och förbättra översättningen för din speciella terminologi och stil.

Med den anpassade översättningstjänsten kan du skapa översättningssystem som hanterar den terminologi som används inom företaget eller branschen. Det anpassade översättnings systemet integreras sedan enkelt i dina befintliga program, arbets flöden och webbplatser på flera olika typer av enheter, via den vanliga översättaren, med hjälp av parametern Category.

Läs mer om [språkanpassning](customization.md)

## <a name="next-steps"></a>Nästa steg

- [Registrera dig](translator-text-how-to-signup.md) för en åtkomst nyckel.
- [API-referensen](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) innehåller den tekniska dokumentationen för API:erna.
- [Prisinformation](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
