---
title: Ladda upp filer till ett Azure Media Services-konto med REST | Microsoft Docs
description: Lär dig hur du hämtar medie innehåll i Media Services genom att skapa och ladda upp till gångar med REST.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 9f27a427df07302840ce719d35c7876f9dc17dbf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012946"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Ladda upp filer till ett Media Services-konto med REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portal](media-services-portal-upload-files.md)
> 

I Media Services överför du dina digitala filer till en tillgång. [Till gångs](/rest/api/media/operations/asset) enheten kan innehålla video, ljud, bilder, miniatyr samlingar, text spår och filer med dold textning (samt metadata om dessa filer.)  När filerna har laddats upp till till gången lagras innehållet på ett säkert sätt i molnet för vidare bearbetning och strömning. 

I den här självstudien får du lära dig hur du laddar upp en fil och annan åtgärd som är kopplad till den:

> [!div class="checklist"]
> * Konfigurera PostMan för alla uppladdnings åtgärder
> * Ansluta till Media Services 
> * Skapa en åtkomst princip med Skriv behörighet
> * Skapa en tillgång
> * Skapa en SAS-lokaliserare och skapa uppladdnings-URL: en
> * Ladda upp en fil till Blob Storage med uppladdnings-URL
> * Skapa en metadata i till gången för medie filen du överförde

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
- [Skapa ett Azure Media Services konto med hjälp av Azure Portal](media-services-portal-create-account.md).
- Läs artikeln om att [komma åt Azure Media Services API med AAD-autentisering](media-services-use-aad-auth-to-access-ams-api.md) .
- Mer information hittar du i [använda Azure AD-autentisering för att komma åt Media Services-API med rest](./media-services-rest-connect-with-aad.md) -artikel.
- Konfigurera **Postman** enligt beskrivningen i [Konfigurera postman för Media Services REST API samtal](media-rest-apis-with-postman.md).

## <a name="considerations"></a>Överväganden

Följande överväganden gäller när du använder Media Services REST API:
 
