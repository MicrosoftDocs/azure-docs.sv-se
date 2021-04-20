---
title: Felsöka vanliga problem med sökindexerare
titleSuffix: Azure Cognitive Search
description: Åtgärda fel och vanliga problem med indexerare i Azure Cognitive Search, inklusive anslutning till datakälla, brandvägg och dokument som saknas.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: efdd9666c8876ddaf12b9555fa66beb62c56e93e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107740076"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Felsöka vanliga problem med indexerare i Azure Cognitive Search

Indexerare kan få problem när de indexerar data till Azure Cognitive Search. De viktigaste felkategorierna är:

* [Ansluta till en datakälla eller andra resurser](#connection-errors)
* [Dokumentbearbetning](#document-processing-errors)
* [Dokumentinmatning till ett index](#index-errors)

## <a name="connection-errors"></a>Anslutningsfel

> [!NOTE]
> Indexerare har begränsat stöd för åtkomst till datakällor och andra resurser som skyddas av Azures nätverkssäkerhetsmekanismer. För närvarande kan indexerare endast komma åt datakällor via motsvarande begränsningsmekanismer för IP-adressintervall eller NSG-regler när det är tillämpligt. Information om hur du kommer åt varje datakälla som stöds finns nedan.
>
> Du kan ta reda på IP-adressen för söktjänsten genom att pinga dess fullständigt kvalificerade domännamn (t.ex. `<your-search-service-name>.search.windows.net` ).
>
> Du kan ta reda på IP-adressintervallet för tjänsttaggen genom att antingen använda nedladdningsbara `AzureCognitiveSearch` [](../virtual-network/service-tags-overview.md#available-service-tags) [JSON-filer](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) eller via [API:et för identifiering av tjänsttaggar.](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) IP-adressintervallet uppdateras varje vecka.

### <a name="configure-firewall-rules"></a>Konfigurera brandväggsregler

Azure Storage, CosmosDB och Azure SQL en konfigurerbar brandvägg. Det finns inget specifikt felmeddelande när brandväggen är aktiverad. Vanligtvis är brandväggsfel allmänna och ser ut som `The remote server returned an error: (403) Forbidden` eller `Credentials provided in the connection string are invalid or have expired` .

Det finns två alternativ för att tillåta indexerare att komma åt dessa resurser i en sådan instans:

* Inaktivera brandväggen genom att tillåta åtkomst från **Alla nätverk** (om möjligt).
* Du kan också tillåta åtkomst för IP-adressen för din söktjänst och IP-adressintervallet för tjänsttaggen i brandväggsreglerna för din resurs `AzureCognitiveSearch` [](../virtual-network/service-tags-overview.md#available-service-tags) (begränsning av IP-adressintervall).

Information om hur du konfigurerar begränsningar för IP-adressintervall för varje typ av datakälla finns på följande länkar:

* [Azure Storage](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Cosmos DB](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Azure SQL](../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)

**Begränsning:** Enligt dokumentationen ovan för Azure Storage fungerar begränsningar för IP-adressintervall endast om din söktjänst och ditt lagringskonto finns i olika regioner.

Azure-funktioner (som kan användas som en anpassad [webb-API-färdighet)](cognitive-search-custom-skill-web-api.md)stöder också [IP-adressbegränsningar.](../azure-functions/ip-addresses.md#ip-address-restrictions) Listan över IP-adresser som ska konfigureras är IP-adressen för din söktjänst och IP-adressintervallet för `AzureCognitiveSearch` tjänsttaggen.

Information om åtkomst till data i SQL Server på en virtuell Azure-dator beskrivs [här](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

### <a name="configure-network-security-group-nsg-rules"></a>Konfigurera regler för nätverkssäkerhetsgrupp (NSG)

Vid åtkomst till data i en SQL-hanterad instans, eller när en [](cognitive-search-custom-skill-web-api.md)virtuell Azure-dator används som webbtjänst-URI för en anpassad webb-API-kunskap, behöver kunderna inte bekymra sig om specifika IP-adresser.

I sådana fall kan den virtuella Azure-datorn eller den hanterade SQL-instansen konfigureras att finnas i ett virtuellt nätverk. Sedan kan en nätverkssäkerhetsgrupp konfigureras för att filtrera den typ av nätverkstrafik som kan flöda in och ut från de virtuella nätverksundernäten och nätverksgränssnitten.

`AzureCognitiveSearch`Tjänsttaggen kan användas direkt i de inkommande [NSG-reglerna](../virtual-network/manage-network-security-group.md#work-with-security-rules) utan att ip-adressintervallet behöver sökas upp.

Mer information om åtkomst till data i en SQL-hanterad instans beskrivs [här](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

### <a name="cosmosdb-indexing-isnt-enabled"></a>CosmosDB-indexering är inte aktiverat

Azure Cognitive Search har ett implicit beroende Cosmos DB indexering. Om du inaktiverar automatisk indexering i Cosmos DB, Azure Cognitive Search returnerar ett lyckat tillstånd, men misslyckas med att indexera containerinnehåll. Anvisningar om hur du kontrollerar inställningar och aktiverar indexering finns i [Hantera indexering i Azure Cosmos DB](../cosmos-db/how-to-manage-indexing-policy.md#use-the-azure-portal).

### <a name="sharepoint-online-conditional-access-policies"></a>Principer för villkorlig åtkomst för SharePoint Online

När du skapar en SharePoint Online-indexerare går du igenom ett steg som kräver att du loggar in på AAD-appen när du har skapat en enhetskod. Om du får meddelandet "Inloggningen lyckades men administratören kräver att enheten som begär åtkomst ska hanteras" blockeras indexeraren troligen från att komma åt SharePoint Online-dokumentbiblioteket på grund av en princip för villkorlig [åtkomst.](https://review.docs.microsoft.com/azure/active-directory/conditional-access/overview)

Följ stegen nedan om du vill uppdatera principen så att indexeraren får åtkomst till dokumentbiblioteket:

1. Öppna dialogrutan Azure Portal azure **AD villkorsstyrd åtkomst och** välj **sedan Principer** på den vänstra menyn. Om du inte har åtkomst till den här sidan måste du antingen hitta någon som har åtkomst eller få åtkomst.

1. Bestäm vilken princip som blockerar SharePoint Online-indexeraren från att komma åt dokumentbiblioteket. Principen som kan blockera indexeraren innehåller det användarkonto som du använde för att autentisera när du skapade indexeraren i **avsnittet Användare och** grupper. Principen kan också ha **villkor** som:
    * Begränsa **Windows-plattformar.**
    * Begränsa **Mobilappar och skrivbordsklienter.**
    * Ha **Enhetstillstånd** konfigurerat till **Ja.**

1. När du har bekräftat att det finns en princip som blockerar indexeraren måste du sedan göra ett undantag för indexeraren. Hämta söktjänstens IP-adress.

    1. Hämta det fullständigt kvalificerade domännamnet (FQDN) för söktjänsten. Det här ser ut så `<search-service-name>.search.windows.net` här: . Du kan ta reda på FQDN genom att leta upp din söktjänst på Azure Portal.

   ![Hämta tjänstens FQDN](media\search-indexer-howto-secure-access\search-service-portal.png "Hämta tjänstens FQDN")

    Söktjänstens IP-adress kan hämtas genom att utföra `nslookup` en (eller `ping` en ) av det FQDN som används. I exemplet nedan skulle du lägga till "150.0.0.1" i en regel för inkommande trafik Azure Storage brandväggen. Det kan ta upp till 15 minuter efter att brandväggsinställningarna har uppdaterats för att söktjänstens indexerare ska kunna komma åt Azure Storage konto.

    ```azurepowershell

    nslookup contoso.search.windows.net
    Server:  server.example.org
    Address:  10.50.10.50
    
    Non-authoritative answer:
    Name:    <name>
    Address:  150.0.0.1
    Aliases:  contoso.search.windows.net
    ```

1. Hämta IP-adressintervallen för indexerarkörningsmiljön för din region.

    Ytterligare IP-adresser används för begäranden som kommer från indexeraren i [körningsmiljön för flera innehavare.](search-indexer-securing-resources.md#indexer-execution-environment) Du kan hämta det här IP-adressintervallet från tjänsttaggen.

    IP-adressintervallen för tjänsttaggen kan antingen hämtas `AzureCognitiveSearch` via [identifierings-API:et (förhandsversion)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) eller den [nedladdningsbara JSON-filen](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

    Under förutsättning att söktjänsten är det offentliga Azure-molnet för den här genomgången ska [Azure Public JSON-filen](https://www.microsoft.com/download/details.aspx?id=56519) laddas ned.

   ![Ladda ned JSON-fil](media\search-indexer-troubleshooting\service-tag.png "Ladda ned JSON-fil")

    Från JSON-filen, förutsatt att söktjänsten finns i USA, västra centrala, visas listan över IP-adresser för körningsmiljön för indexerare för flera innehavare nedan.

    ```json
        {
          "name": "AzureCognitiveSearch.WestCentralUS",
          "id": "AzureCognitiveSearch.WestCentralUS",
          "properties": {
            "changeNumber": 1,
            "region": "westcentralus",
            "platform": "Azure",
            "systemService": "AzureCognitiveSearch",
            "addressPrefixes": [
              "52.150.139.0/26",
              "52.253.133.74/32"
            ]
          }
        }
    ```

1. Gå tillbaka till sidan **Villkorsstyrd** åtkomst i Azure Portal välj Namngivna platser på menyn till vänster och välj **sedan + PLATS för IP-intervall.** Ge den nya namngivna platsen ett namn och lägg till IP-intervallen för söktjänsten och indexerarkörningsmiljöerna som du samlade in i de senaste två stegen.
    * För söktjänstens IP-adress kan du behöva lägga till "/32" i slutet av IP-adressen eftersom den bara accepterar giltiga IP-intervall.
    * Kom ihåg att för IP-intervallen för indexerarkörningsmiljön behöver du bara lägga till IP-intervallen för den region som söktjänsten finns i.

1. Undanta den nya namngivna platsen från principen. 
    1. Välj **Principer** på den vänstra menyn. 
    1. Välj den princip som blockerar indexeraren.
    1. Välj **Villkor.**
    1. Välj **Platser.**
    1. Välj **Exkludera och** lägg sedan till den nya namngivna platsen.
    1. **Spara** ändringarna.

1. Vänta några minuter tills principen uppdateras och framtvingar de nya principreglerna.

1. Försök att skapa indexeraren igen
    1. Skicka en uppdateringsbegäran för datakällobjektet som du skapade.
    1. Skicka om begäran om att skapa indexeraren. Använd den nya koden för att logga in och skicka sedan en ny begäran om att skapa indexeraren efter den lyckade inloggningen.

## <a name="document-processing-errors"></a>Dokumentbearbetningsfel

### <a name="unprocessable-or-unsupported-documents"></a>Dokument som inte kan bearbetas eller inte stöds

Blob-indexerardokumenten [som dokumentformat uttryckligen stöds.](search-howto-indexing-azure-blob-storage.md#SupportedFormats). Ibland innehåller en bloblagringscontainer dokument som inte stöds. Andra gånger kan det finnas problematiska dokument. Du kan undvika att stoppa indexeraren i dessa dokument genom att [ändra konfigurationsalternativen](search-howto-indexing-azure-blob-storage.md#DealingWithErrors):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Dokumentinnehåll som saknas

Blob-indexeraren [hittar och extraherar text från blobar i en container](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Några problem med att extrahera text är:

* Dokumentet innehåller endast skannade bilder. PDF-blobbar som har icke-textinnehåll, till exempel skannade bilder (JPEG), producerar inte resultat i en standardpipeline för blobindexering. Om du har bildinnehåll med textelement kan du använda [kognitiv sökning för](cognitive-search-concept-image-scenarios.md) att hitta och extrahera texten.
* Blob-indexeraren är konfigurerad för att endast indexera metadata. För att extrahera innehåll måste blob-indexeraren konfigureras för att [extrahera både innehåll och metadata:](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex)

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Indexfel

### <a name="missing-documents"></a>Dokument som saknas

Indexerare hittar dokument från en [datakälla](/rest/api/searchservice/create-data-source). Ibland verkar ett dokument från datakällan som ska ha indexerats saknas i ett index. Det finns några vanliga orsaker till att de här felen kan inträffa:

* Dokumentet har inte indexerats. Kontrollera om det finns en lyckad indexerarkörning i portalen.
* Kontrollera värdet [för ändringsspårning.](/rest/api/searchservice/create-data-source#data-change-detection-policies) Om värdet för högvattenmärket är ett datum inställt på en framtida tid hoppas alla dokument som har ett datum som är mindre än detta över av indexeraren. Du kan förstå indexerarens ändringsspårningstillstånd med hjälp av fälten "initialTrackingState" och "finalTrackingState" i [indexerarstatusen](/rest/api/searchservice/get-indexer-status#indexer-execution-result).
* Dokumentet uppdaterades efter att indexeraren körts. Om indexeraren har ett [schema](/rest/api/searchservice/create-indexer#indexer-schedule)kommer den så småningom att köra och hämta dokumentet igen.
* Frågan [som](/rest/api/searchservice/create-data-source) anges i datakällan exkluderar dokumentet. Indexerare kan inte indexera dokument som inte ingår i datakällan.
* [Fältmappningar](/rest/api/searchservice/create-indexer#fieldmappings) eller [AI-berikning](./cognitive-search-concept-intro.md) har ändrat dokumentet och det ser annorlunda ut än förväntat.
* Använd [API:et för uppslagsdokument](/rest/api/searchservice/lookup-document) för att hitta dokumentet.