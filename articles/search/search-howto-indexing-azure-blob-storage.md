---
title: Konfigurera en BLOB-indexerare
titleSuffix: Azure Cognitive Search
description: Konfigurera en Azure Blob-indexerare för att automatisera indexering av BLOB-innehåll för fullständiga texts öknings åtgärder i Azure Kognitiv sökning.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.custom: contperf-fy21q3
ms.openlocfilehash: 74813fabec4d5fe43cd158bb4aa359c2a3b0188a
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988727"
---
# <a name="how-to-configure-blob-indexing-in-cognitive-search"></a>Så här konfigurerar du Blob-indexering i Kognitiv sökning

Den här artikeln visar hur du konfigurerar en BLOB-indexerare för indexering av textbaserade dokument (till exempel PDF-filer, Microsoft Office dokument och andra) i Azure Kognitiv sökning. Om du inte är bekant med indexerings koncepten börjar du med [indexerare i Azure kognitiv sökning](search-indexer-overview.md) och [skapar en Sök indexerare](search-howto-create-indexers.md) innan simhopp till BLOB-indexering.

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>Dokumentformat som stöds

Azure Kognitiv sökning BLOB-indexeraren kan extrahera text från följande dokument format:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="data-source-definitions"></a>Definitioner av data källor

Skillnaden mellan en BLOB-indexerare och en annan indexerare är den data käll definition som har tilldelats indexeraren. Data källan kapslar in alla egenskaper som anger typ, anslutning och plats för det innehåll som ska indexeras.

En BLOB data source-definition ser ut ungefär som i exemplet nedan:

```http
{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}
```

