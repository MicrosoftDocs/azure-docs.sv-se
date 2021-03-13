---
title: Så här kodar du en Azure-till gång genom att använda Media Encoder Standard | Microsoft Docs
description: Lär dig hur du använder Media Encoder Standard för att koda medie innehåll på Azure Media Services. Kod exempel använder REST API.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 2a7273c6-8a22-4f82-9bfe-4509ff32d4a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: d4b7a188a9509c3510de2843b232f57e3437177b
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009835"
---
# <a name="how-to-encode-an-asset-by-using-media-encoder-standard"></a>Så här kodar du en till gång genom att använda Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
> * [REST](media-services-rest-encode-asset.md)
> * [Portal](media-services-portal-encode.md)
>
>

## <a name="overview"></a>Översikt

Om du vill leverera digital video via Internet måste du komprimera mediet. Digitala videofiler är stora och kan vara för stora att leverera via Internet, eller för att kundernas enheter ska kunna visas korrekt. Encoding är en process för att komprimera video och ljud så att dina kunder kan se dina media.

Kodnings jobb är en av de vanligaste bearbetnings åtgärderna i Azure Media Services. Du skapar kodnings jobb för att konvertera mediefiler från en kodning till en annan. När du kodar kan du använda Media Services inbyggd kodare (Media Encoder Standard). Du kan också använda en kodare som tillhandahålls av en Media Services-partner. Kodare från tredje part är tillgängliga via Azure Marketplace. Du kan ange information om kodning av aktiviteter med hjälp av förinställda strängar som definierats för kodaren eller med hjälp av förinställda konfigurationsfiler. Information om vilka typer av för hands inställningar som är tillgängliga finns i [aktivitets för inställningar för Media Encoder Standard](./media-services-mes-presets-overview.md).

Varje jobb kan ha en eller flera aktiviteter beroende på vilken typ av bearbetning du vill utföra. Du kan skapa jobb och deras relaterade uppgifter på ett av två sätt med hjälp av REST API:

* Aktiviteter kan definieras infogade via aktiviteternas navigerings egenskap på jobb enheter.
* Använd OData batch-bearbetning.

Vi rekommenderar att du alltid kodar källfilerna till en MP4-uppsättning med anpassad bit hastighet och sedan konverterar uppsättningen till önskat format med hjälp av [dynamisk paketering](media-services-dynamic-packaging-overview.md).

Om din utgående till gång är krypterad måste du konfigurera till gångs leverans principen. Mer information finns i [Konfigurera till gångs leverans princip](media-services-rest-configure-asset-delivery-policy.md).

## <a name="considerations"></a>Överväganden

När du använder entiteter i Media Services måste du ange vissa huvud fält och värden i dina HTTP-begäranden. Mer information finns i [installations programmet för Media Services REST API-utveckling](media-services-rest-how-to-use.md).

Innan du börjar referera till medie processorer kontrollerar du att du har rätt medie processor-ID. Mer information finns i [Hämta medie processorer](media-services-rest-get-media-processor.md).

## <a name="connect-to-media-services"></a>Ansluta till Media Services

