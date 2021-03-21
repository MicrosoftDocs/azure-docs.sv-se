---
title: Saker att tänka på när du använder Video Indexer i skala – Azure
titleSuffix: Azure Media Services
description: I det här avsnittet beskrivs vad du bör tänka på när du använder Video Indexer i stor skala.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: how-to
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: b955c0f494b757fd29c400194ef8b11314a89a03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96483618"
---
# <a name="things-to-consider-when-using-video-indexer-at-scale"></a>Saker att tänka på när du använder Video Indexer i stor skala

Överväg skalning när du använder Azure Media Services video Indexer för att indexera videor och ditt arkiv med videor ökar. 

Den här artikeln svarar på frågor som:

* Finns det några tekniska begränsningar Jag behöver ta med i beräkningen?
* Finns det ett smart och effektivt sätt att göra det?
* Kan jag förhindra att utgifterna överskrids i processen?

Artikeln innehåller sex bästa metoder för hur du använder Video Indexer i stor skala.

## <a name="when-uploading-videos-consider-using-a-url-over-byte-array"></a>När du överför videor bör du använda en URL över byte-matrisen

Video Indexer ger dig möjlighet att ladda upp videor från URL: en eller direkt genom att skicka filen som en byte mat ris, med vissa begränsningar. Mer information finns i [överförings överväganden och begränsningar](upload-index-videos.md#uploading-considerations-and-limitations) .

Först har den fil storleks begränsningar. Storleken på byte mat ris filen är begränsad till 2 GB jämfört med storleks begränsningen på 30 GB vid användning av URL.

Sedan bör du överväga bara några av de problem som kan påverka prestandan och därmed möjlighet att skala:

* Att skicka filer med hjälp av multi-del innebär högt beroende på ditt nätverk, 
* tjänste tillförlitlighet, 
* koppling 
* uppladdnings hastighet, 
* förlorade paket någonstans i World Wide Web.

:::image type="content" source="./media/considerations-when-use-at-scale/first-consideration.png" alt-text="Första överväganden vid användning av Video Indexer i stor skala":::

När du laddar upp videor med URL behöver du bara ange en sökväg till platsen för en mediefil och Video Indexer tar hand om resten (se `videoUrl` fältet i [uppladdnings video](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?&pattern=upload) -API: et).

> [!TIP]
> Använd den `videoUrl` valfria parametern för video-API: t upload.

Om du vill se ett exempel på hur du överför videor med URL kan du läsa [det här exemplet](upload-index-videos.md#code-sample). Du kan också använda [AzCopy](../../storage/common/storage-use-azcopy-v10.md) för ett snabbt och tillförlitligt sätt för att hämta ditt innehåll till ett lagrings konto från vilket du kan skicka det till video Indexer med [SAS-URL: en](../../storage/common/storage-sas-overview.md).

## <a name="increase-media-reserved-units-if-needed"></a>Öka enhetens reserverade enheter om det behövs

Vanligt vis i POC-fasen när du precis börjar använda Video Indexer behöver du inte mycket dator kraft. När du börjar ha ett större Arkiv med videor som du behöver indexera och du vill att processen ska vara i en takt som passar ditt användnings fall måste du skala upp din användning av Video Indexer. Därför bör du tänka på att öka antalet beräknings resurser som du använder om den aktuella mängden data behandlings kraften inte räcker till.

I Azure Media Services måste du, när du vill öka dator kraft och parallellisering, betala uppmärksamheten till ru: er (Media [reservered units](../latest/concept-media-reserved-units.md)). Ru: er är de beräknings enheter som avgör parametrarna för dina medie bearbetnings uppgifter. Antalet ru: er påverkar antalet medie uppgifter som kan bearbetas samtidigt i varje konto och deras typ bestämmer process hastigheten och en video kan kräva mer än en RU om indexeringen är komplex. När ru: er är upptagna lagras nya aktiviteter i en kö tills en annan resurs är tillgänglig.

För att fungera effektivt och för att undvika att ha resurser som håller på att inaktive ras under tiden, erbjuder Video Indexer ett system för automatisk skalning som snurrar ru: er när mindre bearbetning behövs och snurrar ru: er upp när du är i dina skynda timmar (upp till fullo använder hela ru: er). Du kan aktivera den här funktionen genom att aktivera [autoskalning](manage-account-connected-to-azure.md#autoscale-reserved-units) i konto inställningarna eller med hjälp av [Update-betald-Account-Azure-Media-Services-API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&pattern=update).

:::image type="content" source="./media/considerations-when-use-at-scale/second-consideration.jpg" alt-text="Andra överväganden vid användning av Video Indexer i skala":::

:::image type="content" source="./media/considerations-when-use-at-scale/reserved-units.jpg" alt-text="AMS reserverade enheter":::

För att minimera Indexeringens varaktighet och låg genom strömning rekommenderar vi att du börjar med 10 ru: er av typen S3. Senare om du skalar upp för att stödja mer innehåll eller högre samtidighet, och du behöver fler resurser för att göra det, kan du [kontakta oss med support systemet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) (endast på betalda konton) för att be om mer ru: er-allokering.

## <a name="respect-throttling"></a>Respektera begränsning

Video Indexer har skapats för att hantera indexering i stor skala, och när du vill få ut mesta möjliga av det bör du också vara medveten om systemets funktioner och utforma din integrering. Du vill inte skicka en uppladdnings förfrågan för en batch med videor bara för att upptäcka att några av filmerna inte laddats upp och du får en HTTP 429-svarskod (för många begär Anden). Det kan bero på att du har skickat fler förfrågningar än den [gräns för filmer per minut som vi stöder](upload-index-videos.md#uploading-considerations-and-limitations). Video Indexer lägger till ett `retry-after` sidhuvud i HTTP-svaret anger sidhuvudet när du ska försöka med nästa försök. Se till att respektera den innan du provar nästa begäran.

:::image type="content" source="./media/considerations-when-use-at-scale/respect-throttling.jpg" alt-text="Utforma din integrering och respektera begränsning":::

## <a name="use-callback-url"></a>Använd återanrops-URL

Vi rekommenderar att i stället för att söka efter statusen för din begäran konstant från den andra som du skickade uppladdnings förfrågan, kan du lägga till en [återanrops-URL](upload-index-videos.md#callbackurl)och vänta tills video Indexer uppdaterats. Så snart det finns status ändringar i din uppladdnings förfrågan får du ett POST-meddelande till den URL som du har angett.

Du kan lägga till en återanrops-URL som en av parametrarna i [uppladdnings-API: et](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?&pattern=upload). Kolla in kod exemplen i [GitHub lagrings platsen](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/). 

För återanrops-URL kan du också använda Azure Functions, en server lös händelse driven plattform som kan utlösas av HTTP och implementera ett följande flöde.

### <a name="callback-url-definition"></a>definition av callBack-URL

[!INCLUDE [callback url](./includes/callback-url.md)]

## <a name="use-the-right-indexing-parameters-for-you"></a>Använd rätt indexerings parametrar för dig

När du fattar beslut som rör att använda Video Indexer i skala kan du läsa hur du får ut mesta möjliga av den med rätt parametrar för dina behov. Tänk på ditt användnings fall genom att definiera olika parametrar kan du spara pengar och indexera indexerings processen för dina videor snabbare.

Innan du laddar upp och indexerar videon läser du den här korta [dokumentationen](upload-index-videos.md), kontrollerar [indexingPreset](upload-index-videos.md#indexingpreset) och [streamingPreset](upload-index-videos.md#streamingpreset) för att få en bättre uppfattning om vad dina alternativ är.

Ange till exempel inte för inställningen för strömning om du inte planerar att titta på videon, inte indexera video insikter om du bara behöver ljud insikter.

## <a name="index-in-optimal-resolution-not-highest-resolution"></a>Index i optimal upplösning, inte högsta upplösning

Du kanske frågar, vilken video kvalitet behöver du för att indexera dina videor? 

I många fall har indexerings prestanda nästan ingen skillnad mellan HD (720P) videor och 4K-videor. Slutligen får du nästan samma insikter med samma exakthet. Ju högre kvalitet på filmen du laddar upp, desto högre fil storlek och detta leder till högre dator kraft och tid som krävs för att ladda upp videon.

För funktionen ansikts igenkänning kan en högre upplösning hjälpa till med scenariot där det finns många små men sammanhangsbaserade viktiga ansikten. Detta kommer dock att ha en kvadratisk ökning av körningen och en ökad risk för falska positiva identifieringar.

Därför rekommenderar vi att du kontrollerar att du får rätt resultat för ditt användnings fall och att du först testar det lokalt. Ladda upp samma video i 720P och i 4K och jämför de insikter du får.

## <a name="next-steps"></a>Nästa steg

[Granska Azure Video Indexer-utdata som skapats av API](video-indexer-output-json-v2.md)