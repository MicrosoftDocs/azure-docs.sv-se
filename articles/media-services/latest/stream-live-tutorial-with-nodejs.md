---
title: Strömma live med Media Services v3 Node.js
titleSuffix: Azure Media Services
description: Lär dig hur du strömmar live med Node.js.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc, devx-track-nodejs
ms.date: 04/15/2021
ms.author: inhenkel
ms.openlocfilehash: 749d2fc845f036a2802c80c161b3fc8c171c2555
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730550"
---
# <a name="tutorial-stream-live-with-media-services-using-nodejs-and-typescript"></a>Självstudie: Strömma live med Media Services med Node.js och TypeScript

> [!NOTE]
> Även om självstudien använder Node.js exempel är de allmänna stegen desamma [för REST API,](/rest/api/media/liveevents) [CLI](/cli/azure/ams/live-event)eller andra [SDK:er som stöds.](media-services-apis-overview.md#sdks) 

I Azure Media Services ansvarar [livehändelser](/rest/api/media/liveevents) för bearbetning av liveströmmat innehåll. En livehändelse tillhandahåller en slutpunkt (infognings-URL) som du sedan vidarebefordrar till en livekodare. Livehändelsen tar emot live indataströmmar från livekodaren och gör den tillgänglig för strömning via en eller flera [slutpunkter för direktuppspelning](/rest/api/media/streamingendpoints). Livehändelser tillhandahåller också en slutpunkt för förhandsvisning (förhandsvisnings-URL) som du använder för att förhandsgranska och validera din ström inför vidare behandling och leverans. Den här självstudien visar hur du använder Node.js för att skapa en genomströmningstyp av en **livehändelse** och sända en direktsänd ström till den med [hjälp av OBS Studio.](https://obsproject.com/download)

Självstudien visar hur du:

> [!div class="checklist"]
> * Ladda ned exempelkoden som beskrivs i avsnittet .
> * Granska koden som konfigurerar och utför liveuppspelning.
> * Titta på händelsen med [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) på [https://ampdemo.azureedge.net](https://ampdemo.azureedge.net) .
> * Rensa resurser.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att kunna genomföra vägledningen:

- Installera [Node.js](https://nodejs.org/en/download/)
- Installera [TypeScript](https://www.typescriptlang.org/)
- [Skapa ett Media Services konto](./create-account-howto.md).<br/>Se till att komma ihåg de värden som du använde för resursgruppens namn och namnet på Media Services-kontot.
- Följ stegen i [Access Azure Media Services API with the Azure CLI](./access-api-howto.md) (Få åtkomst till Azure Media Services-API med Azure CLI) och spara autentiseringsuppgifterna. Du måste använda dem för att komma åt API:et och konfigurera filen med miljövariabler.
- Gå igenom [konfigurerar och anslut med Node.js](./configure-connect-nodejs-howto.md) först för att förstå hur du använder Node.js klient-SDK
- Installera Visual Studio Code eller Visual Studio.
- [Konfigurera din Visual Studio Code-miljö för](https://code.visualstudio.com/Docs/languages/typescript) att stödja TypeScript-språket.

## <a name="additional-settings-for-live-streaming-software"></a>Ytterligare inställningar för liveuppspelningsprogram

- En kamera eller en enhet (till exempel en bärbar dator) som används för att sända en händelse.
- En lokal programvarukodare som kodar kameraströmmen och skickar den till Media Services Live Streaming-tjänsten med RTMP-protokollet finns i rekommenderade lokala [livekodare.](encode-recommended-on-premises-live-encoders.md) Dataströmmen måste anges i **RTMP**- eller **Smooth Streaming**-format.  
- I det här exemplet rekommenderar vi att du börjar med en programvarukodare som den kostnadsfria [Open Broadcast Software OBS Studio](https://obsproject.com/download) så att det blir enkelt att komma igång.

Det här exemplet förutsätter att du använder OBS Studio för att sända RTMP till inmatningsslutpunkten. Installera OBS Studio först.
Använd följande kodningsinställningar i OBS Studio:

- Kodare: NVIDIA NVENC (om tillgängligt) eller x264
- Hastighetskontroll: CBR
- Bithastighet: 2 500 kbit/s (eller något rimligt för din bärbara dator)
- Nyckelramsintervall: 2 eller 1 s för låg latens  
- Förinställning: Kvalitet eller prestanda med låg latens (NVENC) eller "mycket snabbt" med x264
- Profil: hög
- GPU: 0 (automatiskt)
- Maximalt antal B-bildrutor: 2

> [!TIP]
> Var noga att du kollar igenom [Liveuppspelning med Media Services v3](stream-live-streaming-concept.md) innan du fortsätter.

## <a name="download-and-configure-the-sample"></a>Ladda ned och konfigurera exemplet

Klona följande Git Hub-lagringsplats som innehåller Node.js liveuppspelning till datorn med följande kommando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

Livesändningsexemplet finns i [Live](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live)-mappen.

I [mappen AMSv3Samples](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples) kopierar du filen med namnet "sample.env" till en ny fil med namnet ".env" för att lagra dina miljövariabelinställningar som du samlade in i artikeln Access Azure Media Services API with the Azure CLI (Åtkomst till Azure Media Services API med [Azure CLI).](./access-api-howto.md)
Se till att filen innehåller "punkt" (.) framför .env" för att detta ska fungera korrekt med kodexe exemplet.

[.env-filen innehåller](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/sample.env) din AAD-programnyckel och hemlighet samt kontonamn och prenumerationsinformation som krävs för att autentisera SDK-åtkomst till ditt Media Services konto. .gitignore-filen har redan konfigurerats för att förhindra att den här filen publiceras på den förkonfigurerade lagringsplatsen. Tillåt inte att dessa autentiseringsuppgifter läcks eftersom de är viktiga hemligheter för ditt konto.

> [!IMPORTANT]
> I det här exemplet används ett unikt suffix för varje resurs. Om du avbryter felsökningen eller avslutar appen utan att köra den får du flera livehändelser i ditt konto. <br/>Se till att stoppa de livehändelser som körs. Annars debiteras **du**! Kör programmet hela vägen fram till slutförande för att rensa resurser automatiskt. Om programmet kraschar, eller om du oavsiktligt stoppar felsökaren och bryter ut från programkörningen, bör du kontrollera portalen för att bekräfta att du inte har lämnat några livehändelser i tillstånden Körs eller Fristående som skulle resultera i oönskade debiteringar.

## <a name="examine-the-typescript-code-for-live-streaming"></a>Granska TypeScript-koden för liveuppspelning

I det här avsnittet undersöks funktioner som [definierats i filen index.ts](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/Live/index.ts) i *Live-projektet.*

Exemplet skapar ett unikt suffix för varje resurs så att du inte får namnkollisioner om du kör exemplet flera gånger utan att rensa upp.

### <a name="start-using-media-services-sdk-for-nodejs-with-typescript"></a>Börja använda Media Services SDK för Node.js med TypeScript

Om du vill Media Services API:er med Node.js måste du först lägga till SDK-modulen med [@azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices) npm-pakethanteraren

```bash
npm install @azure/arm-mediaservices
```

I den package.jspå har detta redan konfigurerats åt dig, så du behöver bara köra *npm-installationen* för att läsa in modulerna och beroendena.

1. Öppna en **kommandotolk** och bläddra till exempelkatalogen.
1. Ändra katalogen till mappen AMSv3Samples.

    ```bash
    cd AMSv3Samples
    ```

1. Installera de paket som används i *packages.jspå* filen.

    ```bash
    npm install 
    ```

1. Starta Visual Studio kod från *mappen AMSv3Samples.* (Detta krävs för att starta från mappen där *.vscode-mappen* *ochtsconfig.jspå* filer finns.)

    ```bash
    cd ..
    code .
    ```

Öppna mappen för *Live* och öppna filen *index.ts* i Visual Studio Code-redigeraren.
I filen *index.ts* trycker du på F5 för att starta felsökningsprogrammet.

### <a name="create-the-media-services-client"></a>Skapa Media Services klienten

Följande kodfragment visar hur du skapar Media Services klient i Node.js.
Observera att vi i den här koden först anger egenskapen **longRunningOperationRetryTimeout** för AzureMediaServicesOptions till 2 sekunder för att minska den tid det tar att avssöka statusen för en långvarig åtgärd på Azure Resource Management-slutpunkten.  Eftersom de flesta åtgärder på livehändelser kommer att vara asynkrona och kan ta lite tid att slutföra, bör du minska avsökningsintervallet för SDK från standardvärdet 30 sekunder för att påskynda den tid det tar att slutföra större åtgärder som att skapa livehändelser, starta och stoppa som alla är asynkrona anrop. Två sekunder är det rekommenderade värdet för de flesta användningsfall.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Skapa en livehändelse

Det här avsnittet visar hur du skapar en **pass-through**-typ av livehändelse (LiveEventEncodingType inställd på None). Mer information om andra tillgängliga typer av livehändelser finns i [Typer av livehändelser.](live-event-outputs-concept.md#live-event-types) Förutom genomgångar kan du använda en live-transkodning av livehändelse för molnkodning med 720P eller 1080P med anpassningsbar bithastighet.
 
Några saker som du kanske vill ange när du skapar livehändelsen är:

* Inmatningsprotokollet för livehändelsen (för närvarande stöds RTMP(S) Smooth Streaming protokollen).<br/>Du kan inte ändra protokollalternativet när livehändelsen eller dess associerade liveutdata körs. Om du behöver olika protokoll skapar du separata livehändelse för varje strömningsprotokoll.  
* IP-begränsningar på infogning och förhandsgranskning. Du kan definiera de IP-adresser som får mata in en video till den här livehändelsen. Tillåtna IP-adresser kan anges som en enskild IP-adress (till exempel 10.0.0.1), ett IP-intervall med IP-adress och en CIDR-nätmask (till exempel 10.0.0.1/22) eller ett IP-intervall med en IP-adress och en prickad decimalnätmask (till exempel 10.0.0.1(255.255.252.0)).<br/>Om inga IP-adresser anges och det inte finns någon regeldefinition tillåts ingen IP-adress. Skapa en regel för att tillåta IP-adresser och ange 0.0.0.0/0.<br/>IP-adresserna måste ha något av följande format: IpV4-adress med fyra siffror eller CIDR-adressintervall.
* När du skapar händelsen kan du ange att den ska startas automatiskt. <br/>När autostart är angett till true (sant) startas live-händelsen efter skapandet. Det innebär att faktureringen startar så fort livehändelsen börjar köras. Du måste explicit anropa Stop på livehändelseresursen för att stoppa ytterligare fakturering. Mer information finns i [livehändelsetillstånd och fakturering](live-event-states-billing-concept.md).
Det finns även väntelägen för att starta livehändelsen i ett lägre "allokerat" tillstånd som gör det snabbare att flytta till tillståndet "Körs". Detta är användbart för situationer som heta pooler som snabbt behöver dela ut kanaler till streamers.
* För att en inmatnings-URL ska vara förutsägande och enklare att underhålla i en maskinvarubaserad livekodare anger du egenskapen "useStaticHostname" till true, samt använder ett anpassat unikt GUID i "accessToken". Detaljerad information finns i [Url:er för inmatning av livehändelse.](live-event-outputs-concept.md#live-event-ingest-urls)

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveEvent)]

### <a name="create-an-asset-to-record-and-archive-the-live-event"></a>Skapa en tillgång för att spela in och arkivera livehändelsen

I det här kodblocket skapar du en tom tillgång som ska användas som "band" för att spela in livehändelsearkivet till.
När du lär dig dessa begrepp är det bäst att tänka på objektet "Tillgång" som det band som du skulle infoga i en videobandspelare förr i tiden. "Liveutdata" är bandinspelningsdatorn. "Livehändelsen" är bara videosignalen som kommer in på baksidan av datorn.

Tänk på att tillgången, eller "band", kan skapas när som helst. Det är bara en tom "tillgång" som du lämnar till liveutdataobjektet, bandinspelaren i den här analogin.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateAsset)]

### <a name="create-the-live-output"></a>Skapa liveutdata

I det här avsnittet skapar vi liveutdata som använder tillgångsnamnet som indata för att se var livehändelsen ska spela in. Dessutom ställer vi in det tidsväxlingsfönster (DVR) som ska användas i inspelningen.
Exempelkoden visar hur du ställer in ett tidsväxlingsfönster på 1 timme. Detta gör att klienter kan spela upp var som helst under den senaste timmen av händelsen.  Dessutom finns endast den senaste timmen av livehändelsen kvar i arkivet. Du kan utöka detta till upp till 25 timmar om det behövs.  Observera också att du kan styra namngivningen av utdatamanifestet som använde HLS- och DASH-manifesten i URL-sökvägarna när de publicerades.

Liveutdata, eller "bandinspelning" i vår analogi, kan också skapas när som helst. Det innebär att du kan skapa liveutdata innan du startar signalflödet eller efter. Om du behöver snabba upp saker är det ofta bra att skapa det innan du startar signalflödet.

Liveutdata startar när de skapas och avbryts när de tas bort.  När du tar bort liveutdata tar du inte bort den underliggande tillgången eller innehållet i tillgången. Tänk på det som att mata ut bandet. Tillgången med inspelningen kommer att vara så länge du vill och när den matas ut (vilket innebär att när liveutdata tas bort) blir den tillgänglig för visning på begäran omedelbart.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveOutput)]


### <a name="get-ingest-urls"></a>Hämta infognings-URL:er

När livehändelsen har skapats kan du få ingest-URL:er som du anger till livekodaren. Kodaren använder dessa URL:er för att mata in en liveström med RTMP-protokollet

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetIngestURL)]

### <a name="get-the-preview-url"></a>Hämta förhandsgransknings-URL:en

Använd previewEndpoint för att förhandsgranska och verifiera att indata från kodaren faktiskt tas emot.

> [!IMPORTANT]
> Kontrollera att videon flödar till förhandsgransknings-URL:en innan du fortsätter.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetPreviewURL)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Skapa och hantera livehändelser och liveutdata

