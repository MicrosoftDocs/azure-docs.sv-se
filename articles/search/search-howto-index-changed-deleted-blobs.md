---
title: Ändrade och borttagna blobbar
titleSuffix: Azure Cognitive Search
description: När ett inledande sökindex bygger som importerar från Azure Blob Storage kan efterföljande indexering bara hämta de blobbar som har ändrats eller tagits bort. I den här artikeln förklaras detaljerna.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: b23dabb4388331de9e37ee9db1d4b9d727ccde68
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430568"
---
# <a name="how-to-set-up-change-and-deletion-detection-for-blobs-in-azure-cognitive-search-indexing"></a>Så här konfigurerar du ändrings-och borttagnings identifiering för blobbar i Azure Kognitiv sökning indexering

När ett inledande sökindex har skapats kanske du vill att efterföljande indexerings jobb bara ska hämta nya och ändrade dokument. För Sök innehåll som kommer från Azure Blob Storage sker ändrings identifiering automatiskt när du använder ett schema för att utlösa indexering. Som standard indexerar tjänsten om bara de ändrade blobbar som fastställs av blobens `LastModified` tidsstämpel. Till skillnad från andra data källor som stöds av Sök indexerare har blobbar alltid en tidsstämpel, vilket eliminerar behovet av att konfigurera en princip för ändrings identifiering manuellt.

Även om ändrings identifieringen är en specifik, är inte borttagnings identifieringen. Om du vill identifiera borttagna dokument, se till att använda en "mjuk borttagning"-metod. Om du tar bort Blobbarna till höger tas inte motsvarande dokument bort från Sök indexet.

Det finns två sätt att implementera metoden för mjuk borttagning:

+ Intern BLOB-mjuk borttagning (förhands granskning), beskrivning nästa
+ [Mjuk borttagning med anpassade metadata](#soft-delete-using-custom-metadata)

## <a name="native-blob-soft-delete-preview"></a>Intern BLOB-mjuk borttagning (förhands granskning)

För den här metoden för borttagnings identifiering är Kognitiv sökning beroende av den [inbyggda funktionen BLOB-mjuk borttagning](../storage/blobs/soft-delete-blob-overview.md) i Azure Blob Storage för att avgöra om blobbar har övergått till ett mjukt borttaget tillstånd. När blobbar identifieras i det här läget använder en Sök indexerare den här informationen för att ta bort motsvarande dokument från indexet.

> [!IMPORTANT]
> Stöd för intern BLOB-mjuk borttagning är i för hands version. För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Den [REST API version 2020-06-30 – för hands version](./search-api-preview.md) innehåller den här funktionen. Det finns för närvarande inget stöd för Portal eller .NET SDK.

### <a name="prerequisites"></a>Förutsättningar

+ [Aktivera mjuk borttagning för blobbar](../storage/blobs/soft-delete-blob-enable.md).
+ Blobbar måste finnas i en Azure Blob Storage-behållare. Den Kognitiv sökning inbyggda BLOB-principen för mjuk borttagning stöds inte för blobbar från Azure Data Lake Storage Gen2.
+ Dokument nycklar för dokumenten i ditt index måste mappas till antingen vara en BLOB-egenskap eller BLOB-metadata.
+ Du måste använda förhands gransknings REST API ( `api-version=2020-06-30-Preview` ) för att konfigurera stöd för mjuk borttagning.

### <a name="how-to-configure-deletion-detection-using-native-soft-delete"></a>Så här konfigurerar du borttagnings identifiering med inbyggd mjuk borttagning

1. I Blob Storage, vid aktivering av mjuk borttagning, anger du bevarande principen till ett värde som är mycket högre än schemats intervall schema. På det här sättet är det mycket tid för indexeraren att bearbeta de mjuka borttagna blobarna om det uppstår ett problem med att köra indexeraren eller om du har ett stort antal dokument att indexera. Azure Kognitiv sökning-indexerare tar bara bort ett dokument från indexet om det bearbetar blobben när det är i ett mjukt borttaget tillstånd.

1. I Kognitiv sökning anger du en intern identifierings princip för mjuk borttagning av BLOB på data källan. Ett exempel på detta visas nedan. Eftersom den här funktionen är en för hands version måste du använda REST API för förhands granskning.

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

1. [Kör indexeraren](/rest/api/searchservice/run-indexer) eller Ställ in indexeraren så att den körs [enligt ett schema](search-howto-schedule-indexers.md). När indexeraren kör och bearbetar en blob med läget för mjuk borttagning tas motsvarande sökdokument bort från indexet.

### <a name="reindexing-undeleted-blobs-using-native-soft-delete-policies"></a>Indexera om återtagitna blobbar (med interna principer för mjuk borttagning)

Om du återställer en mjuk borttagen BLOB i Blob Storage, kommer indexeraren inte alltid att indexera om den. Detta beror på att indexeraren använder blobens `LastModified` tidstämpel för att avgöra om indexering behövs. När en mjuk borttagen BLOB tas bort, `LastModified` uppdateras inte tidsstämpeln, så om indexeraren redan har bearbetat blobbar med nyare `LastModified` tidsstämplar, kommer den inte att indexera om den borttagna blobben. 

För att se till att en ångrad BLOB indexeras om måste du uppdatera blobens `LastModified` tidstämpel. Ett sätt att göra detta är genom att spara om metadata för denna blob. Du behöver inte ändra metadata, men om du sparar om metadata uppdateras blobens `LastModified` tidstämpel så att indexeraren vet att den ska kunna väljas.

## <a name="soft-delete-using-custom-metadata"></a>Mjuk borttagning med anpassade metadata

Den här metoden använder en blobs metadata för att avgöra om ett Sök dokument ska tas bort från indexet. Den här metoden kräver två separata åtgärder, tar bort Sök dokumentet från indexet följt av BLOB-borttagning i Azure Storage.

Det finns steg att följa i både Blob Storage och Kognitiv sökning, men det finns inga andra funktions beroenden. Den här funktionen stöds i allmänt tillgängliga API: er.

1. Lägg till ett anpassat nyckel/värde-par för metadata i blobben för att indikera Azure-Kognitiv sökning att den tas bort logiskt.

1. Konfigurera en princip för identifiering av mjuk borttagnings kolumner på data källan. Följande princip anser till exempel att en BLOB tas bort om den har en metadata-egenskap `IsDeleted` med värdet `true` :

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
    ```

1. När indexeraren har bearbetat blobben och tagit bort dokumentet från indexet kan du ta bort blobben i Azure Blob Storage.

### <a name="reindexing-undeleted-blobs-using-custom-metadata"></a>Indexera om återtagitna blobbar (med anpassade metadata)

När en indexerare bearbetar en borttagen blob och tar bort motsvarande sökdokument från indexet, återkommer inte denna BLOB om du återställer den senare om blobens `LastModified` tidstämpel är äldre än den sista indexeraren som körs.

Om du vill indexera om dokumentet ändrar du `"softDeleteMarkerValue" : "false"` för bloben och kör om indexeraren.

## <a name="next-steps"></a>Nästa steg

+ [Indexerare i Azure Cognitive Search](search-indexer-overview.md)
+ [Så här konfigurerar du en BLOB-indexerare](search-howto-indexing-azure-blob-storage.md)
+ [Översikt över BLOB-indexering](search-blob-storage-integration.md)