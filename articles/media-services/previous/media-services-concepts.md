---
title: Azure Media Services koncept | Microsoft Docs
description: Den här artikeln innehåller en kort översikt över Microsoft Azure Media Services koncept och länkar till andra artiklar för mer information.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 180965942285e99300c8aa054e638f2f0a2c0580
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98695966"
---
# <a name="azure-media-services-concepts"></a>Azure Media Services begrepp

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Det här avsnittet ger en översikt över de viktigaste Media Services begreppen.

## <a name="assets-and-storage"></a><a name="assets"></a>Till gångar och lagring
### <a name="assets"></a>Tillgångar
En [till gång](/rest/api/media/operations/asset) innehåller digitala filer (inklusive video, ljud, bilder, miniatyr samlingar, text spår och filer med dold textning) och metadata om dessa filer. När de digitala filerna har laddats upp till en till gång kan de användas i Media Services kodning och strömnings arbets flöden.

En till gång mappas till en BLOB-behållare i Azure Storage-kontot och filerna i till gången lagras som block-blobbar i den behållaren. Page blobbar stöds inte av Azure Media Services.

När du bestämmer vilket medie innehåll som ska laddas upp och lagras i en till gång gäller följande saker:

* En till gång får bara innehålla en enda, unik instans av medie innehåll. Till exempel en enskild redigering av ett TV-avsnitt, en film eller en annons.
* En till gång får inte innehålla flera åter givningar eller redigeringar av en audiovisuell fil. Ett exempel på en felaktig användning av en till gång skulle försöka lagra fler än ett TV-avsnitt, annons eller flera kamera vinklar från en enda produktion i en till gång. Att lagra flera åter givningar eller redigeringar av en audiovisuell fil i en till gång kan leda till problem med att skicka kodnings jobb, strömma och säkra leverans av till gången senare i arbets flödet.  

### <a name="asset-file"></a>Till gångs fil
En [AssetFile](/rest/api/media/operations/assetfile) representerar en faktisk video-eller ljudfil som lagras i en BLOB-behållare. En till gångs fil är alltid kopplad till en till gång och en till gång kan innehålla en eller flera filer. Media Services Encoder-aktiviteten Miss lyckas om ett till gångs fil objekt inte är associerat med en digital fil i en BLOB-behållare.

**AssetFile** -instansen och den faktiska medie filen är två distinkta objekt. AssetFile-instansen innehåller metadata om medie filen, medan medie filen innehåller det faktiska medie innehållet.

Du bör inte försöka ändra innehållet i BLOB-behållare som har genererats av Media Services utan att använda API: er för media service.

### <a name="asset-encryption-options"></a>Resurs krypterings alternativ
Beroende på vilken typ av innehåll som du vill ladda upp, lagra och leverera Media Services tillhandahåller de olika krypterings alternativ som du kan välja bland.

>[!NOTE]
>Ingen kryptering används. Detta är standardvärdet. När du använder det här alternativet skyddas inte innehållet i överföring eller i vila i lagring.

Om du planerar att leverera en MP4 med progressiv nedladdning kan du använda det här alternativet för att ladda upp ditt innehåll.

**StorageEncrypted** – Använd det här alternativet om du vill kryptera ditt rensade innehåll lokalt med AES 256-bitars kryptering och sedan ladda upp det till Azure Storage där det lagras krypterat i vila. Till gångar som skyddas med lagrings kryptering krypteras automatiskt och placeras i ett krypterat fil system före kodning, och alternativt omkrypteras innan de överförs igen som en ny till gång. Det primära användnings fallet för lagrings kryptering är när du vill skydda dina mediefiler med hög kvalitet med stark kryptering i vila på disk. 

För att kunna leverera en lagrings krypterad till gång måste du konfigurera till gångens leverans princip så Media Services vet hur du vill leverera ditt innehåll. Innan din till gång kan strömmas tar streaming-servern bort lagrings krypteringen och strömmar ditt innehåll med hjälp av den angivna leverans principen (till exempel AES, PlayReady eller ingen kryptering). 

