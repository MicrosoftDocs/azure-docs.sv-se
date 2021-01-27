---
title: Koda video och ljud med Media Services
description: Den här artikeln förklarar hur du kodar video och ljud med Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 159336d3e8949739cb00dbe66ed82fa90256dcfd
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897908"
---
# <a name="encoding-video-and-audio-with-media-services"></a>Koda video och ljud med Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Termen encoding i Media Services gäller för processen att konvertera filer som innehåller digital video och/eller ljud från ett standardformat till en annan, med syftet att (a) minska storleken på filerna och/eller (b) som producerar ett format som är kompatibelt med ett brett utbud av enheter och appar. Den här processen kallas även video komprimering eller kodning. Se [data komprimeringen](https://en.wikipedia.org/wiki/Data_compression) och [vad som är kodning och omkodning?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) för att få ytterligare information om begreppen.

Videor levereras vanligt vis till enheter och appar genom [progressiv nedladdning](https://en.wikipedia.org/wiki/Progressive_download) eller genom [strömning med anpassningsbar bit hastighet](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming).

> [!IMPORTANT]
> Media Services debiteras inte för annullerade eller felaktiga jobb. Ett jobb som har nått 50% Progress och annulleras faktureras exempelvis inte vid 50% av jobb minuterna. Du debiteras bara för färdiga jobb.

* Om du vill leverera med progressiv nedladdning kan du använda Azure Media Services för att konvertera en digital mediafil (mezzaninfil) till en [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) -fil som innehåller video som har kodats med [H. 264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) -kodeken och ljud som har kodats med [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) -kodeken. Den här MP4-filen skrivs till en till gång i ditt lagrings konto. Du kan använda Azure Storage-API: er eller SDK: er (till exempel [lagrings REST API](../../storage/common/storage-rest-api-auth.md) eller [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) för att hämta filen direkt. Om du skapade utmatnings till gången med ett angivet behållar namn i lager, använder du den platsen. Annars kan du använda Media Services för att [Visa URL: erna till till gångs containern](/rest/api/media/assets/listcontainersas). 
* För att förbereda innehåll för leverans av direkt uppspelning med anpassningsbar bit hastighet måste mezzaninfil-filen kodas vid flera bit hastigheter (högt till lågt). För att säkerställa en korrekt över gång av kvalitet sänks videons upplösning när bit hastigheten sänks. Detta resulterar i en så kallad kodnings steg – en tabell över lösningar och bit hastigheter (se [autogenererad steg-för anpassad bit hastighet](autogen-bitrate-ladder.md)). Du kan använda Media Services för att koda dina mezzaninfil-filer på flera bit hastigheter. När du gör det får du en uppsättning MP4-filer och tillhör ande konfigurationsfiler för direkt uppspelning som skrivs till en till gång i ditt lagrings konto. Du kan sedan använda funktionen för [dynamisk paketering](dynamic-packaging-overview.md) i Media Services för att leverera videon via strömmande protokoll som [MPEG-streck](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) och [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming). Detta kräver att du skapar en [strömmande lokaliserare](streaming-locators-concept.md) och skapar strömmande URL: er som motsvarar de protokoll som stöds, som sedan kan skickas vidare till enheter/appar baserat på deras funktioner.

Följande diagram visar arbets flödet för kodning på begäran med dynamisk paketering.

![Arbets flöde för kodning på begäran med dynamisk paketering](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Det här avsnittet ger vägledning om hur du kodar ditt innehåll med Media Services v3.

## <a name="transforms-and-jobs"></a>Transformeringar och jobb

Om du vill koda med Media Services v3 måste du skapa en [transformering](/rest/api/media/transforms) och ett [jobb](/rest/api/media/jobs). Transformeringen definierar ett recept för kodnings inställningar och utdata. jobbet är en instans av receptet. Mer information finns i [Transformeringar och jobb](transforms-jobs-concept.md).

När du kodar med Media Services använder du för inställningar för att berätta för kodaren hur indatafilerna ska bearbetas. I Media Services v3 använder du standard kodare för att koda dina filer. Du kan till exempel ange video upplösning och/eller antalet ljud kanaler som du vill ha i det kodade innehållet.

Du kan komma igång snabbt med en av de rekommenderade inbyggda för inställningarna baserat på bransch bästa praxis eller så kan du välja att skapa en anpassad för inställning för att rikta in dig på specifika scenarier eller enhets krav. Mer information finns i [koda med en anpassad transformering](customize-encoder-presets-how-to.md).

Från och med januari 2019 skapas en ny. MPI-fil och läggs till i utmatnings till gången, när den kodas med Standard-kodaren för att skapa MP4-filer. Den här MPI-filen är avsedd att förbättra prestanda för dynamiska scenarier för [paketering](dynamic-packaging-overview.md) och strömning.

> [!NOTE]
> Du bör inte ändra eller ta bort MPI-filen, eller ta bort beroendet i tjänsten om det finns en sådan fil.

### <a name="creating-job-input-from-an-https-url"></a>Skapa jobb inmatade från en HTTPS-URL

När du skickar jobb för att bearbeta dina videor måste du tala om för Media Services var du hittar Indataporten. Ett av alternativen är att ange en HTTPS-URL som ett jobb inflöde. För närvarande stöder Media Services v3 inte konvertering av chunked-överföring via HTTPS-URL: er.

#### <a name="examples"></a>Exempel

* [Koda från en HTTPS-URL med .NET](stream-files-dotnet-quickstart.md)
* [Koda från en HTTPS-URL med REST](stream-files-tutorial-with-rest.md)
* [Koda från en HTTPS-URL med CLI](stream-files-cli-quickstart.md)
* [Koda från en HTTPS-URL med Node.js](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Skapa jobb indata från en lokal fil

Indataporten kan lagras som en medie tjänst till gång, i vilket fall du skapar en indata-till gång baserat på en fil (lagras lokalt eller i Azure Blob Storage).

#### <a name="examples"></a>Exempel

[Koda en lokal fil med inbyggda för hands inställningar](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Skapa jobb ingångar med under Urklipp

När du kodar en video kan du ange att även trimma eller klippa ut käll filen och skapa utdata som bara har en önskad del av indata-videon. Den här funktionen fungerar med alla [transformeringar](/rest/api/media/transforms) som har skapats med antingen [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) för inställningar eller [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) för inställningar.

Du kan ange att du vill skapa ett [jobb](/rest/api/media/jobs/create) med ett enda klipp av en video på begäran eller Live Archive (en inspelad händelse). Jobbets inmatare kan vara en till gång eller en HTTPS-URL.

> [!TIP]
> Om du vill strömma en sublip av videon utan att behöva koda om videon kan du överväga att använda [för filtrerings manifest med dynamisk Paketeraren](filters-dynamic-manifest-overview.md).

#### <a name="examples"></a>Exempel

Se exempel:

* [Klipp en video med .NET](subclip-video-dotnet-howto.md)
* [Klipp en video med REST](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>Inbyggda för hands inställningar

Media Services stöder följande inbyggda kodnings för inställningar:  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) används för att ange en inbyggd för inställning för kodning av inmatad video med Standard-kodaren.

Följande för inställningar stöds för närvarande:

- **EncoderNamedPreset. AACGoodQualityAudio**: skapar en enda MP4-fil som bara innehåller stereo ljud som är kodat med 192 kbps.
- **EncoderNamedPreset. AdaptiveStreaming** (rekommenderas): Mer information finns i [skapa en bit hastighets steg automatiskt](autogen-bitrate-ladder.md).
- **EncoderNamedPreset. ContentAwareEncoding**: visar en för inställning för innehålls medveten kodning. Med allt indata-innehåll, försöker tjänsten automatiskt fastställa det optimala antalet lager och lämpliga bit hastighets-och upplösnings inställningar för leverans genom anpassad direkt uppspelning. De underliggande algoritmerna kommer att fortsätta att utvecklas över tid. Utdata kommer att innehålla MP4-filer med video och ljud som är överlagrade. Mer information finns i [Content medveten encoding](content-aware-encoding.md).

  > [!NOTE]
  > Se till att använda **ContentAwareEncoding** inte ContentAwareEncodingExperimental.
- **EncoderNamedPreset. H264MultipleBitrate1080p**: genererar en uppsättning av åtta GOP MP4-filer, mellan 6000 och 400 kbit/s och stereo AAC-ljud. Lösningen börjar vid 1080p och går ned till 360p.
- **EncoderNamedPreset. H264MultipleBitrate720p**: skapar en uppsättning av sex GOP MP4-filer, mellan 3400 och 400 kbit/s och stereo AAC-ljud. Lösningen börjar vid 720p och går ned till 360p.
- **EncoderNamedPreset. H264MultipleBitrateSD**: genererar en uppsättning av fem GOP MP4-filer, mellan 1600 och 400 kbit/s och stereo AAC-ljud. Lösningen börjar vid 480p och går ned till 360p.
- **EncoderNamedPreset. H264SingleBitrate1080p**: genererar en MP4-fil där videon kodas med H. 264-codec vid 6750 kbit/s och en bild höjd på 1080 bild punkter, och stereo ljudet kodas med AAC-LC-kodek vid 64 kbit/s.
- **EncoderNamedPreset. H264SingleBitrate720p**: genererar en MP4-fil där videon kodas med H. 264-codec vid 4500 kbit/s och en bild höjd på 720 bild punkter, och stereo ljudet kodas med AAC-LC-kodek vid 64 kbit/s.
- **EncoderNamedPreset. H264SingleBitrateSD**: genererar en MP4-fil där videon kodas med H. 264-codec vid 2200 kbit/s och en bild höjd på 480 bild punkter, och stereo ljudet kodas med AAC-LC-kodek vid 64 kbit/s.

Om du vill se listan med de senaste aktuella för inställningarna, se [inbyggda för inställningar som används för att koda videor](/rest/api/media/transforms/createorupdate#encodernamedpreset).

Se [Ladda upp, koda och strömma filer](stream-files-tutorial-with-api.md)för att se hur för inställningarna används.

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) beskriver vilka inställningar som ska användas när du kodar Indataporten med Standard-kodaren. Använd den här för inställningen när du anpassar förinställda transformeringar.

#### <a name="considerations"></a>Överväganden

När du skapar anpassade för inställningar gäller följande aspekter:

- Alla värden för höjd och bredd på AVC-innehåll måste vara en multipel av fyra.
- I Azure Media Services v3 är alla kodnings bit hastigheter i bitar per sekund. Detta skiljer sig från för inställningarna med våra v2-API: er, som använde kilobit/sekund som enhet. Om bit hastigheten i v2 exempelvis angavs som 128 (kilobit/sekund), skulle den vara inställd på 128000 (bitar/sekund) i v3.

### <a name="customizing-presets"></a>Anpassa för inställningar

Media Services har fullständigt stöd för att anpassa alla värden i för inställningar för att uppfylla dina särskilda kodnings behov och krav. Exempel som visar hur du anpassar förval för kodare finns i listan nedan:

#### <a name="examples"></a>Exempel

- [Anpassa för inställningar med .NET](customize-encoder-presets-how-to.md)
- [Anpassa för inställningar med CLI](custom-preset-cli-howto.md)
- [Anpassa för inställningar med REST](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>Förinställt schema

I Media Services v3 är för inställningar starkt skrivna entiteter i själva API: et. Du hittar definitionen "schema" för dessa objekt i [Open API-specifikationen (eller Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Du kan också Visa förinställda definitioner (som **StandardEncoderPreset**) i [REST API](/rest/api/media/transforms/createorupdate#standardencoderpreset), [.net SDK](/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (eller andra Media Services v3 SDK referens dokumentation).

## <a name="scaling-encoding-in-v3"></a>Skalnings kodning i v3

Om du vill skala medie bearbetning, se [skala med CLI](media-reserved-units-cli-how-to.md).

## <a name="billing"></a>Fakturering

Media Services debiteras inte för annullerade eller felaktiga jobb. Ett jobb som har nått 50% Progress och annulleras faktureras exempelvis inte vid 50% av jobb minuterna. Du debiteras bara för färdiga jobb.

Mer information finns i [prissättning](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, hämta uppdateringar

Kolla in [Azure Media Services community](media-services-community.md) -artikeln för att se olika sätt att ställa frågor, lämna feedback och få uppdateringar om Media Services.

## <a name="next-steps"></a>Nästa steg

* [Ladda upp, koda och strömma med Media Services](stream-files-tutorial-with-api.md).
* [Koda från en HTTPS-URL med hjälp av inbyggda för inställningar](job-input-from-http-how-to.md).
* [Koda en lokal fil med inbyggda för hands inställningar](job-input-from-local-file-how-to.md).
* [Bygg en anpassad för inställning för att rikta in dig på specifika scenarier eller enhets krav](customize-encoder-presets-how-to.md).
