---
title: Konfigurera cache och stegvis anrikning (för hands version)
titleSuffix: Azure Cognitive Search
description: Aktivera cachelagring och bevara tillstånd för berikat innehåll för kontrollerad bearbetning i en kognitiv färdigheter. Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: a1b317b651b0e17c07eb17dbdb8a7c6657d39564
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90971613"
---
# <a name="how-to-configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>Konfigurera cachelagring för stegvis berikning i Azure Kognitiv sökning

> [!IMPORTANT] 
> Stegvis anrikning är för närvarande en offentlig för hands version. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
> [REST API för hands versioner](search-api-preview.md) innehåller den här funktionen. Det finns för närvarande ingen portal eller .NET SDK-support.

Den här artikeln visar hur du lägger till cachelagring i en pipeline-pipeline så att du stegvis kan ändra stegen utan att behöva återskapa varje gång. Som standard är en färdigheter tillstånds lös, och om du ändrar någon del av dess sammansättning krävs en fullständig körning av indexeraren. Med stegvis anrikning kan indexeraren avgöra vilka delar av dokument trädet som behöver uppdateras baserat på ändringar som upptäckts i färdigheter eller Indexer definitioner. Befintliga bearbetade utdata bevaras och återanvänds när det är möjligt. 

Cachelagrat innehåll placeras i Azure Storage med hjälp av konto information som du anger. Behållaren, som heter `ms-az-search-indexercache-<alpha-numerc-string>` , skapas när du kör indexeraren. Den bör betraktas som en intern komponent som hanteras av Sök tjänsten och får inte ändras.

Om du inte är bekant med att konfigurera indexerare börjar du med [indexerare översikt](search-indexer-overview.md) och fortsätter sedan till [färdighetsuppsättningar](cognitive-search-working-with-skillsets.md) för att lära dig om anriknings pipelines. Mer bakgrunds information om viktiga begrepp finns i [stegvis berikning](cognitive-search-incremental-indexing-conceptual.md).

## <a name="enable-caching-on-an-existing-indexer"></a>Aktivera cachelagring på en befintlig indexerare

Om du har en befintlig indexerare som redan har en färdigheter följer du stegen i det här avsnittet för att lägga till cachelagring. Som en engångs åtgärd måste du återställa och köra om indexeraren i fullständig innan stegvis bearbetning kan börja gälla.

> [!TIP]
> Som proof-of-Concept kan du köra genom den här [snabb](cognitive-search-quickstart-blob.md) starten för portalen för att skapa nödvändiga objekt och sedan använda Postman eller portalen för att göra dina uppdateringar. Du kanske vill koppla en fakturerbar Cognitive Services-resurs. Att köra indexeraren flera gånger kommer att belasta den kostnads fria dagliga fördelningen innan du kan slutföra alla steg.

### <a name="step-1-get-the-indexer-definition"></a>Steg 1: Hämta indexare definition

Börja med en giltig, befintlig indexerare som har dessa komponenter: data källa, färdigheter, index. Indexeraren ska vara körbara. 

Med en API-klient skapar du en [Get Indexer-begäran](/rest/api/searchservice/get-indexer) för att hämta den aktuella konfigurationen av indexeraren. När du använder för hands versionen av API-versionen för att hämta indexeraren `cache` läggs en egenskap som är inställd på null till i definitionerna.

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Kopiera Indexer definitionen från svaret.

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>Steg 2: ändra egenskapen cache i index definitions definitionen

Som standard `cache` är egenskapen NULL. Använd en API-klient för att ange cache-konfigurationen (portalen stöder inte den här partikel uppdateringen). 

Ändra cache-objektet så att det innehåller följande obligatoriska och valfria egenskaper: 

+ `storageConnectionString`Är obligatoriskt och måste vara inställt på en anslutnings sträng för Azure Storage. 
+ Den `enableReprocessing` booleska egenskapen är valfri ( `true` som standard) och anger att den stegvisa berikningen är aktive rad. Vid behov kan du ställa in det för `false` att pausa stegvis bearbetning medan andra resurs intensiva åtgärder, till exempel indexera nya dokument, pågår och sedan vända tillbaka till `true` senare.

```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

### <a name="step-3-reset-the-indexer"></a>Steg 3: återställa indexeraren

En återställning av indexeraren krävs när du konfigurerar stegvis berikning för befintliga indexerare så att alla dokument är i ett konsekvent tillstånd. Du kan använda portalen eller en API-klient och [REST API Återställ indexerare](/rest/api/searchservice/reset-indexer) för den här aktiviteten.

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>Steg 4: Spara den uppdaterade definitionen

[Uppdatera indexeraren](/rest/api/searchservice/preview-api/update-indexer) med en Request-begäran ska bröd texten i begäran innehålla den uppdaterade index definitions definitionen som har egenskapen cache. Om du får en 400 kontrollerar du indexerings definitionen för att se till att alla krav är uppfyllda (data källa, färdigheter, index).

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
{
    "name" : "<YOUR-INDEXER-NAME>",
    ...
    "cache": {
        "storageConnectionString": "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    }
}
```

Om du nu utfärdar en annan GET-begäran på indexeraren, kommer svaret från tjänsten att innehålla en `ID` egenskap i objektet cache. Den alfanumeriska strängen läggs till i namnet på behållaren som innehåller alla cachelagrade resultat och mellanliggande tillstånd för varje dokument som bearbetas av denna indexerare. ID: t används för att unikt namnge cachen i Blob Storage.

```http
    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }
```

### <a name="step-5-run-the-indexer"></a>Steg 5: köra indexeraren

