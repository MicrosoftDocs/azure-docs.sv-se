---
title: Översikt över Azure Media Services strömnings slut punkt | Microsoft Docs
description: Den här artikeln ger en översikt över Azure Media Services slut punkter för direkt uppspelning.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 0961b52ebc7271fabf4cc05ed99eea23d911a2d4
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009121"
---
# <a name="streaming-endpoints-overview"></a>Översikt över slut punkter för direkt uppspelning  

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

I Microsoft Azure Media Services (AMS) representerar en **strömmande slut punkt** en strömmande tjänst som kan leverera innehåll direkt till ett klient program eller till ett Content Delivery Network (CDN) för vidare distribution. Media Services ger också sömlös Azure CDN-integrering. Den utgående strömmen från en StreamingEndpoint-tjänst kan vara en Live Stream, en video på begäran eller progressiv nedladdning av din till gång på ditt Media Servicess konto. Varje Azure Media Services konto innehåller en standard StreamingEndpoint. Ytterligare strömnings slut punkter kan skapas under kontot. Det finns två versioner av strömnings slut punkter, 1,0 och 2,0. Från och med 10 januari 2017 kommer eventuella nyligen skapade AMS-konton att inkludera version 2,0 **standard** StreamingEndpoint. Ytterligare slut punkter för direkt uppspelning som du lägger till i det här kontot kommer också att vara version 2,0. Ändringen påverkar inte befintliga konton. befintliga strömnings slut punkter kommer att vara version 1,0 och kan uppgraderas till version 2,0. Med den här ändringen kan du göra ändringar i funktioner, fakturerings-och funktions ändringar (mer information finns i avsnittet **strömnings typer och versioner** som beskrivs nedan).

Azure Media Services lade till följande egenskaper i entiteten för strömnings slut punkt: **CdnProvider**, **CdnProfile**, **StreamingEndpointVersion**. Detaljerad översikt över dessa egenskaper finns i [detta](/rest/api/media/operations/streamingendpoint). 

När du skapar ett Azure Media Services konto skapas en standard slut punkt för standard strömning för dig i **stoppat** läge. Det går inte att ta bort standard slut punkten för direkt uppspelning. Beroende på Azure CDN tillgänglighet i mål regionen innehåller som standard den nyskapade standard slut punkten för direkt uppspelning även "StandardVerizon" CDN Provider integration. 
                
> [!NOTE]
> Azure CDN integration kan inaktive ras innan du startar slut punkten för direkt uppspelning. `hostname`URL: en och direkt uppspelnings-URL: en är oförändrad oavsett om du aktiverar CDN eller inte.

Det här avsnittet ger en översikt över de viktigaste funktionerna som tillhandahålls av slut punkter för direkt uppspelning.

## <a name="naming-conventions"></a>Namngivningskonventioner

För standard slut punkten: `{AccountName}.streaming.mediaservices.windows.net`

