---
title: Migrera befintliga data till ett tabell-API-konto i Azure Cosmos DB
description: Lär dig hur du migrerar eller importerar lokala eller molnbaserade data till Azure Tabell-API-kontot i Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: e876ca028532bb3721146e90a91d68c4c12bf79f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93096089"
---
# <a name="migrate-your-data-to-azure-cosmos-db-table-api-account"></a>Migrera data till Azure Cosmos DB Table-API-konto
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Den här självstudien innehåller instruktioner om hur du importerar data för användning med Azure Cosmos DB [tabell-API](table-introduction.md). Om du har lagrade data i Azure Table Storage kan du använda datamigreringsverktyget eller AzCopy för att importera dina data till Azure Cosmos DB Table-API:t. Om du har data som lagras i ett Azure Cosmos DB Table API-konto (förhandsversion) måste du använda datamigreringsverktyget för att migrera data. 

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Importera data med datamigreringsverktyget
> * Importera data med AzCopy
> * Migrera från Table API (förhandsversion) till Table API 

## <a name="prerequisites"></a>Förutsättningar

* **Öka data flödet:** Varaktigheten för din datamigrering beror på mängden data flöde som du har konfigurerat för en enskild behållare eller en uppsättning behållare. Vi rekommenderar att du ökar dataflödet för större datamigreringar. När du har slutfört migreringen minskar du dataflödet för att spara kostnader. Mer information om hur du ökar dataflödet i Azure Portal finns i avsnittet om prestandanivåer och prisnivåer i Azure Cosmos DB.

* **Skapa Azure Cosmos DB-resurser:** Innan du börjar migrera data skapar du alla dina tabeller i förväg från Azure-portalen. Om du migrerar till ett Azure Cosmos DB-konto som har dataflöde på databasnivå anger du en partitionsnyckel när du skapar Azure Cosmos DB-tabellerna.

## <a name="data-migration-tool"></a>Datamigreringsverktyget

Du kan använda det kommandoradsbaserade datamigreringsverktyget för Azure Cosmos DB (dt.exe) för att importera befintliga Azure Table Storage-data till ett Table API GA-konto, eller för att migrera data från ett Table API-konto (förhandsversion) till ett Table API GA-konto. Andra källor stöds inte för närvarande. Det går för närvarande inte att använda det användargränssnittsbaserade datamigreringsverktyget (dtui.exe) med Table API-konton. 

Utför följande åtgärder om du vill migrera tabelldata:

