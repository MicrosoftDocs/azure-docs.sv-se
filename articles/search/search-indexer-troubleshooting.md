---
title: Felsök vanliga problem med Sök indexering
titleSuffix: Azure Cognitive Search
description: Åtgärda fel och vanliga problem med indexerare i Azure Kognitiv sökning, inklusive anslutning av data källor, brand väggar och saknade dokument.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7eadc9121c54b636fa8b42579284d4018043e1c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91355133"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Felsöka vanliga indexerings problem i Azure Kognitiv sökning

Indexerare kan köra ett antal problem när de indexerar data i Azure Kognitiv sökning. De huvudsakliga kategorier av felen är:

* [Ansluta till en data källa eller andra resurser](#connection-errors)
* [Dokument bearbetning](#document-processing-errors)
* [Dokument inmatning till ett index](#index-errors)

## <a name="connection-errors"></a>Anslutningsfel

> [!NOTE]
> Indexerare har begränsat stöd för åtkomst till data källor och andra resurser som skyddas av Azure Network Security-mekanismer. För närvarande kan indexerarna bara komma åt data källor via motsvarande begränsningar för begränsning av IP-adressintervall eller NSG-regler i förekommande fall. Information om hur du kommer åt varje data källa som stöds finns nedan.
>
> Du kan ta reda på IP-adressen för din Sök tjänst genom att pinga det fullständigt kvalificerade domän namnet (t. ex. `<your-search-service-name>.search.windows.net` ).
>
> Du kan ta reda på IP-adressintervallet för `AzureCognitiveSearch` [service tag](../virtual-network/service-tags-overview.md#available-service-tags) genom att antingen använda [nedladdnings bara JSON-filer](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) eller genom att använda [API: et för identifiering av service tag](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview). IP-adressintervallet uppdateras varje vecka.

### <a name="configure-firewall-rules"></a>Konfigurera brandväggsregler

Azure Storage, CosmosDB och Azure SQL tillhandahåller en konfigurerbar brand vägg. Det finns inget speciellt fel meddelande när brand väggen är aktive rad. Normalt är brand Väggs fel allmänt och ser ut som `The remote server returned an error: (403) Forbidden` eller `Credentials provided in the connection string are invalid or have expired` .

Det finns två alternativ för att tillåta indexerare att få åtkomst till dessa resurser i en sådan instans:

* Inaktivera brand väggen genom att tillåta åtkomst från **alla nätverk** (om möjligt).
* Alternativt kan du tillåta åtkomst till IP-adressen för Sök tjänsten och IP-adressintervallet för `AzureCognitiveSearch` [service tag](../virtual-network/service-tags-overview.md#available-service-tags) i brand Väggs reglerna för din resurs (begränsning för IP-adressintervall).

Information om hur du konfigurerar begränsningar för IP-adressintervall för varje typ av data källa finns i följande länkar:

* [Azure Storage](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Cosmos DB](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Azure SQL](../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)

**Begränsning**: som anges i dokumentationen ovan för Azure Storage fungerar begränsningarna för IP-adressintervall bara om Sök tjänsten och ditt lagrings konto finns i olika regioner.

Azure Functions (som kan användas som en [anpassad webb-API-kunskap](cognitive-search-custom-skill-web-api.md)) stöder också [IP-adressbegränsningar](../azure-functions/ip-addresses.md#ip-address-restrictions). Listan med IP-adresser som ska konfigureras är IP-adressen för Sök tjänsten och IP-adressintervallet för `AzureCognitiveSearch` tjänst tag gen.

Information om hur du kommer åt data i SQL Server på en virtuell Azure-dator beskrivs [här](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

### <a name="configure-network-security-group-nsg-rules"></a>Konfigurera regler för nätverks säkerhets grupper (NSG)

Vid åtkomst till data i en SQL-hanterad instans, eller när en virtuell Azure-dator används som webb tjänst-URI för en [anpassad webb-API-kunskap](cognitive-search-custom-skill-web-api.md), behöver kunder inte bekymra sig om vissa IP-adresser.

I sådana fall kan den virtuella Azure-datorn eller SQL-hanterade instansen konfigureras så att de finns i ett virtuellt nätverk. Sedan kan en nätverks säkerhets grupp konfigureras för att filtrera den typ av nätverks trafik som kan flöda in i och ut ur de virtuella nätverkets undernät och nätverks gränssnitt.

`AzureCognitiveSearch`Service tag gen kan användas direkt i regler för inkommande [NSG](../virtual-network/manage-network-security-group.md#work-with-security-rules) utan att behöva leta upp dess IP-adressintervall.

Mer information om hur du kommer åt data i en SQL-hanterad instans beskrivs [här](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

### <a name="cosmosdb-indexing-isnt-enabled"></a>CosmosDB "indexering" är inte aktive rad

Azure Kognitiv sökning har ett implicit beroende av Cosmos DB indexering. Om du inaktiverar automatisk indexering i Cosmos DB, returnerar Azure Kognitiv sökning ett lyckat tillstånd, men det går inte att indexera container innehåll. Instruktioner för hur du kontrollerar inställningar och aktiverar indexering finns i [Hantera indexering i Azure Cosmos DB](../cosmos-db/how-to-manage-indexing-policy.md#use-the-azure-portal).

## <a name="document-processing-errors"></a>Fel vid dokument bearbetning

### <a name="unprocessable-or-unsupported-documents"></a>Dokument som inte kan hanteras eller som inte stöds

BLOB-indexeraren [dokument vars dokument format stöds explicit.](search-howto-indexing-azure-blob-storage.md#SupportedFormats).. Ibland innehåller en Blob Storage-behållare dokument som inte stöds. Andra gånger kan det finnas problematiska dokument. Du kan undvika att stoppa din indexerare på dessa dokument genom att [ändra konfigurations alternativ](search-howto-indexing-azure-blob-storage.md#DealingWithErrors):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Dokument innehåll saknas

BLOB-indexeraren [söker efter och extraherar text från blobbar i en behållare](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Vissa problem med extrahering av text är:

* Dokumentet innehåller bara skannade bilder. PDF-blobar som inte har text innehåll, till exempel scannade bilder (JPGs), genererar inte resultat i en standard-BLOB för BLOB-indexering. Om du har bild innehåll med text element kan du använda [kognitiv sökning](cognitive-search-concept-image-scenarios.md) för att hitta och extrahera texten.
* BLOB-indexeraren har kon figurer ATS för att endast indexera metadata. För att extrahera innehåll måste BLOB-indexeraren konfigureras för att [extrahera både innehåll och metadata](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Index fel

### <a name="missing-documents"></a>Dokument som saknas

Indexerare hittar dokument från en [data källa](/rest/api/searchservice/create-data-source). Ibland visas ett dokument från data källan som har indexerats för att saknas i ett index. Det finns några vanliga orsaker till att dessa fel inträffar:

* Dokumentet har inte indexerats. Kontrol lera portalen för att köra en lyckad indexerare.
* Kontrol lera värdet för [ändrings spårning](/rest/api/searchservice/create-data-source#data-change-detection-policies) . Om ditt högsta värde för vatten märket är ett datum som anges till en framtida tid, hoppas alla dokument som har ett datum som är mindre än detta att hoppas över av indexeraren. Du kan förstå indexeraren ändrings spårnings tillstånd med hjälp av fälten "initialTrackingState" och "finalTrackingState" i [indexerings status](/rest/api/searchservice/get-indexer-status#indexer-execution-result).
* Dokumentet uppdaterades när indexeraren kördes. Om indexeraren är enligt ett [schema](/rest/api/searchservice/create-indexer#indexer-schedule), kommer den att köras igen och hämta dokumentet.
* [Frågan](/rest/api/searchservice/create-data-source) som anges i data källan utesluter dokumentet. Indexerare kan inte indexera dokument som inte är en del av data källan.
* [Fält mappningar](/rest/api/searchservice/create-indexer#fieldmappings) eller [AI-berikning](./cognitive-search-concept-intro.md) har ändrat dokumentet och det ser annorlunda ut än förväntat.
* Använd [Sök-API: et](/rest/api/searchservice/lookup-document) för att hitta ditt dokument.