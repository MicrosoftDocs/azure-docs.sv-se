---
title: Kom igång med att leverera innehåll på begäran med hjälp av REST | Microsoft Docs
description: Den här självstudien vägleder dig genom stegen för att implementera ett leverans program på begäran med Azure Media Services med hjälp av REST API.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 88194b59-e479-43ac-b179-af4f295e3780
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: ccf4bc18270dcdbcae02f7b45bea2a05ed663227
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008883"
---
# <a name="get-started-with-delivering-content-on-demand-using-rest"></a>Kom igång med att leverera innehåll på begäran med hjälp av REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

I den här snabb starten får du stegvisa anvisningar för hur du implementerar ett leverans program för video på begäran (VoD) med hjälp av Azure Media Services (AMS) REST-API: er.

Självstudierna innehåller det grundläggande Media Services-arbetsflödet och de vanligaste programmeringsobjekt och -uppgifter som krävs för utveckling av Media Services. När du har slutfört självstudien kan du strömma eller progressivt hämta en exempel medie fil som du har laddat upp, kodat och laddat ned.

Följande bild visar några av de vanligast använda objekten när du utvecklar VoD-program mot Media Services OData-modellen.

Klicka på bilden för att visa den i full storlek.  

[![Diagram över några av de vanligaste objekten i Azure Media Services objekt data modell för utveckling av video på begäran-program.](./media/media-services-rest-get-started/media-services-overview-object-model-small.png)](./media/media-services-rest-get-started/media-services-overview-object-model.png#lightbox)

## <a name="prerequisites"></a>Förutsättningar
Följande förutsättningar måste vara uppfyllda för att börja utveckla med Media Services med REST-API: er.

* Ett Azure-konto. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* Ett Media Services-konto. Information om hur du skapar ett Media Services konto finns i [så här skapar du ett Media Services konto](media-services-portal-create-account.md).
* Förståelse för hur du utvecklar med Media Services REST API. Mer information finns i [Översikt över Media Services REST API](media-services-rest-how-to-use.md).
* Ett program som du väljer som kan skicka HTTP-begäranden och-svar. I den här självstudien används [Fiddler](https://www.telerik.com/download/fiddler).

Följande aktiviteter visas i den här snabb starten.

1. Starta slutpunkt för direktuppspelning (med hjälp av Azure Portal).
2. Anslut till Media Services kontot med REST API.
3. Skapa en ny till gång och överför en videofil med REST API.
4. Koda käll filen till en uppsättning MP4-filer med anpassningsbar bit hastighet med REST API.
5. Publicera till gången och få URL: er för strömning och progressiv nedladdning med REST API.
6. Spela upp ditt innehåll.

>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Använd samma princip-ID om du alltid använder samma dagar/åtkomst behörigheter, till exempel principer för positionerare som är avsedda att vara på plats under en längre tid (principer som inte uppladdas). Mer information finns i [den här](media-services-dotnet-manage-entities.md#limit-access-policies) artikeln.

Mer information om AMS REST-enheter som används i den här artikeln finns [Azure Media Services REST API referens](/rest/api/media/operations/azure-media-services-rest-api-reference). Se även [Azure Media Services begrepp](media-services-concepts.md).

>[!NOTE]
>När du använder entiteter i Media Services måste du ange vissa huvud fält och värden i dina HTTP-begäranden. Mer information finns i [installations programmet för Media Services REST API-utveckling](media-services-rest-how-to-use.md).

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Starta slutpunkter för direktuppspelning med Azure Portal

När du arbetar med Azure Media Services, är ett av de vanligaste scenarierna att leverera video via strömning med anpassningsbar bit hastighet. Media Services tillhandahåller en dynamisk paketering som gör att du kan leverera ditt MP4-kodade innehåll med anpassningsbar bithastighet i direktuppspelningsformat som stöds av Media Services (MPEG DASH, HLS, Smooth Streaming) direkt när du så önskar, utan att du behöver lagra på förhand paketerade versioner av vart och ett av dessa direktuppspelningsformat.

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**.

Starta slutpunkten för direktuppspelning genom att göra följande:

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. I fönstret Inställningar klickar du på Slutpunkter för direktuppspelning.
3. Klicka på den slutpunkt för direktuppspelning som är standard.

    Fönstret INFORMATION OM DEN SLUTPUNKT FÖR DIREKTUPPSPELNING SOM ÄR STANDARD visas.

4. Klicka på ikonen Start.
5. Klicka på knappen Spara för att spara ändringarna.

## <a name="connect-to-the-media-services-account-with-rest-api"></a><a id="connect"></a>Anslut till Media Services kontot med REST API

Information om hur du ansluter till AMS-API: et finns i [komma åt Azure Media Services-API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-a-new-asset-and-upload-a-video-file-with-rest-api"></a><a id="upload"></a>Skapa en ny till gång och ladda upp en videofil med REST API

I Media Services överför du dina digitala filer till en tillgång. **Till gångs** enheten kan innehålla video, ljud, bilder, miniatyr samlingar, text spår och filer med dold textning (samt metadata om dessa filer.)  När filerna har laddats upp till till gången lagras innehållet på ett säkert sätt i molnet för vidare bearbetning och strömning.

Ett av de värden som du måste ange när du skapar en till gång är till gångs skapande alternativ. Egenskapen **Options** är ett uppräknings värde som beskriver de krypterings alternativ som en till gång kan skapas med. Ett giltigt värde är ett av värdena i listan nedan, inte en kombination av värden från den här listan:

* **Ingen**  =  **0** – ingen kryptering används. När du använder det här alternativet skyddas inte innehållet i överföring eller i vila i lagring.
    Om du planerar att leverera en MP4 med progressivt nedladdning ska du använda det här alternativet.
* **StorageEncrypted**  =  **1** – krypterar ditt rensade innehåll lokalt med AES-256-bitars kryptering och laddar sedan upp det till Azure Storage där det lagras krypterat i vila. Tillgångar som skyddas med Lagringskryptering avkrypteras automatiskt och placeras i ett krypterat filsystem före kodning och kan krypteras igen innan de överförs tillbaka som en ny utdatatillgång. Lagringskryptering används i första hand när du vill skydda indatamediefiler av hög kvalitet med stark kryptering i vila på disk.
* **CommonEncryptionProtected**  =  **2** – Använd det här alternativet om du överför innehåll som redan har krypterats och skyddas med common Encryption eller PlayReady DRM (till exempel Smooth Streaming som skyddas med PlayReady DRM).
* **EnvelopeEncryptionProtected**  =  **4** – Använd det här alternativet om du överför HLS krypterad med AES. Filerna måste ha kodats och krypterats av Transform Manager.

### <a name="create-an-asset"></a>Skapa en tillgång
En till gång är en behållare för flera typer eller uppsättningar med objekt i Media Services, inklusive video, ljud, bilder, miniatyr samlingar, text spår och filer med dold textning. När du skapar en till gång i REST API måste du skicka POST-begäran till Media Services och placera all egenskaps information om din till gång i begär ande texten.

I följande exempel visas hur du skapar en till gång.

**HTTP-begäran**

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 45

{"Name":"BigBuckBunny.mp4", "Options":"0"}
```

**HTTP-svar**

Om det lyckas returneras följande:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 452
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
request-id: e98be122-ae09-473a-8072-0ccd234a0657
x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 18 Jan 2015 22:06:40 GMT

{  
    odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
   "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "State":0,
   "Created":"2015-01-18T22:06:40.6010903Z",
   "LastModified":"2015-01-18T22:06:40.6010903Z",
   "AlternateId":null,
   "Name":"BigBuckBunny2.mp4",
   "Options":0,
   "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "StorageAccountName":"storagetestaccount001"
}
```

### <a name="create-an-assetfile"></a>Skapa en AssetFile
Entiteten [AssetFile](/rest/api/media/operations/assetfile) representerar en video-eller ljudfil som lagras i en BLOB-behållare. En till gångs fil är alltid kopplad till en till gång och en till gång kan innehålla en eller flera AssetFiles. Media Services Encoder-aktiviteten Miss lyckas om ett till gångs fil objekt inte är associerat med en digital fil i en BLOB-behållare.

När du har överfört din digitala mediefil till en BLOB-behållare använder du kommandot **slå samman** http för att uppdatera AssetFile med information om medie filen (som visas senare i avsnittet).

**HTTP-begäran**

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 164

{  
   "IsEncrypted":"false",
   "IsPrimary":"false",
   "MimeType":"video/mp4",
   "Name":"BigBuckBunny.mp4",
   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
}
```

**HTTP-svar**

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 535
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
Server: Microsoft-IIS/8.5
request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 00:34:07 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
   "Name":"BigBuckBunny.mp4",
   "ContentFileSize":"0",
   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "EncryptionVersion":null,
   "EncryptionScheme":null,
   "IsEncrypted":false,
   "EncryptionKeyId":null,
   "InitializationVector":null,
   "IsPrimary":false,
   "LastModified":"2015-01-19T00:34:08.1934137Z",
   "Created":"2015-01-19T00:34:08.1934137Z",
   "MimeType":"video/mp4",
   "ContentChecksum":null
}
```

### <a name="creating-the-accesspolicy-with-write-permission"></a>Skapa Access policy med Skriv behörighet
Innan du överför filer till Blob Storage anger du åtkomst princip rättigheter för skrivning till en till gång. Det gör du genom att skicka en HTTP-begäran till enhets uppsättningen AccessPolicies. Definiera ett DurationInMinutes-värde när du skapar eller så får du ett internt 500-server fel meddelande i svaret. Mer information om AccessPolicies finns i [Access policy](/rest/api/media/operations/accesspolicy).

I följande exempel visas hur du skapar en Access policy:

**HTTP-begäran**

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 74

{"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"}
```

**HTTP-svar**

Om det lyckas returneras följande svar:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 312
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
Server: Microsoft-IIS/8.5
request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 18 Jan 2015 22:18:06 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
   "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
   "Created":"2015-01-18T22:18:06.6370575Z",
   "LastModified":"2015-01-18T22:18:06.6370575Z",
   "Name":"NewUploadPolicy",
   "DurationInMinutes":440.0,
   "Permissions":2
}
```

### <a name="get-the-upload-url"></a>Hämta överförings-URL: en

Skapa en SAS-lokaliserare för att få den faktiska uppladdnings-URL: en. Lokaliserare definierar start tiden och typen av anslutnings slut punkt för klienter som vill komma åt filer i en till gång. Du kan skapa flera lokaliserade entiteter för en specifik Access policy och till gångs par för att hantera olika klient begär Anden och behov. Var och en av dessa positionerare använder StartTime-värdet plus DurationInMinutes-värdet för Access policy för att bestämma hur lång tid en URL kan användas. Mer information finns i [Locator](/rest/api/media/operations/locator).

En SAS-URL har följande format:

`{https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}`

Vissa förutsättningar gäller:

* Du kan inte ha fler än fem unika positionerare kopplade till en specifik till gång på samma gång. 
* Om du behöver ladda upp dina filer direkt ska du ställa in StartTime-värdet på fem minuter före den aktuella tiden. Detta beror på att det kan finnas en klock skevning mellan klient datorn och Media Services. StartTime-värdet måste också vara i följande DateTime-format: ÅÅÅÅ-MM-DDTHH: mm: ssZ (till exempel "2014-05-23T17:53:50Z").    
* Det kan finnas en fördröjning på 30-40 sekunder efter att en positionerare har skapats till när den är tillgänglig för användning. Det här problemet gäller både [SAS-URL](../../storage/common/storage-sas-overview.md) och ursprungs positionerare.

I följande exempel visas hur du skapar en SAS-URL Locator, som definieras av type-egenskapen i begär ande texten ("1" för en SAS-lokaliserare och "2" för en käll plats på begäran). **Sök vägs** egenskapen som returnerade innehåller den URL som du måste använda för att ladda upp filen.

**HTTP-begäran**

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 178

{  
   "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
   "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "StartTime":"2015-02-18T16:45:53",
   "Type":1
}
```

**HTTP-svar**

Om det lyckas returneras följande svar:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 949
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
Server: Microsoft-IIS/8.5
request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 03:01:29 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
   "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
   "ExpirationDateTime":"2015-02-19T00:05:53",
   "Type":1,
   "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
   "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
   "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
   "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
   "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "StartTime":"2015-02-18T16:45:53",
   "Name":null
}
```

### <a name="upload-a-file-into-a-blob-storage-container"></a>Ladda upp en fil till en Blob Storage-behållare
När du har angett Access policy och lokaliseraren laddas den faktiska filen upp till en Azure Blob Storage-behållare med hjälp av Azure Storage REST-API: er. Du måste överföra filerna som block-blobbar. Page blobbar stöds inte av Azure Media Services.  

> [!NOTE]
> Du måste lägga till fil namnet för den fil som du vill överföra till värdet för sökvägen till lokaliserings **Sök vägen** som togs emot i föregående avsnitt. Till exempel `https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4?`.
>
>

Mer information om hur du arbetar med Azure Storage-blobar finns i [BLOB Service REST API](/rest/api/storageservices/blob-service-rest-api).

### <a name="update-the-assetfile"></a>Uppdatera AssetFile
Nu när du har laddat upp filen uppdaterar du FileAsset storlek (och annan). Exempel:

```console
MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net

{  
   "ContentFileSize":"1186540",
   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
   "MimeType":"video/mp4",
   "Name":"BigBuckBunny.mp4",
   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
}
```

**HTTP-svar**

Om det lyckas returneras följande:

```console
HTTP/1.1 204 No Content
...
```

## <a name="delete-the-locator-and-accesspolicy"></a>Ta bort lokaliseraren och Access policy
**HTTP-begäran**

```console
DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
```

**HTTP-svar**

Om det lyckas returneras följande:

```console
HTTP/1.1 204 No Content
...
```

**HTTP-begäran**

```console
DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
```

**HTTP-svar**

Om det lyckas returneras följande:

```console
HTTP/1.1 204 No Content
...
```

## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a><a id="encode"></a>Koda käll filen till en uppsättning MP4-filer med anpassningsbar bit hastighet

När du har matat in till gångar i Media Services kan Media kodas, transmux, vattenstämplas och så vidare innan de levereras till klienter. Dessa aktiviteter schemaläggs och körs mot flera bakgrundsrollinstanser för höga prestanda och tillgänglighet. Dessa aktiviteter kallas jobb och varje jobb består av atomiska uppgifter som gör det faktiska arbetet i till gångs filen (mer information finns i [jobb](/rest/api/media/operations/job), [aktivitets](/rest/api/media/operations/task) beskrivningar).

Som tidigare nämnts kan du när du arbetar med Azure Media Services ett av de vanligaste scenarierna leverera strömning med anpassad bit hastighet till dina klienter. Media Services kan dynamiskt paketera en uppsättning MP4-filer med anpassningsbar bit hastighet i något av följande format: HTTP Live Streaming (HLS), Smooth Streaming, MPEG-streck.

I följande avsnitt visas hur du skapar ett jobb som innehåller en kodnings uppgift. Uppgiften anger att Omkoda mezzaninfil-filen till en uppsättning hastigheter med anpassningsbar bit hastighet med **Media Encoder Standard**. Avsnittet visar också hur du övervakar jobb bearbetnings förloppet. När jobbet har slutförts kan du skapa positionerare som behövs för att få åtkomst till dina till gångar.

### <a name="get-a-media-processor"></a>Hämta en medie processor
I Media Services är en medie processor en komponent som hanterar en speciell bearbetnings aktivitet, till exempel kodning, format konvertering, kryptering eller dekryptering av medie innehåll. För den kodnings uppgift som visas i den här självstudien ska vi använda Media Encoder Standard.

Följande kod begär kodarens ID.

**HTTP-begäran**

```console
    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net
```

**HTTP-svar**

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 273
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 07:54:09 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
   "value":[  
      {  
         "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "Description":"Media Encoder Standard",
         "Name":"Media Encoder Standard",
         "Sku":"",
         "Vendor":"Microsoft",
         "Version":"1.1"
      }
   ]
}
```

### <a name="create-a-job"></a>Skapa ett jobb
Varje jobb kan ha en eller flera aktiviteter beroende på vilken typ av bearbetning du vill utföra. Du kan skapa jobb och deras relaterade uppgifter på ett av två sätt med hjälp av REST API: aktiviteter kan definieras infogade via aktiviteternas navigerings egenskap på jobb enheter eller genom OData batch-bearbetning. Media Services SDK använder batchbearbetning. För läsning av kod exemplen i den här artikeln definieras dock uppgifter som infogas. Information om batchbearbetning finns i batch- [bearbetning för Open data Protocol (OData)](https://www.odata.org/documentation/odata-version-3-0/batch-processing/).

I följande exempel visas hur du skapar och publicerar ett jobb med en aktivitets uppsättning för att koda en video med en angiven upplösning och kvalitet. Följande dokumentations avsnitt innehåller en lista över alla [aktivitets för inställningar](./media-services-mes-presets-overview.md) som stöds av Media Encoder Standard-processorn.  

**HTTP-begäran**

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 482

{  
   "Name":"NewTestJob",
   "InputMediaAssets":[  
      {  
         "__metadata":{  
            "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
         }
      }
   ],
   "Tasks":[  
      {  
         "Configuration":"Adaptive Streaming",
         "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
            <outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
      }
   ]
}
```

**HTTP-svar**

Om det lyckas returneras följande svar:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 1215
Content-Type: application/json;odata=verbose;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
Server: Microsoft-IIS/8.5
request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 08:04:35 GMT

{  
   "d":{  
      "__metadata":{  
         "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
         "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
      },
      "Tasks":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
         }
      },
      "OutputMediaAssets":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
         }
      },
      "InputMediaAssets":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
         }
      },
      "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
      "Name":"NewTestJob",
      "Created":"2015-01-19T08:04:34.3287057Z",
      "LastModified":"2015-01-19T08:04:34.3287057Z",
      "EndTime":null,
      "Priority":0,
      "RunningDuration":0,
      "StartTime":null,
      "State":0,
      "TemplateId":null,
      "JobNotificationSubscriptions":{  
         "__metadata":{  
            "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
         },
         "results":[  

         ]
      }
   }
}
```

Det finns några viktiga saker att notera i en jobb förfrågan:

* TaskBody-egenskaper måste använda literal XML för att definiera antalet indata eller utgående till gångar som används av aktiviteten. Uppgifts artikeln innehåller XML-schema definitionen för XML-koden.
* I TaskBody-definitionen måste varje inre värde för `<inputAsset>` och `<outputAsset>` anges som JobInputAsset (värde) eller JobOutputAsset (värde).
* En aktivitet kan ha flera utmatnings till gångar. En JobOutputAsset (x) kan bara användas en gång som utdata för en aktivitet i ett jobb.
* Du kan ange JobInputAsset eller JobOutputAsset som en inmatad till gång för en aktivitet.
* Aktiviteter får inte utgöra en cykel.
* Värde parametern som du skickar till JobInputAsset eller JobOutputAsset representerar index värdet för en till gång. De faktiska till gångarna definieras i InputMediaAssets-och OutputMediaAssets-navigerings egenskaperna på jobb enhets definitionen.

> [!NOTE]
> Eftersom Media Services bygger på OData v3 refereras de enskilda till gångarna i InputMediaAssets-och OutputMediaAssets-navigerings egenskaps samlingarna via ett "__metadata: URI"-namn-värdepar.
>
>

* InputMediaAssets mappar till en eller flera till gångar som du har skapat i Media Services. OutputMediaAssets skapas av systemet. De refererar inte till en befintlig till gång.
* OutputMediaAssets kan namnges med attributet assetName. Om det här attributet inte finns är namnet på OutputMediaAsset det som är det inre text värdet för `<outputAsset>` elementet med ett suffix för antingen jobbnamn eller jobb-ID-värdet (i de fall där namn egenskapen inte har definierats). Om du till exempel anger ett värde för assetName till "Sample", skulle egenskapen OutputMediaAsset name anges till "Sample". Men om du inte har angett ett värde för assetName, men ställt in jobb namnet på "NewJob", blir OutputMediaAsset-namnet "JobOutputAsset (värde) _NewJob".

    I följande exempel visas hur du ställer in attributet assetName:

    ```console
    "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"
    ```
* Så här aktiverar du aktivitets länkning:

  * Ett jobb måste innehålla minst två aktiviteter
  * Det måste finnas minst en aktivitet vars indata är utdata från en annan aktivitet i jobbet.

Mer information finns i [skapa ett kodnings jobb med Media Services REST API](media-services-rest-encode-asset.md).

### <a name="monitor-processing-progress"></a>Övervaka bearbetnings förlopp
Du kan hämta jobb statusen genom att använda egenskapen state, som du ser i följande exempel:

**HTTP-begäran**

```console
GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs.net
Content-Length: 0
```

**HTTP-svar**

Om det lyckas returneras följande svar:

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 17
Content-Type: application/json;odata=verbose;charset=utf-8
Server: Microsoft-IIS/7.5
x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
X-Content-Type-Options: nosniff
DataServiceVersion: 1.0;
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
Date: Sun, 13 May 2012 05:16:53 GMT

{"d":{"State":2}}
```