**CommonEncryptionProtected** – Använd det här alternativet om du vill kryptera (eller ladda upp redan krypterat) innehåll med common Encryption eller PlayReady DRM (till exempel Smooth Streaming som skyddas med PlayReady DRM).

**EnvelopeEncryptionProtected** – Använd det här alternativet om du vill skydda (eller överföra redan skyddade) http Live Streaming (HLS) krypterad med Advanced Encryption Standard (AES). Om du laddar upp HLS som redan är krypterad med AES måste den ha krypterats av Transform Manager.

### <a name="access-policy"></a>Åtkomstprincip

En [Access policy](/rest/api/media/operations/accesspolicy) definierar behörigheter (t. ex. läsa, skriva och lista) och varaktigheten för åtkomst till en till gång. Du skickar vanligt vis ett Access policy-objekt till en positionerare som sedan används för att komma åt filerna som finns i en till gång.

>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Du bör använda samma princip-ID om du alltid använder samma dagar/åtkomstbehörigheter, till exempel principer för positionerare som är avsedda att vara på plats under en längre tid (icke-överföringsprinciper). Mer information finns i [detta](media-services-dotnet-manage-entities.md#limit-access-policies) avsnitt.

### <a name="blob-container"></a>Blobcontainer
En BLOB-behållare tillhandahåller en gruppering av en uppsättning blobbar. BLOB-behållare används i Media Services som gränser för åtkomst kontroll och platser för signatur för delad åtkomst (SAS) på till gångar. Ett Azure Storage konto kan innehålla ett obegränsat antal BLOB-behållare. En container kan lagra ett obegränsat antal blobar.

>[!NOTE]
> Du bör inte försöka ändra innehållet i BLOB-behållare som har genererats av Media Services utan att använda API: er för media service.
> 
> 

### <a name="locators"></a><a name="locators"></a>Positionerare
[Locator](/rest/api/media/operations/locator)s ger en start punkt för att komma åt filerna som ingår i en till gång. En åtkomst princip används för att definiera behörigheter och varaktighet som en klient har åtkomst till en specifik till gång. Lokaliserare kan ha en många-till-en-relation med en åtkomst princip, så att olika positionerare kan ge olika start tider och anslutnings typer till olika klienter samtidigt som du använder samma behörighets-och varaktighets inställningar. men på grund av en begränsning för delad åtkomst princip som anges av Azure Storage-tjänster kan du inte ha fler än fem unika positionerare som är kopplade till en specifik till gång på samma gång. 

Media Services stöder två typer av lokaliserare: OnDemandOrigin-positionerare som används för att strömma media (till exempel MPEG-streck, HLS eller Smooth Streaming) eller progressivt Ladda ned Media och SAS URL-positionerare som används för att ladda upp eller ladda ned mediefiler to\from Azure Storage. 

>[!NOTE]
>List behörigheten (AccessPermissions. list) bör inte användas när du skapar en OnDemandOrigin-lokaliserare. 

### <a name="storage-account"></a>Lagringskonto
All åtkomst till Azure Storage görs via ett lagrings konto. Ett media service-konto kan associeras med ett eller flera lagrings konton. Ett konto kan innehålla ett obegränsat antal behållare, så länge den totala storleken är under 500TB per lagrings konto.  Media Services tillhandahåller verktyg för SDK-nivå så att du kan hantera flera lagrings konton och belastningsutjämna distributionen av dina till gångar under överföring till dessa konton baserat på mått eller slumpmässig distribution. Mer information finns i arbeta med [Azure Storage](/previous-versions/azure/dn767951(v=azure.100)). 

## <a name="jobs-and-tasks"></a>Jobb och uppgifter
Ett [jobb](/rest/api/media/operations/job) används vanligt vis för att bearbeta (till exempel indexera eller koda) en ljud-/video presentation. Om du bearbetar flera videor skapar du ett jobb för varje video som ska kodas.

Ett jobb innehåller metadata om den bearbetning som ska utföras. Varje jobb innehåller en eller flera [uppgifter](/rest/api/media/operations/task)som anger en atomisk bearbetnings uppgift, dess inmatnings till gångar, utgående till gångar, en medie processor och dess associerade inställningar. Aktiviteter i ett jobb kan kopplas samman, där utdata till gång till en aktivitet anges som indata till gång till nästa uppgift. På så sätt kan ett jobb innehålla all bearbetning som krävs för en Media presentation.

## <a name="encoding"></a><a id="encoding"></a>Kodning
Azure Media Services tillhandahåller flera alternativ för kodning av media i molnet.

När du börjar med Media Services är det viktigt att förstå skillnaden mellan codec-och fil format.
Codecenheter är program varan som implementerar algoritmerna för komprimering/expandering, medan fil format är behållare som innehåller den komprimerade videon.

Media Services tillhandahåller en dynamisk paketering som gör att du kan leverera din anpassningsbara bit hastighets-MP4 eller Smooth Streaming kodat innehåll i strömnings format som stöds av Media Services (MPEG-streck, HLS Smooth Streaming) utan att du behöver paketera om till dessa strömnings format.

Om du vill dra nytta av [dynamisk paketering](media-services-dynamic-packaging-overview.md)måste du koda din mezzaninfil (källa) till en uppsättning MP4-filer med anpassningsbar bit hastighet eller anpassade bit hastighets Smooth Streaming filer och ha minst en slut punkt för standard-eller Premium-direktuppspelning i Start läge.

Media Services stöder följande kodare på begäran som beskrivs i den här artikeln:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)

