---
title: 'Självstudie: Migrera MongoDB online till Azure Cosmos DB API för MongoDB'
titleSuffix: Azure Database Migration Service
description: Lär dig att migrera från MongoDB lokalt till Azure Cosmos DB API för MongoDB online genom att använda Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-nov-2020
ms.topic: tutorial
ms.date: 02/03/2021
ms.openlocfilehash: 359f268f69918ccfd9fe34a28c3f8d1c79988393
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575630"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-online-using-dms"></a>Självstudie: Migrera MongoDB till Azure Cosmos DB s API för MongoDB online med DMS

Du kan använda Azure Database Migration Service för att utföra en migrering online (minimal nedtid) av databaser från en lokal eller moln instans av MongoDB till Azure Cosmos DB s API för MongoDB.

Den här självstudien visar de steg som är kopplade till att använda Azure Database Migration Service för att migrera MongoDB-data till Azure Cosmos DB:
> [!div class="checklist"]
> 
> * Skapa en instans av Azure Database Migration Service. 
> * Skapa ett migreringsjobb. 
> * Ange källan. 
> * Ange målet. 
> * Mappa till mål databaser. 
> * Köra migreringen. 
> * Övervaka migreringen. 
> * Verifiera data i Azure Cosmos DB. 
> * Slutför migreringen när du är klar. 

I den här självstudien migrerar du en data uppsättning i MongoDB som finns på en virtuell Azure-dator till Azure Cosmos DB s API för MongoDB med minimal nedtid genom att använda Azure Database Migration Service. Om du inte har konfigurerat någon MongoDB-källa, kan du läsa artikeln [Installera och konfigurera MongoDB på en virtuell Windows-dator i Azure](../virtual-machines/windows/install-mongodb.md).

> [!NOTE]
> Om du använder Azure Database Migration Service för att utföra en online-migrering måste du skapa en instans utifrån pris nivån Premium.

> [!IMPORTANT]
> För en optimal migrering rekommenderar Microsoft att du skapar en instans av Azure Database Migration Service i samma Azure-region som mål databasen. Att flytta data mellan regioner eller geografiska områden kan göra migreringsprocessen långsammare.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