När du har strömmat till livehändelsen kan du starta direktuppspelningshändelsen genom att publicera en positionerare för direktuppspelning som dina klientspelare kan använda. Detta gör den tillgänglig för tittare via slutpunkten för direktuppspelning.

Först skapar du signalen genom att skapa "Livehändelsen".  Signalen flödar inte förrän du startar livehändelsen och ansluter kodaren till indata.

Om du vill stoppa bandinspelaren anropar du delete på LiveOutput. Detta tar inte bort innehållet **i** arkivet på bandet "Asset", utan tar bara bort "bandspelaren" och stoppar arkivering. Tillgången sparas alltid med det arkiverade videoinnehållet tills du uttryckligen anropar borttagning för själva tillgången. Så snart du tar bort liveOutput är det registrerade innehållet i "Tillgången" fortfarande tillgängligt för uppspelning genom alla redan publicerade URL:er för positionerare för direktuppspelning. Om du vill ta bort möjligheten för en kund att spela upp det arkiverade innehållet måste du först ta bort alla positionerare från tillgången och även tömma CDN-cachen på URL-sökvägen om du använder ett CDN för leverans. Annars kommer innehållet att finnas i CDN-cacheminnet för standardinställningen för time to live på CDN (som kan vara upp till 72 timmar.)