1. Ladda ned migreringsverktyget från [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Kör `dt.exe` med kommandoradsargumenten för ditt scenario. `dt.exe` stöder kommandon i följande format:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

Alternativen som stöds för det här kommandot är:

* **/Errorlog:** Valfritt. Namnet på CSV-filen för att dirigera om data överförings problem
* **/OverwriteErrorLog:** Valfritt. Skriv över fel logg filen
* **/ProgressUpdateInterval:** Valfritt, standard är 00:00:01. Tidsintervall för uppdatering av data överförings förlopp på skärmen
* **/ErrorDetails:** Valfritt, standard är ingen. Anger att detaljerad fel information ska visas för följande fel: ingen, kritiskt, alla
* **/EnableCosmosTableLog:** Valfritt. Dirigera loggen till ett Cosmos-tabell konto. Om det här alternativet är inställt, är detta standardvärdet för anslutnings strängen för mål kontot om inte/CosmosTableLogConnectionString också Detta är användbart om flera instanser av DT körs samtidigt.
* **/CosmosTableLogConnectionString:** Valfritt. ConnectionString för att dirigera loggen till ett fjärran sluten Cosmos-tabell konto.

### <a name="command-line-source-settings"></a>Kommandoradsinställningar för källan

Använd följande alternativ för källan när du definierar Azure Table Storage eller Table API (förhandsversion) som källan för migreringen.

* **/s: AzureTable:** Läser data från Azure Table Storage
* **/s.ConnectionString:** Anslutnings sträng för tabell slut punkten. Detta kan hämtas från Azure Portal
* **/s.LocationMode:** Valfritt, standard är PrimaryOnly. Anger vilket plats läge som ska användas vid anslutning till Azure Table Storage: PrimaryOnly, PrimaryThenSecondary, SecondaryOnly, SecondaryThenPrimary
* **/s.table:** Namn på Azure-tabellen
* **/s.InternalFields:** Ange som RowKey och PartitionKey krävs för att importera.
* **/s.filter:** Valfritt. Filter sträng som ska användas
* **/s.Projection:** Valfritt. Lista med kolumner som ska väljas

Om du vill hämta käll anslutnings strängen när du importerar från Azure Table Storage öppnar du Azure Portal och klickar på **lagrings konton**  >  **konto**  >  **åtkomst nycklar** och använder sedan kopierings knappen för att kopiera **anslutnings strängen**.

:::image type="content" source="./media/table-import/storage-table-access-key.png" alt-text="Skärm bild som visar alternativ för lagrings konton > konto > åtkomst nycklar och markerar knappen Kopiera.":::

Om du vill hämta käll anslutnings strängen när du importerar från ett Azure Cosmos DB tabell-API (förhands granskning), öppnar du Azure Portal, klickar på **Azure Cosmos DB**  >  **kontots**  >  **anslutnings sträng** och använder kopierings knappen för att kopiera **anslutnings strängen**.

:::image type="content" source="./media/table-import/cosmos-connection-string.png" alt-text="Skärmbild över alternativ för HBase-källor":::

[Azure Table Storage-exempelkommando](#azure-table-storage)

[Azure Cosmos DB Table API-exempelkommando (förhandsversion)](#table-api-preview)

### <a name="command-line-target-settings"></a>Kommandoradsinställningar för målet

Använd följande alternativ för målet när du definierar Azure Cosmos DB Table-API:et som målet för migreringen.

* **/t: TableAPIBulk:** Överför data till Azure dataCosmosDBs-tabellen i batchar
* **/t.ConnectionString:** Anslutnings sträng för tabell slut punkten
* **/t.TableName:** Anger namnet på den tabell som ska skrivas till
* **/t.overwrite:** Valfritt, standardvärdet är false. Anger om befintliga värden ska skrivas över
* **/t.MaxInputBufferSize:** Valfritt är standard 1 GB. Ungefärlig uppskattning av inkommande byte till buffert innan data töms till Sink
* **/t.Throughput:** Valfritt, service standardinställningar om inget anges. Anger data flöde som ska konfigureras för tabellen
* **/t.MaxBatchSize:** Valfritt, standard är 2 MB. Ange batchstorleken i byte

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>Exempelkommando: Källan är Azure Table Storage

Här är ett kommandoradsexempel som visar hur du importerar från Azure Table Storage till Table API:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>Exempelkommando: Källan är Azure Cosmos DB Table API (förhandsversion)

Här är ett kommandoradsexempel som visar hur du importerar från Table API (förhandsversion) till Table API GA:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Migrera data med AzCopy

Det andra alternativet som du kan använda när du migrerar data från Azure Table Storage till Azure Cosmos DB Table API är kommandoradsverktyget AzCopy. Om du vill använda AzCopy börjar du med att exportera dina data genom att följa anvisningarna i [Exportera data från Table Storage](/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage) och importerar dem sedan till Azure Cosmos DB genom att följa anvisningarna i [Azure Cosmos DB Table API](/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage).

Använd följande exempel som referens när du importerar till Azure Cosmos DB. Observera att /Dest-värdet använder cosmosdb, inte core.

Exempel på importkommando:

```bash
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>Migrera från Table API (förhandsversion) till Table API

> [!WARNING]
> Om du genast vill dra nytta av fördelarna med allmänt tillgängliga tabeller i GA-versionen migrerar du dina befintliga tabeller i förhandsversionen genom att följa anvisningarna i det här avsnittet. Annars kommer vi att genomföra en automatisk migrering av befintliga kunder med förhandsversionen under de kommande veckorna. Observera att tabeller i förhandsversionen har vissa begränsningar som nyligen skapade tabeller inte har.

Nu är Table API allmänt tillgängligt (GA). Det finns skillnader mellan förhandsversionen och GA-versionen för tabeller både i koden som körs i molnet och i koden som körs på klienten. Därför avråder vi dig från att försöka kombinera en SDK-klient som använder förhandsversionen med ett Table API-konto i GA-versionen och tvärtom. Kunder med förhandsversionen av Table API som vill fortsätta att använda sina befintliga tabeller men i en produktionsmiljö, måste migrera från förhandsversionen till GA-miljön eller vänta på den automatiska migreringen. Om du väljer att skjuta upp migreringen till den automatiska migreringen kommer du att informeras om begränsningarna i migrerade tabeller. Efter migreringen kommer du att kunna skapa nya tabeller i ditt befintliga konto utan begränsningar (endast migrerade tabeller har begränsningar).

Så här migrerar du från förhandsversionen av Table API till GA-versionen:

1. Skapa ett nytt Azure Cosmos DB-konto och ange kontots API-typ till Azure Table genom att följa anvisningarna i [Skapa ett databaskonto](create-table-dotnet.md#create-a-database-account).

2. Ändra klienterna så att de använder en GA-version (allmänt tillgänglig) av [Table API-SDK:erna](table-sdk-dotnet.md).

3. Migrera klientdata från tabeller i förhandsversionen till GA-tabeller med hjälp av datamigreringsverktyget. Anvisningar för hur du använder datamigreringsverktyget i detta syfte beskrivs i avsnittet om [datamigreringsverktyget](#data-migration-tool). 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Importera data med datamigreringsverktyget
> * Importera data med AzCopy
> * Migrera från Table API (förhandsversion) till Table API

Nu kan du fortsätta med nästa självstudiekurs där du lär dig hur du kör frågor mot data med hjälp av Azure Cosmos DB Table-API:et. 

> [!div class="nextstepaction"]
>[Hur frågar jag efter data?](../cosmos-db/tutorial-query-table.md)