Information om hur du ansluter till AMS-API: et finns i [komma åt Azure Media Services-API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-a-job-with-a-single-encoding-task"></a>Skapa ett jobb med en enda kodnings uppgift

> [!NOTE]
> När du arbetar med Media Services REST API gäller följande saker:
>
> När du använder entiteter i Media Services måste du ange vissa huvud fält och värden i dina HTTP-begäranden. Mer information finns i [installations programmet för Media Services REST API-utveckling](media-services-rest-how-to-use.md).
>
> När du använder JSON och anger att ska använda **__metadata** nyckelordet i begäran (t. ex. för att referera till ett länkat objekt) måste du ange [formatet](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/)för att **godkänna** huvud till JSON-verbose: accept: Application/JSON; OData = verbose.
>
>

I följande exempel visas hur du skapar och publicerar ett jobb med en aktivitets uppsättning för att koda en video med en angiven upplösning och kvalitet. När du kodar med Media Encoder Standard kan du använda de för inställningar för aktivitets konfiguration som anges [här](./media-services-mes-presets-overview.md).

Begäran:

```console
POST https://media.windows.net/API/Jobs HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.windows.net

{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "Adaptive Streaming", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}
```

Svar:

```console
HTTP/1.1 201 Created

. . .
```

### <a name="set-the-output-assets-name"></a>Ange namnet på den utgående till gången
I följande exempel visas hur du ställer in attributet assetName:

```console
{ "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}`
```

### <a name="considerations"></a>Överväganden
* TaskBody-egenskaper måste använda literal XML för att definiera antalet indata eller utgående till gångar som används av aktiviteten. Uppgifts artikeln innehåller XML-schema definitionen för XML-koden.
* I TaskBody-definitionen måste varje inre värde för `<inputAsset>` och `<outputAsset>` anges som JobInputAsset (värde) eller JobOutputAsset (värde).
* En aktivitet kan ha flera utmatnings till gångar. En JobOutputAsset (x) kan bara användas en gång som utdata för en aktivitet i ett jobb.
* Du kan ange JobInputAsset eller JobOutputAsset som en inmatad till gång för en aktivitet.
* Aktiviteter får inte utgöra en cykel.
* Värde parametern som du skickar till JobInputAsset eller JobOutputAsset representerar index värdet för en till gång. De faktiska till gångarna definieras i InputMediaAssets-och OutputMediaAssets-navigerings egenskaperna på jobb enhets definitionen.
* Eftersom Media Services bygger på OData v3 refereras de enskilda till gångarna i navigerings egenskaps samlingarna InputMediaAssets och OutputMediaAssets med hjälp av ett "__metadata: URI"-namn-värdepar.
* InputMediaAssets mappar till en eller flera till gångar som du skapade i Media Services. OutputMediaAssets skapas av systemet. De refererar inte till en befintlig till gång.
* OutputMediaAssets kan namnges med hjälp av attributet assetName. Om det här attributet inte finns är namnet på OutputMediaAsset det som är det inre text värdet för `<outputAsset>` elementet med ett suffix för antingen jobbnamn eller jobb-ID-värdet (i de fall där namn egenskapen inte har definierats). Om du till exempel anger ett värde för assetName till "Sample" anges egenskapen OutputMediaAsset Name till "Sample". Men om du inte har angett ett värde för assetName, men ställt in jobb namnet på "NewJob", blir OutputMediaAsset-namnet "JobOutputAsset (värde) _NewJob".

## <a name="create-a-job-with-chained-tasks"></a>Skapa ett jobb med länkade aktiviteter
I många program scenarier vill utvecklare skapa en serie bearbetnings uppgifter. I Media Services kan du skapa en serie länkade aktiviteter. Varje aktivitet utför olika bearbetnings steg och kan använda olika medie processorer. De länkade aktiviteterna kan lämna ut en till gång från en aktivitet till en annan, och utföra en linjär följd av aktiviteter på till gången. De uppgifter som utförs i ett jobb behöver dock inte vara i följd. När du skapar en länkad aktivitet skapas länkade **ITask** -objekt i ett enda **IJob** -objekt.

> [!NOTE]
> Det finns för närvarande en gräns på 30 uppgifter per jobb. Om du behöver länka fler än 30 uppgifter skapar du fler än ett jobb som innehåller aktiviteterna.
>
>

```console
POST https://media.windows.net/api/Jobs HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

{  
   "Name":"NewTestJob",
   "InputMediaAssets":[  
      {  
         "__metadata":{  
            "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
         }
      }
   ],
   "Tasks":[  
      {  
         "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
         "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
      },
      {  
         "Configuration":"H264 Smooth Streaming 720p",
         "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
      }
   ]
}
```

### <a name="considerations"></a>Överväganden
Så här aktiverar du aktivitets länkning:

* Ett jobb måste innehålla minst två uppgifter.
* Det måste finnas minst en aktivitet vars indata är utdata från en annan aktivitet i jobbet.

## <a name="use-odata-batch-processing"></a>Använda OData batch-bearbetning
I följande exempel visas hur du använder OData batch-bearbetning för att skapa ett jobb och aktiviteter. Information om batchbearbetning finns i batch- [bearbetning för Open data Protocol (OData)](https://www.odata.org/documentation/odata-version-3-0/batch-processing/).

```console
POST https://media.windows.net/api/$batch HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
Accept: multipart/mixed
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
Host: media.windows.net


--batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d

--changeset_122fb0a4-cd80-4958-820f-346309967e4d
Content-Type: application/http
Content-Transfer-Encoding: binary

POST https://media.windows.net/api/Jobs HTTP/1.1
Content-ID: 1
Content-Type: application/json
Accept: application/json
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

{"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}

--changeset_122fb0a4-cd80-4958-820f-346309967e4d
Content-Type: application/http
Content-Transfer-Encoding: binary

POST https://media.windows.net/api/$1/Tasks HTTP/1.1
Content-ID: 2
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

{  
   "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
   "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
   "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
}

--changeset_122fb0a4-cd80-4958-820f-346309967e4d--
--batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--
```


## <a name="create-a-job-by-using-a-jobtemplate"></a>Skapa ett jobb med hjälp av en JobTemplate
När du bearbetar flera till gångar genom att använda en gemensam uppsättning aktiviteter använder du en JobTemplate för att ange standard aktivitets för inställningarna eller för att ange ordningen på aktiviteter.

I följande exempel visas hur du skapar en JobTemplate med en TaskTemplate som definieras infogad. TaskTemplate använder Media Encoder Standard som MediaProcessor för att koda till gångs filen. Andra MediaProcessors kan dock också användas.

```console
POST https://media.windows.net/API/JobTemplates HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net


{"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
```

> [!NOTE]
> Till skillnad från andra Media Services entiteter måste du definiera en ny GUID-identifierare för varje TaskTemplate och placera den i egenskapen taskTemplateId och ID i din begär ande text. Innehålls identifierings schemat måste följa det schema som beskrivs i identifiera Azure Media Services entiteter. JobTemplates kan inte heller uppdateras. I stället måste du skapa en ny med dina uppdaterade ändringar.
>
>

Om det lyckas returneras följande svar:

```console
HTTP/1.1 201 Created

. . .
```

I följande exempel visas hur du skapar ett jobb som hänvisar till ett JobTemplate-ID:

```console
POST https://media.windows.net/API/Jobs HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net


{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}
```

Om det lyckas returneras följande svar:

```console
HTTP/1.1 201 Created

. . .
```


## <a name="advanced-encoding-features-to-explore"></a>Avancerade kodnings funktioner som du kan utforska
* [Så här genererar du miniatyrer](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Skapa miniatyrer under kodning](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Beskär videor under kodning](media-services-crop-video.md)
* [Anpassa kodnings för inställningar](media-services-custom-mes-presets-with-dotnet.md)
* [Överlagra eller lägga till en video med en bild](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg
Nu när du vet hur du skapar ett jobb för att koda en till gång, se [hur du kontrollerar jobb förloppet med Media Services](media-services-rest-check-job-progress.md).

## <a name="see-also"></a>Se även
[Hämta medie processorer](media-services-rest-get-media-processor.md)