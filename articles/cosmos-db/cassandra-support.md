---
title: Apache Cassandra-funktioner som stöds av Azure Cosmos DB Cassandra-API
description: Lär dig om stöd för Apache Cassandra-funktionen i Azure Cosmos DB Cassandra-API
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: f48935edd5746b0f3f3440e249024cbe2901317b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374379"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Apache Cassandra-funktioner som stöds av Azure Cosmos DB Cassandra-API 
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan kommunicera med Azure Cosmos DB API för Cassandra via CQL Binary Protocol v4 [Wire Protocol](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) -kompatibla klient [driv rutiner](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver)för Cassandra med öppen källkod. 

Med hjälp av Azure Cosmos DB Cassandra-API kan du utnyttja fördelarna med Apache Cassandra-API:er samt företagsfunktioner som tillhandahåller Azure Cosmos DB. Företagsfunktionerna är [global distribution](distribute-data-globally.md), [automatisk utskalning av partitionering](cassandra-partitioning.md), garantier för tillgänglighet och svarstid, vilande kryptering, säkerhetskopior och mycket mer.

## <a name="cassandra-protocol"></a>Cassandra-protokollet 

Den Azure Cosmos DB API för Cassandra är kompatibel med Cassandra-CQL () v 3.11 (bakåtkompatibelt med version 2. x). De CQL-kommandon, verktyg, begränsningar och undantag som stöds anges nedan. Klientdrivrutiner som förstår dessa protokoll bör kunna ansluta till Cosmos DB med Azure Cosmos DB Cassandra-API:et.

## <a name="cassandra-driver"></a>Cassandra-drivrutinen

Följande versioner av Cassandra-drivrutiner stöds av Azure Cosmos DB Cassandra-API:et:

