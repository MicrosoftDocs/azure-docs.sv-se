---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>Obligatoriska fält. Läs mer på aka.ms/skyeye/meta.
rubrik: utveckla med v3-API: er: Azure Media Services Beskrivning: Lär dig mer om regler som gäller för entiteter och API: er när du utvecklar med Media Services v3. tjänster: Media-Services documentationcenter: ' ' author: IngridAtMicrosoft Manager: femila Editor: ' '

MS. service: Media-Services MS. arbets belastning: MS. topic: konceptuell MS. Date: 10/23/2020 MS. author: inhenkel MS. Custom: seodec18

---

# <a name="develop-with-media-services-v3-apis"></a>Utveckla med API:er för Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Som utvecklare kan du använda Media Services [REST API](/rest/api/media/) eller klientbibliotek så att du kan interagera med REST API för att enkelt skapa, hantera och underhålla anpassade mediearbetsflöden. [Media Services v3](https://aka.ms/ams-v3-rest-sdk) -API: et baseras på openapi-specifikationen (tidigare kallat Swagger).

Den här artikeln beskriver regler som gäller för entiteter och API: er när du utvecklar med Media Services v3.

## <a name="accessing-the-azure-media-services-api"></a>Åtkomst till Azure Media Services-API:et

Du måste autentiseras innan du kan få åtkomst till Media Services-resurser och Media Services-API:et. Media Services stöder [Azure Active Directory-baserad (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) autentisering. Två vanliga autentiseringsalternativ:
 
* **Autentisering av tjänstens huvudnamn**: Används för att autentisera en tjänst (till exempel webbappar, funktionsappar, logikappar, API:er och mikrotjänster). Program som ofta använder den här autentiseringsmetoden är appar som kör daemon-tjänster, tjänster på mellan nivå eller schemalagda jobb. För Web Apps bör till exempel alltid vara en mellan nivå som ansluter till Media Services med ett huvud namn för tjänsten.
* **Användarautentisering**: Används för att autentisera en person som använder appen för att interagera med Media Services-resurser. Den interaktiva appen bör först fråga efter användarens autentiseringsuppgifter. Ett exempel är en hanteringskonsolapp som används av behöriga användare för att övervaka kodningsjobb eller liveuppspelning.

Media Services-API:et kräver att användaren eller appen skickar REST API-begäranden för att få åtkomst till Media Services-kontoresursen och att rollen **Deltagare** eller **Ägare** används. Det går att komma åt API:et med rollen **Läsare** men då är endast åtgärderna **Get** eller **List** tillgängliga.Mer information finns i [rollbaserad åtkomst kontroll i Azure (Azure RBAC) för Media Services-konton](rbac-overview.md).

I stället för att skapa tjänstens huvudnamn kan du överväga att använda hanterade identiteter för Azure-resurser så att du kan komma åt Media Services-API:et via Azure Resource Manager. Mer information om hanterade identiteter för Azure-resurser finns i [Vad är hanterade identiteter för Azure-resurser?](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Azure AD-tjänstens huvud namn

Azure AD-appen och tjänstens huvud namn ska finnas i samma klient organisation. När du har skapat appen ger du appens **deltagare** eller **ägar** rollen åtkomst till Media Services kontot.

Om du inte är säker på om du har behörighet att skapa en Azure AD-App, se de [behörigheter som krävs](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

I följande figur representerar talen flödet för förfrågningarna i kronologisk ordning:

![App-autentisering mellan nivåer med AAD från ett webb-API](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. En app på mellan nivå begär en Azure AD-åtkomsttoken som har följande parametrar:  

   * Azure AD-klientens slut punkt.
   * Media Services resurs-URI.
   * Resurs-URI för REST-Media Services.
   * Azure AD-App-värden: klient-ID och klient hemlighet.

   För att hämta alla nödvändiga värden, se [åtkomst Azure Media Services-API](./access-api-howto.md).

2. Azure AD-åtkomsttoken skickas till mellan nivån.
4. Mellan nivån skickar begäran till Azure Media-REST API med Azure AD-token.
5. Mellan nivån hämtar data från Media Services.

### <a name="samples"></a>Exempel

Se följande exempel som visar hur du ansluter till Azure AD-tjänstens huvud namn:

* [Anslut med REST](media-rest-apis-with-postman.md)  
* [Ansluta med Java](configure-connect-java-howto.md)
* [Ansluta med .NET](configure-connect-dotnet-howto.md)
* [Ansluta med .Node.js](configure-connect-nodejs-howto.md)
* [Ansluta med Python](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Namngivningskonventioner

Azure Media Services v3-resursnamn (till exempel tillgångar, jobb, transformeringar) är föremål Azure Resource Manager-namnbegränsningar. I enlighet med Azure Resource Manager är resursnamnen alltid unika. Därför kan du använda alla strängar som unika identifierare (till exempel GUID) för ditt resursnamn.

Media Services resurs namn får inte innehålla: "<", ">", "%", "&", ":", "&#92;", "?", "/", "*", "+", ".", tecknet för enkla citat tecken eller alla kontroll tecken. Alla andra tecken tillåts. Maxlängden för ett resursnamn är 260 tecken.

Mer information om Azure Resource Manager namn finns i [namngivnings krav](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) och [namngivnings konventioner](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="names-of-filesblobs-within-an-asset"></a>Namn på filer/blobbar i en till gång

Namnen på filer/blobbar i en till gång måste följa både BLOB- [namnets krav](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) och [kraven för NTFS-namn](/windows/win32/fileio/naming-a-file). Orsaken till dessa krav är att filerna kan kopieras från Blob Storage till en lokal NTFS-disk för bearbetning.

## <a name="long-running-operations"></a>Tids krävande åtgärder

Åtgärderna som marker ATS med `x-ms-long-running-operation` i Azure Media Services [Swagger-filer](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) är långvariga åtgärder. 

Mer information om hur du spårar asynkrona Azure-åtgärder finns i [asynkrona åtgärder](../../azure-resource-manager/management/async-operations.md).

Media Services har följande tids krävande åtgärder:

* [Skapa Live-händelser](/rest/api/media/liveevents/create)
* [Uppdatera Live-händelser](/rest/api/media/liveevents/update)
* [Ta bort Live-händelse](/rest/api/media/liveevents/delete)
* [Starta Live-händelse](/rest/api/media/liveevents/start)
* [Stoppa LiveEvent](/rest/api/media/liveevents/stop)

  Använd `removeOutputsOnStop` parametern för att ta bort alla associerade Live-utdata när händelsen stoppas.  
* [Återställ LiveEvent](/rest/api/media/liveevents/reset)
* [Skapa LiveOutput](/rest/api/media/liveevents/create)
* [Ta bort LiveOutput](/rest/api/media/liveevents/delete)
* [Skapa StreamingEndpoint](/rest/api/media/streamingendpoints/create)
* [Uppdatera StreamingEndpoint](/rest/api/media/streamingendpoints/update)
* [Ta bort StreamingEndpoint](/rest/api/media/streamingendpoints/delete)
* [Starta StreamingEndpoint](/rest/api/media/streamingendpoints/start)
* [Stoppa StreamingEndpoint](/rest/api/media/streamingendpoints/stop)
* [Skala StreamingEndpoint](/rest/api/media/streamingendpoints/scale)

Vid lyckad sändning av en lång åtgärd visas en "201 skapad" och den måste avsöka för att slutföra åtgärden med det returnerade åtgärds-ID: t.

Artikeln [spåra asynkrona Azure-åtgärder](../../azure-resource-manager/management/async-operations.md) förklarar i djup hur du spårar statusen för asynkrona Azure-åtgärder via värden som returneras i svaret.

Endast en långvarig åtgärd stöds för en specifik Live-händelse eller någon av dess associerade Live-utdata. En tids krävande åtgärd måste slutföras innan en efterföljande tids krävande åtgärd påbörjas på samma LiveEvent eller associerade Live-utdata. För Live-händelser med flera Live-utdata måste du vänta på att en långvarig åtgärd ska slutföras innan en tids krävande åtgärd aktive ras på en annan Live-utdata.

## <a name="sdks"></a>SDK:er

> [!NOTE]
> Azure Media Services v3-SDK: er är inte garanterat tråd säkra. När du utvecklar en app med flera trådar bör du lägga till din egen tråd för trådens synkronisering för att skydda klienten eller använda ett nytt AzureMediaServicesClient-objekt per tråd. Du bör också vara försiktig med problem med flera trådar som introduceras av valfria objekt från din kod till klienten (t. ex. en HttpClient-instans i .NET).

|SDK|Referens|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[.NET-referens](/dotnet/api/overview/azure/mediaservices/management)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Java-referens](/java/api/overview/azure/mediaservices/management)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Python-referens](/python/api/overview/azure/mediaservices/management)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Node.js-referens](/javascript/api/overview/azure/mediaservices/management)| 
|[Go SDK](https://aka.ms/ams-v3-go-sdk) |[Go-referens](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Se även

- [EventGrid .NET SDK som innehåller media service-händelser](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definitioner av Media Services händelser](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) är ett verktyg som är tillgängligt för Windows-kunder som vill lära sig om Media Services. AMSE är ett Winforms-/C#-program som laddar upp, laddar ned, kodar och strömmar VOD- och live-innehåll med Media Services. AMSE-verktyget är till för klienter som vill testa Media Services utan att skriva någon kod. AMSE-koden tillhandahålls som en resurs för kunder som vill utveckla med Media Services.

AMSE är ett projekt med öppen källkod och får support från communityn (problem kan rapporteras till https://github.com/Azure/Azure-Media-Services-Explorer/issues). Det här projektet använder sig av [Microsofts uppförandekod för öppen källkod](https://opensource.microsoft.com/codeofconduct/). Mer information finns i vanliga frågor och [svar om vanliga](https://opensource.microsoft.com/codeofconduct/faq/) opencode@microsoft.com frågor eller kommentarer.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtrering, sortering, sid indelning för Media Services entiteter

Se [filtrering, sortering, sid indelning för Azure Media Services entiteter](filter-order-page-entitites-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, hämta uppdateringar

Kolla in [Azure Media Services community](media-services-community.md) -artikeln för att se olika sätt att ställa frågor, lämna feedback och få uppdateringar om Media Services.

## <a name="see-also"></a>Se även

För att hämta alla nödvändiga värden, se [åtkomst Azure Media Services-API](./access-api-howto.md).

## <a name="next-steps"></a>Nästa steg

* [Ansluta till Media Services med Java](configure-connect-java-howto.md)
* [Ansluta till Media Services med .NET](configure-connect-dotnet-howto.md)
* [Ansluta till Media Services med Node.js](configure-connect-nodejs-howto.md)
* [Ansluta till Media Services med python](configure-connect-python-howto.md)