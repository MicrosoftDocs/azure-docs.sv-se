---
title: Migrera data till ett API för Cassandra konto i Azure Cosmos DB – självstudier
description: I den här självstudien får du lära dig hur du använder CQL Copy-kommandot & Spark för att kopiera data från Apache Cassandra till ett API för Cassandra konto i Azure Cosmos DB
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: bd2d27addb6860e49ac12eb36d8b625b8bf92001
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93100413"
---
# <a name="tutorial-migrate-your-data-to-cassandra-api-account-in-azure-cosmos-db"></a>Självstudie: Migrera data till ett Cassandra API-konto i Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Som utvecklare kan du ha befintliga Cassandra-arbetsbelastningar som körs lokalt eller i molnet och du kan vilja migrera dem till Azure. Du kan migrera sådana arbetsbelastningar till ett Cassandra-API-konto i Azure Cosmos DB. Den här självstudien ger instruktioner för olika alternativ som finns för att migrera Apache Cassandra-data till Cassandra-API-kontot i Azure Cosmos DB.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Planera för migrering
> * Förutsättningar för migrering
> * Migrera data med hjälp av COPY-kommandot i cqlsh
> * Migrera data med Spark

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites-for-migration"></a>Förutsättningar för migrering

* **Beräkna dina dataflödesbehov:** Innan du migrerar data till Cassandra-API-kontot i Azure Cosmos DB bör du beräkna dataflödesbehoven för din arbetsbelastning. I allmänhet rekommenderar vi att du börjar med ett genomsnittligt dataflöde som krävs av CRUD-åtgärderna och att du sedan lägger till det ytterligare dataflödet som krävs för ETL-åtgärder (Extract Transform Load) eller vid belastningstoppar. Du behöver följande information för att planera för migreringen: 

  * **Befintlig datastorlek eller beräknad datastorlek:** Definierar den minsta databasstorleken och det minsta dataflödet som krävs. Om du beräknar datastorleken för ett nytt program kan du anta att data är jämnt fördelade över raderna och beräkna värdet genom att multiplicera med datastorleken. 

  * **Nödvändigt dataflöde:** Ungefärlig dataflödeshastighet för läsningar (fråga/hämta) och skrivningar (uppdatera/ta bort/infoga). Det här värdet krävs för att beräkna de nödvändiga enheterna för programbegäran tillsammans med datastorleken för stabilt tillstånd.  

  * **Schemat:** Anslut till ditt befintliga Cassandra-kluster via cqlsh och exportera schemat från Cassandra: 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    När du har identifierat kraven för din befintliga arbetsbelastning bör du skapa ett konto, en databas och containrar för Azure Cosmos baserat på dataflödesbehovet.  

  * **Fastställ RU-avgiften för en åtgärd:** Du kan fastställa RU med hjälp av SDK:erna som stöds av Cassandra-API. I det här exemplet används .NET-versionen för att hämta RU-kostnaden.

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
      {
         byte[] valueInBytes = customPayload[key];
         double value = Encoding.UTF8.GetString(valueInBytes);
         Console.WriteLine($"CustomPayload:  {key}: {value}");
      }
    ```

* **Allokera det nödvändiga dataflödet:** Azure Cosmos DB kan automatiskt skala lagringen och dataflödet allteftersom dina behov ökar. Du kan beräkna dataflödesbehovet med hjälp av [RU-kalkylatorn för Azure Cosmos DB](https://www.documentdb.com/capacityplanner). 

* **Skapa tabeller i kontot för Cassandra-API:t:** Innan du börjar migrera data skapar du alla dina tabeller i förväg från Azure-portalen eller från cqlsh. Om du migrerar till ett Azure Cosmos-konto som har dataflöde på databasnivå ser du till att tillhandahålla en partitionsnyckel när du skapar Azure Cosmos-containrar.

* **Öka dataflödet:** Hur lång tid datamigreringen tar beror på hur stort dataflöde du etablerade för tabellerna i Azure Cosmos DB. Öka dataflödet under migreringen. Med ett högre dataflöde kan du undvika begränsningar och migrera snabbare. När du har slutfört migreringen minskar du dataflödet för att spara kostnader. Vi rekommenderar också att du har ett Azure Cosmos-konto i samma region som din källdatabas. 

* **Aktivera TLS:** Azure Cosmos DB har strikta säkerhets krav och standarder. Se till att aktivera TLS när du interagerar med ditt konto. När du använder CQL med SSH har du ett alternativ för att tillhandahålla TLS-information.

## <a name="options-to-migrate-data"></a>Alternativ för att migrera data

Du kan flytta data från befintliga Cassandra-arbetsbelastningar till Azure Cosmos DB med hjälp av följande alternativ:

* [COPY-kommandot i Cqlsh](#migrate-data-using-cqlsh-copy-command)  
* [Med hjälp av Spark](#migrate-data-using-spark) 

## <a name="migrate-data-using-cqlsh-copy-command"></a>Migrera data med hjälp av COPY-kommandot i cqlsh

[COPY-kommandot i CQL](https://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) används för att kopiera lokala data till Cassandra-API-kontot i Azure Cosmos DB. Använd följande steg för att kopiera data:

1. Hämta information om anslutningssträngen för Cassandra-API:ets konto:

   * Logga in på [Azure-portalen](https://portal.azure.com) och navigera till ditt Azure Cosmos-konto.

   * Öppna fönstret **Anslutningssträng** som innehåller all information som du behöver för att ansluta till kontot för Cassandra-API:et från cqlsh.

2. Logga in i cqhsh med hjälp av informationen från portalen.

3. Använd COPY-kommandot i CQL för att kopiera lokala data till kontot för Cassandra-API:et.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-using-spark"></a>Migrera data med Spark 

Använd följande steg för att migrera data till Cassandra-API-kontot med Spark:

- Etablera ett [Azure Databricks-kluster](cassandra-spark-databricks.md) eller ett [HDInsight-kluster](cassandra-spark-hdinsight.md) 

- Flytta data till målslutpunkten för Cassandra-API:t med hjälp av [tabellkopieringsåtgärden](cassandra-spark-table-copy-ops.md) 

Vi rekommenderar att du migrerar data med hjälp av Spark-jobb om du har data som finns i ett befintligt kluster på virtuella Azure-datorer eller i ett annat moln. Det här alternativet kräver att Spark konfigureras som mellanhand för en engångsinmatning eller för regelbunden datainmatning. Du kan påskynda migreringen med hjälp av Azure ExpressRoute-anslutningar mellan den lokala infrastrukturen och Azure. 

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, Azure Cosmos-kontot och alla relaterade resurser när de inte längre behövs. Om du vill göra detta väljer du resursgruppen för den virtuella datorn. Välj sedan **Ta bort** och kontrollera namnet på resursgruppen som du vill ta bort.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du migrerar data till ett Cassandra-API-konto i Azure Cosmos DB. Du kan fortsätta till följande artikel för att få information om andra Azure Cosmos DB-begrepp:

> [!div class="nextstepaction"]
> [Justerbara datakonsekvensnivåer i Azure Cosmos DB](../cosmos-db/consistency-levels.md)


