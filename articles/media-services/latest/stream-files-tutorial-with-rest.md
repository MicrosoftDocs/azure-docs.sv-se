---
title: Koda en fjärrfil och strömma med Media Services
description: Följ stegen i den här självstudien för att koda en fil baserat på en URL och strömma ditt innehåll med Azure Media Services med hjälp av REST.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/17/2021
ms.author: inhenkel
ms.openlocfilehash: 92f89bafe1b96b575d12e8a09289a05c2379d837
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280028"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---rest"></a>Självstudier: Koda en fjärrfil baserat på URL och strömma videon – REST

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Med Azure Media Services kan du koda dina mediefiler till format som kan spelas upp på en mängd olika webbläsare och enheter. Du kanske vill strömma ditt innehåll i Apples HLS- eller MPEG DASH-formaten. Innan du strömmar, bör du koda dina högkvalitativa digitala mediafiler. Vägledning om kodning finns i [Kodningskoncept](encode-concept.md).

I den här självstudien får du lära dig att koda en fil baserat på en URL och strömma videon med Azure Media Services med hjälp av REST. 

![Spela upp videon](./media/stream-files-tutorial-with-api/final-video.png)

I den här självstudiekursen lär du dig att:    

> [!div class="checklist"]
> * Skapa ett Media Services-konto
> * Åtkomst till Media Services-API:n
> * Hämta Postman-filer
> * Konfigurera Postman
> * Skicka begäranden med Postman
> * Testa strömnings-URL:en
> * Rensa resurser

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

- [Skapa ett Media Services-konto](./account-create-how-to.md).

    Se till att komma ihåg de värden som du använde för resursgruppens namn och namnet på Media Services-kontot

