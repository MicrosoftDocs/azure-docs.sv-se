---
title: Migrera data från Oracle till Azure Cosmos DB API för Cassandra med Blitzz
description: Lär dig hur du migrerar data från Oracle Database till Azure Cosmos DB API för Cassandra med hjälp av Blitzz.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 15bcd0c54fc5f6614f4d1925759704309048acae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336450"
---
# <a name="migrate-data-from-oracle-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>Migrera data från Oracle till Azure Cosmos DB API för Cassandra konto med Blitzz
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

API för Cassandra i Azure Cosmos DB har blivit ett bra val för företags arbets belastningar som körs på Oracle av en rad olika orsaker, till exempel:

* **Bättre skalbarhet och tillgänglighet:** Den eliminerar de enskilda felen, bättre skalbarhet och tillgänglighet för dina program.

* **Betydande kostnads besparingar:** Du kan spara kostnader med Azure Cosmos DB, vilket inkluderar kostnaden för VM, bandbredd och eventuella tillämpliga Oracle-licenser. Dessutom behöver du inte hantera data Center, servrar, SSD-lagring, nätverk och el kostnader.

* **Ingen omkostnader för hantering och övervakning:** Som en helt hanterad moln tjänst, Azure Cosmos DB ta bort behovet av att hantera och övervaka en myriaden av inställningar.