#### <a name="create-a-streaming-locator-to-publish-hls-and-dash-manifests"></a>Skapa en positionerare för direktuppspelning för att publicera HLS- och DASH-manifest

> [!NOTE]
> När ditt Media Services-konto skapas läggs en **standardslutpunkt** för direktuppspelning till i ditt konto med tillståndet **Stoppad.** Om du vill börja strömma [](encode-dynamic-packaging-concept.md) ditt innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill strömma innehåll från ha tillståndet **Körs.**

När du publicerar tillgången med hjälp av en positionerare för direktuppspelning fortsätter livehändelsen (upp till DVR-fönstrets längd) att visas tills positioneraren för direktuppspelning upphör att gälla eller tas bort, beroende på vilket som kommer först. På så sätt gör du den virtuella bandinspelningen tillgänglig för tittarna så att de kan se live och på begäran. Samma URL kan användas för att titta på livehändelsen, DVR-fönstret eller tillgången på begäran när inspelningen är klar (när liveutdata tas bort.)

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateStreamingLocator)]

#### <a name="build-the-paths-to-the-hls-and-dash-manifests"></a>Skapa sökvägarna till HLS- och DASH-manifesten

Metoden BuildManifestPaths i exemplet visar hur du deterministiskt skapar de strömningsvägar som ska användas för DASH- eller HLS-leverans till olika klienter och spelarramverk.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#BuildManifestPaths)]

