---
title: Vanliga frågor och svar om Text till tal
titleSuffix: Azure Cognitive Services
description: Få svar på vanliga frågor om Text till tal tjänsten.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: panosper
ms.openlocfilehash: 67284225299641a71050342056ad7ca91bf39740
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96353722"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Vanliga frågor och svar om Text till tal

Om du inte kan hitta svar på dina frågor i dessa vanliga frågor och svar kan du titta närmare på [andra support alternativ](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext).

## <a name="general"></a>Allmänt

**F: Vad är skillnaden mellan en standard röst modell och en anpassad röst modell?**

**A**: standard röst modellen (även kallat _röst teckensnitt_) har tränats med Microsoft-ägda data och har redan distribuerats i molnet. Du kan använda en anpassad röst modell för att anpassa en genomsnitts modell och överföra Timbre och uttryck för talare: s röst stil eller träna en fullständig ny modell som baseras på de utbildnings data som användaren har skapat. Idag vill fler och fler kunder ha en enda, anpassad röst för sin robotar. Den anpassade röst skapande plattformen är det rätta valet för det alternativet.

**F: Hur börjar jag om jag vill använda en standard röst modell?**

**A**: fler än 80 standard röst modeller på över 45-språk är tillgängliga via HTTP-begäranden. Börja med att hämta en [prenumerations nyckel](./overview.md#try-the-speech-service-for-free). Om du vill göra REST-anrop till de fördistribuerade röst modellerna går du till [REST API](./overview.md#reference-docs).

**F: är API: et detsamma som det som används för standard röster om jag vill använda en anpassad röst modell?**

**A**: när en anpassad röst modell skapas och distribueras får du en unik slut punkt för din modell. Om du vill använda rösten för att tala i dina appar måste du ange slut punkten i dina HTTP-begäranden. Samma funktioner som är tillgängliga i REST API för tjänsten Text till tal är tillgänglig för din anpassade slut punkt. Lär dig hur du [skapar och använder din anpassade slut punkt](./how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint).

**F: behöver jag förbereda tränings data för att skapa anpassade röst modeller?**

**A**: Ja, du måste förbereda tränings data själv för en anpassad röst modell.

En samling tal data krävs för att skapa en anpassad röst modell. Den här samlingen består av en uppsättning ljudfiler med tal inspelningar och en text fil med avskriften för varje ljudfil. Resultatet av den digitala rösten är mycket beroende av kvaliteten på dina utbildnings data. För att skapa en bra text till tal-röst är det viktigt att inspelningarna görs i ett tyst rum med en hög kvalitet med hög kvalitet. Ett konsekvent volym-, tal-och inläsnings frekvens, och till och med konsekvens i lättfattliga programspecifika mannerisms av tal är viktigt för att skapa en bra digital röst. Vi rekommenderar starkt att du registrerar rösterna i en inspelning Studio.

För närvarande ger vi inte support för online-inspelning eller har några rekommendationer för inspelning Studio. För format kravet, se [hur du förbereder inspelningar och avskrifter](./how-to-custom-voice-create-voice.md).

**F: vilka skript ska jag använda för att registrera tal information för en anpassad röst träning?**

**A**: vi begränsar inte skripten för röst inspelning. Du kan använda dina egna skript för att spela in talet. Se bara till att du har tillräckligt med fonetisk täckning i dina tal data. För att träna en anpassad röst kan du börja med en liten mängd tal data, vilket kan vara 50 olika meningar (cirka 3-5 minuter med tal). Den mer information som du anger, desto mer naturlig är din röst. Du kan börja träna ett fullständigt röst teckensnitt när du tillhandahåller inspelningar på över 2 000 meningar (cirka 3-4 timmar med tal). För att få en hög kvalitet, full röst måste du förbereda inspelningar av mer än 6 000 meningar (cirka 8-10 timmars tal).

Vi tillhandahåller ytterligare tjänster som hjälper dig att förbereda skript för inspelning. Kontakta [anpassad röst kund support](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) för förfrågningar.

**F: Vad händer om jag behöver högre samtidighet än standardvärdet eller vad som erbjuds i portalen?**

**A**: du kan skala upp din modell i steg om 20 samtidiga begär Anden. Kontakta [Custom Voice support](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) för frågor om högre skalning.

**F: kan jag hämta min modell och köra den lokalt?**

**A**: det går inte att ladda ned och köra modeller lokalt.

**F: är mina förfrågningar begränsade?**

**A**: se [kvoter och begränsningar för tal tjänster](speech-services-quotas-and-limits.md).

## <a name="next-steps"></a>Nästa steg

- [Felsökning](troubleshooting.md)
- [Viktig information](releasenotes.md)