- Installera [Postman](https://www.getpostman.com/) REST-klienten för att köra REST API:er som visas i några AMS REST-självstudierna. 

    Vi använder **Postman** men ett REST-verktyg skulle vara lämpligt. Andra alternativ är: **Visual Studio Code** med rest-plugin-programmet eller **Telerik-Fiddler**. 

## <a name="download-postman-files"></a>Hämta Postman-filer

Klona en GitHub-lagringsplats som innehåller Postman-samlingen och miljöfilerna.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="access-api"></a>API-åtkomst

Detaljerad information finns i [Hämta autentiseringsuppgifter för åtkomst Media Services-API](access-api-howto.md)

## <a name="configure-postman"></a>Konfigurera Postman

### <a name="configure-the-environment"></a>Konfigurera miljön 

1. Öppna **Postman** -appen.
2. På höger sida om skärmen, väljer du alternativet **Hantera miljö**.

    ![Hantera miljö](./media/develop-with-postman/postman-import-env.png)
4. Från dialogrutan **Hantera miljö**, klickar du på **Importera**.
2. Bläddra till `Azure Media Service v3 Environment.postman_environment.json`-filen som hämtades när du klonade `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
6. Miljön **Azure Media Service v3-miljö** läggs till.

    > [!Note]
    > Uppdatera åtkomstvariablerna med värden som du fick från avsnittet **Åtkomst till Media Services API** ovan.

7. Dubbelklicka på den valda filen och ange värden som du har fått genom att följa stegen i [åtkomst till API](#access-api).
8. Stäng dialogrutan.
9. Välj miljön **Azure Media Service v3-miljö** från listmenyn.

    ![Välj miljö](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Konfigurera samlingen

1. Klicka på **Importera** för att importera samlingsfilen.
1. Bläddra till `Media Services v3.postman_collection.json`-filen som hämtades när du klonade `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`
3. Välj filen **Media Services v3.postman_collection.json**.

    ![Importera en fil](./media/develop-with-postman/postman-import-collection.png)

## <a name="send-requests-using-postman"></a>Skicka begäranden med Postman

I det här avsnittet skickar vi begäranden som är relevanta för att koda och skapa webbadresser, så att du kan direktuppspela en fil. Mer specifikt skickas följande begäranden:

1. Hämta Azure AD-token för autentisering för tjänstens huvudnamn
1. Starta en slut punkt för direkt uppspelning
2. Skapa en utdatatillgång
3. Skapa en transformering
4. Skapa ett jobb
5. Skapa en positionerare för direktuppspelning
6. Lista sökvägar för positioneraren för direktuppspelning

> [!Note]
>  Den här kursen förutsätter att du skapar alla resurser med unika namn.  

### <a name="get-azure-ad-token"></a>Hämta Azure AD-token 

1. I det vänstra fönstret i Postman-appen väljer du "steg 1: Hämta AAD auth-token".
2. Välj sedan Hämta Azure AD-token för autentisering för tjänstens huvudnamn.
3. Tryck på **Skicka**.

    Följande **POST**-åtgärd skickas.

    ```
    https://login.microsoftonline.com/:aadTenantDomain/oauth2/token
    ```

4. Svaret kommer tillbaka med token och anger miljövariabeln AccessToken till token-värdet. Om du vill se koden som anger AccessToken, klickar du på fliken **Tester**. 

    ![Hämta AAD-token](./media/develop-with-postman/postman-get-aad-auth-token.png)


### <a name="start-a-streaming-endpoint"></a>Starta en slut punkt för direkt uppspelning

Om du vill aktivera direkt uppspelning måste du först starta den [strömnings slut punkt](./stream-streaming-endpoint-concept.md) som du vill strömma videon från.

> [!NOTE]
> Du faktureras bara när slut punkten för direkt uppspelning är i körnings läge.

1. I det vänstra fönstret i Postman-appen väljer du direkt uppspelning och Live.
2. Välj sedan "starta StreamingEndpoint".
3. Tryck på **Skicka**.

    * Följande **post** -åtgärd skickas:

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaservices/:accountName/streamingEndpoints/:streamingEndpointName/start?api-version={{api-version}}
        ```
    * Om begäran lyckas `Status: 202 Accepted` returneras.

        Denna status innebär att begäran har godkänts för bearbetning. men bearbetningen har inte slutförts. Du kan fråga efter åtgärds status baserat på värdet i `Azure-AsyncOperation` svars huvudet.

        Följande GET-åtgärd returnerar till exempel status för din åtgärd:
        
        `https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<resourceGroupName>/providers/Microsoft.Media/mediaservices/<accountName>/streamingendpointoperations/1be71957-4edc-4f3c-a29d-5c2777136a2e?api-version=2018-07-01`

        Artikeln [spåra asynkrona Azure-åtgärder](../../azure-resource-manager/management/async-operations.md) förklarar i djup hur du spårar statusen för asynkrona Azure-åtgärder via värden som returneras i svaret.

### <a name="create-an-output-asset"></a>Skapa en utdatatillgång

Utmatnings [till gången](/rest/api/media/assets) lagrar resultatet av ditt kodnings jobb. 

1. I det vänstra fönstret i Postman-appen väljer du "till gångar".
2. Välj därefter Skapa eller uppdatera en tillgång.
3. Tryck på **Skicka**.

    * Följande **placerings** åtgärd skickas:

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/assets/:assetName?api-version={{api-version}}
        ```
    * Åtgärden har följande text:

        ```json
        {
        "properties": {
            "description": "My Asset",
            "alternateId" : "some GUID",
            "storageAccountName": "<replace from environment file>",
            "container": "<supply any valid container name of your choosing>"
         }
        }
        ```

> [!NOTE]
> Se till att ersätta lagrings kontots och behållarens namn antingen med de från miljö filen eller ange egna.
>
> När du slutför stegen som beskrivs i resten av den här artikeln ser du till att du anger giltiga parametrar i begär ande organ.

### <a name="create-a-transform"></a>Skapa en transformering

När kodningen eller bearbetningen av innehåll i Media Services görs, konfigureras vanligtvis kodningsinställningarna som ett recept. Du skickar sedan ett **Jobb** som tillämpar receptet på en video. Genom att skicka nya jobb för varje ny video tillämpar du receptet på alla videor i biblioteket. Ett recept i Media Services kallas för en **Transformering**. Mer information finns i [Transformeringar och jobb](./transform-jobs-concept.md). Det exempel som beskrivs i självstudien definierar ett recept som kodar videon för att strömma den till olika iOS- och Android-enheter. 

När du skapar en ny instans för en [Transformering](/rest/api/media/transforms), måste du ange vilken utdata du vill att den ska skapa. Den obligatoriska parametern är ett **TransformOutput**-objekt. Varje **TransformOutput** innehåller en **Förinställning**. I **Förinställning** finns stegvisa anvisningar för den video- och/eller ljudbearbetning som ska användas för att generera önskad **TransformOutput**. Det exempel som beskrivs i artikeln använder en inbyggd förinställning som kallas **AdaptiveStreaming**. Förinställningen kodar indatavideon i en automatiskt genererad bithastighetsstege (par för bithastighetsupplösning) som baseras på indatans upplösning och bithastighet, samt producerar ISO MP4-filer med H.264-video och AAC-ljud som motsvarar varje par för bithastighetsupplösningen. Information om denna förinställning finns i [Automatisk generering av bithastighetsstege](encode-autogen-bitrate-ladder.md).

Du kan använda en inbyggd EncoderNamedPreset eller anpassade förinställningar. 

> [!Note]
> När du skapar en [Transformering](/rest/api/media/transforms) bör du först kontrollera om det redan finns en med **Get**-metoden. Den här kursen förutsätter att du skapar transformeringen med ett unikt namn.

1. I det vänstra fönstret i Postman-appen väljer du kodning och analys.
2. Välj sedan Skapa transformering.
3. Tryck på **Skicka**.

    * Följande **PUT**-åtgärd skickas.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
        ```
    * Åtgärden har följande text:

        ```json
        {
            "properties": {
                "description": "Standard Transform using an Adaptive Streaming encoding preset from the library of built-in Standard Encoder presets",
                "outputs": [
                    {
                    "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                    "preset": {
                        "@odata.type": "#Microsoft.Media.BuiltInStandardEncoderPreset",
                        "presetName": "AdaptiveStreaming"
                    }
                    }
                ]
            }
        }
        ```

### <a name="create-a-job"></a>Skapa ett jobb

Ett [Jobb](/rest/api/media/jobs) är den faktiska begäran till Media Services om att tillämpa den skapade **Transformeringen** på en given indatavideo eller ett ljudinnehåll. **Jobb** anger information som platsen för indatavideon och platsen för utdatan.

I det här exemplet baseras jobbets Indatatyp på en HTTPS-URL ("https: \/ /nimbuscdn-nimbuspm.streaming.MediaServices.Windows.net/2b533311-b215-4409-80af-529c3e853622/").

1. I det vänstra fönstret i Postman-appen väljer du kodning och analys.
2. Välj därefter Skapa eller uppdatera jobbet.
3. Tryck på **Skicka**.

    * Följande **PUT**-åtgärd skickas.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
        ```
    * Åtgärden har följande text:

        ```json
        {
        "properties": {
            "input": {
            "@odata.type": "#Microsoft.Media.JobInputHttp",
            "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
            "files": [
                    "Ignite-short.mp4"
                ]
            },
            "outputs": [
            {
                "@odata.type": "#Microsoft.Media.JobOutputAsset",
                "assetName": "testAsset1"
            }
            ]
        }
        }
        ```

Jobbet tar en stund att slutföra och du meddelas när detta sker. Om du vill se förloppet för jobbet rekommenderar vi att du använder Event Grid. Det är utformat för hög tillgänglighet, konsekvent prestanda och dynamisk skalning. Med Event Grid kan dina appar lyssna efter och reagera på händelser från i princip alla Azure-tjänster, samt även från anpassade källor. Med enkel och HTTP-baserad reaktiv händelsehantering blir det lättare att skapa effektiva lösningar med hjälp av intelligent filtrering och dirigering av händelser.  Se [Dirigera händelser till en anpassad webbslutpunkt](monitoring/job-state-events-cli-how-to.md).

**Jobb** har vanligtvis följande tillstånd: **Schemalagd**, **I kö**, **Bearbetas**, **Slutförd** (slutlig status). Om jobbet har påträffat ett fel visas tillståndet **Fel**. Om jobbet avbryts visas **Avbryter** och **Avbruten** när det är klart.

#### <a name="job-error-codes"></a>Jobbfelkoder

Se [Felkoder](/rest/api/media/jobs/get#joberrorcode).

### <a name="create-a-streaming-locator"></a>Skapa en positionerare för direktuppspelning

När kodningsjobbet är klart, är nästa steg att göra videon i **utdatatillgången** tillgänglig för uppspelning av klienter. Du kan göra detta i två steg: Först skapar du en [StreamingLocator](/rest/api/media/streaminglocators) och därefter skapar du de strömmande URL:er som klienterna ska använda. 

Processen för att skapa en strömmande positionerare kallas publicering. Som standard är streaming Locator giltig omedelbart efter att du har gjort API-anrop och varar tills den tas bort, om du inte konfigurerar de valfria start-och slut tiderna. 

När du skapar en [StreamingLocator](/rest/api/media/streaminglocators) behöver du ange önskat **StreamingPolicyName**. I det här exemplet kommer du att strömma in-Clear (eller icke-krypterad) innehåll, så den fördefinierade principen för att rensa strömmande Predefined_ClearStreamingOnly används.

> [!IMPORTANT]
> Om du använder en anpassad [StreamingPolicy](/rest/api/media/streamingpolicies) bör du skapa en begränsad uppsättning av sådana principer för ditt Media Service-konto, och återanvända dem för dina StreamingLocators när samma krypterings- och protokollalternativ krävs. 

Ditt medie tjänst konto har en kvot för antalet poster i **strömnings principen** . Du bör inte skapa en ny **strömmande princip** för varje strömmande positionerare.

1. Välj "strömmande principer och positionerare" i det vänstra fönstret i Postman-appen.
2. Välj sedan "skapa en strömmande Locator (rensa)".
3. Tryck på **Skicka**.

    * Följande **PUT**-åtgärd skickas.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingLocators/:streamingLocatorName?api-version={{api-version}}
        ```
    * Åtgärden har följande text:

        ```json
        {
          "properties": {
            "streamingPolicyName": "Predefined_ClearStreamingOnly",
            "assetName": "testAsset1",
            "contentKeys": [],
            "filters": []
         }
        }
        ```

### <a name="list-paths-and-build-streaming-urls"></a>Lista sökvägar och skapa URL:er för strömning

#### <a name="list-paths"></a>Lista sökvägar

Nu när [positioneraren för direktuppspelning](/rest/api/media/streaminglocators) har skapats kan du hämta direktuppspelningswebbadresserna

1. Välj "strömmande principer" i det vänstra fönstret i Postman-appen.
2. Välj sedan Lista sökvägar.
3. Tryck på **Skicka**.

    * Följande **POST**-åtgärd skickas.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingLocators/:streamingLocatorName/listPaths?api-version={{api-version}}
        ```
        
    * Den här åtgärden har inget innehåll:
        
4. Notera en av sökvägarna du vill använda för strömning, du använder den i nästa avsnitt. I det här fallet returnerades följande sökvägar:
    
    ```
    "streamingPaths": [
        {
            "streamingProtocol": "Hls",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)"
            ]
        },
        {
            "streamingProtocol": "Dash",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=mpd-time-csf)"
            ]
        },
        {
            "streamingProtocol": "SmoothStreaming",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest"
            ]
        }
    ]
    ```

#### <a name="build-the-streaming-urls"></a>Skapa strömnings-URL:erna

I det här avsnittet ska vi skapa en HLS-strömnings-URL. URL:er består av följande värden:

1. Protokollet som data skickas över. I det här fallet https.

    > [!NOTE]
    > Om en spelare finns på en HTTPS-webbplats uppdaterar du URL:en till ”HTTPS”.

2. Värdnamnet för StreamingEndpoint. I det här fallet är namnet amsaccount-usw22.streaming.media.azure.net.

    Du kan använda följande hämtningsåtgärd för att hämta värdnamnet:
    
    ```
    https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaservices/:accountName/streamingEndpoints/default?api-version={{api-version}}
    ```
    och se till att du anger `resourceGroupName` parametrarna och `accountName` för att matcha miljö filen. 
    
3. En sökväg som du fick i föregående avsnitt (listsökvägar).  

Därmed skapades följande HLS-URL

```
https://amsaccount-usw22.streaming.media.azure.net/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)
```

## <a name="test-the-streaming-url"></a>Testa strömnings-URL:en


> [!NOTE]
> Kontrol lera att den **strömnings slut punkt** som du vill strömma från körs.

I den här artikeln används Azure Media Player till att testa strömningen. 

1. Öppna en webbläsare och gå till [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. I **URL:**-rutan klistrar du in den URL som du skapat. 
3. Tryck på **Uppdatera spelare**.

Azure Media Player kan användas vid testning, men bör inte användas i en produktionsmiljö. 

## <a name="clean-up-resources-in-your-media-services-account"></a>Rensa resurser på ditt Media Services-konto

Vanligtvis bör du rensa bort allt utom objekt som du tänker återanvända (om du t.ex. återanvänder **transformeringar** behåller du **positionerare för direktuppspelning** osv.). Om du vill att ditt konto ska vara rensat efter experimentet, bör du ta bort de resurser som du inte tänker återanvända.  

Om du vill ta bort en resurs, väljer du åtgärden Ta bort... under den resurs som du vill ta bort.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av resurserna i resursgruppen, inklusive Media Services och de lagringskonton som du skapade för självstudien, tar du bort resursgruppen som du skapade tidigare.  

Kör följande CLI-kommando:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, hämta uppdateringar

Kolla in [Azure Media Services community](media-services-community.md) -artikeln för att se olika sätt att ställa frågor, lämna feedback och få uppdateringar om Media Services.

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du ska ladda upp, koda och strömma videon kan du läsa följande artikel: 

> [!div class="nextstepaction"]
> [Analysera videor](analyze-videos-tutorial.md)
