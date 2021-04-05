---
title: Migrera hundratals terabyte data till Azure Cosmos DB
description: Det här dokumentet beskriver hur du kan migrera 100s av terabyte data till Cosmos DB
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/23/2019
ms.openlocfilehash: b24ea79737c9e1f64abb7f62807352dbd9573695
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018079"
---
# <a name="migrate-hundreds-of-terabytes-of-data-into-azure-cosmos-db"></a>Migrera hundratals terabyte data till Azure Cosmos DB 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB kan lagra flera terabyte data. Du kan utföra en storskalig datamigrering för att flytta din produktionsarbetsbelastning till Azure Cosmos DB. I den här artikeln beskriver vi utmaningarna med att flytta storskaliga data till Azure Cosmos DB och introducerar ett verktyg som hjälper dig med migreringen. I den här fallstudien använde kunden Cosmos DB SQL API.  

Innan du migrerar hela arbets belastningen till Azure Cosmos DB kan du migrera en delmängd data för att verifiera några av de aspekter som partitionsalternativ, fråga prestanda och data modellering. När du har validerat POC-beviset kan du flytta hela arbets belastningen till Azure Cosmos DB.  

## <a name="tools-for-data-migration"></a>Verktyg för datamigrering 

Azure Cosmos DB migrations strategier skiljer sig för närvarande baserat på API-valet och storleken på data. För att migrera mindre data mängder – för att verifiera data modellering, fråga om prestanda, partitionsalternativ osv. – du kan välja [verktyget datamigrering](import-data.md) eller [Azure Data Factory Azure Cosmos DB koppling](../data-factory/connector-azure-cosmos-db.md). Om du är bekant med Spark kan du också välja att använda [Azure Cosmos DB Spark-anslutningsprogrammet](spark-connector.md) för att migrera data.

## <a name="challenges-for-large-scale-migrations"></a>Utmaningar för storskaliga migreringar 

De befintliga verktygen för att migrera data till Azure Cosmos DB har vissa begränsningar som är särskilt synliga vid stora skalor:

 * **Funktioner för begränsad skalning**: för att kunna migrera terabyte data till Azure Cosmos DB så snabbt som möjligt och för att effektivt förbruka hela det etablerade data flödet bör migrerings klienterna ha möjlighet att skala ut på obestämd tid.  

* **Brist på förlopps spårning och kontroll pekare**: det är viktigt att spåra migreringens förlopp och kontrol lera att de har en kontroll som pekar samtidigt som du migrerar stora data mängder. Annars stoppas migreringen av ett fel som inträffar under migreringen och du måste starta processen från grunden. Det skulle inte vara produktivt att starta om hela migreringsprocessen när 99% av den redan har slutförts.  

* **Avsaknad av kön för obeställbara meddelanden**: i stora data mängder kan det finnas problem med delar av data källan. Dessutom kan det finnas tillfälliga problem med klienten eller nätverket. Något av dessa fall bör inte medföra att hela migreringen fungerar. Även om de flesta Migreringsverktyg har robusta återförsöks funktioner som skyddar mot tillfälliga problem, är det inte alltid tillräckligt. Om till exempel mindre än 0,01% av käll data dokumenten är större än 2 MB, kommer det att göra att dokumentet skrivs för att Miss Miss Azure Cosmos DB. Vi rekommenderar att du använder Migreringsverktyget för att spara dessa "misslyckade" dokument i en annan kö för obeställbara meddelanden, som kan bearbetas efter migreringen. 

Många av de här begränsningarna är fasta för verktyg som Azure Data Factory, Azure Data Migration Services. 

## <a name="custom-tool-with-bulk-executor-library"></a>Anpassat verktyg med bulk utförar Library 

Utmaningarna som beskrivs i ovanstående avsnitt kan lösas med hjälp av ett anpassat verktyg som enkelt kan skalas ut över flera instanser och det är elastiskt för tillfälliga haverier. Dessutom kan det anpassade verktyget pausa och återuppta migrering vid olika kontroll punkter. Azure Cosmos DB har redan ett [samlings utförar-bibliotek](./bulk-executor-overview.md) som införlivar några av dessa funktioner. Till exempel har utförar-biblioteket redan funktioner för att hantera tillfälliga fel och kan skala ut trådar i en enda nod för att förbruka cirka 500 K ru: er per nod. Utförar-biblioteket partitionerar också käll data uppsättningen i mikrobatchar som drivs oberoende som en form av kontroll punkter.  