Information om den här kodare som stöds finns i [kodare](media-services-encode-asset.md).

## <a name="live-streaming"></a>Liveuppspelning
I Azure Media Services representerar en kanal en pipeline för bearbetning av direktuppspelat strömmande innehåll. En kanal tar emot direktsända indata strömmar på något av två sätt:

* En lokal Live-kodare skickar RTMP med flera bit hastigheter eller Smooth Streaming (fragmenterad MP4) till kanalen. Du kan använda följande Live-kodare som utvärderar multi-bitrs Smooth Streaming: MediaExcel, Ateme, Föreställing Communications, Envivio, Cisco och grundämne. Följande Live-kodare utdata RTMP: Adobe Flash Live Encoder, [Wirecast](media-services-configure-wirecast-live-encoder.md), Teradek, Haivision Encoder. De inmatade strömmarna passerar genom kanaler utan ytterligare kodning och kodning. På begäran levererar Media Services strömmen till kunder.
* En data ström med en bit hastighet (i något av följande format: RTMP eller Smooth Streaming (fragmenterad MP4)) skickas till den kanal som är aktive rad för att utföra direktsänd kodning med Media Services. Kanalen utför sedan Live Encoding av strömmen med en enda bithastighet till en video-ström med flera bithastigheter (anpassningsbar). På begäran levererar Media Services strömmen till kunder.

### <a name="channel"></a>Kanal
I Media Services är [kanal](/rest/api/media/operations/channel)s ansvarig för bearbetning av direktsänd strömmande innehåll. En kanal tillhandahåller en inmatnings slut punkt (inmatnings-URL) som du sedan anger till en Live-kodare. Kanalen tar emot direktsända indata strömmar från direktsänd kodare och gör den tillgänglig för strömning via en eller flera strömnings slut punkter. Kanaler tillhandahåller också en förhands gransknings slut punkt (för hands version) som du använder för att förhandsgranska och validera data strömmen innan ytterligare bearbetning och leverans.

Du kan hämta inmatnings-URL: en och URL: en för för hands versionen när du skapar kanalen. För att hämta dessa URL: er behöver kanalen inte vara i Start läge. När du är redo att börja skicka data från en Live-kodare till kanalen måste du starta kanalen. När Live-kodaren börjar mata in data kan du förhandsgranska data strömmen.

Varje Media Services konto kan innehålla flera kanaler, flera program och flera strömnings slut punkter. Beroende på bandbredds-och säkerhets behoven kan StreamingEndpoint-tjänster vara dedikerade till en eller flera kanaler. Alla StreamingEndpoint kan hämta från vilken kanal som helst.