Om du vill köra indexerare kan du använda portalen eller API: et. I portalen går du till listan Indexer och väljer indexeraren och klickar på **Kör**. En fördel med att använda portalen är att du kan övervaka status för indexerare, anteckna varaktigheten för jobbet och hur många dokument som bearbetas. Portal sidor uppdateras en gång i minuten.

Du kan också använda REST för att [köra indexeraren](/rest/api/searchservice/run-indexer):

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

När indexeraren har körts kan du hitta cachen i Azure Blob Storage. Behållar namnet har följande format: `ms-az-search-indexercache-<YOUR-CACHE-ID>`

> [!NOTE]
> En återställning och körning av indexeraren resulterar i en fullständig återskapning så att innehållet kan cachelagras. Alla kognitiva anrikninger kommer att köras igen på alla dokument.
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>Steg 6: ändra en färdigheter och bekräfta stegvis anrikning

Om du vill ändra en färdigheter kan du använda portalen eller API: et. Om du till exempel använder text översättning räcker det med en enkel infogad ändring från `en` till `es` eller något annat språk för koncept bevis testning av stegvis anrikning.

Kör indexeraren igen. Endast de delar av ett fördjupat dokument träd uppdateras. Om du har använt [portalen för snabb start](cognitive-search-quickstart-blob.md) som POC, ändrar text översättning till "es", ser du att endast åtta dokument uppdateras i stället för de ursprungliga 14. Bildfiler som inte påverkas av översättnings processen återanvänds från cachen.

## <a name="enable-caching-on-new-indexers"></a>Aktivera cachelagring på nya indexerare

Allt du behöver göra är att ta med den stegvisa berikningen för en ny indexerare. det är allt du behöver göra är att ta med `cache` egenskapen i definitions nytto lasten indexerare vid anrop av [create Indexer (2020-06-30-för hands version)](/rest/api/searchservice/preview-api/create-indexer). 


```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
    }
}
```

## <a name="checking-for-cached-output"></a>Söker efter cachelagrade utdata

Cachen skapas, används och hanteras av indexeraren och dess innehåll visas inte i ett format som är läsligt. Det bästa sättet att avgöra om cacheminnet används är genom att köra indexeraren och jämföra före-och-efter-statistik för körnings tid och dokument antal. 

Anta till exempel att du använder en färdigheter som börjar med bild analys och optisk tecken igenkänning (OCR) av skannade dokument, följt av underordnad analys av den resulterande texten. Om du ändrar en underordnad text-färdighet kan indexeraren hämta alla tidigare bearbetade bilder och OCR-innehåll från cache, uppdatera och bearbeta enbart textrelaterade ändringar som anges i dina redigeringar. Du kan vänta på att se färre dokument i antalet dokument (till exempel 8/8 i stället för 14/14 i den ursprungliga körningen), kortare körnings tider och färre avgifter på din faktura.

## <a name="working-with-the-cache"></a>Arbeta med cachen

När cachen är i drift kontrollerar indexerarna cachen när [Kör indexerare](/rest/api/searchservice/run-indexer) anropas för att se vilka delar av befintliga utdata som kan användas. 

I följande tabell sammanfattas hur olika API: er relaterar till cachen:

| API           | Inverkan på cache     |
|---------------|------------------|
| [Skapa indexerare (2020-06-30-för hands version)](/rest/api/searchservice/preview-api/create-indexer) | Skapar och kör en indexerare vid första användningen, inklusive att skapa en cache om din Indexer-definition anger den. |
| [Köra indexerare](/rest/api/searchservice/run-indexer) | Kör en anriknings pipeline på begäran. Detta API läser från cachen om det finns eller skapar en cache om du har lagt till cachelagring i en uppdaterad index definition. När du kör en indexerare som har cachelagring aktiverat utelämnar indexeraren steg om cachelagrade utdata kan användas. Du kan använda den allmänt tillgängliga eller för hands versionen av API-versionen av detta API.|
| [Återställ indexerare](/rest/api/searchservice/reset-indexer)| Rensar indexeraren för all stegvis indexerings information. Nästa indexerare körs (antingen på begäran eller schema) är fullständig ombearbetning från grunden, inklusive att köra om alla kunskaper och återskapa cacheminnet. Den fungerar som likvärdig för att ta bort indexeraren och återskapa den. Du kan använda den allmänt tillgängliga eller för hands versionen av API-versionen av detta API.|
| [Återställ kunskaper](/rest/api/searchservice/preview-api/reset-skills) | Anger vilka kunskaper som ska köras igen vid nästa indexerare, även om du inte har ändrat några kunskaper. Cachen uppdateras i enlighet med detta. Utdata, t. ex. ett kunskaps lager eller sökindex, uppdateras med återanvändbara data från cachen plus nytt innehåll per uppdaterad kunskap. |

Mer information om hur du styr vad som händer med cachen finns i [Cache Management](cognitive-search-incremental-indexing-conceptual.md#cache-management).

## <a name="next-steps"></a>Nästa steg

Stegvis anrikning är tillämpligt på indexerare som innehåller färdighetsuppsättningar. I nästa steg ska du gå till färdigheter-dokumentationen för att förstå begrepp och sammansättning. 

När du har aktiverat cachen vill du dessutom veta mer om de parametrar och API: er som används för cachelagring, inklusive hur du åsidosätter eller tvingar fram särskilda beteenden. Mer information finns i följande länkar.

+ [Färdigheter koncept och sammansättning](cognitive-search-working-with-skillsets.md)
+ [Så här skapar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Introduktion till stegvis anrikning och cachelagring](cognitive-search-incremental-indexing-conceptual.md)