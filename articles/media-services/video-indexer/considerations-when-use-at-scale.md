---
title: Saker att tänka på när du Video Indexer i stor skala – Azure
titleSuffix: Azure Media Services
description: Det här avsnittet beskriver vad du bör tänka på när du Video Indexer i stor skala.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: how-to
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: f941d81df670f017d24a7c5011c55fcc4f082605
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531575"
---
# <a name="things-to-consider-when-using-video-indexer-at-scale"></a>Saker att tänka på när du Video Indexer i stor skala

När du Azure Media Services Video-indexeraren för att indexera videor och ditt arkiv med videor växer bör du överväga skalning. 

Den här artikeln besvarar frågor som:

* Finns det några tekniska begränsningar som jag måste ta hänsyn till?
* Finns det ett smart och effektivt sätt att göra det?
* Kan jag förhindra utgifter som överstiger pengar i processen?

Artikeln innehåller sex metodtips för hur du använder Video Indexer i stor skala.

## <a name="when-uploading-videos-consider-using-a-url-over-byte-array"></a>När du laddar upp videor bör du överväga att använda en URL över byte-matris

Video Indexer ger dig möjlighet att ladda upp videor från URL:en eller direkt genom att skicka filen som en bytematris, kommer det senare med vissa begränsningar. Mer information finns i Ladda [upp överväganden och begränsningar)](upload-index-videos.md#uploading-considerations-and-limitations)

Först har den filstorleksbegränsningar. Storleken på bytematrisfilen är begränsad till 2 GB jämfört med storleksbegränsningen på 30 GB vid användning av URL.

För det andra bör du överväga några av de problem som kan påverka prestanda och därmed din möjlighet att skala:

* Att skicka filer med flera delar innebär ett stort beroende av nätverket, 
* tjänstens tillförlitlighet, 
* Anslutning 
* uppladdningshastighet, 
* förlorade paket någonstans i world wide web.

:::image type="content" source="./media/considerations-when-use-at-scale/first-consideration.png" alt-text="Första övervägandet för att Video Indexer i stor skala":::

När du laddar upp videor med hjälp av EN URL behöver du bara ange en sökväg till platsen för en mediefil och Video Indexer tar hand om resten (se fältet i API:et för `videoUrl` [uppladdning av video).](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)

> [!TIP]
> Använd den `videoUrl` valfria parametern för api:et för uppladdning av video.

Om du vill se ett exempel på hur du laddar upp videor med hjälp av EN URL kan du titta [på det här exemplet.](upload-index-videos.md#code-sample) Eller så kan du använda [AzCopy för](../../storage/common/storage-use-azcopy-v10.md) ett snabbt och tillförlitligt sätt att hämta ditt innehåll till ett lagringskonto som du kan skicka det till med hjälp Video Indexer [SAS-URL.](../../storage/common/storage-sas-overview.md)

## <a name="increase-media-reserved-units-if-needed"></a>Öka mediereserverade enheter om det behövs

Vanligtvis i konceptbevisfasen när du precis börjar använda Video Indexer behöver du inte så mycket beräkningskraft. När du börjar ha ett större arkiv med videor som du behöver indexera och du vill att processen ska ske i en takt som passar ditt användningsfall måste du skala upp din användning av Video Indexer. Därför bör du fundera på att öka antalet beräkningsresurser som du använder om den aktuella mängden beräkningskraft inte räcker till.

När Azure Media Services vill öka datorkraften och parallelliseringen måste du vara uppmärksam på [mediereserverade](../latest/concept-media-reserved-units.md)enheter (RU:er). RU:erna är de beräkningsenheter som bestämmer parametrarna för dina mediebearbetningsuppgifter. Antalet RU:er påverkar antalet medieuppgifter som kan bearbetas samtidigt i varje konto och deras typ avgör bearbetningshastigheten och en video kan kräva mer än en RU om dess indexering är komplex. När dina RU:er är upptagna kommer nya uppgifter att finnas i en kö tills en annan resurs är tillgänglig.

För att kunna arbeta effektivt och undvika att ha resurser som håller sig inaktiva en del av tiden erbjuder Video Indexer ett system med automatisk skalning som gör att RU:er sätts ur drift när mindre bearbetning behövs och som kör ru:er när du är på din tid (upp till fullt ut använda alla dina RU:er). Du kan aktivera den här funktionen genom att aktivera [autoskalning](manage-account-connected-to-azure.md#autoscale-reserved-units) i kontoinställningarna eller använda [API:et Update-Paid-Account-Azure-Media-Services.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Paid-Account-Azure-Media-Services)

:::image type="content" source="./media/considerations-when-use-at-scale/second-consideration.jpg" alt-text="Andra övervägandet vid användning Video Indexer i stor skala":::

:::image type="content" source="./media/considerations-when-use-at-scale/reserved-units.jpg" alt-text="Reserverade AMS-enheter":::

För att minimera indexeringstiden och det låga dataflödet rekommenderar vi att du börjar med 10 RU:er av typen S3. Om du senare skalar upp för att stödja mer innehåll eller högre samtidighet, och du behöver fler resurser för att göra det, kan du kontakta oss via [supportsystemet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) (endast för betalda konton) för att be om fler RU-allokering.

## <a name="respect-throttling"></a>Respektera begränsning

Video Indexer har skapats för att hantera indexering i stor skala, och när du vill få ut det mesta av det bör du också vara medveten om systemets funktioner och utforma din integrering därefter. Du vill inte skicka en uppladdningsbegäran för en grupp videor bara för att upptäcka att några av filmerna inte har laddats upp och du får en HTTP 429-svarskod (för många begäranden). Det kan inträffa på grund av att du har skickat fler begäranden än gränsen [för filmer per minut som vi stöder](upload-index-videos.md#uploading-considerations-and-limitations). Video Indexer lägger till `retry-after` en rubrik i HTTP-svaret anger -huvudet när du ska försöka igen. Se till att du respekterar det innan du provar din nästa begäran.

:::image type="content" source="./media/considerations-when-use-at-scale/respect-throttling.jpg" alt-text="Utforma din integrering väl, respektera begränsningar":::

## <a name="use-callback-url"></a>Använda återanrops-URL

Vi rekommenderar att du i stället för att kontinuerligt avsöker statusen för din begäran från den sekund då du skickade uppladdningsbegäran, lägger till en [återanrops-URL](upload-index-videos.md#callbackurl)och väntar tills Video Indexer uppdaterar dig. Så snart det finns någon statusändring i din uppladdningsbegäran får du ett POST-meddelande till den URL som du har angett.

Du kan lägga till en motringning-URL som en av parametrarna för [video-API:et för uppladdning.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) Kolla in kodexe exemplen på [GitHub-lagringsplatsen](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/). 

För motringnings-URL kan Azure Functions även använda en serverlös händelsedriven plattform som kan utlösas av HTTP och implementera ett följande flöde.

### <a name="callback-url-definition"></a>definition av callBack-URL

[!INCLUDE [callback url](./includes/callback-url.md)]

## <a name="use-the-right-indexing-parameters-for-you"></a>Använd rätt indexeringsparametrar åt dig

När du fattar beslut som rör Video Indexer i stor skala kan du titta på hur du får ut det mesta av det med rätt parametrar för dina behov. Tänk på ditt användningsfall genom att definiera olika parametrar som du kan spara pengar på och göra indexeringsprocessen för dina videor snabbare.

Innan du laddar upp och indexerar videon bör du läsa den här korta dokumentationen. Kontrollera [indexingPreset](upload-index-videos.md#indexingpreset) och [streamingPreset](upload-index-videos.md#streamingpreset) för att få en bättre uppfattning om dina alternativ. [](upload-index-videos.md)

Ange till exempel inte förinställningen till strömning om du inte planerar att titta på videon, indexera inte videoinsikter om du bara behöver ljudinsikter.

## <a name="index-in-optimal-resolution-not-highest-resolution"></a>Index i optimal upplösning, inte högsta upplösning

Du kanske undrar vilken videokvalitet du behöver för att indexera dina videor? 

I många fall har indexeringsprestanda nästan ingen skillnad mellan HD-videor (720P) och 4K-videor. Så småningom får du nästan samma insikter med samma förtroende. Ju högre kvalitet på filmen du laddar upp, desto högre filstorlek, vilket leder till högre beräkningskraft och tid som krävs för att ladda upp videon.

För ansiktsavkänningsfunktionen kan till exempel en högre upplösning hjälpa till med scenariot där det finns många små men sammanhangsberoende viktiga ansikten. Detta kommer dock att ge en kvadratisk ökning av körningen och en ökad risk för falska positiva resultat.

Därför rekommenderar vi att du kontrollerar att du får rätt resultat för ditt användningsfall och att du först testar det lokalt. Ladda upp samma video i 720P och i 4K och jämför de insikter du får.

## <a name="next-steps"></a>Nästa steg

[Granska Azure-Video Indexer som produceras av API](video-indexer-output-json-v2.md)