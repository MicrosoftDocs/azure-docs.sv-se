---
title: Tjänsten Microsoft Translator
titlesuffix: Azure Cognitive Services
description: Integrera översättare i dina program, webbplatser, verktyg och andra lösningar för att tillhandahålla användar upplevelser med flera språk.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 09/11/2020
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: översättare, text översättning, maskin översättning, översättnings tjänst
ms.openlocfilehash: 37f0d64879493abcf3e5b9448068b86b7a36dccb
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896705"
---
# <a name="what-is-the-translator-service"></a>Vad är tjänsten Translator?

Translator är en molnbaserad maskin översättnings tjänst och ingår i [Azure Cognitive Services](../../index.yml?panel=ai&pivot=products) -familjen av kognitiva API: er som används för att bygga smarta appar. Translator är enkelt att integrera i dina program, webbplatser, verktyg och lösningar. Det gör att du kan lägga till användar upplevelser med flera språk på [fler än 70 språk](./language-support.md)och kan användas på alla maskin varu plattformar med alla operativ system för text översättning.

## <a name="about-microsoft-translator"></a>Om Microsoft Translator

Translator har ett antal Microsoft-produkter och-tjänster och används av tusentals företag i hela världen i sina program och arbets flöden, vilket ger sitt innehåll möjlighet att komma åt en global publik.

Tal översättning, som drivs av Translator, är också tillgängligt via [Azure Speech service](../speech-service/index.yml). Den kombinerar funktioner från Translator Speech API och Custom Speech Service till en enhetlig och helt anpassningsbar tjänst. 

## <a name="language-support"></a>Stöd för språk

Translator ger stöd för flera språk för text översättning, transkriberingsspråk, språk identifiering och ord listor. Se [språkstöd](language-support.md) för en fullständig lista, eller kom åt listan programmatiskt med [REST API](./reference/v3-0-languages.md).  

## <a name="microsoft-translator-neural-machine-translation"></a>Neural maskinöversättning med Microsoft Translator

Neural maskinöversättning är den nya standarden för högkvalitativ AI-driven maskinöversättning. Den ersätter den äldre statistiska maskinöversättningen, som nådde en kvalitetsplatå i mitten av 2010-talet.

Neural maskinöversättning ger bättre översättningar än den statistiska varianten. Den ger bättre överensstämmelse betydelsemässigt och låter dessutom mer mänsklig. Den viktigaste orsaken till det är att hela meningskontexten används när ord översätts. I den statistiska översättningen användes bara direkta samband med några få ord före och efter varje ord.

Neurala modeller är kärnan i API:et och visas inte för slutanvändaren. Den enda märkbara skillnaden är förbättrad översättning, och särskilt för språk som arabiska, kinesiska och japanska.

Lär dig mer om [hur NMT fungerar](https://www.microsoft.com/en-us/translator/mt.aspx#nnt).

## <a name="improve-translations-with-custom-translator"></a>Förbättra översättningar med anpassad översättare

Ett tillägg till tjänsten Translator, anpassad översättare kan användas tillsammans med Translator för att anpassa neurala översättnings systemet och förbättra översättningen för din speciella terminologi och stil.

Med den anpassade översättningstjänsten kan du skapa översättningssystem som hanterar den terminologi som används inom företaget eller branschen. Det anpassade översättnings systemet integreras sedan enkelt i dina befintliga program, arbets flöden och webbplatser på flera olika typer av enheter, via den vanliga översättaren, med hjälp av parametern Category.

Läs mer om [Custom Translator](customization.md).

## <a name="next-steps"></a>Nästa steg

- [Registrera dig](./translator-how-to-signup.md) för en åtkomst nyckel.
- Prova vår [snabb start](quickstart-translator.md) för att snabbt anropa tjänsten Translator.
- [API-referensen](./reference/v3-0-reference.md) innehåller den tekniska dokumentationen för API:erna.
- [Prisinformation](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)