* När du använder entiteter som använder Media Services REST API måste du ange vissa huvud fält och värden i dina HTTP-begäranden. Mer information finns i [installations programmet för Media Services REST API-utveckling](media-services-rest-how-to-use.md). <br/>Postman-samlingen som används i den här självstudien tar hand om att ställa in alla nödvändiga rubriker.
* Media Services använder värdet för egenskapen IAssetFile.Name när du skapar URL: er för strömmande innehåll (till exempel http://{AMSAccount}. ORIGIN. Media Services. Windows. net/{GUID}/{IAssetFile. name}/streamingParameters.) Därför är procent kodning inte tillåtet. Värdet för **namn** egenskapen får inte ha något av följande [%-encoding-reserverade tecken](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! * ' ();: @ &= + $,/?% # [] ". Det kan också finnas en "." för fil namns tillägget.
* Längden på namnet får inte vara längre än 260 tecken.
* Det finns en gräns för maximal filstorlek för bearbetning i Media Services. Information om filstorleksbegränsningen finns i [den här](media-services-quotas-and-limitations.md) artikeln.

## <a name="set-up-postman"></a>Konfigurera Postman

Anvisningar om hur du konfigurerar Postman för den här självstudien finns i [Konfigurera PostMan](media-rest-apis-with-postman.md).

## <a name="connect-to-media-services"></a>Ansluta till Media Services

1. Lägg till anslutnings värden i din miljö. 

    Vissa variabler som är en del av **Media Services** - [miljön](postman-environment.md) måste anges manuellt innan du kan börja köra åtgärder som definierats i [samlingen](postman-collection.md).

    För att hämta värden för de första fem variablerna, se [åtkomst till Azure Media Services-API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

    ![Skärm bild som visar ikonen "kugg hjuls" som valts från det övre högra hörnet och de första fem variablerna som valts från fliken "hanterings miljöer".](./media/media-services-rest-upload-files/postman-import-env.png)
2. Ange värdet för **MediaFileName** -miljövariabeln.

    Ange fil namnet på det medium som du planerar att ladda upp. I det här exemplet ska vi ladda upp BigBuckBunny.mp4. 
3. Granska **AzureMediaServices.postman_environment.jsi** filen. Du kommer att se att nästan alla åtgärder i samlingen kör ett "test"-skript. Skripten tar några värden som returneras av svaret och ställer in lämpliga miljövariabler.

    Den första åtgärden får till exempel en åtkomsttoken och ställer in den på **AccessToken** -miljövariabeln som används i alla andra åtgärder.

    ```    
    "listen": "test",
    "script": {
        "type": "text/javascript",
        "exec": [
            "var json = JSON.parse(responseBody);",
            "postman.setEnvironmentVariable(\"AccessToken\", json.access_token);"
        ]
    }
    ```
4. Till vänster i fönstret **Postman** klickar du på **1. Hämta AAD auth token**  ->  **Hämta Azure AD-token för tjänstens huvud namn**.

    URL-delen fylls med **AzureADSTSEndpoint** -miljövariabeln (tidigare i självstudien anger du värden för miljövariabler som stöder samlingen).

    ![Skärm bild som visar "1. Hämta en D auth-token – Hämta Azure A D-token för tjänstens huvud namn "valt i fönstret" Postman "och knappen" Skicka "vald.](./media/media-services-rest-upload-files/postment-get-token.png)

5. Tryck på **Skicka**.

    Du kan se svaret som innehåller "access_token". Skriptet "test" tar detta värde och ställer in miljövariabeln **AccessToken** (enligt beskrivningen ovan). Om du undersöker dina miljövariabler ser du att den här variabeln nu innehåller värdet åtkomsttoken (Bearer token) som används i resten av åtgärderna. 

    Om token går ut går du till steget "Hämta Azure AD-token för tjänstens huvud namn" igen. 

## <a name="create-an-access-policy-with-write-permission"></a>Skapa en åtkomst princip med Skriv behörighet

### <a name="overview"></a>Översikt 

>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Du bör använda samma princip-ID om du alltid använder samma dagar/åtkomstbehörigheter, till exempel principer för positionerare som är avsedda att vara på plats under en längre tid (icke-överföringsprinciper). Mer information finns i [den här](media-services-dotnet-manage-entities.md#limit-access-policies) artikeln.

Innan du överför filer till Blob Storage anger du åtkomst princip rättigheter för skrivning till en till gång. Det gör du genom att skicka en HTTP-begäran till enhets uppsättningen AccessPolicies. Definiera ett DurationInMinutes-värde när du skapar eller så får du ett internt 500-server fel meddelande i svaret. Mer information om AccessPolicies finns i [Access policy](/rest/api/media/operations/accesspolicy).

### <a name="create-an-access-policy"></a>Skapa en åtkomst princip

1. Välj **Access policy**  ->  **skapa Access policy för uppladdning**.
2. Tryck på **Skicka**.

    ![Skärm bild som visar alternativet "Access policy-skapa Access policy för uppladdning" valt på menyn i den vänstra menyn och knappen "Skicka" är markerad.](./media/media-services-rest-upload-files/postman-access-policy.png)

    Skriptet "test" hämtar Access Policy-ID och anger lämplig miljö variabel.

## <a name="create-an-asset"></a>Skapa en tillgång

### <a name="overview"></a>Översikt

En [till gång](/rest/api/media/operations/asset) är en behållare för flera typer eller uppsättningar med objekt i Media Services, inklusive video, ljud, bilder, miniatyr samlingar, text spår och filer med dold textning. När du skapar en till gång i REST API måste du skicka POST-begäran till Media Services och placera all egenskaps information om din till gång i begär ande texten.

En av de egenskaper som du kan lägga till när du skapar en till gång är **alternativ**. Du kan ange något av följande krypterings alternativ: **ingen** (standard, ingen kryptering används), **StorageEncrypted** (för innehåll som har krypterats med lagrings kryptering på klient sidan), **CommonEncryptionProtected** eller **EnvelopeEncryptionProtected**. När du har en krypterad till gång måste du konfigurera en leverans princip. Mer information finns i [Konfigurera till gångs leverans principer](media-services-rest-configure-asset-delivery-policy.md).

Om din till gång är krypterad måste du skapa en **ContentKey** och länka den till till gången enligt beskrivningen i följande artikel: [så här skapar du en ContentKey](media-services-rest-create-contentkey.md). När du har överfört filerna till till gången måste du uppdatera krypterings egenskaperna i **AssetFile** -entiteten med de värden som du fick vid **till gångs** krypteringen. Gör det med hjälp av **sammanfogningen** av http-begäran. 

I det här exemplet skapar vi en okrypterad till gång. 

### <a name="create-an-asset"></a>Skapa en tillgång

1. Välj **till gångar**  ->  **skapa till gång**.
2. Tryck på **Skicka**.

    ![Skärm bild som visar "till gångar – skapa till gång" som valts från menyn samlingar och knappen "Skicka" är markerad.](./media/media-services-rest-upload-files/postman-create-asset.png)

    Skriptet "test" hämtar till gångs-ID och anger lämplig miljö variabel.

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>Skapa en SAS-lokaliserare och skapa uppladdnings-URL: en

### <a name="overview"></a>Översikt

När du har angett Access policy och lokaliseraren laddas den faktiska filen upp till en Azure Blob Storage-behållare med hjälp av Azure Storage REST-API: er. Du måste överföra filerna som block-blobbar. Page blobbar stöds inte av Azure Media Services.  

Mer information om hur du arbetar med Azure Storage-blobar finns i [BLOB Service REST API](/rest/api/storageservices/blob-service-rest-api).

Skapa en SAS-positionerare (se nedan) för att ta emot den faktiska uppladdnings-URL: en. Lokaliserare definierar start tiden och typen av anslutnings slut punkt för klienter som vill komma åt filer i en till gång. Du kan skapa flera lokaliserade entiteter för en specifik Access policy och till gångs par för att hantera olika klient begär Anden och behov. Var och en av dessa positionerare använder StartTime-värdet plus DurationInMinutes-värdet för Access policy för att bestämma hur lång tid en URL kan användas. Mer information finns i [Locator](/rest/api/media/operations/locator).

En SAS-URL har följande format:

`{https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}`

### <a name="considerations"></a>Överväganden

Vissa förutsättningar gäller:

* Du kan inte ha fler än fem unika positionerare kopplade till en specifik till gång på samma gång. Mer information finns i Locator.
* Om du behöver ladda upp dina filer direkt ska du ställa in StartTime-värdet på fem minuter före den aktuella tiden. Detta beror på att det kan finnas en klock skevning mellan klient datorn och Media Services. StartTime-värdet måste också vara i följande DateTime-format: ÅÅÅÅ-MM-DDTHH: mm: ssZ (till exempel "2014-05-23T17:53:50Z").    
* Det kan finnas en fördröjning på 30-40 sekunder efter att en positionerare har skapats till när den är tillgänglig för användning.

### <a name="create-a-sas-locator"></a>Skapa en SAS-positionerare

1. Välj **Locator**  ->  **skapa SAS-positionerare**.
2. Tryck på **Skicka**.

    Skriptet "test" skapar "överförings webb adress" baserat på det medie fil namn du angav och information om SAS-positionerare och anger lämplig miljö variabel.

    ![Skärm bild som visar "Locator-Create S A S Locator" som valts från menyn samlingar och knappen "Skicka" är markerad.](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Ladda upp en fil till Blob Storage med uppladdnings-URL

### <a name="overview"></a>Översikt

Nu när du har uppladdnings-URL: en måste du skriva kod med hjälp av Azure Blob-API: er direkt för att överföra filen till SAS-behållaren. Mer information finns i följande artiklar:

- [Använda Azure Storage REST API](../../storage/common/storage-rest-api-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Lägg till BLOB](/rest/api/storageservices/put-blob)
- [Ladda upp blobar till Blob Storage](/previous-versions/azure/storage/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Ladda upp en fil med Postman

Vi använder exempelvis Postman för att ladda upp en liten. mp4-fil. Det kan finnas en fil storleks gräns för överföring av binärt via Postman.

Uppladdnings förfrågan ingår inte i **AzureMedia** -samlingen. 

Skapa och konfigurera en ny begäran:
1. Tryck på **+** för att skapa en ny begäran-flik.
2. Välj åtgärden **Lägg** och klistra in **{{UploadURL}}** i URL: en.
2. Lämna fliken **auktorisering** som är (ange inte till **Bearer-token**).
3. På fliken **sidhuvud** anger du: **nyckel**: "x-MS-BLOB-Type" och **Value**: "BlockBlob".
2. Klicka på **binär** på fliken **brödtext** .
4. Välj filen med det namn som du angav i miljövariabeln **MediaFileName** .
5. Tryck på **Skicka**.

    ![Skärm bild som visar fliken "(UploadU R L)" vald.](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>Skapa metadata i till gången

När filen har laddats upp måste du skapa en metadata i till gången för medie filen som du laddade upp i blob-lagringen som är kopplad till din till gång.

1. Välj **AssetFiles**  ->  **CreateFileInfos**.
2. Tryck på **Skicka**.

    ![Ladda upp en fil](./media/media-services-rest-upload-files/postman-create-file-info.png)

Filen ska laddas upp och dess metadata har angetts.

## <a name="validate"></a>Verifiera

För att kontrol lera att filen har laddats upp kan du vilja fråga [AssetFile](/rest/api/media/operations/assetfile) och jämföra **ContentFileSize** (eller annan information) till det du förväntar dig att se i den nya till gången. 

Följande **Get** -åtgärd hämtar till exempel fildata för din till gångs fil (i eller fall BigBuckBunny.mp4-filen). Frågan använder de [miljövariabler](postman-environment.md) som du anger tidigare.

`{{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files`

Svaret kommer att innehålla storlek, namn och annan information.

```console
"Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
"Name": "BigBuckBunny.mp4",
"ContentFileSize": "3186542",
"ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
```
  
## <a name="next-steps"></a>Nästa steg

Du kan nu koda överförda tillgångar. Mer information finns i [Koda tillgångar](media-services-portal-encode.md).

Du kan också använda Azure Functions för att utlösa ett kodningsjobb baserat på en fil som skickas till den konfigurerade containern. Mer information finns i [det här exemplet](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).
