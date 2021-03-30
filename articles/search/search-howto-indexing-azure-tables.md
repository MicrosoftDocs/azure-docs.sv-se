---
title: Sök över innehåll i Azure Table Storage
titleSuffix: Azure Cognitive Search
description: Lär dig hur du indexerar data som lagras i Azure Table Storage med en Azure Kognitiv sökning-indexerare.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: 2c67cd4d071660da2ca5714623695ca434329263
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91275191"
---
# <a name="how-to-index-tables-from-azure-table-storage-with-azure-cognitive-search"></a>Indexera tabeller från Azure Table Storage med Azure Kognitiv sökning

Den här artikeln visar hur du använder Azure Kognitiv sökning för att indexera data som lagras i Azure Table Storage.

## <a name="set-up-azure-table-storage-indexing"></a>Konfigurera lagrings indexering i Azure Table

Du kan konfigurera en Azure Table Storage-indexerare med hjälp av följande resurser:

* [Azure-portalen](https://ms.portal.azure.com)
* Azure Kognitiv sökning [REST API](/rest/api/searchservice/Indexer-operations)
* Azure Kognitiv sökning [.NET SDK](/dotnet/api/overview/azure/search)

Här demonstrerar vi flödet med hjälp av REST API. 

### <a name="step-1-create-a-datasource"></a>Steg 1: skapa en data Källa

En data källa anger vilka data som ska indexeras, de autentiseringsuppgifter som krävs för att komma åt data och de principer som gör det möjligt för Azure Kognitiv sökning att effektivt identifiera ändringar i data.

För tabell indexering måste data källan ha följande egenskaper:

- **namn** är det unika namnet på data källan i din Sök tjänst.
- **typen** måste vara `azuretable` .
- parametern **credentials** innehåller anslutnings strängen för lagrings kontot. Mer information finns i avsnittet [ange autentiseringsuppgifter](#Credentials) .
- **container** anger tabell namnet och en valfri fråga.
    - Ange tabell namnet med hjälp av `name` parametern.
    - Du kan också ange en fråga med hjälp av `query` parametern. 

> [!IMPORTANT] 
> När det är möjligt ska du använda ett filter på PartitionKey för bättre prestanda. Alla andra frågor gör en fullständig tabells ökning, vilket resulterar i dåliga prestanda för stora tabeller. Se avsnittet om [prestanda överväganden](#Performance) .


Så här skapar du en data Källa:

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   
```

Mer information om API för att skapa data källor finns i [skapa data källa](/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>Sätt att ange autentiseringsuppgifter ####

Du kan ange autentiseringsuppgifterna för tabellen på något av följande sätt: 

- **Anslutnings sträng för hanterad identitet**: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;` den här anslutnings strängen kräver ingen konto nyckel, men du måste följa instruktionerna för [att konfigurera en anslutning till ett Azure Storage konto med hjälp av en hanterad identitet](search-howto-managed-identities-storage.md).
- **Anslutnings sträng för lagrings konto med fullständig åtkomst**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` du kan hämta anslutnings strängen från Azure Portal genom att gå till **bladet lagrings konto blad**  >  **Inställningar**  >   (för klassiska lagrings konton) eller   >  **åtkomst nycklar** för inställningar (för Azure Resource Manager lagrings konton).
- **Anslutnings sträng för signatur för delad åtkomst till lagrings konto**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` signaturen för delad åtkomst ska ha listan och Läs behörigheter för behållare (tabeller i det här fallet) och objekt (tabell rader).
-  **Signatur för delad** åtkomst för tabell: signaturen för `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` delad åtkomst ska ha Läs behörighet för den här tabellen.

Mer information om signaturer för delad åtkomst för lagring finns i [använda signaturer för delad åtkomst](../storage/common/storage-sas-overview.md).

> [!NOTE]
> Om du använder autentiseringsuppgifter för signaturen för delad åtkomst måste du uppdatera autentiseringsuppgifterna för data källan regelbundet med förnyade signaturer för att förhindra att deras förfallo datum går ut. Om autentiseringsuppgifter för signaturen för delad åtkomst upphör att gälla, Miss lyckas indexeraren med ett fel meddelande som liknar "autentiseringsuppgifterna i anslutnings strängen är ogiltiga eller har upphört att gälla".  

### <a name="step-2-create-an-index"></a>Steg 2: Skapa ett index
Indexet anger fält i ett dokument, attributen och andra konstruktioner som formar Sök upplevelsen.

Så här skapar du ett index:

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }
```

Mer information om hur du skapar index finns i [skapa index](/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>Steg 3: skapa en indexerare
En indexerare ansluter en data källa med ett mål Sök index och ger ett schema för att automatisera data uppdateringen. 

När indexet och data källan har skapats är du redo att skapa indexeraren:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Indexeraren körs var två: e timme. (Schema intervallet är inställt på "PT2H".) Om du vill köra en indexerare var 30: e minut anger du intervallet till "PT30M". Det kortaste intervall som stöds är fem minuter. Schemat är valfritt. om detta utelämnas körs en indexerare bara en gång när den skapas. Du kan dock köra en indexerare på begäran när du vill.   

Mer information om API för att skapa index finns i [skapa indexerare](/rest/api/searchservice/create-indexer).

Mer information om hur du definierar indexerare scheman finns i [så här schemalägger du indexerare för Azure kognitiv sökning](search-howto-schedule-indexers.md).

## <a name="deal-with-different-field-names"></a>Hantera olika fält namn
Ibland skiljer sig fält namnen i ditt befintliga index från egenskaps namnen i tabellen. Du kan använda fält mappningar för att mappa egenskaps namnen från tabellen till fält namnen i Sök indexet. Mer information om fält mappningar finns i [fält mappningar för Azure kognitiv sökning Indexer överbrygga skillnaderna mellan data källor och Sök index](search-indexer-field-mappings.md).

## <a name="handle-document-keys"></a>Hantera dokument nycklar
I Azure Kognitiv sökning identifierar dokument nyckeln ett dokument unikt. Varje Sök index måste ha exakt ett nyckel fält av typen `Edm.String` . Nyckel fältet krävs för varje dokument som läggs till i indexet. (I själva verket är det det enda obligatoriska fältet.)

Eftersom tabell rader har en sammansatt nyckel genererar Azure Kognitiv sökning ett syntetiskt fält `Key` med namnet som är en sammanfogning av partitionsnyckel och rad nyckel värden. Om t. ex. en rads PartitionKey är `PK1` och RowKey är `RK1` `Key` fältets värde `PK1RK1` .

> [!NOTE]
> `Key`Värdet kan innehålla ogiltiga tecken i dokument nycklar, till exempel bindestreck. Du kan hantera ogiltiga tecken med hjälp av `base64Encode` [fält mappnings funktionen](search-indexer-field-mappings.md#base64EncodeFunction). Om du gör detta bör du komma ihåg att även använda URL-säker Base64-kodning när du skickar dokumentnycklar i API-anrop såsom Lookup.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Identifiering och borttagning av stegvis indexering
När du ställer in en tabell Indexer så att den körs enligt ett schema, indexerar den bara nya eller uppdaterade rader, vilket avgörs av en rads `Timestamp` värde. Du behöver inte ange en princip för ändrings identifiering. Stegvis indexering aktive ras automatiskt.

Om du vill ange att vissa dokument måste tas bort från indexet kan du använda en strategi för mjuk borttagning. I stället för att ta bort en rad lägger du till en egenskap för att visa att den har tagits bort och konfigurerar en princip för att upptäcka en mjuk borttagning på data källan. Följande princip anser till exempel att en rad tas bort om raden har en egenskap `IsDeleted` med värdet `"true"` :

```http
    PUT https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "<query>" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   
```

<a name="Performance"></a>
## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

Som standard använder Azure Kognitiv sökning följande Frågefilter: `Timestamp >= HighWaterMarkValue` . Eftersom Azure-tabeller inte har något sekundärt index i `Timestamp` fältet, kräver den här typen av fråga en fullständig tabells ökning och är därför långsam för stora tabeller.


Här följer två olika metoder för att förbättra prestanda för tabell indexering. Båda dessa metoder förlitar sig på att använda Table-partitioner: 

- Om dina data naturligt kan partitioneras i flera partitions intervall skapar du en data källa och en motsvarande indexerare för varje partition intervall. Varje indexerare måste nu bearbeta ett särskilt partitionerings intervall, vilket resulterar i bättre prestanda för frågor. Om data som behöver indexeras har ett litet antal fasta partitioner, ännu bättre: varje indexerare gör en partitions ökning. Om du till exempel vill skapa en data källa för bearbetning av ett partition intervall med nycklar från `000` till `100` , använder du en fråga som detta: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Om dina data är partitionerade efter tid (till exempel när du skapar en ny partition varje dag eller vecka), bör du tänka på följande: 
    - Använd en fråga i formatet: `(PartitionKey ge <TimeStamp>) and (other filters)` . 
    - Övervaka indexerings förloppet med hjälp av API för att [Hämta indexerings status](/rest/api/searchservice/get-indexer-status)och uppdatera sedan regelbundet `<TimeStamp>` villkoret för frågan baserat på det senaste lyckade värdet med högt vatten märke. 
    - Med den här metoden måste du, om du behöver aktivera en fullständig Omindexering, återställa data källans fråga, förutom att återställa indexeraren. 


## <a name="help-us-make-azure-cognitive-search-better"></a>Hjälp oss att göra Azure Kognitiv sökning bättre
Om du har funktions förfrågningar eller idéer om förbättringar kan du skicka dem till vår [UserVoice-webbplats](https://feedback.azure.com/forums/263029-azure-search/).