Det finns olika sätt att migrera databas arbets belastningar från en plattform till en annan. [Blitzz](https://www.blitzz.io) är ett verktyg som erbjuder ett säkert och tillförlitligt sätt att utföra migrering av noll nedtid från en mängd olika databaser till Azure Cosmos dB. I den här artikeln beskrivs de steg som krävs för att migrera data från Oracle Database till Azure Cosmos DB API för Cassandra med hjälp av Blitzz.

## <a name="benefits-using-blitzz-for-migration"></a>Fördelar med att använda Blitzz för migrering

Blitzz för migrering följer en steg-för-steg-metod för att migrera komplexa drift arbets belastningar. Följande är några av de viktigaste aspekterna av Blitzz: s noll-nedtid:

* Den erbjuder automatisk migrering av affärs logik (tabeller, index, vyer) från Oracle Database till Azure Cosmos DB. Du behöver inte skapa scheman manuellt.

* Blitzz erbjuder hög volym-och parallell databasreplikering. Det gör att både käll-och mål plattformarna kan synkroniseras under migreringen med hjälp av en metod som kallas för ändrings data fångst (CDC). Genom att använda CDC hämtar Blitzz kontinuerligt en ström med ändringar från käll databasen (Oracle) och tillämpar den på mål databasen (Azure Cosmos DB).

* Den är feltolerant och garanterar exakt när leverans av data sker även under ett maskin-eller program varu fel i systemet.

* Den skyddar data under överföringen med hjälp av en rad säkerhets metoder som TLS/SSL, kryptering.

* Den erbjuder tjänster för att konvertera komplex affärs logik skriven i PL/SQL till motsvarande affärs logik i Azure Cosmos DB.

## <a name="steps-to-migrate-data"></a>Steg för att migrera data

I det här avsnittet beskrivs de steg som krävs för att konfigurera Blitzz och migrera data från Oracle Database till Azure Cosmos DB.

1. Lägg till ett säkerhetscertifikat från den dator där du planerar att installera Blitzz-replicant. Det här certifikatet krävs av Blitzz-replicant för att upprätta en TLS-anslutning med det angivna Azure Cosmos DB kontot. Du kan lägga till certifikatet med följande steg:

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. ou kan hämta Blitzz-installationen och de binära filerna genom att begära en demonstration på [Blitzz-webbplatsen](https://www.blitzz.io). Du kan också skicka ett [e-postmeddelande](mailto:success@blitzz.io) till teamet.

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/blitzz-replicant-download.png" alt-text="Hämtning av Blitzz replicant-verktyget":::

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/replicant-files.png" alt-text="Blitzz replicant-filer":::

1. Konfigurera käll databas konfigurationen från CLI-terminalen. Öppna konfigurations filen med **`vi conf/conn/oracle.yml`** kommandot och Lägg till en kommaavgränsad lista med IP-adresser för Oracle-noder, port nummer, användar namn, lösen ord och annan information som krävs. Följande kod visar ett exempel på en konfigurations fil:

   ```bash
   type: ORACLE

   host: localhost
   port: 53546

   service-name: IO

   username: '<Username of your Oracle database>'
   password: '<Password of your Oracle database>'

   conn-cnt: 30
   use-ssl: false
   ```

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/open-connection-editor-oracle.png" alt-text="Öppna redigeraren för Oracle-anslutning":::

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/oracle-connection-configuration.png" alt-text="Konfiguration av Oracle-anslutning":::

   Spara och Stäng filen när du har fyllt i konfigurations informationen.

1. Du kan också konfigurera käll databasens filter fil. Filter filen anger vilka scheman eller tabeller som ska migreras. Öppna konfigurations filen med **`vi filter/oracle_filter.yml`** kommandot och ange följande konfigurations information:

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```
 
   Spara och Stäng filen när du har fyllt i databas filter informationen.

1. Sedan konfigurerar du konfigurationen av mål databasen. Innan du definierar konfigurationen skapar du [ett Azure Cosmos DB API för Cassandra-konto](create-cassandra-dotnet.md#create-a-database-account). [Välj rätt partitionsnyckel](partitioning-overview.md#choose-partitionkey) från dina data och skapa sedan ett nyckel utrymme och en tabell för att lagra migrerade data.

1. Innan du migrerar data ska du öka behållar flödet till det antal som krävs för att ditt program ska kunna migrera snabbt. Du kan till exempel öka data flödet till 100000 ru: er. Genom att skala data flödet innan du påbörjar migreringen kan du migrera dina data på kortare tid. 

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/scale-throughput.png" alt-text="Skala Azure Cosmos-behållaren i hela":::

   Du måste minska data flödet när migreringen är klar. Baserat på mängden data som lagras och ru: er krävs för varje åtgärd kan du beräkna data flödet som krävs efter datamigreringen. Mer information om hur du uppskattar de ru: er som krävs finns i [etablera data flöde på behållare och databaser](set-throughput.md) och [Beräkna ru/s med hjälp av artiklar om kapacitets planering för Azure Cosmos DB](estimate-ru-with-capacity-planner.md) .

1. Hämta **kontakt punkt, port, användar namn** och **primärt lösen ord** för ditt Azure Cosmos-konto från fönstret **anslutnings sträng** . Du kommer att använda dessa värden i konfigurations filen.

1. Konfigurera mål databas konfigurationen från CLI-terminalen. Öppna konfigurations filen med **`vi conf/conn/cosmosdb.yml`** kommandot och Lägg till en kommaavgränsad lista över värd-URI, port nummer, användar namn, lösen ord och andra obligatoriska parametrar. Följande är ett exempel på innehåll i konfigurations filen:

   ```bash
   type: COSMOSDB

   host: `<Azure Cosmos account’s Contact point>`
   port: 10350

   username: 'blitzzdemo'
   password: `<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   use-ssl: false
   ```

1. Migrera sedan data med Blitzz. Du kan köra Blizz-replicant i läget **fullständig** eller **ögonblicks bild** :

   * **Fullständigt läge** – i det här läget fortsätter replicant att köras efter migreringen och lyssnar efter ändringar på käll-Oracle-systemet. Om ändringarna upptäcks replikeras de på Azure Cosmos-kontot i real tid.

   * **Ögonblicks bilds läge** – i det här läget kan du utföra schema migrering och datareplikering vid ett tillfälle. Replikering i real tid stöds inte med det här alternativet.


   Genom att använda ovanstående två lägen kan migreringen utföras med noll stillestånds tid.

1. Kör följande kommando för att migrera data från Blitzz replicant CLI-terminalen:

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing
   ```

   Replicant-ANVÄNDARGRÄNSSNITTET visar replikeringens förlopp. När schema migreringen och ögonblicks bild åtgärden är klar, visar förloppet 100%. När migreringen är klar kan du verifiera data i Azure Cosmos-databasen.

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/oracle-data-migration-output.png" alt-text="Utdata för Oracle-datamigrering":::

1. Eftersom du har använt fullständigt läge för migrering kan du utföra åtgärder som att infoga, uppdatera eller ta bort data på käll-Oracle-databasen. Senare kan du kontrol lera att de är replikerade real tid i Azure Cosmos-databasen. Efter migreringen ser du till att minska det data flöde som kon figurer ATS för din Azure Cosmos-behållare.

1. Du kan stoppa replicant varje punkt och starta om den med **--Resume** switch. Replikeringen återupptas från den punkt den har stoppats utan att kompromissa med data konsekvens. Följande kommando visar hur du använder återställnings växeln.

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing --resume
   ```

Om du vill veta mer om migreringen till målet går du till real tids migrering i [Blitzz replicant-demon](https://www.youtube.com/watch?v=y5ZeRK5A-MI).

## <a name="next-steps"></a>Nästa steg

* [Etablera dataflöde på containrar och databaser](set-throughput.md) 
* [Metod tips för partitions nyckel](partitioning-overview.md#choose-partitionkey)
* [Beräkna ru/s med Azure Cosmos DB kapacitets planerings](estimate-ru-with-capacity-planner.md) artiklar