## <a name="watch-the-event"></a>Titta på händelsen

Om du vill titta på händelsen kopierar du strömnings-URL:en som du fick när du körde koden som beskrivs i Skapa en positionerare för direktuppspelning. Du kan använda valfri mediespelare. [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) är tillgänglig för att testa din dataström på https://ampdemo.azureedge.net .

Livehändelser konverterar automatiskt händelser till innehåll-på-begäran när de stoppas. Även när du stoppar och tar bort händelsen kan användarna strömma ditt arkiverade innehåll som en video på begäran så länge du inte tar bort tillgången. En tillgång kan inte tas bort om den används av en händelse. händelsen måste tas bort först.

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Rensa upp resurserna på ditt Media Services-konto

Om du kör programmet hela vägen rensas automatiskt alla resurser som används i funktionen "cleanUpResources". Kontrollera att programmet eller felsökaren körs hela vägen till slutförandet eller att du kan läcka resurser och få livehändelser att köras på ditt konto. Dubbelkolla i Azure Portal att alla resurser har rensats i ditt Media Services konto.  

Mer information finns i metoden **cleanUpResources** i exempelkoden.

> [!IMPORTANT]
> Om du lämnar livehändelsen igång så medför det kostnader. Obs! Om projektet/programmet kraschar eller stängs av någon anledning så kan det lämna livehändelsen igång i ett debiterbart tillstånd.

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback och få uppdateringar

Kolla in [artikeln Azure Media Services community](media-services-community.md) för att se olika sätt att ställa frågor, ge feedback och få uppdateringar om Media Services.

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Mer utvecklardokumentation för Node.js på Azure

- [Azure för JavaScript& Node.js utvecklare](/azure/developer/javascript/)
- [Media Services källkod i Git @azure/azure-sdk-for-js Hub-lagringsplatsen](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Dokumentation om Azure-paket för Node.js utvecklare](/javascript/api/overview/azure/)


## <a name="next-steps"></a>Nästa steg

[Strömma filer](stream-files-tutorial-with-api.md)