* [Java 3.5+](https://github.com/datastax/java-driver)  
* [C# 3.5+](https://github.com/datastax/csharp-driver)  
* [Nodejs 3.5+](https://github.com/datastax/nodejs-driver)  
* [Python 3.15+](https://github.com/datastax/python-driver)  
* [C++ 2.9](https://github.com/datastax/cpp-driver)   
* [PHP 1.3](https://github.com/datastax/php-driver)  
* [Gocql](https://github.com/gocql/gocql)  
 

## <a name="cql-data-types"></a>CQL-datatyper 

Azure Cosmos DB Cassandra-API:et stöder följande CQL-datatyper:

|Typ  |Stöds |
|---------|---------|
| ascii  | Ja |
| bigint  | Ja |
| blob  | Ja |
| boolean  | Ja |
| räknare  | Ja |
| date  | Ja |
| decimal  | Ja |
| double  | Ja |
| flyt  | Ja |
| frusen  | Ja |
| inet  | Ja |
| int  | Ja |
| lista  | Ja |
| set  | Ja |
| smallint  | Ja |
| text  | Ja |
| time  | Ja |
| timestamp  | Ja |
| timeuuid  | Ja |
| tinyint  | Ja |
| tuppel  | Ja |
| uuid  | Ja |
| varchar  | Ja |
| varint  | Ja |
| tupplar | Ja | 
| udts  | Ja |
| map | Ja |

Statiskt stöds för data typs deklaration.

## <a name="cql-functions"></a>CQL-funktioner

Azure Cosmos DB Cassandra-API:et stöder följande CQL-funktioner:

|Kommando  |Stöds |
|---------|---------|
| Åtkomsttokenbegäran | Ja |
| TTL * * * | Ja |
| writetime *** | Ja |
| Cast * * | Ja |

> [!NOTE] 
> \* API för Cassandra stöder token som en projektion/väljare och tillåter bara token (PK) till vänster i en WHERE-sats. Stöds till exempel `WHERE token(pk) > 1024` , men `WHERE token(pk) > token(100)` stöds **inte** .  
> \*\*`cast()`Funktionen kan inte kapslas i API för Cassandra. Stöds till exempel `SELECT cast(count as double) FROM myTable` , men `SELECT avg(cast(count as double)) FROM myTable` stöds **inte** .    
> \*\*\* Anpassade tidsstämplar och TTL som anges med `USING` alternativet appliceras på en rad nivå (och inte per cell).



Mängd funktioner:

|Kommando  |Stöds |
|---------|---------|
| Gmsn | Ja |
| count | Ja |
| min | Ja |
| max | Ja |
| fordra | Ja |

> [!NOTE]
> Mängd funktioner fungerar på vanliga kolumner, men agg regeringar i kluster kolumner stöds **inte** .


BLOB-konverterings funktioner:
 
|Kommando  |Stöds |
|---------|---------|
| typeAsBlob(value)   | Ja |
| blobAsType(value) | Ja |


UUID-och timeuuid-funktioner:
 
|Kommando  |Stöds |
|---------|---------|
| dateOf()  | Ja |
| now()  | Ja |
| minTimeuuid()  | Ja |
| unixTimestampOf()  | Ja |
| toDate(timeuuid)  | Ja |
| toTimestamp(timeuuid)  | Ja |
| toUnixTimestamp(timeuuid)  | Ja |
| toDate(timestamp)  | Ja |
| toUnixTimestamp(timestamp)  | Ja |
| toTimestamp(date)  | Ja |
| toUnixTimestamp(date) | Ja |


  
## <a name="cql-commands"></a>CQL-kommandon

Azure Cosmos DB stöder följande databaskommandon på alla Cassandra API-konton.

|Kommando  |Stöds |
|---------|---------|
| TILLÅT FILTRERING | Ja |
| ÄNDRA TECKEN AVSTÅND | Ej tillämpligt (PaaS-tjänst, replikering hanteras internt)|
| ÄNDRA MATERIALISERAD VY | Inga |
| ÄNDRA ROLL | Inga |
| ALTER TABLE | Ja |
| ÄNDRA TYP | Inga |
| ÄNDRA ANVÄNDARE | Inga |
| BATCHUPPGIFTEN | Ja (endast inloggad batch)|
| KOMPAKT LAGRING | Ej tillämpligt (PaaS-tjänst) |
| SKAPA MÄNGD | Inga | 
| SKAPA ETT ANPASSAT INDEX (SASI) | Inga |
| CREATE INDEX | Ja (utan att [Ange index namn](cassandra-secondary-index.md)och index på kluster nycklar eller en fullständig frusen samling stöds inte) |
| CREATE FUNCTION | Inga |
| SKAPA ett tecken utrymme (replikeringsinställningar ignoreras) | Ja |
| SKAPA MATERIALISERAD VY | Inga |
| CREATE TABLE | Ja |
| SKAPA UTLÖSARE | Inga |
| SKAPA TYP | Ja |
| SKAPA ROLL | Inga |
| Skapa användare (inaktuellt i ursprunglig Apache-Cassandra) | Inga |
| DELETE | Ja |
| DISTINKTA | Inga |
| SLÄPP AGG REGERING | Inga |
| DROP FUNCTION | Inga |
| DROP INDEX | Ja |
| SLÄPP BLANK STEG | Ja |
| TA BORT MATERIALISERAD VY | Inga |
| TA BORT ROLL | Inga |
| DROP TABLE | Ja |
| SLÄPP UTLÖSARE | Inga | 
| SLÄPP TYP | Ja |
| SLÄPP användare (inaktuellt i native Apache Cassandra) | Inga |
| GRANT | Inga |
| INSERT | Ja |
| LIST BEHÖRIGHETER | Inga |
| LIST ROLLER | Inga |
| LISTA användare (föråldrade i ursprunglig Apache-Cassandra) | Inga |
| REVOKE | Inga |
| SELECT | Ja |
| UPDATE | Ja |
| TRUNCATE | Inga |
| USE | Ja |

## <a name="lightweight-transactions-lwt"></a>Lightweight-transaktioner (LWT)

| Komponent  |Stöds |
|---------|---------|
| TA BORT OM FINNS | Ja |
| TA bort villkor | Inga |
| INFOGA OM DEN INTE FINNS | Ja |
| UPPDATERA OM FINNS | Ja |
| UPPDATERA OM DEN INTE FINNS | Ja |
| UPPDATERINGs villkor | Inga |

## <a name="cql-shell-commands"></a>CQL Shell-kommandon

Azure Cosmos DB stöder följande databaskommandon på alla Cassandra API-konton.

|Kommando  |Stöds |
|---------|---------|
| WEBBINFÅNGSTEN | Ja |
| Rensa | Ja |
| KONTROLL | Ej tillämpligt |
| EXEMPLAR | Inga |
| BESKRIVNINGAR | Ja |
| cqlshExpand | Inga |
| PROGRAMMET | Ja |
| GÄST | Saknas (CQL-funktionen `USER` stöds inte, därför `LOGIN` är redundant) |
| VÄXLINGS | Ja |
| SERIE KONSEKVENS * | Ej tillämpligt |
| SHOW | Ja |
| KÄLLA | Ja |
| SPÅRNING | Ej tillämpligt (API för Cassandra backas upp av Azure Cosmos DB-Använd [diagnostikloggning](cosmosdb-monitor-resource-logs.md) för fel sökning) |

> [!NOTE] 
> \* Konsekvens fungerar annorlunda i Azure Cosmos DB finns [här](cassandra-consistency.md) för mer information.  


## <a name="json-support"></a>JSON-stöd
|Kommando  |Stöds |
|---------|---------|
| VÄLJ JSON | Ja |
| INFOGA JSON | Ja |
| fromJson() | Inga |
| toJson() | Inga |


## <a name="cassandra-api-limits"></a>Begränsningar i API:et för Cassandra

Det finns inga begränsningar i Azure Cosmos DB Cassandra API för storleken på data som lagras i en tabell. Hundratals terabyte eller petabyte data kan lagras samtidigt om partitionsnyckelgränserna respekteras. På samma sätt har varje entitet eller rad motsvarighet inte några begränsningar för antalet kolumner. Den totala storleken på entiteten bör dock inte överstiga 2 MB. Data per partitionsnyckel får inte överstiga 20 GB som i alla andra API: er.

## <a name="tools"></a>Verktyg 

Azure Cosmos DB Cassandra API är en hanterad tjänst-plattform. Det krävs inga hanteringskostnader eller verktyg som skräpinsamlare, Java Virtual Machine (JVM) eller nodverktyg för att hantera klustret. Det stöder verktyg som cqlsh som använder binär CQLv4-kompatibilitet. 

* Azure Portal data Utforskaren, statistik, log Diagnostics, PowerShell och CLI är andra mekanismer som stöds för att hantera kontot.

## <a name="hosted-cql-shell-preview"></a>Värdbaserad CQL Shell (för hands version)

Du kan öppna en värdbaserad Cassandra Shell (CQLSH v 5.0.1) direkt från Datautforskaren i [Azure Portal](data-explorer.md) eller [Azure Cosmos DB Explorer](https://cosmos.azure.com/). Innan du aktiverar CQL-gränssnittet måste du [aktivera funktionen Notebooks](enable-notebooks.md) i ditt konto (om den inte redan är aktive rad uppmanas du att klicka på `Open Cassandra Shell` ). Se artikeln [Aktivera antecknings böcker för Azure Cosmos DB konton](enable-notebooks.md#supported-regions) för Azure-regioner som stöds.

:::image type="content" source="./media/cassandra-support/cqlsh.png" alt-text="Öppna CQLSH":::

Du kan också ansluta till API för Cassandra i Azure Cosmos DB genom att använda CQLSH som är installerad på en lokal dator. Den levereras med Apache Cassandra 3.1.1 och fungerar direkt i rutan genom att ställa in miljövariabler. I följande avsnitt finns anvisningar om hur du installerar, konfigurerar och ansluter till API för Cassandra i Azure Cosmos DB, i Windows eller Linux med CQLSH.

> [!NOTE]
> Anslutningar till Azure Cosmos DB API för Cassandra fungerar inte med DataStax Enterprise (DSE)-versioner av CQLSH. Se till att du bara använder Apache Cassandra-versioner med öppen källkod för CQLSH när du ansluter till API för Cassandra. 

**Aktivitets**

Om du använder Windows rekommenderar vi att du aktiverar [Windows-fil systemet för Linux](/windows/wsl/install-win10#install-the-windows-subsystem-for-linux). Du kan sedan följa Linux-kommandona nedan.

**Unix/Linux/Mac:**

```bash
# Install default-jre and default-jdk
sudo apt install default-jre
sudo apt-get update
sudo apt install default-jdk

# Import the Baltimore CyberTrust root certificate:
curl https://cacert.omniroot.com/bc2025.crt > bc2025.crt
keytool -importcert -alias bc2025ca -file bc2025.crt

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to Azure Cosmos DB API for Cassandra:
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl

```

Alla CRUD-åtgärder som utförs via en CQL v4-kompatibel SDK returnerar extra information om fel och begärda enheter som används. Kommandona ta bort och uppdatera bör hanteras med resurs styrning för att säkerställa den mest effektiva användningen av det etablerade data flödet.

* Observera att gc_grace_seconds-värdet måste vara noll om det anges.

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

## <a name="consistency-mapping"></a>Mappning av konsekvens 

Azure Cosmos DB Cassandra-API innehåller val av konsekvens för läsåtgärder.  Konsekvens mappningen beskrivs [här](./cassandra-consistency.md#mapping-consistency-levels).

## <a name="permission-and-role-management"></a>Behörighets- och rollhantering

Azure Cosmos DB stöder rollbaserad åtkomst kontroll i Azure (Azure RBAC) för etablering, rotation av nycklar, visning av mått och skriv-och skrivskyddade lösen ord/nycklar som kan hämtas via [Azure Portal](https://portal.azure.com). Azure Cosmos DB stöder inte roller för CRUD-aktiviteter.

## <a name="keyspace-and-table-options"></a>Alternativ för tecken utrymme och tabell

Alternativen för region namn, klass, replication_factor och data Center i kommandot "skapa inloggnings utrymme" ignoreras för närvarande. Systemet använder den underliggande Azure Cosmos DBens [globala distributions](global-dist-under-the-hood.md) Metod för att lägga till regionerna. Om du behöver data över flera regioner kan du aktivera det på konto nivå med PowerShell, CLI eller portal, och läsa mer i artikeln [så här lägger](how-to-manage-database-account.md#addremove-regions-from-your-database-account) du till regioner. Durable_writes kan inte inaktive ras eftersom Azure Cosmos DB säkerställer att varje skrivning är varaktig. I varje region replikerar Azure Cosmos DB data över replik uppsättningen som består av fyra repliker och den här replik uppsättnings [konfigurationen](global-dist-under-the-hood.md) kan inte ändras.
 
Alla alternativ ignoreras när tabellen skapas, förutom gc_grace_seconds, vilket ska vara inställt på noll.
Tecken området och tabellen har ett extra alternativ med namnet "cosmosdb_provisioned_throughput" med ett minimivärde på 400 RU/s. Genom strömningen med hjälp av det här flödet kan du dela data flöde över flera tabeller och det är användbart för scenarier när alla tabeller inte använder det etablerade data flödet. Alter Table-kommandot tillåter ändring av det etablerade data flödet i regionerna. 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```
## <a name="secondary-index"></a>Sekundärt index
API för Cassandra stöder sekundära index för alla data typer, förutom för frysta samlings typer, decimaler och variant-typer. 

## <a name="usage-of-cassandra-retry-connection-policy"></a>Användning av principen för nytt anslutningsförsök i Cassandra

Azure Cosmos DB är ett resurs styrt system. Det innebär att du kan utföra ett visst antal åtgärder i en viss sekund baserat på de enheter för enheter som används av åtgärderna. Om ett program överskrider den gränsen inom en viss sekund kommer begär Anden att bli avgiftsbelagda och undantag kommer att genereras. API för Cassandra i Azure Cosmos DB översätter dessa undantag till överbelastade fel på det inbyggda Cassandra-protokollet. För att säkerställa att ditt program kan fånga upp och försöka göra förfrågningar om hastighets begränsning, så tillhandahålls [Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) -och [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) -tilläggen. Se även exempel på Java-kod för [version 3](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) och [version 4](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4) DataStax-drivrutiner vid anslutning till API för Cassandra i Azure Cosmos dB. Om du använder andra SDK: er för att komma åt API för Cassandra i Azure Cosmos DB skapar du en anslutnings princip för att försöka igen med dessa undantag.

## <a name="next-steps"></a>Nästa steg

- Kom igång med [skapa ett Cassandra API-konto, databas och en tabell](create-cassandra-api-account-java.md) med hjälp av ett Java-program
