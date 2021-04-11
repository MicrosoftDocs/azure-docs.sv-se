---
title: 'Självstudie: Migrera MongoDB offline till Azure Cosmos DB API för MongoDB'
titleSuffix: Azure Database Migration Service
description: Migrera från MongoDB lokalt till Azure Cosmos DB API för MongoDB offline genom att använda Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 02/03/2021
ms.openlocfilehash: 8977bb90087d9d3d4962d7eda50903d97da93539
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443875"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-db-api-for-mongodb-offline"></a>Självstudie: Migrera MongoDB till Azure Cosmos DB API för MongoDB offline

Använd Azure Database Migration Service för att utföra en offline-migrering av databaser från en lokal eller moln instans av MongoDB till Azure Cosmos DB API för MongoDB.

I den här guiden får du lära dig att:
> [!div class="checklist"]
>
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsjobb med hjälp av Azure Database Migration Service.
> * Köra migreringen.
> * Övervaka migreringen.

I den här självstudien migrerar du en data uppsättning i MongoDB som finns på en virtuell Azure-dator. Genom att använda Azure Database Migration Service migrerar du data uppsättningen till Azure Cosmos DB-API: t för MongoDB. Om du inte redan har skapat en MongoDB-källa kan du läsa mer i [Installera och konfigurera MongoDB på en virtuell Windows-dator i Azure](/previous-versions/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* [Slutför stegen innan migreringen](../cosmos-db/mongodb-pre-migration.md) , till exempel genom att uppskatta data flödet och välja en partitionsnyckel.
* [Skapa ett konto för Azure Cosmos DB API för MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Skapa en Microsoft Azure Virtual Network för Azure Database Migration Service med Azure Resource Manager. Den här distributions modellen ger plats-till-plats-anslutning till dina lokala käll servrar med hjälp av antingen [Azure ExpressRoute](../expressroute/expressroute-introduction.md) eller [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Mer information om hur du skapar ett virtuellt nätverk finns i [Azure Virtual Network-dokumentationen](../virtual-network/index.yml), i synnerhet snabb starts artiklar med stegvisa anvisningar.

    > [!NOTE]
    > Om du använder ExpressRoute med nätverks-peering till Microsoft under installationen av det virtuella nätverket lägger du till följande tjänst [slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md) i under nätet där tjänsten ska tillhandahållas:
    >
    > * Slut punkt för mål databas (till exempel SQL-slutpunkt eller Azure Cosmos DB slut punkt)
    > * Lagrings slut punkt
    > * Service Bus-slutpunkt
    >
    > Den här konfigurationen är nödvändig eftersom Azure Database Migration Service saknar Internet anslutning.

* Se till att reglerna för nätverks säkerhets gruppen (NSG) för ditt virtuella nätverk inte blockerar följande kommunikations portar: 53, 443, 445, 9354 och 10000-20000. Mer information finns i [Filtrera nätverkstrafik med nätverkssäkerhetsgrupper](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Öppna Windows-brandväggen för att tillåta Azure Database Migration Service att få åtkomst till MongoDB-servern, som standard är TCP-port 27017.
* När du använder en brand Väggs enhet framför käll databasen kan du behöva lägga till brand Väggs regler för att tillåta Azure Database Migration Service åtkomst till käll databasen för migrering.

## <a name="configure-the-server-side-retry-feature"></a>Konfigurera funktionen för att försöka igen på Server Sidan

Du kan dra nytta av resurs styrnings funktioner om du migrerar från MongoDB till Azure Cosmos DB. Med dessa funktioner kan du få fullständig användning av de allokerade enheterna (RU/s) av data flödet. Azure Cosmos DB kan begränsa en viss Database Migration Service begäran under migreringen, om denna begäran överskrider behållarna – etablerade RU/s. Sedan måste begäran göras om.

Database Migration Service kan utföra nya försök. Det är viktigt att förstå att den svars tid som ingår i nätverks hoppet mellan Database Migration Service och Azure Cosmos DB påverkar den totala svars tiden för denna begäran. Att förbättra svars tiden för begränsade begär Anden kan förkorta den totala tiden som krävs för migrering.

Funktionen för att försöka igen på Server sidan i Azure Cosmos DB tillåter att tjänsten fångar upp begränsnings felkoder och gör ett nytt försök med en mycket låg fördröjning, så att svars tiderna för begäran blir betydligt bättre.

Om du vill använda ett nytt försök på Server sidan går du till Azure Cosmos DB Portal och väljer **funktioner**  >  **Server sidan försök igen**.

![Skärm bild som visar var du hittar funktionen för att försöka igen på Server sidan.](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

Om funktionen är inaktive rad väljer du **Aktivera**.

![Skärm bild som visar hur du aktiverar Server sidans försök igen.](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="register-the-resource-provider"></a>Registrera resursprovidern

1. Logga in på Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

   ![Skärm bild som visar Portal prenumerationer.](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. Välj den prenumeration där du vill skapa instansen av Azure Database Migration Service och välj sedan **resurs leverantörer**.

    ![Skärm bild som visar resurs leverantörer.](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. Sök efter migrering och välj sedan **Registrera** till höger om **Microsoft. data migration**.

    ![Skärm bild som visar hur du registrerar resurs leverantören.](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Skapa en instans

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Skärmbild som visar Azure Marketplace.](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skärm bild som visar hur du skapar en instans av Azure Database Migration Service.](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. I **skapa migreringstjänster** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resurs grupp.

4. Välj den plats där du vill skapa instansen av Azure Database Migration Service. 

5. Välj ett befintligt virtuellt nätverk eller skapa ett nytt.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till käll MongoDB-instansen och mål Azure Cosmos DB kontot.

    Mer information om hur du skapar ett virtuellt nätverk i Azure Portal finns i [skapa ett virtuellt nätverk med hjälp av Azure Portal](../virtual-network/quick-create-portal.md).

6. Välj en prisnivå.

    Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

    ![Skärm bild som visar konfigurations inställningar för instansen av Azure Database Migration Service.](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När du har skapat tjänsten letar du upp den i Azure Portal och öppnar den. Skapa sedan ett nytt migreringsjobb.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

      ![Skärm bild som visar hur du hittar alla instanser av Azure Database Migration Service.](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. På skärmen **Azure Database migration Services** söker du efter namnet på Azure Database migration service-instansen som du skapade och väljer sedan instansen.

3. Välj **+ nytt migreringsjobb**.

4. I **nytt migreringsjobb** anger du ett namn för projektet och i text rutan **käll Server typ** väljer du **MongoDB**. I text rutan **mål server typ** väljer du **COSMOSDB (MongoDB API)** och väljer sedan **datamigrering offline** i **Välj typ av aktivitet**. 

    ![Skärm bild som visar projekt alternativ.](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. Välj **Skapa och kör aktivitet** för att skapa projektet och köra migreringsaktiviteten.

## <a name="specify-source-details"></a>Ange källinformation

1. På sidan **Källinformation** anger du anslutningsinformationen för MongoDB-källservern.

   > [!IMPORTANT]
   > Azure Database Migration Service stöder inte Azure Cosmos DB som källa.

    Det finns tre lägen för att ansluta till en datakälla:
   * **Standard läge**, som accepterar ett fullständigt kvalificerat domän namn eller en IP-adress, port nummer och autentiseringsuppgifter för anslutning.
   * **Anslutnings sträng läge**, som godkänner en MongoDB-anslutningssträng enligt beskrivningen i [URI-format för anslutnings sträng](https://docs.mongodb.com/manual/reference/connection-string/).
   * **Data från Azure-lagring** som tar emot en blobcontainers SAS-URL. Välj **BLOB innehåller bson-dum par** om BLOB-behållaren har bson dumpar som producerats av MongoDB [bsondump-verktyget](https://docs.mongodb.com/manual/reference/program/bsondump/). Välj inte det här alternativet om behållaren innehåller JSON-filer.

     Om du väljer det här alternativet måste du se till att anslutnings strängen för lagrings kontot visas i följande format:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Du kan hitta den här BLOB-behållarens SAS-anslutningssträng i Azure Storage Explorer. När du skapar SAS för den berörda behållaren får du webb adressen i det begärda formatet.
     
     Tänk också på följande i Azure Storage, utifrån information om typ dump i:

     * För BSON-dum par måste data i BLOB-behållaren vara i bsondump-format. Placera datafiler i mappar som heter efter den som innehåller databaser i formatet *Collection. bson*. Namnge alla metadatafiler med formatet *collection.metadata.js*.

     * För JSON-dumpar måste filerna i blob-containern placeras i mappar som namnges efter de innehållande databaserna. I varje mapp i databasen måste datafiler placeras i en undermapp med namnet *data*, och de namnges med hjälp av formatet *collection.js*. Placera alla metadatafiler i en undermapp som heter *metadata* och namnge dem med samma format *collection.jspå*. Metadatafilerna måste vara i samma format som det som skapas av verktyget MongoDB bsondump.

    > [!IMPORTANT]
    > Vi rekommenderar inte att du använder ett självsignerat certifikat på MongoDB-servern. Om du måste använda en anslutning ansluter du till servern med hjälp av anslutnings Strängs läget och kontrollerar att anslutnings strängen har citat tecken ("").
    >
    >```
    >&sslVerifyCertificate=false
    >```

   Du kan också använda IP-adressen för situationer där DNS-namnmatchning inte är möjlig.

   ![Skärm bild som visar Ange käll information.](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Välj **Spara**.

## <a name="specify-target-details"></a>Ange målinformation

1. På skärmen **information om migrerings mål** anger du anslutnings information för mål Azure Cosmos DB kontot. Det här kontot är det företablerade Azure Cosmos DB-API: et för MongoDB-kontot som du migrerar dina MongoDB-data till.

    ![Skärm bild som visar ange mål information.](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Välj **Spara**.

## <a name="map-to-target-databases"></a>Mappa till måldatabaser

1. På skärmen **Mappa till måldatabaser** mappar du käll- och måldatabasen för migreringen.

    Om mål databasen innehåller samma databas namn som käll databasen Azure Database Migration Service väljer mål databasen som standard.

    Om **skapa** visas bredvid databas namnet, indikerar det att Azure Database migration service inte har hittat mål databasen, och att tjänsten skapar databasen åt dig.

    I det här läget i migreringen, kan du [etablera ett dataflöde](../cosmos-db/set-throughput.md). I Azure Cosmos DB kan du etablera data flöde antingen på databas nivå eller individuellt för varje samling. Data flödet mäts i [enheter för programbegäran](../cosmos-db/request-units.md). Mer information om [Azure Cosmos DB-prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Skärm bild som visar mappning till mål databaser.](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Välj **Spara**.
3. På skärmen **Mängdinställning** expanderar du samlingslistan och granskar sedan listan över de samlingar som ska migreras.

    Azure Database Migration Service väljer automatiskt alla samlingar som finns på käll-MongoDB-instansen som inte finns på mål Azure Cosmos DB kontot. Om du vill migrera om samlingar som redan innehåller data måste du uttryckligen välja samlingarna i det här fönstret.

    Du kan ange hur många RU:er som du vill att samlingarna ska använda. Azure Database Migration Service föreslår smarta standardvärden baserat på samlingens storlek.

    > [!NOTE]
    > Utför migreringen och insamlingen av databasen parallellt. Om det behövs kan du använda flera instanser av Azure Database Migration Service för att påskynda körningen.

    Du kan också ange en shardnyckel för att kunna utnyttja [partitionering i Azure Cosmos DB](../cosmos-db/partitioning-overview.md) för optimal skalbarhet. Granska de [bästa metoderna för att välja en Shard/partitionsnyckel](../cosmos-db/partitioning-overview.md#choose-partitionkey).

    ![Skärm bild som visar hur du väljer samlings tabeller.](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Välj **Spara**.

5. På sidan **Migreringssammanfattning**, i textrutan **Aktivitetsnamn** anger du ett namn på migreringsaktiviteten.

    ![Skärm bild som visar nigration-sammanfattningen.](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Köra migreringen

Välj **Kör migrering**. Fönstret migrera aktivitet visas och aktivitetens status har **inte startats**.

![Skärm bild som visar aktivitets status.](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Övervaka migreringen

På skärmen migrerings aktivitet väljer du **Uppdatera** för att uppdatera skärmen tills migreringens status visas som **slutförd**.

> [!NOTE]
> Du kan välja aktiviteten för att få information om flytt mått på databas-och samlings nivå.

![Screnshot som visar aktivitets statusen slutförd.](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-azure-cosmos-db"></a>Verifiera data i Azure Cosmos DB

När migreringen är klar kan du kontrol lera Azure Cosmos DB kontot för att kontrol lera att alla samlingar har migrerats.

![Skärm bild som visar var du kan kontrol lera Azure Cosmos DB-kontot för att kontrol lera att alla samlingar har migrerats.](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Optimering efter migrering

När du har migrerat data som lagras i MongoDB-databasen till Azure Cosmos DB API för MongoDB kan du ansluta till Azure Cosmos DB och hantera data. Du kan också utföra optimerings steg efter migreringen. Detta kan inkludera optimering av indexerings principen, uppdatera standard konsekvens nivån eller konfigurera global distribution för ditt Azure Cosmos DB-konto. Mer information finns i [optimering av efter migrering](../cosmos-db/mongodb-post-migration.md).

## <a name="next-steps"></a>Nästa steg

Läs igenom migrations vägledningen för ytterligare scenarier i [Azure Database migration guide](https://datamigration.microsoft.com/).