### <a name="program-event"></a>Program (händelse)
Ett [program (Event)](/rest/api/media/operations/program) gör att du kan styra publicering och lagring av segment i en Live-dataström. Kanaler hanterar program (händelser). Kanal-och program relationen liknar traditionella medier där en kanal har en konstant ström med innehåll och ett program är begränsad till en viss tids period på den kanalen.
Du kan ange hur många timmar du vill behålla det inspelade innehållet för programmet genom att ange egenskapen **ArchiveWindowLength** . Det här värdet kan anges från minst 5 minuter till högst 25 timmar.

ArchiveWindowLength anger också den maximala tid som klienter kan söka bakåt i tiden från den nuvarande aktiva positionen. Program kan köras under den angivna tidsperioden men innehåll som understiger fönsterlängden ignoreras kontinuerligt. Värdet för den här egenskapen avgör också hur länge klientmanifesten kan växa.

Varje program (händelse) är associerat med en till gång. För att publicera programmet måste du skapa en positionerare för den associerade till gången. Med den här lokaliseraren kan du skapa en strömnings-URL som du kan tillhandahålla till dina klienter.

En kanal har stöd för upp till tre program som körs samtidigt så du kan skapa flera arkiv för samma inkommande dataström. På så sätt kan du publicera och arkivera olika delar av en händelse efter behov. Ditt verksamhetsbehov kan till exempel vara att arkivera 6 timmar av ett program, men bara sända 10 minuter. För att åstadkomma detta måste du skapa två program som körs samtidigt. Ett program ställs in för att arkivera 6 timmar av händelsen, men programmet publiceras inte. Det andra programmet ställs in för att arkivera i 10 minuter och det här programmet publiceras.

Mer information finns i:

* [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Arbeta med kanaler som tar emot liveuppspelningar med flera bithastigheter från lokala kodare](media-services-live-streaming-with-onprem-encoders.md)
* [Kvoter och begränsningar](media-services-quotas-and-limitations.md).

## <a name="protecting-content"></a>Skydda innehåll
### <a name="dynamic-encryption"></a>Dynamisk kryptering
Med Azure Media Services kan du skydda dina medier från den tid det lämnar datorn genom lagring, bearbetning och leverans. Med Media Services kan du leverera ditt innehåll dynamiskt med Advanced Encryption Standard (AES) (med 128-bitars krypterings nycklar) och common Encryption (CENC) med hjälp av PlayReady och/eller Widevine DRM. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och PlayReady-licenser till auktoriserade klienter.

För närvarande kan du kryptera följande strömnings format: HLS, MPEG-streck och Smooth Streaming. Det går inte att kryptera Progressive hämtningar.

Om du vill att Media Services ska kryptera en till gång måste du associera en krypterings nyckel (CommonEncryption eller EnvelopeEncryption) med din till gång och även konfigurera Auktoriseringsprinciper för nyckeln.

Om du vill strömma en lagrings krypterad till gång måste du konfigurera till gångens leverans princip för att ange hur du vill leverera till gången.

När en data ström begärs av en spelare, använder Media Services den angivna nyckeln för att dynamiskt Kryptera ditt innehåll med hjälp av en kuvert kryptering (med AES) eller gemensam kryptering (med PlayReady eller Widevine). För att dekryptera data strömmen kommer spelaren att begära nyckeln från Key Delivery Service. För att avgöra om användaren har behörighet att hämta nyckeln utvärderar tjänsten de Auktoriseringsprinciper som du har angett för nyckeln.

### <a name="token-restriction"></a>Begränsning av token
Auktoriseringsprincipen för innehålls nyckeln kan ha en eller flera begränsningar för auktorisering: Open, token-begränsning eller IP-begränsning. Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säker tokentjänst (Secure Token Service – STS). Media Services stöder token i formaten Simple Web Tokens (SWT) och JSON Web Token (JWT). Media Services tillhandahåller inte Secure token-tjänster. Du kan skapa en anpassad STS. STS måste konfigureras för att skapa en token som signerats med den angivna nyckeln och utfärda anspråk som du angav i konfigurationen för token-begränsning. Den Media Services nyckel leverans tjänsten returnerar den begärda nyckeln (eller licensen) till klienten om token är giltig och anspråk i token matchar de som kon figurer ATS för nyckeln (eller licensen).

