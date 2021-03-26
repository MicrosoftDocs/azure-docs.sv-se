---
title: Media Services v3-terminologi och enhets ändringar
description: I den här artikeln beskrivs skillnaderna mellan Azure Media Services v2 och v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 5c1fbaf9be4cb128f0e4390a8c97c6f0b9330ce2
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559884"
---
# <a name="terminology-and-entity-changes-between-media-services-v2-and-v3"></a>Terminologi och enhets ändringar mellan Media Services v2 och v3

![logo typ för migrations guide](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![steg 2 i migreringen](./media/migration-guide/steps-2.svg)

I den här artikeln beskrivs skillnaderna mellan Azure Media Services v2 och v3.

## <a name="naming-conventions"></a>Namngivningskonventioner

Granska de namngivnings konventioner som tillämpas på Media Services v3-resurser. Granska också [namngivning av blobbar](assets-concept.md#naming)

## <a name="terminology-changes"></a>Ändringar i terminologi

- En *positionerare* kallas nu för en *strömmande positionerare*.
- En *kanal* kallas nu för en *Live-händelse*.
- Ett *program* kallas nu för *Live-utdata*.
- En *uppgift* kallas nu för en *JobOutput*, som är en del av ett jobb.

## <a name="entity-changes"></a>Ändra enhet
| **V2-entitet**<!-- row --> | **V3-entitet** | **Vägledning** | **Tillgängligt för v3** | **Uppdaterat av v3** |
|--|--|--|--|--|
| `AccessPolicy`<!-- row --> | <!-- empty --> |  Entiteten `AccessPolicies` finns inte i v3. | Inga | Inga |
| `Asset`<!-- row --> | `Asset` | <!-- empty --> | Ja | Ja |
| `AssetDeliveryPolicy`<!-- row --> | `StreamingPolicy` | <!-- empty --> | Ja | Inga |
| `AssetFile`<!-- row --> | <!-- empty --> |Entiteten `AssetFiles` finns inte i v3. Även om filer (Storage blobbar) som du laddar upp är fortfarande filer som betraktas som filer.<br/><br/> Använd Azure Storage-API: er för att räkna upp blobarna i en behållare i stället. Det finns två sätt att tillämpa en omvandling till filerna med ett jobb:<br/><br/>Filer som redan har överförts till lagring: URI: n innehåller till gångs-ID för jobb som ska göras på till gångar i ett lagrings konto.<br/><br/>Filer som ska överföras under transformeringen och jobb processen: till gången skapas i lagring, en SAS-URL returneras, filer laddas upp till lagring och sedan tillämpas transformeringen på filerna. | Inga | Inga |
| `Channel`<!-- row --> | `LiveEvent` | Live-händelser ersätter kanaler från v2-API: et. De innehåller de flesta funktioner och har fler nya funktioner som direkt avskrifter, stand-by-läge och stöd för RTMP-inmatning. <br/><br/>Se [Live event i scenario baserat Live streaming](migrate-v-2-v-3-migration-scenario-based-live-streaming.md) | Inga | Inga |
| `ContentKey`<!-- row --> | <!-- empty --> | `ContentKeys` är inte längre en entitet, nu är det en egenskap för en strömmande positionerare.<br/><br/>  I v3 är innehålls nyckel data antingen associerade med `StreamingLocator` (för utgående kryptering) eller själva till gången (för lagrings kryptering på klient sidan). | Ja | Inga |
| `ContentKeyAuthorizationPolicy`<!-- row --> | `ContentKeyPolicy` | <!-- empty --> | Ja | Inga |
| `ContentKeyAuthorizationPolicyOption` <!-- row --> | <!-- empty --> |  `ContentKeyPolicyOptions` ingår i `ContentKeyPolicy` . | Ja | Inga |
| `IngestManifest`<!-- row --> | <!-- empty --> | Entiteten `IngestManifests` finns inte i v3. Överföring av filer i v3 omfattar API för Azure Storage. Till gångar skapas först och sedan överförs filerna till den tillhör ande lagrings behållaren. Det finns många sätt att hämta data till en Azure Storage behållare som kan användas i stället. `JobInputHttp` är också ett sätt att ladda ned jobb inmatade från en viss URL om du vill. | Inga | Inga |
| `IngestManifestAsset`<!-- row --> | <!-- empty --> | Det finns många sätt att hämta data till en Azure Storage behållare som kan användas i stället. `JobInputHttp` är också ett sätt att ladda ned jobb inmatade från en viss URL om du vill. | Inga | Inga |
| `IngestManifestFile`<!-- row --> | <!-- empty --> | Det finns många sätt att hämta data till en Azure Storage behållare som kan användas i stället. `JobInputHttp` är också ett sätt att ladda ned jobb inmatade från en viss URL om du vill. | Inga | Inga |
| `Job`<!-- row --> | `Job` | Skapa en `Transform` innan du skapar en `Job` . | Inga | Inga |
| `JobTemplate`<!-- row --> | `Transform` | Använd en `Transform` i stället. En transformering är en separat entitet från ett jobb och kan återanvändas. | Inga | Inga |
| `Locator`<!-- row --> | `StreamingLocator` | <!--empty --> | Ja | Inga |
| `MediaProcessor`<!-- row --> | <!-- empty --> | I stället för att söka efter `MediaProcessor` namn använder du önskad för inställning när du definierar en transformering. Den för inställning som används avgör vilken Media processor som används av jobb systemet. Se encoding-ämnen i [scenario-baserad kodning](migrate-v-2-v-3-migration-scenario-based-encoding.md). <!--Probably needs a link to its own article so customers know Indexerv1 maps to AudioAnalyzerPreset in basic mode, etc.--> | No | NA (ReadOnly i v2) |
| `NotificationEndPoint`<!-- row --> | <!--empty --> | Meddelanden i v3 hanteras via Azure Event Grid. `NotificationEndpoint`Ersätts av event Grid prenumerations registrering som också kapslar in konfigurationen för de typer av meddelanden som ska tas emot (som i v2 hanterades av `JobNotificationSubscription` jobbet, `TaskNotificationSubscription` för aktiviteten och telemetri `ComponentMonitoringSetting` ). Den 2: a telemetri delas mellan Azure Event Grid och Azure Monitor för att passa in i förbättringarna av det större Azure-eko systemet. | Inga | Inga |
| `Program`<!-- row --> | `LiveOutput` | Live-utdata ersätter nu program i v3-API: et.  | Inga | Inga |
| `StreamingEndpoint`<!-- row --> | `StreamingEndpoint` | Slut punkter för direkt uppspelning förblir i huvudsak samma. De används för dynamisk paketering, kryptering och leverans av HLS-och tank streck för både Live och on-demand streaming, antingen direkt från ursprung eller via CDN. Nya funktioner är stöd för bättre Azure Monitor integrering och diagramering. |  Ja | Ja |
| `Task`<!-- row --> | `JobOutput` | Ersatt av `JobOutput` (som inte längre är en separat entitet i API: et).  Se encoding-ämnen i [scenario-baserad kodning](migrate-v-2-v-3-migration-scenario-based-encoding.md). | Inga | Inga |
| `TaskTemplate`<!-- row --> | `TransformOutput` | Ersatt av `TransformOutput` (som inte längre är en separat entitet i API: et). Se encoding-ämnen i [scenario-baserad kodning](migrate-v-2-v-3-migration-scenario-based-encoding.md). | Inga | Inga |
| `Inputs`<!-- row --> | `Inputs` | Indata och utdata finns nu på jobb nivå. Se kodnings ämnen i [scenario-baserad kodning](migrate-v-2-v-3-migration-scenario-based-encoding.md) | Inga | Inga |
| `Outputs`<!-- row --> | `Outputs` | Indata och utdata finns nu på jobb nivå.  I v3 har metadata-formatet ändrats från XML till JSON.  Liveutdata startar när de skapas och avbryts när de tas bort. Se kodnings ämnen i [scenario-baserad kodning](migrate-v-2-v-3-migration-scenario-based-encoding.md) | Inga | Inga |


| **Andra ändringar** | **2**  | **V3** |
|---|---|---|
| **Storage** <!--new row --> |||
| Storage <!--new row --> | | V3 SDK: er är nu frikopplade från Storage SDK, vilket ger dig mer kontroll över vilken version av Storage SDK som du vill använda och undviker versions problem.                      |
| **Kodning** <!--new row --> |||
| Kodnings bit hastigheter <!--new row --> | bit hastigheter mätt i kbps ex: 128 (kbit/s)| bitar per sekund ex: 128000 (bitar/sekund)|
| Koda DRM-FairPlay <!--new row --> | I Media Services v2 kan du ange initierings vektor (IV). | Det går inte att ange FairPlay IV i Media Services v3.|
| Premium-kodare <!--new row --> | Premium-kodare och äldre indexerare| [Premium-kodaren](../previous/media-services-encode-asset.md) och de äldre [Media Analytics-processorerna](../previous/legacy-components.md) (Azure Media Services indexerare 2 för hands version, ansikts bortredigering osv.) är inte tillgängliga via v3. Vi har lagt till stöd för ljud kanal mappning till Standard-kodaren.  Se [ljud i Media Services Encoding Swagger-dokumentationen](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json).  | Se kodnings ämnen i [scenario-baserad kodning](migrate-v-2-v-3-migration-scenario-based-encoding.md) |
| **Transformeringar och jobb** <!--new row -->|||
| Job based Processing HTTPS <!--new row --> |<!-- empty -->| För filbaserad jobb bearbetning kan du använda en HTTPS-URL som indata. Du behöver inte ha något innehåll som redan är lagrat i Azure, eller så behöver du inte skapa till gångar. |
| ARM-mallar för jobb <!--new row --> | ARM-mallar fanns inte i v2. | En transformering kan användas för att skapa återanvändbara konfigurationer, skapa Azure Resource Manager mallar och isolera bearbetnings inställningar mellan flera kunder eller klienter. |
| **Live-händelser** <!--new row --> |||
| Slutpunkt för direktuppspelning <!--new row --> | En slut punkt för direkt uppspelning representerar en strömmande tjänst som kan leverera innehåll direkt till ett klients pelar program eller till ett Content Delivery Network (CDN) för vidare distribution. | Slut punkter för direkt uppspelning förblir i huvudsak samma. De används för dynamisk paketering, kryptering och leverans av HLS-och tank streck för både Live och on-demand streaming, antingen direkt från ursprung eller via CDN.  Nya funktioner är stöd för bättre Azure Monitor integrering och diagramering. |
| Live Event-kanaler <!--new row --> | Kanaler ansvarar för bearbetning av direktuppspelat innehåll. En kanal tillhandahåller en inmatnings slut punkt (inmatnings-URL) som du sedan anger till en Live-kodare. Kanalen tar emot direktsända indata strömmar från direktsänd kodare och gör den tillgänglig för strömning via en eller flera slut punkter för direkt uppspelning. Kanaler tillhandahåller också en förhands gransknings slut punkt (för hands version) som du använder för att förhandsgranska och validera data strömmen innan ytterligare bearbetning och leverans.| Live-händelser ersätter kanaler från v2-API: et. De innehåller de flesta funktioner och har fler nya funktioner som direkt avskrifter, stand-by-läge och stöd för RTMP-inmatning. |
| Live Event-program <!--new row --> | Ett program gör att du kan styra publicering och lagring av segment i en direktsänd dataström. Kanaler hanterar program. Kanal-och program relationen liknar traditionella medier där en kanal har en konstant ström med innehåll och ett program är begränsad till en viss tids period på den kanalen. Du kan ange hur många timmar du vill behålla det inspelade innehållet för programmet genom att ange `ArchiveWindowLength` egenskapen. Det här värdet kan anges från minst 5 minuter till högst 25 timmar.| Live-utdata ersätter nu program i v3-API: et. |
| Längd på Live-händelse <!--new row --> |<!-- empty -->| Du kan strömma live-händelser 24/7 när du använder Media Services för att koda ett bidrags flöde för en enskild bit hastighet till en utdataström som har flera bit hastigheter.|
| Svars tid för Live-händelse <!--new row --> |<!-- empty -->| Ny support för Live-direktuppspelning med låg latens för Live-händelser. |
| För hands version av live event <!--new row --> |<!-- empty -->| För hands versionen av live event stöder dynamisk paketering och dynamisk kryptering. Detta aktiverar innehålls skydd för för hands versionen samt streck-och HLS-paket. |
| RTMP för live event <!--new row --> |<!-- empty-->| Förbättrat stöd för RTMP med ökad stabilitet och fler stöd för käll kodare. |
| Publicera händelser i real tid med RTMP <!--new row --> | | När du skapar en Live-händelse får du 4 inmatnings-URL: er. De fyra inmatnings-URL: erna är nästan identiska, har samma strömmande token `AppId` , men endast port nummer delen är annorlunda. Två av URL: erna är primära och säkerhets kopiering för RTMP.| 
| Direkt händelse avskrift <!--new row --> |<!-- empty--> | Azure Media service levererar video, ljud och text i olika protokoll. När du publicerar din Live Stream med MPEG-streck eller HLS/CMAF, tillsammans med video och ljud, levererar vår tjänst den inskrivna texten i IMSC 1.1-kompatibla TTML.|
| Vänte läge för Live-händelse <!--new row --> | Det finns inget vänte läge för v2. | I det här läget finns en ny v3-funktion som hjälper dig att hantera frekventa pooler med Live-händelser. Kunder kan nu starta en Live-händelse i stand-by-läge till lägre kostnad innan den övergår till körnings tillstånd. Detta förbättrar kanalens start tider och minskar kostnaderna för att använda heta pooler för snabbare start av UPS. |
| Direkt fakturering av evenemang <!--new row --> | <!-- empty-->| Faktureringen av Live-händelser baseras på Live Channel-mätare. |
| Live-utdata <!--new row --> | Programmen måste startas när de har skapats. | Liveutdata startar när de skapas och avbryts när de tas bort. |