Det anpassade verktyget använder bulk utförar-biblioteket och har stöd för skalning på flera klienter och för att spåra fel under inmatnings processen. För att kunna använda det här verktyget bör källdata partitioneras i distinkta filer i Azure Data Lake Storage (ADLS) så att olika migreringar kan hämta varje fil och mata in dem i Azure Cosmos DB. Det anpassade verktyget använder en separat samling, som lagrar metadata om migreringens förlopp för varje enskild källfil i ADLS och spårar eventuella fel som är kopplade till dem.  

Följande bild beskriver migreringsprocessen med det här anpassade verktyget. Verktyget körs på en uppsättning virtuella datorer och varje virtuell dator frågar spårnings samlingen i Azure Cosmos DB för att erhålla ett lån på en av datapartitionerna. När detta görs läses käll data partitionen av verktyget och matas in i Azure Cosmos DB med hjälp av utförar-biblioteket. Sedan uppdateras spårnings samlingen för att registrera förloppet för data inhämtningen och eventuella fel som påträffas. När en datapartition har bearbetats försöker verktyget fråga efter nästa tillgängliga källmapp. Den fortsätter att bearbeta nästa käll partition tills alla data har migrerats. Käll koden för verktyget finns på Azure Cosmos DB lagrings platsen för [Mass](https://github.com/Azure-Samples/azure-cosmosdb-bulkingestion) inmatning.  

 
:::image type="content" source="./media/migrate-cosmosdb-data/migrationsetup.png" alt-text="Installation av Migreringsverktyg" border="false":::
 

 

Spårnings samlingen innehåller dokument som visas i följande exempel. Du ser dessa dokument ett för varje partition i käll data.  Varje dokument innehåller metadata för käll data partitionen, till exempel dess plats, migrerings status och fel (om sådana finns):  

```json
{ 
  "owner": "25812@bulkimporttest07", 
  "jsonStoreEntityImportResponse": { 
    "numberOfDocumentsReceived": 446688, 
    "isError": false, 
    "totalRequestUnitsConsumed": 3950252.2800000003, 
    "errorInfo": [], 
    "totalTimeTakenInSeconds": 188, 
    "numberOfDocumentsImported": 446688 
  }, 
  "storeType": "AZURE_BLOB", 
  "name": "sourceDataPartition", 
  "location": "sourceDataPartitionLocation", 
  "id": "sourceDataPartitionId", 
  "isInProgress": false, 
  "operation": "unpartitioned-writes", 
  "createDate": { 
    "seconds": 1561667225, 
    "nanos": 146000000 
  }, 
  "completeDate": { 
    "seconds": 1561667515, 
    "nanos": 180000000 
  }, 
  "isComplete": true 
} 
```
 

## <a name="prerequisites-for-data-migration"></a>Krav för datamigrering 

Innan datamigreringen startar finns det några krav att tänka på:  

#### <a name="estimate-the-data-size"></a>Uppskatta data storleken:  

Käll data storleken kan inte exakt mappas till data storleken i Azure Cosmos DB. Några exempel dokument från källan kan infogas för att kontrol lera data storleken i Azure Cosmos DB. Beroende på storleken på exempel dokumentet kan den totala data storleken i Azure Cosmos DB efter migreringen beräknas. 

Om till exempel varje dokument efter migreringen i Azure Cosmos DB är cirka 1 KB och om det finns omkring 60 000 000 000 dokument i käll data uppsättningen skulle det betydas att den uppskattade storleken i Azure Cosmos DB är nära 60 TB. 

 

#### <a name="pre-create-containers-with-enough-rus"></a>För att skapa behållare med tillräckligt ru: er: 

Även om Azure Cosmos DB skalar ut lagringen automatiskt, är det inte tillrådligt att starta från den minsta behållarens storlek. Mindre behållare har lägre data flödes tillgänglighet, vilket innebär att migreringen skulle ta mycket längre tid att slutföra. I stället är det bra att skapa behållarna med den slutliga data storleken (som uppskattas i föregående steg) och se till att arbets belastningen för migreringen utnyttjar det etablerade data flödet fullt ut.  

I föregående steg. eftersom data storleken har uppskattats vara cirka 60 TB krävs en behållare på minst 2,4 M ru: er för att rymma hela data uppsättningen.  

 

#### <a name="estimate-the-migration-speed"></a>Uppskatta migreringens hastighet: 

Förutsatt att arbets belastningen för migreringen kan förbruka hela det etablerade data flödet skulle den etablerades genom att ge en uppskattning av migrations hastigheten. Om du fortsätter med föregående exempel krävs 5 ru: er för att skriva ett dokument på 1 KB för att Azure Cosmos DB SQL API-konto.  2 400 000 ru: er tillåter en överföring av 480 000-dokument per sekund (eller 480 MB/s). Det innebär att den fullständiga migreringen av 60 TB tar 125 000 sekunder eller cirka 34 timmar.  

Om du vill att migreringen ska slutföras inom en dag bör du öka det etablerade data flödet till 5 000 000 ru: er. 

 

#### <a name="turn-off-the-indexing"></a>Inaktivera indexeringen:  

Eftersom migreringen ska slutföras så snart som möjligt är det tillrådligt att minimera tid och ru: er som ägnas åt att skapa index för varje dokument som matats in.  Azure Cosmos DB automatiskt indexerar alla egenskaper, det är praktiskt att minimera indexeringen till ett valt antal villkor eller inaktivera den helt under migreringen. Du kan inaktivera behållarens indexerings princip genom att ändra indexingMode till ingen som visas nedan:  

 
```
  { 
        "indexingMode": "none" 
  } 
```
 

När migreringen är klar kan du uppdatera indexeringen.  

## <a name="migration-process"></a>Migreringsprocessen 

När förutsättningarna har slutförts kan du migrera data med följande steg:  

1. Importera först data från källan till Azure Blob Storage. För att öka hastigheten på migreringen är det bra att parallellisera över olika källentiteter. Innan du påbörjar migreringen bör käll data uppsättningen partitioneras i filer med en storlek på omkring 200 MB.   

2. Bulk utförar-biblioteket kan skalas upp för att förbruka 500 000-ru: er i en enda virtuell klient dator. Eftersom det tillgängliga data flödet är 5 000 000 ru: er ska 10 Ubuntu 16,04-VM: ar (Standard_D32_v3) vara etablerade i samma region där din Azure Cosmos-databas finns. Du bör förbereda de här virtuella datorerna med migreringsverktyget och dess inställnings fil.  

3. Kör Queue-steget på en av de virtuella klient datorerna. Det här steget skapar spårnings samlingen, som söker igenom behållaren ADLS och skapar ett förlopps spårnings dokument för var och en av data uppsättningens diskpartitioner.  

4. Kör sedan import-steget på alla virtuella klient datorer. Varje klient kan bli ägare till en källmapp och mata in data i Azure Cosmos DB. När den har slutförts och dess status har uppdaterats i spårnings samlingen, kan klienterna fråga efter nästa tillgängliga källmapp i spårnings samlingen.  

5. Den här processen fortsätter tills hela uppsättningen med käll-partitioner har matats in. När alla käll-partitioner har bearbetats ska verktyget köras igen i fel korrigerings läge på samma spårnings samling. Det här steget krävs för att identifiera de källentiteter som ska bearbetas på nytt på grund av fel.  

6. Vissa av dessa fel kan bero på felaktiga dokument i källdata. Dessa bör identifieras och åtgärdas. Sedan bör du köra import-steget på de partitioner som misslyckades för att mata in dem igen. 

När migreringen är klar kan du kontrol lera att antalet dokument i Azure Cosmos DB är samma som antalet dokument i käll databasen. I det här exemplet har den totala storleken i Azure Cosmos DB varit i 65 terabyte. Efter migreringen kan indexering aktive ras selektivt och ru: er kan sänkas till den nivå som krävs för arbets Belastningens åtgärder.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer genom att testa exempel programmen som använder bulk utförar-biblioteket i [.net](bulk-executor-dot-net.md) och [Java](bulk-executor-java.md). 
* Bulk utförar-biblioteket är integrerat i Cosmos DB Spark-anslutningsprogrammet för mer information finns i artikeln [Azure Cosmos DB Spark Connector](spark-connector.md) .  
* Kontakta Azure Cosmos DB produkt teamet genom att öppna ett support ärende under "allmän råd givande" problem typ och "stora (TB +) migreringar" problem under typ för ytterligare hjälp med storskalig migrering.