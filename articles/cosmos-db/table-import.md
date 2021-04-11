---
title: Migrera befintliga data till ett Tabell-API konto i Azure Cosmos DB
description: Lär dig hur du migrerar eller importerar lokala eller molnbaserade data till ett Azure Tabell-API-konto i Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 77f4c928db695bd4193ad46c93e0efbd16decf29
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443363"
---
# <a name="migrate-your-data-to-an-azure-cosmos-db-table-api-account"></a>Migrera dina data till ett Azure Cosmos DB Tabell-API konto
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Den här självstudien innehåller instruktioner om hur du importerar data för användning med Azure Cosmos DB [tabell-API](table-introduction.md). Om du har data som lagras i Azure Table Storage kan du använda antingen verktyget datamigrering eller AzCopy för att importera data till Azure Cosmos DB Tabell-API. 

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Importera data med verktyget datamigrering
> * Importera data med AzCopy

## <a name="prerequisites"></a>Förutsättningar

* **Öka data flödet:** Varaktigheten för din datamigrering beror på mängden data flöde som du har konfigurerat för en enskild behållare eller en uppsättning behållare. Vi rekommenderar att du ökar dataflödet för större datamigreringar. När du har slutfört migreringen minskar du dataflödet för att spara kostnader.

* **Skapa Azure Cosmos DB-resurser:** Innan du börjar migrera data skapar du alla tabeller från Azure Portal. Om du migrerar till ett Azure Cosmos DB konto som har data flöde på databas nivå, måste du ange en partitionsnyckel när du skapar Azure Cosmos DB tabeller.

## <a name="data-migration-tool"></a>Migreringsverktyg för data

Du kan använda verktyget för migrering av data (dt.exe) i Azure Cosmos DB för att importera dina befintliga Azure Table Storage-data till ett Tabell-API-konto. 

Så här migrerar du tabell data:

1. Ladda ned migreringsverktyget från [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Kör `dt.exe` med kommando rads argument för ditt scenario. `dt.exe` stöder kommandon i följande format:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

Alternativen som stöds för det här kommandot är:

* **/Errorlog:** Valfritt. Namnet på CSV-filen för att dirigera om data överförings felen.
* **/OverwriteErrorLog:** Valfritt. Skriv över fel logg filen.
* **/ProgressUpdateInterval:** Valfritt, standard är `00:00:01` . Tidsintervallet för att uppdatera data överförings förloppet på skärmen.
* **/ErrorDetails:** Valfritt, standard är `None` . Anger att detaljerad fel information ska visas för följande fel: `None` , `Critical` , eller `All` .
* **/EnableCosmosTableLog:** Valfritt. Dirigera loggen till ett Azure Cosmos DB tabell konto. Om det är inställt anges standardvärdet för mål kontots anslutnings sträng, om det inte `/CosmosTableLogConnectionString` också anges. Detta är användbart om flera instanser av verktyget körs samtidigt.
* **/CosmosTableLogConnectionString:** Valfritt. Anslutnings strängen för att dirigera loggen till ett fjärrAzure Cosmos DBs tabell konto.

### <a name="command-line-source-settings"></a>Kommandoradsinställningar för källan

Använd följande käll alternativ när du definierar Azure Table Storage som källa för migreringen.

* **/s: AzureTable:** Läser data från Table Storage.
* **/s.ConnectionString:** Anslutnings sträng för tabell slut punkten. Du kan hämta detta från Azure Portal.
* **/s.LocationMode:** Valfritt, standard är `PrimaryOnly` . Anger vilket plats läge som ska användas vid anslutning till Table Storage: `PrimaryOnly` ,, `PrimaryThenSecondary` `SecondaryOnly` , `SecondaryThenPrimary` .
* **/s.table:** Namnet på Azure-tabellen.
* **/s.InternalFields:** Ange till `All` för tabell migrering, eftersom `RowKey` och `PartitionKey` krävs för import.
* **/s.filter:** Valfritt. Filter sträng som ska användas.
* **/s.Projection:** Valfritt. Lista med kolumner som ska väljas

Öppna Azure Portal om du vill hämta käll anslutnings strängen när du importerar från Table Storage. Välj **lagrings konton**  >  **konto**  >  **åtkomst nycklar** och kopiera **anslutnings strängen**.

:::image type="content" source="./media/table-import/storage-table-access-key.png" alt-text="Skärm bild som visar lagrings konton > konto > åtkomst nycklar alternativ och markerar kopiera-ikonen.":::

### <a name="command-line-target-settings"></a>Kommandoradsinställningar för målet

Använd följande mål alternativ när du definierar Azure Cosmos DB Tabell-API som målet för migreringen.

* **/t: TableAPIBulk:** Överför data till Azure Cosmos DB Tabell-API i batchar.
* **/t.ConnectionString:** Anslutnings strängen för tabell slut punkten.
* **/t.TableName:** Anger namnet på den tabell som ska skrivas till.
* **/t.overwrite:** Valfritt, standard är `false` . Anger om befintliga värden ska skrivas över.
* **/t.MaxInputBufferSize:** Valfritt, standard är `1GB` . Ungefärlig uppskattning av indata-byte till buffert innan data töms till Sink.
* **/t.Throughput:** Valfritt, service standardinställningar om inget anges. Anger data flöde som ska konfigureras för tabellen.
* **/t.MaxBatchSize:** Valfritt, standard är `2MB` . Ange batchstorleken i byte.

### <a name="sample-command-source-is-table-storage"></a>Exempel kommando: källan är Table Storage

Här är ett kommando rads exempel som visar hur du importerar från Table Storage till Tabell-API:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Migrera data med AzCopy

Du kan också använda kommando rads verktyget AzCopy för att migrera data från Table Storage till Azure Cosmos DB Tabell-API. Om du vill använda AzCopy exporterar du först dina data enligt beskrivningen i [Exportera data från Table Storage](/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage). Sedan importerar du data till Azure Cosmos DB enligt beskrivningen i [Azure Cosmos DB tabell-API](/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage).

Se följande exempel när du importerar till Azure Cosmos DB. Observera att `/Dest` värdet inte använder `cosmosdb` , inte `core` .

Exempel på importkommando:

```bash
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du frågar efter data med hjälp av Azure Cosmos DB Tabell-API. 

> [!div class="nextstepaction"]
>[Hur frågar jag efter data?](../cosmos-db/tutorial-query-table.md)