`"credentials"`Egenskapen kan vara en anslutnings sträng, som du ser i exemplet ovan, eller någon av de alternativa metoder som beskrivs i nästa avsnitt. `"container"`Egenskapen ger platsen för innehållet i Azure Storage och `"query"` används för att ange en undermapp i behållaren. Mer information om definitioner av data källor finns i [skapa data källa (rest)](/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>

## <a name="credentials"></a>Autentiseringsuppgifter

Du kan ange autentiseringsuppgifter för BLOB-behållaren på något av följande sätt:

**Anslutnings sträng för hanterad identitet**: `{ "connectionString" : "ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;" }`

Den här anslutnings strängen kräver ingen konto nyckel, men du måste följa instruktionerna för [att konfigurera en anslutning till ett Azure Storage konto med hjälp av en hanterad identitet](search-howto-managed-identities-storage.md).

**Anslutnings sträng för lagrings konto med fullständig åtkomst**: `{ "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>;" }`

Du kan hämta anslutnings strängen från Azure Portal genom att gå till bladet lagrings konto > inställningar > nycklar (för klassiska lagrings konton) eller inställningar > åtkomst nycklar (för Azure Resource Manager lagrings konton).

Anslutnings sträng för **signatur för delad åtkomst för lagrings konto** (SAS):`{ "connectionString" : "BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl;" }`

SAS ska ha listan och Läs behörigheter för behållare och objekt (blobbar i detta fall).

**Signatur för delad åtkomst för container**: `{ "connectionString" : "ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl;" }`

SAS ska ha listan och Läs behörigheter för behållaren. Mer information om signaturer för delad åtkomst för lagring finns i [använda signaturer för delad åtkomst](../storage/common/storage-sas-overview.md).

> [!NOTE]
> Om du använder SAS-autentiseringsuppgifter måste du uppdatera autentiseringsuppgifterna för data källan regelbundet med förnyade signaturer för att förhindra att de upphör att gälla. Om autentiseringsuppgifter för SAS går ut Miss Miss känner indexeraren med ett fel meddelande som liknar "autentiseringsuppgifterna i anslutnings strängen är ogiltiga eller har upphört att gälla".  

## <a name="index-definitions"></a>Index definitioner

Indexet anger fält i ett dokument, attribut och andra konstruktioner som formar Sök funktionen. I följande exempel skapas ett enkelt index med hjälp av [create index (REST API)](/rest/api/searchservice/create-index). 

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
      "name" : "my-target-index",
      "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
      ]
}
```

Index definitioner kräver ett fält i `"fields"` samlingen för att fungera som dokument nyckel. Index definitioner bör även innehålla fält för innehåll och metadata.

Ett **`content`** fält används för att lagra den text som extraherats från blobbar. Din definition av det här fältet kan se ut ungefär så här. Du behöver inte använda det här namnet, men om du gör det kan du utnyttja implicita fält mappningar. BLOB-indexeraren kan skicka BLOB-innehåll till ett innehålls-EDM. String-fält i indexet, inga fält mappningar krävs.

Du kan också lägga till fält för alla BLOB-metadata som du vill ha i indexet. Indexeraren kan läsa anpassade egenskaper för metadata, egenskaper för [standard-metadata](#indexing-blob-metadata) och egenskaper för [innehålls speciella metadata](search-blob-metadata-properties.md) . Mer information om index finns i [skapa ett index](search-what-is-an-index.md).

<a name="DocumentKeys"></a>

### <a name="defining-document-keys-and-field-mappings"></a>Definiera dokument nycklar och fält mappningar

I ett Sök index identifierar dokument nyckeln varje dokument unikt. Fältet du väljer måste vara av typen `Edm.String` . För BLOB-innehåll är de bästa kandidater för en dokument nyckel metadata-egenskaper i blobben.

+ **`metadata_storage_name`** -den här egenskapen är en kandidat, men endast om namn är unika för alla behållare och mappar som du indexerar. Oavsett BLOB-plats är slut resultatet att dokument nyckeln (namn) måste vara unik i Sök indexet när allt innehåll har indexerats. 

  Ett annat potentiellt problem med lagrings namnet är att det kan innehålla tecken som inte är giltiga för dokument nycklar, till exempel bindestreck. Du kan hantera ogiltiga tecken med `base64Encode` [fält mappnings funktionen](search-indexer-field-mappings.md#base64EncodeFunction). Om du gör det, kom ihåg att även koda dokument nycklar när de skickas i API-anrop som [Lookup-dokument (rest)](/rest/api/searchservice/lookup-document). I .NET kan du använda [metoden UrlTokenEncode](/dotnet/api/system.web.httpserverutility.urltokenencode) för att koda tecken.

+ **`metadata_storage_path`** – Om du använder den fullständiga sökvägen ser du till att den är unik, men sökvägen innehåller definitivt `/` [ogiltiga tecken i en dokument nyckel](/rest/api/searchservice/naming-rules). Som ovan kan du använda `base64Encode` [funktionen](search-indexer-field-mappings.md#base64EncodeFunction) för att koda tecken.

+ Ett tredje alternativ är att lägga till en anpassad metadata-egenskap till Blobbarna. Det här alternativet kräver att BLOB-uppladdnings processen lägger till denna metadata-egenskap till alla blobbar. Eftersom nyckeln är en obligatorisk egenskap kommer alla blobbar som saknar ett värde att indexeras.

> [!IMPORTANT]
> Om det inte finns någon explicit mappning för nyckel fältet i indexet, används automatiskt Azure Kognitiv sökning `metadata_storage_path` som nyckel-och bas-64-kodningar nyckel värden (det andra alternativet ovan).
>

#### <a name="example"></a>Exempel

Följande exempel visar `metadata_storage_name` som dokument nyckeln. Anta att indexet har ett nyckel fält med namnet `key` och ett annat fält med namnet `fileSize` för att lagra dokument storleken. [Fält mappningar](search-indexer-field-mappings.md) i index definitions definitionen upprättar fält associationer och `metadata_storage_name` har [ `base64Encode` fält mappnings funktionen](search-indexer-field-mappings.md#base64EncodeFunction) för att hantera tecken som inte stöds.

```http
PUT https://[service name].search.windows.net/indexers/my-blob-indexer?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "dataSourceName" : "my-blob-datasource ",
  "targetIndexName" : "my-target-index",
  "schedule" : { "interval" : "PT2H" },
  "fieldMappings" : [
    { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
    { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
  ]
}
```

#### <a name="how-to-make-an-encoded-field-searchable"></a>Så här gör du ett kodat fält "sökbart"

Det finns tillfällen när du behöver använda en kodad version av ett fält `metadata_storage_path` , t. ex. nyckeln, men du måste också att fältet ska vara sökbart (utan kodning) i Sök indexet. För att stödja båda användnings fall kan du mappa `metadata_storage_path` till två fält, en för nyckeln (kodad) och en sekund för ett Sök vägs fält som vi kan anta som "sökbart" i index schemat. Exemplet nedan visar två fält mappningar för `metadata_storage_path` .

```http
PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "dataSourceName" : " blob-datasource ",
  "targetIndexName" : "my-target-index",
  "schedule" : { "interval" : "PT2H" },
  "fieldMappings" : [
    { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
    { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "path" }
  ]
}
```

<a name="PartsOfBlobToIndex"></a>

## <a name="index-content-and-metadata"></a>Indexera innehåll och metadata

Blobbar innehåller innehåll och metadata. Du kan styra vilka delar av blobbar som indexeras med hjälp av `dataToExtract` konfigurations parametern. Det kan ha följande värden:

+ `contentAndMetadata` -anger att alla metadata och text innehåll som extraheras från blobben indexeras. Detta är standardvärdet.

+ `storageMetadata` -anger att endast [standard-BLOB-egenskaperna och användardefinierade metadata](../storage/blobs/storage-blob-container-properties-metadata.md) indexeras.

+ `allMetadata` -anger att standard-BLOB-egenskaper och [metadata för hittade innehålls typer](search-blob-metadata-properties.md) extraheras från BLOB-innehållet och indexeras.

Om du till exempel bara vill indexera lagringens metadata använder du:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
}
```

<a name="how-azure-search-indexes-blobs"></a>

### <a name="indexing-blob-content"></a>Indexera BLOB-innehåll

Som standard indexeras blobbar med strukturerat innehåll, till exempel JSON eller CSV, som ett enda text segment. Men om JSON-eller CSV-dokumenten har en intern struktur (avgränsare) kan du tilldela tolknings lägen för att skapa enskilda Sök dokument för varje rad eller element. Mer information finns i [INDEXERA JSON-blobbar](search-howto-index-json-blobs.md) och [Indexera CSV-blobbar](search-howto-index-csv-blobs.md).

Ett sammansatt eller inbäddat dokument (till exempel ett ZIP-arkiv, ett Word-dokument med inbäddad Outlook-e-post med bifogade filer eller en. MSG-filen med bifogade filer är också indexerad som ett enda dokument. Till exempel alla bilder som extraheras från bilagorna till en. MSG-filen kommer att returneras i fältet normalized_images.

Text innehållet i dokumentet extraheras till ett sträng fält med namnet `content` .

  > [!NOTE]
  > Azure Kognitiv sökning begränsar hur mycket text den extraherar beroende på pris nivå. De aktuella [tjänst gränserna](search-limits-quotas-capacity.md#indexer-limits) är 32 000 tecken för kostnads fri nivå, 64 000 för Basic, 4 000 000 för standard, 8 000 000 för standard S2 och 16 000 000 för standard S3. En varning ingår i status svaret för indexeraren för trunkerade dokument.  

<a name="indexing-blob-metadata"></a>

### <a name="indexing-blob-metadata"></a>Indexera BLOB-metadata

Indexerare kan också indexera BLOB-metadata. Först kan alla användare-angivna metadataegenskaper extraheras orda Grant. Om du vill ta emot värdena måste du definiera ett fält i Sök indexet av typen `Edm.String` , med samma namn som metadata-nyckeln för blobben. Om en blob till exempel har en nyckel för `Sensitivity` med värde `High` , bör du definiera ett fält med namnet `Sensitivity` i Sök indexet så att det fylls med värdet `High` .

Andra, standard egenskaper för BLOB-metadata kan extraheras till fälten i listan nedan. BLOB-indexeraren skapar automatiskt interna fält mappningar för dessa egenskaper för BLOB-metadata. Du måste fortfarande lägga till de fält som du vill använda för index definitionen, men du kan utesluta att skapa fält mappningar i indexeraren.

  + **metadata_storage_name** ( `Edm.String` ) – fil namnet för blobben. Om du till exempel har en BLOB-/My-container/My-Folder/subfolder/resume.pdf är värdet för det här fältet `resume.pdf` .

  + **metadata_storage_path** ( `Edm.String` ) – den fullständiga URI: n för blobben, inklusive lagrings kontot. Till exempel `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

  + **metadata_storage_content_type** ( `Edm.String` ) – innehålls typ som anges av den kod som du använde för att ladda upp blobben. Till exempel `application/octet-stream`.

  + **metadata_storage_last_modified** ( `Edm.DateTimeOffset` )-den senaste ändrade tidsstämpeln för blobben. Azure Kognitiv sökning använder den här tidsstämpeln för att identifiera ändrade blobbar, för att undvika att indexera om allt efter den inledande indexeringen.

  + **metadata_storage_size** ( `Edm.Int64` )-BLOB-storlek i byte.

  + **metadata_storage_content_md5** ( `Edm.String` ) – MD5-hash av BLOB-innehållet, om det är tillgängligt.

  + **metadata_storage_sas_token** ( `Edm.String` ) – en tillfällig SAS-token som kan användas av [anpassade kunskaper](cognitive-search-custom-skill-interface.md) för att få åtkomst till blobben. Denna token ska inte lagras för senare användning eftersom den kan gå ut.

Slutligen kan alla metadata-egenskaper som är speciella för dokument formatet för de blobbar som du indexerar också visas i index schemat. Mer information om leverantörsspecifika metadata finns i [Egenskaper för innehålls metadata](search-blob-metadata-properties.md).

Det är viktigt att du pekar på att du inte behöver definiera fält för alla egenskaper som anges ovan i sökindexet – du behöver bara hämta de egenskaper du behöver för ditt program.

<a name="WhichBlobsAreIndexed"></a>

## <a name="how-to-control-which-blobs-are-indexed"></a>Så här kontrollerar du vilka blobbar som indexeras

Du kan kontrol lera vilka blobbar som är indexerade och vilka som hoppas över, av blobens filtyp eller genom att ange egenskaper för själva blobben, vilket gör att indexeraren hoppar över dem.

### <a name="include-specific-file-extensions"></a>Inkludera vissa fil namns tillägg

Används `indexedFileNameExtensions` för att tillhandahålla en kommaavgränsad lista över fil namns tillägg som ska indexeras (med en inledande punkt). Om du till exempel bara vill indexera. PDF och. DOCX-blobbar gör du följande:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-specific-file-extensions"></a>Undanta vissa fil namns tillägg

Används `excludedFileNameExtensions` för att tillhandahålla en kommaavgränsad lista över fil namns tillägg som ska hoppas över (återigen med en inledande punkt). Om du till exempel vill indexera alla blobbar förutom de med. PNG och. JPEG-tillägg gör du följande:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

Om både `indexedFileNameExtensions` och `excludedFileNameExtensions` -parametrarna finns, tittar indexeraren först på `indexedFileNameExtensions` och sedan på `excludedFileNameExtensions` . Om samma fil namns tillägg finns i båda listorna, kommer de att undantas från indexering.

### <a name="add-skip-metadata-the-blob"></a>Lägg till metadata för "Skip" i blobben

Konfigurations parametrarna för indexeraren gäller för alla blobar i behållaren eller mappen. Ibland vill du styra hur *enskilda blobbar* indexeras. Du kan göra detta genom att lägga till följande egenskaper och värden för metadata i blobar i Blob Storage. När indexeraren påträffar dessa egenskaper hoppar den över blobben eller dess innehåll i indexerings körningen.

| Egenskapsnamn | Egenskaps värde | Förklaring |
| ------------- | -------------- | ----------- |
| `AzureSearch_Skip` |`"true"` |Instruerar BLOB-indexeraren att helt hoppa över blobben. Varken metadata eller innehålls extrahering görs. Detta är användbart när en viss BLOB Miss lyckas upprepade gånger och avbryter indexerings processen. |
| `AzureSearch_SkipContent` |`"true"` |Detta motsvarar den `"dataToExtract" : "allMetadata"` inställning som beskrivs [ovan](#PartsOfBlobToIndex) begränsad till en viss blob. |

## <a name="index-large-datasets"></a>Indexera stora data uppsättningar

Indexering av blobbar kan vara en tids krävande process. I de fall där du har miljon tals blobbar att indexera kan du påskynda indexeringen genom att partitionera data och använda flera indexerare för att [bearbeta data parallellt](search-howto-large-index.md#parallel-indexing). Så här kan du konfigurera detta:

+ Partitionera dina data i flera BLOB-behållare eller virtuella mappar

+ Konfigurera flera data källor, en per behållare eller mapp. Använd parametern för att peka på en BLOB-mapp `query` :

    ```json
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

+ Skapa en motsvarande indexerare för varje data källa. Alla indexerare ska peka på samma mål Sök index.  

+ En Sök enhet i din tjänst kan köra en indexerare vid en specifik tidpunkt. Att skapa flera indexerare enligt beskrivningen ovan är bara användbart om de faktiskt körs parallellt.

  Om du vill köra flera indexerare parallellt kan du skala ut Sök tjänsten genom att skapa ett lämpligt antal partitioner och repliker. Om Sök tjänsten till exempel har 6 Sök enheter (till exempel 2 partitioner x 3 repliker) kan 6 indexerare köras samtidigt, vilket resulterar i en Six-vikt i index data flödet. Mer information om skalning och kapacitets planering finns i [Justera kapaciteten för en Azure kognitiv sökning-tjänst](search-capacity-planning.md).

<a name="DealingWithErrors"></a>

## <a name="handling-errors"></a>Hantera fel

Fel som ofta uppstår under indexeringen är innehålls typer som inte stöds, innehåll som saknas eller överstorleksändrade blobbar.

Som standard stoppas BLOB-indexeraren så snart den påträffar en blob med en innehålls typ som inte stöds (till exempel en bild). Du kan använda `excludedFileNameExtensions` parametern för att hoppa över vissa innehålls typer. Men du kanske vill indexera för att fortsätta även om det uppstår fel och sedan felsöka enskilda dokument senare. Mer information om fel i indexeraren finns i [Felsöka vanliga indexerings problem](search-indexer-troubleshooting.md) och [index fel och varningar](cognitive-search-common-errors-warnings.md).

### <a name="respond-to-errors"></a>Svara på fel

Det finns fyra indexerings egenskaper som styr indexeraren svar när fel inträffar. I följande exempel visas hur du ställer in dessa egenskaper i index definition. Om det redan finns en indexerare kan du lägga till dessa egenskaper genom att redigera definitionen i portalen.

#### <a name="maxfaileditems-and-maxfaileditemsperbatch"></a>`"maxFailedItems"` och `"maxFailedItemsPerBatch"`

Fortsätt att indexera om några fel inträffar, antingen vid parsning av blobbar eller när dokument läggs till i ett index. Ange de här egenskaperna till antalet godkända försök. Värdet `-1` tillåter bearbetning oavsett hur många fel som inträffar. Annars är värdet ett positivt heltal.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

#### <a name="failonunsupportedcontenttype-and-failonunprocessabledocument"></a>`"failOnUnsupportedContentType"` och `"failOnUnprocessableDocument"` 

För vissa blobbar går det inte att identifiera innehålls typen i Azure Kognitiv sökning eller så det går inte att bearbeta ett dokument av en innehålls typ som stöds. Om du vill ignorera dessa fel villkor ställer du in konfigurations parametrar på `false` :

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="relax-indexer-constraints"></a>Minska indexerings begränsningar

Du kan också ange [Egenskaper för BLOB-konfiguration](/rest/api/searchservice/create-indexer#blob-configuration-parameters) som effektivt avgör om ett fel tillstånd finns. Följande egenskap kan minska begränsningar och utelämna fel som annars skulle inträffa.

+ `"indexStorageMetadataOnlyForOversizedDocuments"` för att indexera lagrings metadata för BLOB-innehåll som är för stort för att bearbeta. Överändrade blobbar behandlas som standard som fel. Begränsningar för BLOB-storlek finns i [tjänst begränsningar](search-limits-quotas-capacity.md).

## <a name="see-also"></a>Se även

+ [Indexerare i Azure Cognitive Search](search-indexer-overview.md)
+ [Skapa en indexerare](search-howto-create-indexers.md)
+ [Översikt över AI-anrikning över blobbar](search-blob-ai-integration.md)
+ [Översikt över att söka över blobbar](search-blob-storage-integration.md)