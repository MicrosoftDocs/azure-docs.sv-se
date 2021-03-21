---
title: Stream Live med Media Services v3: Azure Media Services Beskrivning: Lär dig att strömma live med Azure Media Services v3.
tjänster: Media-Services documentationcenter: ' ' author: IngridAtMicrosoft Manager: femila Editor: ' '

MS. service: Media-Services MS. arbets belastning: Media ms.tgt_pltfrm: na MS. devlang: na MS. topic: självstudie MS. Custom: "MVC, DevX-Track-csharp" MS. Date: 06/13/2019 MS. author: inhenkel

---

# <a name="tutorial-stream-live-with-media-services"></a>Självstudie: strömma live med Media Services

> [!NOTE]
> Även om självstudien använder [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent) -exempel är de allmänna stegen desamma för [REST API](/rest/api/media/liveevents), [CLI](/cli/azure/ams/live-event)eller andra [SDK](media-services-apis-overview.md#sdks): er som stöds. 

I Azure Media Services ansvarar [livehändelser](/rest/api/media/liveevents) för bearbetning av liveströmmat innehåll. En livehändelse tillhandahåller en slutpunkt (infognings-URL) som du sedan vidarebefordrar till en livekodare. Livehändelsen tar emot live indataströmmar från livekodaren och gör den tillgänglig för strömning via en eller flera [slutpunkter för direktuppspelning](/rest/api/media/streamingendpoints). Livehändelser tillhandahåller också en slutpunkt för förhandsvisning (förhandsvisnings-URL) som du använder för att förhandsgranska och validera din ström inför vidare behandling och leverans. Den här självstudien visar hur du använder .NET Core för att skapa en **genomströmnings** typ av en livehändelse.

Självstudien visar hur du:

> [!div class="checklist"]
> * Ladda ned exempel appen som beskrivs i avsnittet.
> * Granska koden som utför direkt uppspelning.
> * Titta på händelsen med [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) på [https://ampdemo.azureedge.net](https://ampdemo.azureedge.net) .
> * Rensa resurser.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att kunna genomföra vägledningen:

- Installera Visual Studio Code eller Visual Studio.
- [Skapa ett Media Services-konto](./create-account-howto.md).<br/>Se till att kopiera API-åtkomsten i JSON-format eller lagra värdena som behövs för att ansluta till Media Services-kontot i det. kuvert-filformat som används i det här exemplet.
- Följ stegen i [Access Azure Media Services API with the Azure CLI](./access-api-howto.md) (Få åtkomst till Azure Media Services-API med Azure CLI) och spara autentiseringsuppgifterna. Du måste använda dem för att få åtkomst till API: et i det här exemplet eller ange dem i fil formatet. kuvert. 
- En kamera eller en enhet (till exempel en bärbar dator) som används för att sända en händelse.
- En lokal program varu kodare som kodar kamera strömmen och skickar den till Media Services Live streaming service med RTMP-protokollet finns i [rekommenderade lokala Live-kodare](recommended-on-premises-live-encoders.md). Dataströmmen måste anges i **RTMP**- eller **Smooth Streaming**-format.  
- För det här exemplet rekommenderar vi att du börjar med en program varu kodare som det kostnads fria [Open broadcast-programmet onlinebanksystem Studio](https://obsproject.com/download) för att göra det enkelt att komma igång. 

> [!TIP]
> Var noga att du kollar igenom [Liveuppspelning med Media Services v3](live-streaming-overview.md) innan du fortsätter. 

## <a name="download-and-configure-the-sample"></a>Ladda ned och konfigurera exemplet

Klona följande git Hub-lagringsplats som innehåller live streaming .NET-exemplet till din dator med hjälp av följande kommando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```

Livesändningsexemplet finns i [Live](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live)-mappen.

Öppna [appsettings.jspå](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/appsettings.json) i det nedladdade projektet. Ersätt värdena med de autentiseringsuppgifter som du har fått från [att komma åt API: er](./access-api-howto.md).

Observera att du också kan använda fil formatet. kuvert i roten i projektet för att ange miljövariabler bara en gång för alla projekt i databasen för .NET-exempel. Kopiera bara exempel. kuvert-filen, Fyll i informationen som du får från sidan Azure Portal Media Services-API-åtkomst eller från Azure CLI.  Byt namn på filen sample. kuvert till bara ". kuvert" för att använda den i alla projekt.
. Gitignore-filen har redan kon figurer ATS för att undvika att publicera innehållet i den här filen till din förgrenade lagrings plats. 

> [!IMPORTANT]
> I det här exemplet används ett unikt suffix för varje resurs. Om du avbryter fel sökningen eller avslutar appen utan att köra den via, kommer du att få flera Live-händelser i ditt konto. <br/>Se till att stoppa de livehändelser som körs. Annars **faktureras** du!

## <a name="examine-the-code-that-performs-live-streaming"></a>Granska den kod som utför du liveuppspelningen

I det här avsnittet beskrivs de funktioner som definierats i filen [program. cs](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/Program.cs) i *LiveEventWithDVR* -projektet.

Exemplet skapar ett unikt suffix för varje resurs så att du inte har några namn konflikter om du kör exemplet flera gånger utan att rensa.


### <a name="start-using-media-services-apis-with-net-sdk"></a>Börja använda API:er för Media Services med .NET SDK

Om du vill börja använda API:er för Media Services med .NET, måste du skapa ett **AzureMediaServicesClient**-objekt. När du skapar objektet måste du ange de autentiseringsuppgifter som krävs för att klienten ska kunna ansluta till Azure med hjälp av Azure AD. I koden som du klonade i början av artikeln skapar funktionen **GetCredentialsAsync** ServiceClientCredentials-objektet baserat på de autentiseringsuppgifter som angavs i den lokala konfigurations filen (appsettings.jspå) eller via filen. miljö-miljövariabler som finns i lagrings platsens rot.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Skapa en livehändelse

Det här avsnittet visar hur du skapar en **pass-through**-typ av livehändelse (LiveEventEncodingType inställd på None). Mer information om andra tillgängliga typer av Live-händelser finns i [Live Event types](live-events-outputs-concept.md#live-event-types). Förutom direkt sändning kan du använda en Live-kodning av Live-händelser för en inbyggd eller 1080P-kodad bit kodning. 
 
Några saker som du kanske vill ange när du skapar en Live-händelse är:

* Inläsnings protokollet för live event (för närvarande, RTMP (S) och Smooth Streaming protokoll stöds).<br/>Du kan inte ändra alternativet protokoll när Live-händelsen eller dess associerade Live-utdata körs. Om du behöver olika protokoll kan du skapa separata Live-händelser för varje strömnings protokoll.  
* IP-begränsningar på infogning och förhandsgranskning. Du kan definiera de IP-adresser som får mata in en video till den här livehändelsen. Tillåtna IP-adresser kan anges som en enskild IP-adress (till exempel 10.0.0.1), ett IP-intervall med IP-adress och en CIDR-nätmask (till exempel 10.0.0.1/22) eller ett IP-intervall med en IP-adress och en prickad decimalnätmask (till exempel 10.0.0.1(255.255.252.0)).<br/>Om inga IP-adresser har angetts och det inte finns någon regel definition kommer ingen IP-adress att tillåtas. Skapa en regel för att tillåta IP-adresser och ange 0.0.0.0/0.<br/>IP-adresserna måste vara i något av följande format: IpV4-adress med fyra nummer eller CIDR-adressintervall.
* När du skapar händelsen kan du ange att den ska autostartas. <br/>När autostart är angett till true (sant) startas live-händelsen efter skapandet. Det innebär att faktureringen börjar så fort direkt händelsen börjar köras. Du måste explicit anropa Stop på livehändelseresursen för att stoppa ytterligare fakturering. Mer information finns i [livehändelsetillstånd och fakturering](live-event-states-billing.md).
Det finns även vänte läges lägen som är tillgängliga för att starta direkt sändningen i en lägre kostnad "allokerat" tillstånd som gör det snabbare att flytta till ett körnings tillstånd. Detta är användbart för situationer som hotpools som behöver distribuera kanaler snabbt till strömmar.
* För att en inmatnings-URL ska vara förutsägelse och enklare att underhålla i en maskinvarubaserad Live-kodare anger du egenskapen "useStaticHostname" till true. Mer detaljerad information finns i [Live Event](live-events-outputs-concept.md#live-event-ingest-urls)inmatnings-URL: er.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>Hämta infognings-URL:er

När Live-händelsen har skapats kan du hämta inmatnings-URL: er som du skickar till Live-kodaren. Kodaren använder dessa URL:er för att mata in en direktsänd dataström.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Hämta förhandsgransknings-URL:en

Använd previewEndpoint för att förhandsgranska och verifiera att indata från kodaren faktiskt tas emot.

> [!IMPORTANT]
> Se till att videon flödar till för hands versionen av URL: en innan du fortsätter.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Skapa och hantera livehändelser och liveutdata

När dataströmmen väl flödar till livehändelsen kan du påbörja strömningshändelsen genom att skapa en tillgång, liveutdata och en positionerare för direktuppspelning. Detta arkiverar dataströmmen och gör den tillgänglig för visning via strömningsslutpunkten.

När du lär dig dessa begrepp är det bäst att tänka på "till gång"-objektet som bandet som du skulle infoga i en video band inspelning under de gamla dagarna. "Live output" är en band inspelnings dator. "Live event" är bara video signalen som kommer till bak sidan av datorn.

Först skapar du signalen genom att skapa "live event".  Signalen flödar inte förrän du startar den Live-händelsen och ansluter din kodare till inmatade.

Du kan skapa bandet när som helst. Det är bara en tom "till gång" som du kommer till objektet Live-utdata, band inspelaren i denna analoga enhet.

Band inspelaren kan skapas när som helst. Det innebär att du kan skapa Live-utdata innan du startar signal flödet eller efter. Om du behöver snabba på saker är det ibland bra att skapa det innan du startar signal flödet.

Om du vill stoppa band inspelaren anropar du Delete på LiveOutput. Detta tar inte bort innehållet på bandet "till gång".  Till gången hålls alltid med det arkiverade video innehållet tills du anropar ta bort dem manuellt på själva till gången.

Nästa avsnitt går igenom hur du skapar till gången ("band") och Live-utdata ("band inspelare").

#### <a name="create-an-asset"></a>Skapa en tillgång

Skapa en tillgång som kan användas av liveutdata. I den analoga bilden ovan kommer det att vara vårt band som vi spelar in i den direktsända video signalen till. Användarna kan se innehållet Live eller på begäran från det här virtuella bandet.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Skapa liveutdata

Liveutdata startar när de skapas och avbryts när de tas bort. Detta ska vara "band inspelare" för vår händelse. När du tar bort Live-utdata tar du inte bort den underliggande till gången eller innehållet i till gången. Tänk på det när bandet matas ut. Till gången med inspelningen kommer att räcka så länge som du vill, och när den matas ut (vilket innebär att när Live-utdata tas bort) kommer den att vara tillgänglig för visning på begäran omedelbart. 

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>Skapa en positionerare för direktuppspelning

> [!NOTE]
> När ditt Media Services-konto skapas läggs en **standard** slut punkt för direkt uppspelning till på ditt konto i **stoppat** tillstånd. Om du vill börja strömma ditt innehåll och dra nytta av [dynamisk paketering](dynamic-packaging-overview.md) och dynamisk kryptering, måste den strömmande slut punkten från vilken du vill strömma innehåll vara i **Kör** tillstånd.

När du publicerar till gången med en plats för strömning, fortsätter Live-händelsen (upp till fönstret för DVR-fönster) att visas tills utgångs punkts-eller borttagnings platsens förfallo datum eller borttagning, beroende på vilket som kommer först. På så sätt kan du göra den virtuella band inspelningen tillgänglig för din visnings publik för att se live och på begäran. Samma URL kan användas för att se live event, DVR-fönstret eller till gången på begäran när inspelningen är klar (när Live-utdata tas bort.)

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateStreamingLocator)]

```csharp

// Get the url to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Rensa upp resurserna på ditt Media Services-konto

Om du är klar med strömnings händelser och vill rensa de resurser som etablerades tidigare följer du stegen nedan:

* Stoppa sändningen av dataströmmen från kodaren.
* Stoppa livehändelsen. När Live-händelsen har stoppats debiteras inga avgifter. När du vill starta den igen har den samma infognings-URL så att du inte behöver konfigurera om din kodare.
* Du kan avbryta din strömningsslutpunkt om du inte vill fortsätta att tillhandahålla arkivet för din direktsända händelse som en strömning på begäran. Om Live-händelsen är i stoppat tillstånd debiteras inga avgifter.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

## <a name="watch-the-event"></a>Titta på händelsen

Du kan titta på händelsen genom att kopiera den strömmande URL som du fick när du körde kod som beskrivs i skapa en strömmande positionerare. Du kan använda en valfri media spelare. [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) tillgängligt för att testa data strömmen på https://ampdemo.azureedge.net .

Livehändelser konverterar automatiskt händelser till innehåll-på-begäran när de stoppas. Även efter att du har stoppat och tagit bort händelsen kan användarna strömma ditt arkiverade innehåll som en video på begäran så länge du inte tar bort till gången. En till gång kan inte tas bort om den används av en händelse. händelsen måste tas bort först.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av resurserna i resursgruppen, inklusive Media Services och de lagringskonton som du skapade för självstudien, tar du bort resursgruppen som du skapade tidigare.

Kör följande CLI-kommando:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Om du lämnar livehändelsen igång så medför det kostnader. Obs! Om projektet/programmet kraschar eller stängs av någon anledning så kan det lämna livehändelsen igång i ett debiterbart tillstånd.

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, hämta uppdateringar

Kolla in [Azure Media Services community](media-services-community.md) -artikeln för att se olika sätt att ställa frågor, lämna feedback och få uppdateringar om Media Services.

## <a name="next-steps"></a>Nästa steg

[Strömma filer](stream-files-tutorial-with-api.md)
 
