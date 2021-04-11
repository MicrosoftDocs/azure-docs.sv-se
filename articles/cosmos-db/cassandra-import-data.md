---
title: Migrera data till ett API för Cassandra konto i Azure Cosmos DB – självstudier
description: I den här självstudien får du lära dig hur du kopierar data från Apache Cassandra till ett API för Cassandra konto i Azure Cosmos DB.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 7c55cbf4b8739a885c499c820a7e68825522ea4e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449298"
---
# <a name="tutorial-migrate-your-data-to-a-cassandra-api-account"></a>Självstudie: migrera dina data till ett API för Cassandra-konto
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Som utvecklare kan du ha befintliga Cassandra-arbetsbelastningar som körs lokalt eller i molnet och du kan vilja migrera dem till Azure. Du kan migrera sådana arbetsbelastningar till ett Cassandra-API-konto i Azure Cosmos DB. Den här självstudien ger instruktioner för olika alternativ som finns för att migrera Apache Cassandra-data till Cassandra-API-kontot i Azure Cosmos DB.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Planera för migrering
> * Förutsättningar för migrering
> * Migrera data med hjälp av `cqlsh` `COPY` kommandot
> * Migrera data med Spark

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites-for-migration"></a>Förutsättningar för migrering

* **Beräkna dina dataflödesbehov:** Innan du migrerar data till Cassandra-API-kontot i Azure Cosmos DB bör du beräkna dataflödesbehoven för din arbetsbelastning. I allmänhet börjar du med det genomsnittliga data flöde som krävs av CRUD-åtgärderna och lägger sedan till ytterligare data flöde som krävs för belastningen på extraherings omvandling eller toppar. Du behöver följande information för att planera för migreringen: 

  * **Befintlig datastorlek eller beräknad datastorlek:** Definierar den minsta databasstorleken och det minsta dataflödet som krävs. Om du uppskattar data storleken för ett nytt program kan du förutsätta att data är enhetligt fördelade på raderna och beräkna värdet genom att multiplicera med data storleken. 

  * **Nödvändigt data flöde:** Ungefärlig data flödes frekvens för Läs åtgärder (fråga/Hämta) och skriv (uppdatera/ta bort/infoga). Det här värdet krävs för att beräkna nödvändiga enheter för begäran, tillsammans med data storlek med stabil nivå.  

  * **Schemat:** Anslut till ditt befintliga Cassandra-kluster via `cqlsh` och exportera schemat från Cassandra: 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    När du har identifierat kraven för din befintliga arbets belastning skapar du ett Azure Cosmos DB konto, en databas och behållare enligt de insamlade data flödes kraven.  

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

* **Skapa tabeller i API för Cassandra-kontot:** Innan du börjar migrera data ska du skapa alla dina tabeller i förväg från Azure Portal eller från `cqlsh` . Om du migrerar till ett Azure Cosmos DB konto som har data flöde på databas nivå, måste du ange en partitionsnyckel när du skapar behållarna.

* **Öka dataflödet:** Hur lång tid datamigreringen tar beror på hur stort dataflöde du etablerade för tabellerna i Azure Cosmos DB. Öka dataflödet under migreringen. Med ett högre dataflöde kan du undvika begränsningar och migrera snabbare. När du har slutfört migreringen minskar du dataflödet för att spara kostnader. Vi rekommenderar också att du har Azure Cosmos DB konto i samma region som din käll databas. 

* **Aktivera TLS:** Azure Cosmos DB har strikta säkerhets krav och standarder. Se till att aktivera TLS när du interagerar med ditt konto. När du använder CQL med SSH har du ett alternativ för att tillhandahålla TLS-information.

## <a name="options-to-migrate-data"></a>Alternativ för att migrera data

Du kan flytta data från befintliga Cassandra-arbetsbelastningar till Azure Cosmos DB med hjälp av `cqlsh` `COPY` kommandot eller genom att använda Spark. 

### <a name="migrate-data-by-using-the-cqlsh-copy-command"></a>Migrera data med hjälp av kommandot cqlsh COPY

Använd [CQL Copy-kommandot](https://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) för att kopiera lokala data till API för Cassandra kontot i Azure Cosmos dB.

1. Hämta information om anslutningssträngen för Cassandra-API:ets konto:

   * Logga in på [Azure Portal](https://portal.azure.com)och gå till ditt Azure Cosmos DB-konto.

   * Öppna fönstret **anslutnings sträng** . Här ser du all information som du behöver för att ansluta till ditt API för Cassandra-konto `cqlsh` .

1. Logga in på `cqlsh` med anslutnings informationen från portalen.

1. Använd `CQL` `COPY` kommandot för att kopiera lokala data till API för Cassandra-kontot.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

### <a name="migrate-data-by-using-spark"></a>Migrera data med Spark 

Använd följande steg för att migrera data till Cassandra-API-kontot med Spark:

1. Etablera ett [Azure Databricks kluster](cassandra-spark-databricks.md) eller ett [Azure HDInsight-kluster](cassandra-spark-hdinsight.md). 

1. Flytta data till mål API för Cassandra slut punkten med hjälp av [tabell kopierings åtgärden](cassandra-spark-table-copy-ops.md). 

Vi rekommenderar att du migrerar data med hjälp av Spark-jobb om du har data som finns i ett befintligt kluster på virtuella Azure-datorer eller i ett annat moln. För att göra detta måste du ställa in Spark som en mellanhand vid en eller vanlig inmatning. Du kan påskynda migreringen genom att använda Azure ExpressRoute-anslutning mellan din lokala miljö och Azure. 

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort resurs gruppen, Azure Cosmos DB kontot och alla relaterade resurser. Om du vill göra detta väljer du resursgruppen för den virtuella datorn. Välj sedan **Ta bort** och kontrollera namnet på resursgruppen som du vill ta bort.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du migrerar dina data till ett API för Cassandra konto i Azure Cosmos DB. Nu kan du lära dig mer om andra koncept i Azure Cosmos DB:

> [!div class="nextstepaction"]
> [Justerbara datakonsekvensnivåer i Azure Cosmos DB](../cosmos-db/consistency-levels.md)




