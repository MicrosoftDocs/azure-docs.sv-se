---
title: Konfigurera en SharePoint Online-indexerare (för hands version)
titleSuffix: Azure Cognitive Search
description: Konfigurera en SharePoint Online-indexerare för att automatisera indexering av dokument biblioteks innehåll i Azure Kognitiv sökning.
manager: luisca
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: 5a44c40838b7f7fa9ca499ade49317ff9ce828fe
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102498905"
---
# <a name="how-to-configure-sharepoint-online-indexing-in-cognitive-search-preview"></a>Så här konfigurerar du SharePoint online-indexering i Kognitiv sökning (för hands version)

> [!IMPORTANT] 
> SharePoint Online-Support finns för närvarande i en **gated offentlig för hands version**. Du kan begära åtkomst till gated Preview genom att fylla i [det här formuläret](https://aka.ms/azure-cognitive-search/indexer-preview).
>
> För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 
> Den [REST API version 2020-06-30 – för hands version](search-api-preview.md) innehåller den här funktionen. Det finns för närvarande inget stöd för Portal eller SDK.

> [!NOTE]
> SharePoint Online stöder en detaljerad auktoriserings modell som avgör åtkomsten per användare på dokument nivå. SharePoint Online-indexeraren hämtar inte dessa behörigheter till sökindexet och Kognitiv sökning har inte stöd för auktorisering på dokument nivå. När ett dokument indexeras från SharePoint Online till en Sök tjänst är innehållet tillgängligt för alla som har Läs behörighet till indexet. Om du behöver behörigheter på dokument nivå bör du undersöka säkerhets filter för att trimma resultaten av obehörigt innehåll. Mer information finns i [säkerhets trimning med Active Directory identiteter](search-security-trimming-for-azure-search-with-aad.md).

Den här artikeln beskriver hur du använder Azure Kognitiv sökning för att indexera dokument (t. ex. PDF-filer, Microsoft Office dokument och flera andra vanliga format) som lagras i SharePoint Online-dokumentbibliotek till ett Azure Kognitiv sökning-index. Först förklarar det grunderna för att konfigurera och konfigurera indexeraren. Sedan ger den en djupare utforskning av beteenden och scenarier som du sannolikt kommer att stöta på.

## <a name="functionality"></a>Funktioner
En indexerare i Azure Kognitiv sökning är en Crawler som extraherar sökbara data och metadata från en data källa. SharePoint Online-indexeraren kommer att ansluta till SharePoint Online-webbplatsen och indexera dokument från ett eller flera dokument bibliotek. Indexeraren innehåller följande funktioner:
+ Indexera innehåll från ett eller flera dokument bibliotek i SharePoint Online.
+ Indexera innehåll från dokument bibliotek i SharePoint Online som finns i samma klient organisation som Azure Kognitiv sökning-tjänsten. Indexeraren fungerar inte med SharePoint-webbplatser som finns i en annan klient än din Azure Kognitiv sökning-tjänst. 
+ Indexeraren stöder stegvis indexering, vilket innebär att det identifierar vilket innehåll i dokument biblioteket som har ändrats och bara indexerar det uppdaterade innehållet i framtida indexerings körningar. Om t. ex. 5 PDF-filer ursprungligen indexeras av indexeraren, sedan 1 uppdateras, körs indexeraren igen och indexeraren kommer bara att indexera den 1 PDF-fil som uppdaterades.
+ Text och normaliserade avbildningar extraheras som standard från de dokument som indexeras. Du kan också lägga till en färdigheter i pipelinen för ytterligare innehålls berikning. Mer information om färdighetsuppsättningar finns i artikeln [färdigheter-koncept i Azure kognitiv sökning](cognitive-search-working-with-skillsets.md).

## <a name="supported-document-formats"></a>Dokumentformat som stöds

Azure Kognitiv sökning SharePoint Online-indexeraren kan extrahera text från följande dokument format:

[!INCLUDE [search-document-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="incremental-indexing-and-deletion-detection"></a>Identifiering och borttagning av stegvis indexering
Som standard har SharePoint Online-indexeraren stöd för stegvis indexering, vilket innebär att det identifierar vilket innehåll i dokument biblioteket som har ändrats och endast indexerar det uppdaterade innehållet i framtida indexerings körningar. Om t. ex. 5 Word-dokument ursprungligen indexeras av indexeraren, sedan 1 uppdateras, kommer indexeraren att köras igen, indexeraren kommer bara att indexera om det 1 Word-dokument som uppdaterades.

Identifiering av borttagningar stöds också som standard. Det innebär att om ett dokument tas bort från ett SharePoint Online-dokumentbibliotek, kommer indexeraren att identifiera borttagningen under en framtida indexerare och ta bort dokumentet från indexet.

## <a name="setting-up-sharepoint-online-indexing"></a>Konfigurera SharePoint online-indexering
Om du vill konfigurera SharePoint Online-indexeraren måste du utföra vissa åtgärder i Azure Portal och vissa åtgärder med hjälp av förhands gransknings REST API. Den här för hands versionen stöds inte av SDK.

### <a name="step-1-enable-system-assigned-managed-identity"></a>Steg 1: Aktivera systemtilldelad hanterad identitet
När en systemtilldelad hanterad identitet är aktive rad skapar Azure en identitet för din Sök tjänst som kan användas av indexeraren.

![Aktivera systemtilldelad hanterad identitet](media/search-howto-index-sharepoint-online/enable-managed-identity.png "Aktivera systemtilldelad hanterad identitet")

När du har valt **Spara** visas ett objekt-ID som har tilldelats till din Sök tjänst.

![Systemtilldelad hanterad identitet](media/search-howto-index-sharepoint-online/system-assigned-managed-identity.png "Systemtilldelad hanterad identitet")

### <a name="step-2-create-an-aad-application"></a>Steg 2: skapa ett AAD-program
SharePoint Online-indexeraren använder detta AAD-program för autentisering. 

1.  Navigera till [Azure Portal](https://portal.azure.com/).

1.  Öppna menyn på vänster sida av huvud sidan och välj **Azure Active Directory** Välj **Appregistreringar**. Välj **+ ny registrering**.
    1.  Ange ett namn för din app.
    2.  Välj **en enskild klient**.
    3.  Ingen omdirigerings-URI krävs.
    4.  Välj **register**

1.  Välj **API-behörigheter** på menyn till vänster och Lägg sedan **till en behörighet** och **Microsoft Graph** sedan **delegerade behörigheter**. Lägg till följande API-behörigheter: 
    1.  **Delegerade filer. Read. all** 
    2.  **Delegerad-sites. Read. all** 
    3.  **Delegerad-användare. Läs**

    ![Delegerade API-behörigheter](media/search-howto-index-sharepoint-online/delegated-api-permissions.png "Delegerade API-behörigheter")

    Med delegerade behörigheter innebär det att indexeraren kommer åt SharePoint-webbplatsen i användar kontexten. Så när du kör indexeraren har den bara åtkomst till det innehåll som den inloggade användaren har åtkomst till. Användar inloggning inträffar när du skapar indexeraren eller uppdaterar datum källan. Inloggnings steget beskrivs längre fram i den här artikeln.

1.  Välj fliken **autentisering** . Ange **Tillåt att offentliga klient flöden** är **Ja** och välj sedan **Spara**.

1.  Välj **+ Lägg till en plattform**, sedan **mobil-och skriv bords program** och sedan kontrol lera `https://login.microsoftonline.com/common/oauth2/nativeclient` och **Konfigurera**.

    ![Konfiguration av AAD-app-autentisering](media/search-howto-index-sharepoint-online/aad-app-authentication-configuration.png "Konfiguration av AAD-app-autentisering")

1.  Ge administrativt medgivande (krävs endast för vissa klienter).

    Vissa klienter är låsta så att administratörs medgivande krävs för dessa delegerade API-behörigheter. Om så är fallet måste du ha administratörs medgivande för det här AAD-programmet innan du skapar indexeraren. 

    Eftersom inte alla klienter har detta krav rekommenderar vi att du först hoppar över det här steget och fortsätter med instruktionerna. Du vet om du behöver administratörs medgivande om du behöver en administratör för att kunna godkänna autentiseringen när du skapar indexeraren. I så fall har du en innehavaradministratör som ger ett medgivande med knappen nedan.

    ![AAD-appen Granting admin medgivande](media/search-howto-index-sharepoint-online/aad-app-grant-admin-consent.png "AAD-appen Granting admin medgivande")

<a name="create-data-source"></a>

### <a name="step-3-create-data-source"></a>Steg 3: skapa data Källa
> [!IMPORTANT] 
> Från och med det här avsnittet måste du använda förhands gransknings REST API för de återstående stegen. Om du inte är bekant med Azure Kognitiv sökning-REST API rekommenderar vi att du tar en titt på den här [snabb](search-get-started-rest.md)starten.

En data källa anger vilka data som ska indexeras, autentiseringsuppgifter som behövs för att komma åt data och principer för att effektivt identifiera ändringar i data (nya, ändrade eller borttagna rader). En data källa kan användas av flera indexerare i samma Sök tjänst.

För SharePoint-indexering måste data källan ha följande obligatoriska egenskaper:
+ **Name** är det unika namnet på data källan i Sök tjänsten.
+ **typen** måste vara "SharePoint". Detta är Skift läges känsligt.
+ **autentiseringsuppgifterna** tillhandahåller SharePoint Online-slutpunkten och AAD-appens ID (klient). Ett exempel på en SharePoint Online-slutpunkt är `https://microsoft.sharepoint.com/teams/MySharePointSite` . Du kan hämta SharePoint Online-slutpunkten genom att gå till start sidan för din SharePoint-webbplats och kopiera URL: en från webbläsaren.
+ **container** anger vilket dokument bibliotek som ska indexeras. Mer information om hur du skapar behållaren finns i avsnittet [styra vilka dokument som indexeras](#controlling-which-documents-are-indexed) i det här dokumentet.

Så här skapar du en data Källa:

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-datasource",
    "type" : "sharepoint",
    "credentials" : { "connectionString" : "SharePointOnlineEndpoint=[SharePoint Online site url];ApplicationId=[AAD App ID]" },
    "container" : { "name" : "defaultSiteLibrary", "query" : null }
}
```

### <a name="step-4-create-an-index"></a>Steg 4: skapa ett index
Indexet anger fält i ett dokument, attribut och andra konstruktioner som formar Sök funktionen.

Så här skapar du ett index med ett sökbart innehålls fält för att lagra texten som extraheras från dokument i ett dokument bibliotek:

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-index",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "metadata_spo_item_name", "type": "Edm.String", "key": false, "searchable": true, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_path", "type": "Edm.String", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_content_type", "type": "Edm.String", "key": false, "searchable": false, "filterable": true, "sortable": false, "facetable": true },
        { "name": "metadata_spo_item_last_modified", "type": "Edm.DateTimeOffset", "key": false, "searchable": false, "filterable": false, "sortable": true, "facetable": false },
        { "name": "metadata_spo_item_size", "type": "Edm.Int64", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}

```

Mer information finns i [skapa index (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-5-create-an-indexer"></a>Steg 5: skapa en indexerare
En indexerare ansluter en data källa med ett mål Sök index och ger ett schema för att automatisera data uppdateringen. När indexet och data källan har skapats är du redo att skapa indexeraren!

Under det här avsnittet uppmanas du att logga in med dina autentiseringsuppgifter för organisationen som har åtkomst till SharePoint-webbplatsen. Om möjligt rekommenderar vi att du skapar ett nytt organisations användar konto och ger den nya användaren de exakta behörigheter som du vill att indexeraren ska ha.

Det finns några steg för att skapa indexeraren:

1.  Skicka en begäran om att försöka skapa indexeraren.

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
        {
          "name" : "sharepoint-indexer",
          "dataSourceName" : "sharepoint-datasource",
          "targetIndexName" : "sharepoint-index",
          "fieldMappings" : [
            { 
              "sourceFieldName" : "metadata_spo_site_library_item_id", 
              "targetFieldName" : "id", 
              "mappingFunction" : { 
                "name" : "base64Encode" 
              } 
            }
          ]
        }
    
    ```

1.  När du skapar indexeraren för första gången kommer den att Miss klar och du får följande fel meddelande. Gå till länken i fel meddelandet. Om du inte går till länken inom 10 minuter upphör koden att gälla och du måste återskapa [data källan](#create-data-source).

    ```http
    {
        "error": {
            "code": "",
            "message": "Error with data source: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code <CODE> to authenticate.  Please adjust your data source definition in order to proceed."
        }
    }
    ```

1.  Ange den kod som angavs i fel meddelandet.

    ![Ange enhets kod](media/search-howto-index-sharepoint-online/enter-device-code.png "Ange enhets kod")

1.  SharePoint-indexeraren kommer åt SharePoint-innehållet som den inloggade användaren. Användaren som loggar in under det här steget är den inloggade användaren. Så om du loggar in med ett användar konto som inte har åtkomst till ett dokument i det dokument bibliotek som du vill indexera, kommer indexeraren inte att ha åtkomst till dokumentet.

    Om möjligt rekommenderar vi att du skapar ett nytt användar konto och ger den nya användaren de exakta behörigheter som du vill att indexeraren ska ha.

1.  Godkänn de behörigheter som begärs.

    ![Godkänn API-behörigheter](media/search-howto-index-sharepoint-online/aad-app-approve-api-permissions.png "Godkänn API-behörigheter")

1.  Skicka begäran om att skapa indexerare igen. Den här gången bör begäran lyckas. 

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "sharepoint-indexer",
        "dataSourceName" : "sharepoint-datasource",
        "targetIndexName" : "sharepoint-index"
    }
    ```

### <a name="step-6-check-the-indexer-status"></a>Steg 6: kontrol lera indexerings status
När indexeraren har skapats kan du kontrol lera indexerings status genom att göra följande begäran.

```http
GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]
```

Mer information om indexerings status finns här: [Hämta indexerings status](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

## <a name="updating-the-data-source"></a>Uppdaterar data källan
Om det inte finns några uppdateringar av datakällobjektet kan indexeraren köras enligt ett schema utan några åtgärder från användaren. Men varje gång Azure Kognitiv sökning-datakällobjektet uppdateras måste du logga in igen för att indexeraren ska kunna köras. Om du till exempel ändrar data käll frågan måste du logga in igen med hjälp av `https://microsoft.com/devicelogin` och en ny kod.

När data källan har uppdaterats följer du stegen nedan:

1.  Starta om indexeraren manuellt.

    ```http
    POST https://[service name].search.windows.net/indexers/sharepoint-indexer/run?api-version=2020-06-30-Preview  
    Content-Type: application/json
    api-key: [admin key]
    ```

    Mer information om körnings förfrågan för indexerare hittar du här: [Kör indexerare](https://docs.microsoft.com/rest/api/searchservice/run-indexer).

1.  Kontrol lera status för indexeraren. Om den sista indexeraren körs har ett fel som säger att du går till `https://microsoft.com/devicelogin` , går du till sidan och anger den nya koden. 

    ```http
    GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    ```

    Mer information om indexerings status finns här: [Hämta indexerings status](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

1.  Inloggning

1.  Starta en indexerare manuellt och kontrol lera status för indexeraren. Den här gången ska körningen av indexet starta.

## <a name="indexing-document-metadata"></a>Indexera dokumentets metadata
Om du har ställt in indexeraren att indexera dokumentets metadata är följande metadata tillgängliga för index.

> [!NOTE]
> Anpassade metadata ingår inte i den aktuella versionen av förhands granskningen.

| Identifierare | Typ | Beskrivning | 
| ------------- | -------------- | ----------- |
| metadata_spo_site_library_item_id | Edm.String | Kombinations nyckeln för plats-ID, biblioteks-ID och objekt-ID som unikt identifierar ett objekt i ett dokument bibliotek för en plats. |
| metadata_spo_site_id | Edm.String | ID för SharePoint Online-webbplatsen. |
| metadata_spo_library_id | Edm.String | Dokument bibliotekets ID. |
| metadata_spo_item_id | Edm.String | ID för objektet (dokument) i biblioteket. |
| metadata_spo_item_last_modified | Edm.DateTimeOffset | Senaste ändrings datum/tid (UTC) för objektet. |
| metadata_spo_item_name | Edm.String | Objektets namn. |
| metadata_spo_item_size | Edm.Int64 | Objektets storlek (i byte). | 
| metadata_spo_item_content_type | Edm.String | Objektets innehålls typ. | 
| metadata_spo_item_extension | Edm.String | Tillägget för objektet. |
| metadata_spo_item_weburi | Edm.String | Objektets URI. |
| metadata_spo_item_path | Edm.String | Kombinationen av den överordnade sökvägen och objekt namnet. | 

SharePoint Online-indexeraren stöder också metadata som är speciella för varje dokument typ. Mer information finns i egenskaper för [innehålls metadata som används i Azure kognitiv sökning](search-blob-metadata-properties.md).

<a name="controlling-which-documents-are-indexed"></a>

## <a name="controlling-which-documents-are-indexed"></a>Styra vilka dokument som indexeras
En enskild SharePoint Online-indexerare kan indexera innehåll från ett eller flera dokument bibliotek. Använd *container* parametern när du skapar data källan för att ange de dokument bibliotek som du vill indexera. Data källans *behållare* har två egenskaper: *namn* och *fråga*. 

### <a name="name"></a>Name
Egenskapen *namn* är obligatorisk och måste vara ett av tre värden:
+ *defaultSiteLibrary*
    + Indexera allt innehåll från platsens standard dokument bibliotek.
+   *allSiteLibraries*
    + Indexera allt innehåll från alla dokument bibliotek på en plats. Detta kommer inte att indexera dokument bibliotek från en under webbplats. De kan anges i *frågan* även om.
+   *useQuery*
    + Endast index innehåll som definierats i *frågan*.

### <a name="query"></a>Söka i data
*Frågeparametern* består av nyckelord/värde-par. Nedan visas de nyckelord som kan användas. Värdena är antingen webbplats-URL: er eller URL: er för dokument bibliotek.

> [!NOTE]
> Om du vill hämta värdet för ett visst nyckelord rekommenderar vi att du öppnar SharePoint Online i en webbläsare och navigerar till det dokument bibliotek som du försöker ta med/undanta och kopiera URI: n från webbläsaren. Detta är det enklaste sättet att få värdet att använda med ett nyckelord i frågan.

| Följt | Beskrivning av fråga | Exempel |
| ------------- | -------------- | ----------- |
| null | Om det är null eller tomt indexerar du antingen standard dokument biblioteket eller alla dokument bibliotek beroende på behållar namnet. | Indexera allt innehåll från standard webbplats biblioteket: <br><br>  ``` "container" : { "name" : "defaultSiteLibrary", "query" : null } ``` |
| includeLibrariesInSite | Indexera innehåll från alla bibliotek i den definierade platsen i anslutnings strängen. Dessa är begränsade till under platser på din webbplats <br><br> *Frågeparametern* för det här nyckelordet ska vara URI för webbplatsen eller under platsen. | Indexera allt innehåll från alla dokument bibliotek i min webbplats. <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/mysite" } ``` |
| includeLibrary | Indexera innehåll från det här biblioteket. <br><br> *Frågeparametern* för det här nyckelordet ska vara i något av följande format: <br><br> Exempel 1: <br><br> *includeLibrary = [plats eller under plats]/[dokument bibliotek]* <br><br> Exempel 2: <br><br> URI har kopierats från webbläsaren. | Indexera allt innehåll från MyDocumentLibrary: <br><br> Exempel 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/mysite/MyDocumentLibrary" } ``` <br><br> Exempel 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |
| excludeLibrary |  Indexera inte innehåll från det här biblioteket. <br><br> *Frågeparametern* för det här nyckelordet ska vara i något av följande format: <br><br> Exempel 1: <br><br> *excludeLibrary = [webbplats eller under plats-URI]/[dokument bibliotek]* <br><br> Exempel 2: <br><br> URI har kopierats från webbläsaren. | Indexera allt innehåll från alla mina bibliotek utom MyDocumentLibrary: <br><br> Exempel 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mysite.sharepoint.com/subsite1; excludeLibrary=https://mysite.sharepoint.com/subsite1/MyDocumentLibrary" } ``` <br><br> Exempel 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/teams/mysite; excludeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |

## <a name="index-by-file-type"></a>Index efter filtyp
Du kan styra vilka dokument som är indexerade och vilka som hoppas över.

### <a name="include-documents-having-specific-file-extensions"></a>Inkludera dokument med vissa fil namns tillägg
Du kan bara indexera dokumenten med de fil namns tillägg som du anger med hjälp av `indexedFileNameExtensions` konfigurations parametern Indexer. Värdet är en sträng som innehåller en kommaavgränsad lista med fil namns tillägg (med en inledande punkt). Om du till exempel bara vill indexera. PDF och. DOCX-dokument, gör du följande:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-documents-having-specific-file-extensions"></a>Uteslut dokument med vissa fil namns tillägg
Du kan utesluta dokument med vissa fil namns tillägg från indexeringen med hjälp av `excludedFileNameExtensions` konfigurations parametern. Värdet är en sträng som innehåller en kommaavgränsad lista med fil namns tillägg (med en inledande punkt). Om du till exempel vill indexera allt innehåll förutom de med. PNG och. JPEG-tillägg gör du följande:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

Om både `indexedFileNameExtensions` och `excludedFileNameExtensions` -parametrarna finns, tittar Azure kognitiv sökning först på `indexedFileNameExtensions` , sedan på `excludedFileNameExtensions` . Det innebär att om samma fil namns tillägg finns i båda listorna, kommer det att undantas från indexering.

## <a name="handling-errors"></a>Hantera fel
Som standard stoppas SharePoint Online-indexeraren så snart den påträffar ett dokument med en innehålls typ som inte stöds (till exempel en bild). Du kan naturligtvis använda `excludedFileNameExtensions` parametern för att hoppa över vissa innehålls typer. Du kan dock behöva indexera dokument utan att känna till alla möjliga innehålls typer i förväg. Om du vill fortsätta indexera när en innehålls typ som inte stöds har påträffats anger du `failOnUnsupportedContentType` konfigurations parametern till falskt:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
}
```

För vissa dokument går det inte att identifiera innehålls typen i Azure Kognitiv sökning eller så det går inte att bearbeta ett dokument av en innehålls typ som inte stöds. Om du vill ignorera det här felläget ställer du in `failOnUnprocessableDocument` konfigurations parametern på falskt:

```http
"parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

Azure Kognitiv sökning begränsar storleken på dokument som indexeras. De här gränserna dokumenteras i [tjänst begränsningar i Azure kognitiv sökning](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity). Överändrade dokument behandlas som standard som fel. Du kan dock fortfarande indexera lagrings metadata för överändrade dokument om du anger `indexStorageMetadataOnlyForOversizedDocuments` konfigurations parametern till True:

```http
"parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

Du kan också fortsätta att indexera om fel inträffar när som helst, antingen vid parsning av dokument eller när du lägger till dokument i ett index. Om du vill ignorera ett visst antal fel anger du `maxFailedItems` `maxFailedItemsPerBatch` parametrarna och för konfigurationen till önskade värden. Exempel:

```http
{
    ... other parts of indexer definition
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

## <a name="see-also"></a>Se även
+ [Indexerare i Azure Cognitive Search](search-indexer-overview.md)
+ [Egenskaper för innehålls metadata som används i Azure Kognitiv sökning](search-blob-metadata-properties.md)