### <a name="cancel-a-job"></a>Avbryta ett jobb
Med Media Services kan du avbryta jobb som körs via funktionen CancelJob. Anropet returnerar en 400-felkod om du försöker avbryta ett jobb när dess tillstånd har avbrutits, Avbryt, fel eller slutfört.

I följande exempel visas hur du anropar CancelJob.

**HTTP-begäran**

```console
GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs.net
```

Om det lyckas returneras en 204-svars kod utan meddelande text.

> [!NOTE]
> Du måste URL-koda jobb-ID: t (normalt Obs: jid: UUID: someValue) när det skickas i som en parameter till CancelJob.
>
>

### <a name="get-the-output-asset"></a>Hämta utdata till gång
Följande kod visar hur du begär ID för utdata till gång.

**HTTP-begäran**

```console
GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
```

**HTTP-svar**

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 445
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 08:28:13 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
   "value":[  
      {  
         "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
         "State":0,
         "Created":"2015-01-19T07:52:15.603",
         "LastModified":"2015-01-19T07:52:15.603",
         "AlternateId":null,
         "Name":"Multibitrate MP4s",
         "Options":0,
         "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
         "StorageAccountName":"storagetestaccount001"
      }
   ]
}
```

## <a name="publish-the-asset-and-get-streaming-and-progressive-download-urls-with-rest-api"></a><a id="publish_get_urls"></a>Publicera till gången och få URL: er för strömning och progressiv nedladdning med REST API

Om du vill strömma eller hämta en tillgång behöver du först ”publicera” den genom att skapa en positionerare. Lokaliserare ger åtkomst till filer som finns i tillgången. Media Services stöder två typer av positionerare: OnDemandOrigin-positionerare som används för att strömma media (till exempel MPEG DASH, HLS eller Smooth Streaming) och Access Signature (SAS)-positionerare som används för att hämta mediefiler. 

När du har skapat lokaliserarna kan du skapa de URL: er som används för att strömma eller hämta dina filer.

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**.

En strömmande URL för Smooth Streaming har följande format:

```console
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest
```

En strömmande URL för HLS har följande format:

```console
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)
```

En strömmande URL för MPEG DASH har följande format:

```console
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)
```

En SAS-URL som används för att hämta filer har följande format:

```console
{blob container name}/{asset name}/{file name}/{SAS signature}
```

Det här avsnittet visar hur du utför följande uppgifter som krävs för att publicera till gångar.  

* Skapa Access policy med Läs behörighet
* Skapa en SAS-URL för nedladdning av innehåll
* Skapa en ursprunglig URL för strömmande innehåll

### <a name="creating-the-accesspolicy-with-read-permission"></a>Skapa Access policy med Läs behörighet
Innan du hämtar eller strömma media innehåll måste du först definiera en Access policy med Läs behörighet och skapa lämplig enhet för lokaliserare som anger vilken typ av leverans mekanism du vill aktivera för dina klienter. Mer information om tillgängliga egenskaper finns i egenskaper för [Access policy-entitet](/rest/api/media/operations/accesspolicy#accesspolicy_properties).

I följande exempel visas hur du anger Access policy för Läs behörighet för en specifik till gång.

```console
POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
Content-Type: application/json
Accept: application/json
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs.net
Content-Length: 74
Expect: 100-continue