När du konfigurerar den begränsade token-principen måste du ange primär verifierings nyckel, utfärdare och mål grupps parametrar. Den primära verifierings nyckeln innehåller den nyckel som token har signerats med, utfärdare är den Secure token-tjänst som utfärdar token. Mål gruppen (kallas ibland omfång) beskriver syftet med den token eller resurs som token tillåter åtkomst till. Tjänsten Media Services Key Delivery verifierar att värdena i token matchar värdena i mallen.

Mer information finns i följande artiklar:
- [Skydda innehåll – översikt](media-services-content-protection-overview.md)
- [Skydda med AES-128](media-services-protect-with-aes128.md)
- [Skydda med PlayReady/Widevine](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>Kvalitativ
### <a name="dynamic-packaging"></a><a name="dynamic_packaging"></a>Dynamisk paketering
När du arbetar med Media Services bör du koda dina mezzaninfil-filer till en MP4-uppsättning med anpassad bit hastighet och sedan konvertera uppsättningen till önskat format med hjälp av den [dynamiska packningen](media-services-dynamic-packaging-overview.md).

### <a name="streaming-endpoint"></a>Slutpunkt för direktuppspelning
En StreamingEndpoint representerar en strömmande tjänst som kan leverera innehåll direkt till ett klients pelar program eller till ett Content Delivery Network (CDN) för ytterligare distribution (Azure Media Services nu tillhandahåller Azure CDN-integrering.) Den utgående strömmen från en slut punkt för direkt uppspelning kan vara en Live-ström eller en video på begäran på ditt Media Services-konto. Media Services-kunder väljer antingen en **Standard**-slutpunkt för direktuppspelning eller en eller flera **Premium**-slutpunkter för direktuppspelning, utifrån behov. Standard slut punkten för direkt uppspelning är lämplig för de flesta strömmande arbets belastningar. 

Standard-slutpunkt för direktuppspelning passar de flesta arbetsbelastningar för direktuppspelning. Standard slut punkter för direkt uppspelning ger flexibiliteten att leverera ditt innehåll till i stort sett varje enhet via dynamisk paketering till HLS, MPEG-streck och Smooth Streaming samt dynamisk kryptering för Microsoft PlayReady, Google Widevine, Apple Fairplay och AES128.  De kan också skala från mycket små till mycket stora mål grupper med tusentals samtidiga visnings program via Azure CDN-integrering. Om du har en avancerad arbets belastning eller om kapaciteten för strömnings kapacitet inte passar standard strömnings slut punktens data flödes mål eller om du vill kontrol lera kapaciteten hos StreamingEndpoint-tjänsten för att hantera växande bandbredds behov, rekommenderar vi att du allokerar skalnings enheter (kallas även för Premium streaming units).

Vi rekommenderar att du använder dynamisk paketering och/eller dynamisk kryptering.

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 

Mer information finns i [detta](media-services-portal-manage-streaming-endpoints.md) avsnitt.

Som standard kan du ha upp till 2 strömnings slut punkter i Media Services-kontot. Om du vill begära en högre gräns, se [kvoter och begränsningar](media-services-quotas-and-limitations.md).

Du debiteras bara när StreamingEndpoint är i körnings läge.

### <a name="asset-delivery-policy"></a>Till gångs leverans princip
Ett av stegen i arbets flödet för Media Services innehålls leverans är att konfigurera [leverans principer för till gångar](/rest/api/media/operations/assetdeliverypolicy)som du vill strömma. Policyn för till gångs leverans anger Media Services hur du vill att din till gång ska levereras: i vilket strömnings protokoll ska din till gång vara dynamiskt paketerad (till exempel MPEG-streck, HLS, Smooth Streaming eller alla), oavsett om du vill kryptera till gången dynamiskt och hur (kuvert eller gemensam kryptering).

Om du har en lagrings krypterad till gång, innan din till gång kan strömmas, tar streaming-servern bort lagrings krypteringen och strömmar ditt innehåll med den angivna leverans principen. Om du till exempel vill leverera din till gång krypterad med Advanced Encryption Standard AES-kryptering, anger du princip typen till DynamicEnvelopeEncryption. Om du vill ta bort lagrings kryptering och strömma till gången i Clear, anger du princip typen till NoDynamicEncryption.

### <a name="progressive-download"></a>Progressiv nedladdning
Med progressiv nedladdning kan du starta uppspelning av media innan hela filen har laddats ned. Du kan bara progressivt Ladda ned en MP4-fil.

>[!NOTE]
>Du måste dekryptera krypterade resurser om du vill att de ska vara tillgängliga för progressiv nedladdning.

För att ge användarna en URL för progressiv nedladdning måste du först skapa en OnDemandOrigin-lokaliserare. När du skapar en positionerare får du bas sökvägen till till gången. Du måste lägga till namnet på MP4-filen. Till exempel:

`http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`

### <a name="streaming-urls"></a>Strömmande URL: er
Strömma ditt innehåll till klienter. För att ge användare med direkt uppspelnings-URL: er måste du först skapa en OnDemandOrigin-lokaliserare. När du skapar en positionerare får du bas sökvägen till den till gång som innehåller det innehåll som du vill strömma. För att kunna strömma det här innehållet måste du dock ändra sökvägen ytterligare. Om du vill skapa en fullständig URL till den strömmande manifest filen måste du sammanfoga Sök vägs värdets sökväg och manifest filen (filnamn. ISM). Lägg sedan till/manifest och ett lämpligt format (om det behövs) till sökvägen till lokaliseraren.

Du kan också strömma ditt innehåll via en TLS-anslutning. Det gör du genom att se till att dina strömmande webb adresser börjar med HTTPS. För närvarande stöder AMS inte TLS med anpassade domäner.  

>[!NOTE]
>Du kan bara strömma över TLS om den strömmande slut punkten från vilken du levererar ditt innehåll har skapats efter den 10 september 2014. Om dina strömnings-URL: er baseras på slut punkter för direkt uppspelning som skapats efter den 10 september, innehåller URL: en "streaming.mediaservices.windows.net" (det nya formatet). Strömnings-URL: er som innehåller "origin.mediaservices.windows.net" (det gamla formatet) stöder inte TLS. Om din URL är i det gamla formatet och du vill kunna strömma över TLS, skapar du en ny slut punkt för direkt uppspelning. Använd webb adresser som skapats baserat på den nya slut punkten för direkt uppspelning för att strömma ditt innehåll över TLS.

I följande lista beskrivs olika strömnings format och ger exempel:

* Smooth Streaming

{namn på slutpunkt för direktuppspelning-namn på mediaservicekonto}.streaming.mediaservices.windows.net/lokalisator-ID}/{filnamn}.ism/Manifest

http: \/ /testendpoint-testaccount.streaming.MediaServices.Windows.net/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest

* MPEG DASH

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http: \/ /testendpoint-testaccount.streaming.MediaServices.Windows.net/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (format = mpd-Time-CSF)

* Apple HTTP Live Streaming (HLS) v4

{namn på slutpunkt för direktuppspelning-namn på mediaservicekonto}.streaming.mediaservices.windows.net/{lokalisator-ID}/{filnamn}.ism/Manifest(format=m3u8-aapl)

http: \/ /testendpoint-testaccount.streaming.MediaServices.Windows.net/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (format = M3U8-AAPL)

* Apple HTTP Live Streaming (HLS) v3

{namn på direkt uppspelnings slut punkt – Media Services konto namn}. streaming. Media Services. Windows. net/{Locator ID}/{filename}.ism/Manifest (format = M3U8-AAPL-v3)

http: \/ /testendpoint-testaccount.streaming.MediaServices.Windows.net/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (format = M3U8-AAPL-v3)

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