I den här artikeln beskrivs en onlinemigrering från MongoDB till Azure Cosmos DB:s API för MongoDB. Läs mer i [Migrera MongoDB till Azure Cosmos DB:s API för MongoDB offline med DMS](tutorial-mongodb-cosmos-db.md) om offlinemigrering.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* [Slutför stegen innan migreringen](../cosmos-db/mongodb-pre-migration.md) , till exempel genom att uppskatta data flödet, välja en partitionsnyckel och indexerings principen.
* [Skapa ett Azure Cosmos DBS API för MongoDB-konto](https://ms.portal.azure.com/#create/Microsoft.DocumentDB) och se till att [SSR (Server på Server sidan)](../cosmos-db/prevent-rate-limiting-errors.md) har Aktiver ATS.
* Skapa en Microsoft Azure Virtual Network för Azure Database Migration Service med hjälp av Azure Resource Manager distributions modell, som tillhandahåller plats-till-plats-anslutning till dina lokala käll servrar genom att använda antingen [ExpressRoute](../expressroute/expressroute-introduction.md) eller [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

    > [!NOTE]
    > Om du använder ExpressRoute med nätverks-peering till Microsoft under installationen av det virtuella nätverket lägger du till följande tjänst [slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md) i under nätet där tjänsten ska tillhandahållas:
    >
    > * Slut punkt för mål databas (till exempel SQL-slutpunkt, Cosmos DB slut punkt och så vidare)
    > * Lagrings slut punkt
    > * Service Bus-slutpunkt
    >
    > Den här konfigurationen är nödvändig eftersom Azure Database Migration Service saknar Internet anslutning.

* Se till att dina regler för nätverks säkerhets gruppen (NSG) för virtuella nätverk inte blockerar följande kommunikations portar: 53, 443, 445, 9354 och 10000-20000. Mer information om NSG för trafik filtrering i virtuellt nätverk finns i artikeln [filtrera nätverks trafik med nätverks säkerhets grupper](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Öppna Windows-brandväggen för att tillåta Azure Database Migration Service att få åtkomst till MongoDB-servern, som standard är TCP-port 27017.
* När du använder en brand Väggs installation framför dina käll databaser, kan du behöva lägga till brand Väggs regler för att tillåta Azure Database Migration Service åtkomst till käll databaserna för migrering.

## <a name="configure-azure-cosmos-db-server-side-retries-for-efficient-migration"></a>Konfigurera Azure Cosmos DB server sidans nya försök för effektiv migrering

Kunder som migrerar från MongoDB till Azure Cosmos DB dra nytta av resurs styrnings funktioner som garanterar möjligheten att fullt ut använda dina etablerade RU/s-genomflöde. Azure Cosmos DB kan begränsa en begäran om en datamigrerings tjänst under migreringen om denna begäran överskrider den behållare som etablerade RU/s; sedan måste begäran göras om. Datamigrerings tjänsten kan utföra nya försök, men den fördröjning som ingår i nätverks hoppet mellan datamigrerings tjänsten och Azure Cosmos DB påverkar den totala svars tiden för denna begäran. Att förbättra svars tiden för begränsade begär Anden kan förkorta den totala tiden som krävs för migrering. Funktionen *försök igen på Server sidan* på Azure Cosmos DB tillåter att tjänsten fångar upp begränsnings felkoder och gör ett nytt försök med mycket kortare svars tid för svars tider, vilket dramatiskt förbättrar svars tiderna för begäran.

Du hittar funktionen för nya försök på Server sidan på bladet *funktioner* på Azure Cosmos DBS portalen

![Skärm bild av funktionen MongoDB Server-Side försök igen.](media/tutorial-mongodb-to-cosmosdb-online/mongo-server-side-retry-feature.png)

Och om den är *inaktive rad* rekommenderar vi att du aktiverar det enligt nedan

![Skärm bild av MongoDB Server-Side försök aktivera igen.](media/tutorial-mongodb-to-cosmosdb-online/mongo-server-side-retry-enable.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

   ![Visa portalprenumerationer](media/tutorial-mongodb-to-cosmosdb-online/portal-select-subscription1.png)

2. Välj den prenumeration där du vill skapa instansen av Azure Database Migration Service och välj sedan **resurs leverantörer**.

    ![Visa resursprovidrar](media/tutorial-mongodb-to-cosmosdb-online/portal-select-resource-provider.png)

3. Sök efter migrering och välj sedan **Registrera** till höger om **Microsoft. data migration**.

    ![Registrera resursprovider](media/tutorial-mongodb-to-cosmosdb-online/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Skapa en instans

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb-online/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-mongodb-to-cosmosdb-online/dms-create1.png)
  
3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj den plats där du vill skapa instansen av Azure Database Migration Service.

5. Välj ett befintligt virtuellt nätverk eller skapa ett nytt.

   Det virtuella nätverket ger Azure Database Migration Service åtkomst till käll MongoDB-instansen och mål Azure Cosmos DB kontot.

   Mer information om hur du skapar ett virtuellt nätverk i Azure Portal finns i artikeln [skapa ett virtuellt nätverk med hjälp av Azure Portal](../virtual-network/quick-create-portal.md).

6. Välj en SKU på Premium-prisnivån.

    > [!NOTE]
    > Onlinemigreringar stöds bara när du använder Premium-nivån. Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

    ![Konfigurera Azure Database Migration Service-instansinställningar](media/tutorial-mongodb-to-cosmosdb-online/dms-settings3.png)

7. Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

    ![Hitta alla instanser av Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-search.png)

2. På skärmen **Azure Database migration Services** söker du efter namnet på Azure Database migration service-instansen som du skapade och väljer sedan instansen.

    Alternativt kan du identifiera Azure Database migration service-instansen från Sök-fönstret i Azure Portal.

    ![Använda sökrutan i Azure-portalen](media/tutorial-mongodb-to-cosmosdb-online/dms-search-portal.png)

3. Välj + **Nytt migreringsprojekt**.

4. På sidan **Nytt migreringsprojekt** anger du namnet på projektet. I textrutan **Typ av källserver** väljer du **MongoDB**, i textrutan **Målservertyp** väljer du **CosmosDB (MongoDB API)** och i **Välj typ av aktivitet** väljer du sedan **Online-datamigrering (förhandsversion)**.

    ![Skapa ett Database Migration Service-projekt](media/tutorial-mongodb-to-cosmosdb-online/dms-create-project1.png)

5. Välj **Spara** och sedan **Skapa och kör aktivitet** för att skapa projektet och köra migreringsaktiviteten.

## <a name="specify-source-details"></a>Ange källinformation

1. På sidan **Källinformation** anger du anslutningsinformationen för MongoDB-källservern.

   > [!IMPORTANT]
   > Azure Database Migration Service stöder inte Azure Cosmos DB som källa.

    Det finns tre lägen för att ansluta till en datakälla:
   * **Standardläge** som accepterar ett fullständigt kvalificerat domännamn eller en IP-adress, portnummer och autentiseringsuppgifter för anslutningen.
   * **Anslutningssträngsläge** som tar emot en MongoDB-anslutningssträng som beskrivs i artikeln [URI-anslutningssträngens format](https://docs.mongodb.com/manual/reference/connection-string/).
   * **Data från Azure-lagring** som tar emot en blobcontainers SAS-URL. Välj **Bloben innehåller BSON-dumpar** om blobcontainern innehåller BSON-dumpar som producerats av MongoDB:s [bsondump-verktyg](https://docs.mongodb.com/manual/reference/program/bsondump/) och avmarkera den om containern innehåller JSON-filer.

     Om du väljer det här alternativet ska du se till att anslutningssträngen för lagringskontot har följande format:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Tänk också på följande när du använder information om typ dump i Azure Storage:

     * För BSON-dumpar måste data i blob-containern vara i bsondump-format, så att datafilerna placeras i mappar som namnges efter de innehållande databaserna i formatet collection.bson. Metadatafiler (om sådana finns) bör namnges med formatet *samling*.metadata.json.

     * För JSON-dumpar måste filerna i blob-containern placeras i mappar som namnges efter de innehållande databaserna. I varje databasmapp måste datafiler placeras i en undermapp som heter ”data” och namnges med formatet *samling*.json. Metadatafiler (om sådana finns) måste placeras i en undermapp som heter ”metadata” och namnges med samma format, *samling*.json. Metadatafilerna måste vara i samma format som det som skapas av verktyget MongoDB bsondump.

    > [!IMPORTANT]
    > Det rekommenderas inte att använda ett självsignerat certifikat på Mongo-servern. Om ett sådant används måste du dock ansluta till servern med **anslutnings sträng läge** och se till att anslutnings strängen har ""
    >
    >```
    >&sslVerifyCertificate=false
    >```

    Du kan även använda IP-adressen i situationer då DNS-namnmatchning inte är möjlig.

   ![Ange källinformation](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-source1.png)

2. Välj **Spara**.

   > [!NOTE]
   > Källserveradressen ska vara den primära adressen om källan är en replikuppsättning och routern om källan är ett delat MongoDB-kluster. Azure Database Migration Service måste kunna ansluta till enskilda shards i klustret, vilket kan kräva att brandväggen öppnas på flera datorer vid ett fragmenterat MongoDB-kluster.

## <a name="specify-target-details"></a>Ange målinformation

1. På sidan **Information om migreringsmål** anger du anslutningsinformationen för Azure Cosmos DB-målkontot, vilket är det företablerade Azure Cosmos DB-API för MongoDB-kontot som du ska migrera dina MongoDB-data till.

    ![Ange målinformation](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-target1.png)

2. Välj **Spara**.

## <a name="map-to-target-databases"></a>Mappa till måldatabaser

1. På skärmen **Mappa till måldatabaser** mappar du käll- och måldatabasen för migreringen.

   Om mål databasen innehåller samma databas namn som käll databasen Azure Database Migration Service väljer mål databasen som standard.

   Om strängen **create** visas bredvid databas namnet, betyder det att Azure Database migration service inte har hittat mål databasen, och tjänsten kommer att skapa databasen åt dig.

   I det här läget i migreringen, om du vill dela data flöde i databasen, anger du ett data flöde RU. I Cosmos DB kan du etablera dataflöden på databasnivå eller separat för varje samling. Dataflödet mäts i [enheter för programbegäran](../cosmos-db/request-units.md) (RU:er). Mer information om [Azure Cosmos DB-prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/).

   ![Mappa till måldatabaser](media/tutorial-mongodb-to-cosmosdb-online/dms-map-target-databases1.png)

2. Välj **Spara**.

3. På skärmen **Mängdinställning** expanderar du samlingslistan och granskar sedan listan över de samlingar som ska migreras.

   Azure Database Migration Service väljer automatiskt alla samlingar som finns på käll MongoDB-instansen som inte finns på mål Azure Cosmos DB kontot. Om du vill migrera om samlingar som redan innehåller data, måste du uttryckligen välja samlingarna i detta fönster.

   Du kan ange hur många RU:er som du vill att samlingarna ska använda. Ett värde mellan 500 (minst 1 000 för fragmenterade samlingar) och 4 000 bör i de flesta fall vara tillräckligt. Azure Database Migration Service föreslår smarta standardvärden baserat på samlingens storlek.

    > [!NOTE]
    > Utför migreringen och insamling av databasen parallellt med flera instanser av Azure Database Migration Service, om det behövs, för att påskynda körningen.

   Du kan också ange en shardnyckel för att kunna utnyttja [partitionering i Azure Cosmos DB](../cosmos-db/partitioning-overview.md) för optimal skalbarhet. Läs igenom [metodtipsen för att välja en shard-/partitionsnyckel](../cosmos-db/partitioning-overview.md#choose-partitionkey). Om du inte har någon partitionsnyckel, kan du alltid använda **_id** som shardnyckel för att få ett bättre dataflöde.

   ![Välja tabeller för samlingar](media/tutorial-mongodb-to-cosmosdb-online/dms-collection-setting1.png)

4. Välj **Spara**.

5. På sidan **Migreringssammanfattning**, i textrutan **Aktivitetsnamn** anger du ett namn på migreringsaktiviteten.

    ![Migreringssammanfattning](media/tutorial-mongodb-to-cosmosdb-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Köra migreringen

* Välj **Kör migrering**.

   Migreringsaktivitetsfönstret öppnas och **Status** för aktiviteten visas.

   ![Aktivitetsstatus](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Övervaka migreringen

* På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Spelar upp igen**.

   > [!NOTE]
   > Du kan välja Aktivitet för att få information om databasens och samlingsnivåns migreringsmått.

   ![Aktivitetsstatusen spelas upp igen](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-replaying.png)

## <a name="verify-data-in-cosmos-db"></a>Kontrollera data i Cosmos DB

1. Gör ändringar i din källdatabas för MongoDB.
2. Anslut till COSMOS DB för att kontrollera om data replikeras från källservern för MongoDB.

    ![Skärm bild som visar var du kan kontrol lera att data har repliker ATS.](media/tutorial-mongodb-to-cosmosdb-online/dms-verify-data.png)

## <a name="complete-the-migration"></a>Slutföra migreringen

* När alla dokument från källan är tillgängliga i COSMOS DB-målet, väljer du **Slutför** i migreringsaktivitetens snabbmeny för att slutföra migreringen.

    Åtgärden spelar upp alla väntande ändringar igen och slutför migreringen.

    ![Skärm bild som visar meny alternativet Slutför.](media/tutorial-mongodb-to-cosmosdb-online/dms-finish-migration.png)

## <a name="post-migration-optimization"></a>Optimering efter migrering

När du har migrerat data som lagras i MongoDB-databasen till Azure Cosmos DB s API för MongoDB, kan du ansluta till Azure Cosmos DB och hantera data. Du kan också utföra andra optimerings steg efter migrering, till exempel optimera indexerings principen, uppdatera standard konsekvens nivån eller konfigurera global distribution för ditt Azure Cosmos DB-konto. Mer information finns i artikeln [optimering efter migreringen](../cosmos-db/mongodb-post-migration.md) .

## <a name="additional-resources"></a>Ytterligare resurser

* [Information om Cosmos DB-tjänsten](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Nästa steg

* Se fler scenarier i migreringsvägledningen i Microsofts [Guide för databasmigrering](https://datamigration.microsoft.com/).