{"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}
```

Om det lyckas returneras en 201-lyckad kod som beskriver den Access Policy-enhet som du skapade. Sedan använder du Access Policy-ID tillsammans med till gångs-ID: t för den till gång som innehåller den fil som du vill leverera (till exempel en utgående till gång) för att skapa en Locator-enhet.

> [!NOTE]
> Det här grundläggande arbets flödet är detsamma som att överföra en fil när du matar in en till gång (som tidigare beskrevs i det här avsnittet). I likhet med att överföra filer, om du (eller dina klienter) behöver få åtkomst till dina filer direkt, ställer du in StartTime-värdet på fem minuter före den aktuella tiden. Den här åtgärden är nödvändig eftersom det kan finnas en klock förvrängning mellan klienten och Media Services. StartTime-värdet måste vara i följande DateTime-format: ÅÅÅÅ-MM-DDTHH: mm: ssZ (till exempel "2014-05-23T17:53:50Z").
>
>

### <a name="creating-a-sas-url-for-downloading-content"></a>Skapa en SAS-URL för nedladdning av innehåll
Följande kod visar hur du hämtar en URL som kan användas för att hämta en mediefil som skapats och laddats upp tidigare. Access policy har Läs behörighet och sökvägen refererar till en URL för SAS-nedladdning.

```console
POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
Content-Type: application/json
Accept: application/json
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs.net
Content-Length: 182
Expect: 100-continue

{"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}
```

Om det lyckas returneras följande svar:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 1150
Content-Type: application/json;odata=verbose;charset=utf-8
Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
Server: Microsoft-IIS/7.5
x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
X-Content-Type-Options: nosniff
DataServiceVersion: 1.0;
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
Date: Mon, 14 May 2012 21:41:32 GMT

{  
   "d":{  
      "__metadata":{  
         "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
         "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
      },
      "AccessPolicy":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
         }
      },
      "Asset":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
         }
      },
      "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
      "ExpirationDateTime":"\/Date(1337049393000)\/",
      "Type":1,
      "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
      "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
      "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
      "StartTime":"\/Date(1337031393000)\/"
   }
}
```

Den returnerade **Sök vägs** egenskapen innehåller SAS-URL: en.

> [!NOTE]
> Om du hämtar krypterat lagrings innehåll måste du manuellt dekryptera det innan du återger det, eller använda MediaProcessor för dekryptering i en bearbetnings uppgift för att bearbeta bearbetade filer i klartext i en OutputAsset och sedan ladda ned från den till gången. Mer information om bearbetning finns i skapa ett kodnings jobb med Media Services REST API. Dessutom går det inte att uppdatera SAS URL-positionerare när de har skapats. Du kan till exempel inte återanvända samma positionerare med ett uppdaterat StartTime-värde. Detta beror på hur SAS-webbadresser skapas. Om du vill få åtkomst till en till gång för hämtning när en lokaliserare har upphört att gälla, måste du skapa en ny med en ny StartTime.
>
>

### <a name="download-files"></a>Ladda ned filer
När du har angett Access policy och lokaliseraren kan du hämta filer med hjälp av Azure Storage REST-API: er.  

> [!NOTE]
> Du måste lägga till fil namnet för den fil som du vill ladda ned till värdet för sökvägen till lokaliserings **Sök vägen** som togs emot i föregående avsnitt. Till exempel `https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4` ? . . .

Mer information om hur du arbetar med Azure Storage-blobar finns i [BLOB Service REST API](/rest/api/storageservices/blob-service-rest-api).

Som ett resultat av det kodnings jobb som du utförde tidigare (kodning i adaptiv MP4-uppsättning) har du flera MP4-filer som du kan ladda ned progressivt. Exempel:    

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

### <a name="creating-a-streaming-url-for-streaming-content"></a>Skapa en strömmande URL för strömmande innehåll
Följande kod visar hur du skapar en URL-adress för strömning:

```console
POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
Content-Type: application/json
Accept: application/json
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs
Content-Length: 182
Expect: 100-continue

{"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}
```

Om det lyckas returneras följande svar:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 981
Content-Type: application/json;odata=verbose;charset=utf-8
Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
Server: Microsoft-IIS/7.5
x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
X-Content-Type-Options: nosniff
DataServiceVersion: 1.0;
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
Date: Mon, 14 May 2012 21:41:39 GMT

{  
   "d":{  
      "__metadata":{  
         "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
         "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
      },
      "AccessPolicy":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
         }
      },
      "Asset":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
         }
      },
      "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
      "ExpirationDateTime":"\/Date(1337049395000)\/",
      "Type":2,
      "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
      "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
      "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
      "StartTime":"\/Date(1337031395000)\/"
   }
}
```

Om du vill strömma en Smooth Streaming ursprungs-URL i en strömmande medie spelare måste du lägga till egenskapen sökväg med namnet på manifest filen för Smooth Streaming följt av "/manifest".

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest`

Om du vill strömma HLS lägger du till (format = M3U8-AAPL) efter "/manifest".

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)`

Om du vill strömma MPEG-streck lägger du till (format = mpd-Time-CSF) efter "/manifest".

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)`


## <a name="play-your-content"></a><a id="play"></a>Spela upp ditt innehåll
Strömma videon med hjälp av [Azure Media Services Player](https://aka.ms/azuremediaplayer).

Om du vill testa progressiv nedladdning klistrar du in en URL i en webbläsare (till exempel IE, Chrome, Safari).

## <a name="next-steps-media-services-learning-paths"></a>Nästa steg: sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]