För ytterligare slut punkter: `{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>Strömmande typer och versioner

### <a name="standardpremium-types-version-20"></a>Standard/Premium-typer (version 2,0)

Från och med januari 2017-versionen av Media Services har du två typer av direkt uppspelning: **standard** (för hands version) och **Premium**. Dessa typer är en del av slut punkts versionen "2,0" för strömningen.


|Typ|Beskrivning|
|--------|--------|  
|**Standard**|Standard slut punkten för direkt uppspelning är en **standard** typ, kan ändras till Premium-typen genom att justera strömnings enheter.|
|**Premium** |Det här alternativet är lämpligt för professionella scenarier som kräver högre skala eller kontroll. Du flyttar till en **Premium** -typ genom att justera strömnings enheter.<br/>Dedikerade strömnings slut punkter Live i isolerade miljöer och konkurrerar inte om resurser.|

För kunder som vill leverera innehåll till stora Internet-åhörare rekommenderar vi att du aktiverar CDN på slut punkten för direkt uppspelning.

Mer detaljerad information finns i avsnittet [jämföra strömnings typer](#comparing-streaming-types) nedan.

### <a name="classic-type-version-10"></a>Klassisk typ (version 1,0)

För användare som skapade AMS-konton före versionen från januari 10 2017 har du en **klassisk** typ av slut punkt för direkt uppspelning. Den här typen är en del av den strömmande slut punkts versionen "1,0".

Om din **version "1,0"** för strömnings slut punkten har >= 1 Premium streaming Units (SU) är den förstklassiga strömnings slut punkten och tillhandahåller alla AMS-funktioner (precis som **Standard-/Premium** -typen) utan ytterligare konfigurations steg.

>[!NOTE]
>**Klassiska** slut punkter för direkt uppspelning (version "1,0" och 0 SU) tillhandahåller begränsade funktioner och omfattar inte ett service avtal. Vi rekommenderar att du migrerar till **standard** typ för att få en bättre upplevelse och att använda funktioner som dynamisk paketering eller kryptering och andra funktioner som följer **standard** typen. Om du vill migrera till **standard** typen går du till [Azure Portal](https://portal.azure.com/) och väljer **att välja som standard**. Mer information om migrering finns i avsnittet om [migrering](#migration-between-types) .
>
>Tänk på att den här åtgärden inte kan återställas och att den påverkar prissättningen.
>
 
## <a name="comparing-streaming-types"></a>Jämföra strömnings typer

### <a name="versions"></a>Versioner

|Typ|StreamingEndpointVersion|ScaleUnits|CDN|Fakturering|
|--------------|----------|-----------------|-----------------|-----------------|
|Klassisk|1.0|0|NA|Kostnadsfri|
|Standard slut punkt för direkt uppspelning (förhands granskning)|2.0|0|Ja|Betald|
|Premium – direktuppspelningsenheter|1.0|> 0|Ja|Betald|
|Premium – direktuppspelningsenheter|2.0|> 0|Ja|Betald|

### <a name="features"></a>Funktioner

Funktion|Standard|Premium
---|---|---
Dataflöde |Upp till 600 Mbit/s och kan ge ett mycket mer effektivt data flöde när ett CDN används.|200 Mbit/s per strömnings enhet (SU). Kan ge ett mycket högre effektivt data flöde när ett CDN används.
CDN|Azure CDN, CDN för tredje part eller ingen CDN.|Azure CDN, CDN för tredje part eller ingen CDN.
Faktureringen beräknas proportionellt| Varje dag|Varje dag
Dynamisk kryptering|Ja|Ja
Dynamisk paketering|Ja|Ja
Skala|Skalar upp till det riktade data flödet automatiskt.|Ytterligare enheter för strömning.
IP-filtrering/G20/anpassad värd <sup>1</sup>|Ja|Ja
Progressiv nedladdning|Ja|Ja
Rekommenderad användning |Rekommenderas för de flesta strömnings scenarier.|Professional-användning. 

<sup>1</sup> används endast direkt på slut punkten för direkt uppspelning när CDN inte är aktiverat på slut punkten.<br/>

Information om SLA finns i [prissättning och service avtal](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="migration-between-types"></a>Migrering mellan typer

Från | Om du vill | Action
---|---|---
Klassisk|Standard|Du måste välja
Klassisk|Premium| Skala (ytterligare enheter för strömning)
Standard/Premium|Klassisk|Inte tillgängligt (om version för strömnings slut punkt är 1,0. Det går att ändra till klassiskt med inställningen scaleunits till "0")
Standard (med/utan CDN)|Premium med samma konfigurationer|Tillåts i **Start** läge. (via Azure Portal)
Premium (med/utan CDN)|Standard med samma konfigurationer|Tillåts i **Start** tillstånd (via Azure Portal)
Standard (med/utan CDN)|Premium med olika konfigurationer|Tillåts i **stoppat** tillstånd (via Azure Portal). Tillåts inte i körnings tillstånd.
Premium (med/utan CDN)|Standard med olika konfigurationer|Tillåts i **stoppat** tillstånd (via Azure Portal). Tillåts inte i körnings tillstånd.
Version 1,0 med SU >= 1 med CDN|Standard/Premium utan CDN|Tillåts i **stoppat** tillstånd. Tillåts inte i läget **Started** .
Version 1,0 med SU >= 1 med CDN|Standard med/utan CDN|Tillåts i **stoppat** tillstånd. Tillåts inte i läget **Started** . Version 1,0 CDN tas bort och en ny skapas och startas.
Version 1,0 med SU >= 1 med CDN|Premium med/utan CDN|Tillåts i **stoppat** tillstånd. Tillåts inte i läget **Started** . Den klassiska CDN tas bort och nya skapas och startas.

## <a name="next-steps"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
