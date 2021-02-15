---
title: Migrera MongoDB offline till Azure Cosmos DB API för MongoDB med hjälp av MongoDB inbyggda verktyg
description: Lär dig hur MongoDB-inbyggda verktyg kan användas för att migrera små data uppsättningar från MongoDB-instanser till Azure Cosmos DB
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 02/10/2021
ms.reviewer: sngun
ms.openlocfilehash: 2b52a9227e8bd487a8929df11047eef4672f7f4a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100421920"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-mongodb-native-tools"></a>Självstudie: Migrera MongoDB till Azure Cosmos DB s API för MongoDB offline med MongoDB inbyggda verktyg

Du kan använda inbyggda MongoDB-verktyg för att utföra en offline-migrering (eng ång slö sen) av databaser från en lokal eller moln instans av MongoDB till Azure Cosmos DB s API för MongoDB.

I den här guiden får du lära dig att:
> [!div class="checklist"]
>
> * Välj lämpligt internt MongoDB-verktyg för användnings fall
> * Köra migreringen.
> * Övervaka migreringen.
> * Kontrol lera att migreringen lyckades.

I den här självstudien migrerar du en data uppsättning i MongoDB som finns på en virtuell Azure-dator till Azure Cosmos DB s API för MongoDB med hjälp av MongoDB inbyggda verktyg. Inbyggda MongoDB-verktyg är en uppsättning binärfiler som underlättar data manipulation på en befintlig MongoDB-instans. Eftersom Azure Cosmos DB visar ett Mongo-API kan MongoDB-inbyggda verktyg infoga data i Azure Cosmos DB. Detta dokument fokuserar på att migrera data från en MongoDB-instans med hjälp av *mongoexport/mongoimport* eller *mongodump/mongorestore*. Eftersom de inbyggda verktygen ansluter till MongoDB med anslutnings strängar kan du köra verktygen var som helst, men vi rekommenderar att du kör dessa verktyg i samma nätverk som MongoDB-instansen för att undvika brand Väggs problem. 

Inbyggda MongoDB-verktyg kan bara flytta data så snabbt som värd maskin varan tillåter; de inbyggda verktygen kan vara den enklaste lösningen för små data uppsättningar där total migrerings tid inte är ett problem. [MongoDB Spark Connector](https://docs.mongodb.com/spark-connector/current/), [Azure Data Migration service (DMS)](../dms/tutorial-mongodb-cosmos-db.md)eller [Azure Data Factory (ADF)](../data-factory/connector-azure-cosmos-db-mongodb-api.md) kan vara bättre alternativ om du behöver en skalbar pipeline för migrering.

Om du inte har konfigurerat någon MongoDB-källa, kan du läsa artikeln [Installera och konfigurera MongoDB på en virtuell Windows-dator i Azure](../virtual-machines/windows/install-mongodb.md).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* [Slutför stegen innan migreringen](../cosmos-db/mongodb-pre-migration.md) , till exempel genom att uppskatta data flödet, välja en partitionsnyckel och indexerings principen.
* [Skapa ett Azure Cosmos DB-API för MongoDB-konto](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Logga in på din MongoDB-instans
    * [Hämta och installera inbyggda MongoDB-verktyg från den här länken](https://www.mongodb.com/try/download/database-tools).
        * **Se till att versionen av MongoDB Native tools matchar din befintliga MongoDB-instans.**
        * Om MongoDB-instansen har en annan version än Azure Cosmos DB Mongo-API: et, **installerar du båda MongoDB inbyggda verktyg-versionerna och använder lämplig verktygs version för MongoDB och Azure Cosmos DB MONGO API.**
    * Lägg till en användare med `readWrite` behörigheter, om det inte redan finns en. Senare i den här självstudien anger du användar namn/lösen ord för *mongoexport* -och *mongodump* -verktygen.

## <a name="configure-azure-cosmos-db-server-side-retries"></a>Konfigurera Azure Cosmos DB server sidans nya försök

Kunder som migrerar från MongoDB till Azure Cosmos DB dra nytta av resurs styrnings funktioner som garanterar möjligheten att fullt ut använda dina etablerade RU/s-genomflöde. Azure Cosmos DB kan begränsa en specifik begäran under migreringen om denna begäran överskrider den behållare som etablerade RU/s; sedan måste begäran göras om. Tiden för fördröjning i nätverks hoppet mellan Migreringsverktyg och Azure Cosmos DB påverkar den totala svars tiden för denna begäran. Dessutom kan MongoDB inbyggda verktyg inte hantera nya försök. Funktionen *försök igen på Server sidan* på Azure Cosmos DB tillåter att tjänsten fångar upp begränsnings felkoder och gör ett nytt försök med mycket kortare svars tid för svars tider, vilket dramatiskt förbättrar svars tiderna för begäran. I MongoDB inbyggda verktyg är behovet av att hantera återförsök minimerat, vilket kan påverka din upplevelse positivt under migreringen.

Du hittar funktionen för nya försök på Server sidan på bladet *funktioner* på Azure Cosmos DBS portalen

![Skärm bild av MongoDB SSR-funktionen.](../dms/media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

Och om den är *inaktive rad* rekommenderar vi att du aktiverar det enligt nedan

![Skärm bild av MongoDB SSR enable.](../dms/media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="choose-the-proper-mongodb-native-tool"></a>Välj rätt internt MongoDB-verktyg

![Diagram över valet av det bästa MongoDB-inbyggda verktyget.](media/tutorial-mongotools-cosmos-db/mongodb-native-tool-selection-table.png)

* *mongoexport/mongoimport* är det bästa paret Migreringsverktyg för migrering av en delmängd av din MongoDB-databas.
    * *mongoexport* exporterar dina befintliga data till en läsbar JSON-eller CSV-fil. *mongoexport* tar ett argument som anger den delmängd av dina befintliga data som ska exporteras. 
    * *mongoimport* öppnar en JSON-eller CSV-fil och infogar innehållet i mål databas instansen (Azure Cosmos db i det här fallet.). 
    * Observera att JSON och CSV inte är kompakta format. Du kan ådra dig överskott på nätverks avgifter som *mongoimport* skickar data till Azure Cosmos dB.
* *mongodump/mongorestore* är det bästa paret Migreringsverktyg för att migrera hela MongoDB-databasen. Det kompakta BSON-formatet kommer att effektivisera användningen av nätverks resurser när data infogas i Azure Cosmos DB.
    * *mongodump* exporterar dina befintliga data som en bson-fil.
    * *mongorestore* importerar din bson-fildump till Azure Cosmos dB.
* Som fristående – om du bara har en liten JSON-fil som du vill importera till Azure Cosmos DB Mongo API, är *mongoimport* -verktyget en snabb lösning för att mata in data.

## <a name="collect-the-azure-cosmos-db-mongo-api-credentials"></a>Samla in Azure Cosmos DB Mongo API-autentiseringsuppgifter

Azure Cosmos DB Mongo-API ger kompatibla autentiseringsuppgifter som MongoDB inbyggda verktyg kan använda. Du måste ha dessa autentiseringsuppgifter för åtkomst för att kunna migrera data till Azure Cosmos DB Mongo-API. Så här hittar du autentiseringsuppgifterna:

1. Öppna Azure-portalen
1. Navigera till ditt Azure Cosmos DB Mongo API-konto
1. I det vänstra navigerings fältet väljer du bladet *anslutnings sträng* och du bör se en skärm som liknar den nedan:

    ![Skärm bild av Azure Cosmos DB autentiseringsuppgifter.](media/tutorial-mongotools-cosmos-db/cosmos-mongo-credentials.png)

    * *Värd* -Azure Cosmos DB slut punkt fungerar som ett MongoDB-värdnamn
    * *Port* – när MongoDB inbyggda verktyg ansluter till Azure Cosmos DB måste du ange den här porten explicit
    * *Användar* namn – prefixet för Azure Cosmos DB slut punktens domän namn fungerar som MongoDB-användarnamnet
    * *Lösen ord* – Azure Cosmos DB huvud nyckeln fungerar som MongoDB-lösenord
    * Observera också det *SSL* -fält som är `true` – det inbyggda verktyget MONGODB **måste** Aktivera SSL när du skriver data till Azure Cosmos DB

## <a name="perform-the-migration"></a>Utför migreringen

1. Välj vilken eller vilka databaser och samlingar som du vill migrera. I det här exemplet ska vi migrera *Frågeredigeraren* i *EDX* -databasen från MongoDB till Azure Cosmos dB.

Resten av det här avsnittet vägleder dig genom att använda de verktyg som du valde i föregående avsnitt.

### <a name="mongoexportmongoimport"></a>*mongoexport/mongoimport*

1. Om du vill exportera data från källan MongoDB-instansen öppnar du en terminal på MongoDB instance Machine. Om det är en Linux-dator skriver du

    `mongoexport --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx.json`

    Den körbara filen är i Windows `mongoexport.exe` . *Värd*, *port*, *användar namn* och *lösen ord* ska fyllas i baserat på egenskaperna för din befintliga instans av MongoDB-databasen. 
    
    Du kan också välja att endast exportera en delmängd av MongoDB-datauppsättningen. Ett sätt att göra detta är genom att lägga till ett ytterligare filter argument:
    
    `mongoexport --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx.json --query '{"field1":"value1"}'`

    Endast dokument som matchar filtret `{"field1":"value1"}` kommer att exporteras.

    När du har kört anropet bör du se att en `edx.json` fil skapas:

    ![Skärm bild av mongoexport-anrop.](media/tutorial-mongotools-cosmos-db/mongo-export-output.png)
1. Du kan använda samma Terminal för att importera `edx.json` till Azure Cosmos dB. Om du kör `mongoimport` på en Linux-dator skriver du

    `mongoimport --host HOST:PORT -u USERNAME -p PASSWORD --db edx --collection importedQuery --ssl --type json --writeConcern="{w:0}" --file edx.json`

    Den körbara filen är i Windows `mongoimport.exe` . *Värd*, *port*, *användar namn* och *lösen ord* ska fyllas i baserat på de Azure Cosmos DB autentiseringsuppgifter som du samlade in tidigare. 
1. **Övervaka** terminalen utdata från *mongoimport*. Du bör se att den skriver ut text rader till den terminal som innehåller uppdateringar av migrerings statusen:

    ![Skärm bild av mongoimport-anrop.](media/tutorial-mongotools-cosmos-db/mongo-import-output.png)    

1. Granska slutligen Azure Cosmos DB för att **Verifiera** att migreringen lyckades. Öppna Azure Cosmos DB Portal och gå till Datautforskaren. Du bör se (1) att en *EDX* -databas med en *importedQuery* -samling har skapats, och (2) om du bara har exporterat en delmängd data, ska *importedQuery* *bara* innehålla dokument som matchar önskad delmängd av data. I exemplet nedan matchade bara ett dokument filtret `{"field1":"value1"}` :

    ![Skärm bild av Cosmos DB data verifiering.](media/tutorial-mongotools-cosmos-db/mongo-review-cosmos.png)    

### <a name="mongodumpmongorestore"></a>*mongodump/mongorestore*

1. Om du vill skapa en BSON-datadump för din MongoDB-instans öppnar du en terminal på MongoDB instance Machine. Om det är en Linux-dator skriver du

    `mongodump --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx-dump`

    *Värd*, *port*, *användar namn* och *lösen ord* ska fyllas i baserat på egenskaperna för din befintliga instans av MongoDB-databasen. Du bör se att en `edx-dump` katalog skapas och att katalog strukturen i `edx-dump` reproducerar-resursens hierarki (databas och samlings struktur) för din käll MongoDB-instans. Varje samling representeras av en BSON-fil:

    ![Skärm bild av mongodump-anrop.](media/tutorial-mongotools-cosmos-db/mongo-dump-output.png)
1. Du kan använda samma Terminal för att återställa innehållet i `edx-dump` Azure Cosmos dB. Om du kör `mongorestore` på en Linux-dator skriver du

    `mongorestore --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection importedQuery --ssl edx-dump/edx/query.bson`

    Den körbara filen är i Windows `mongorestore.exe` . *Värd*, *port*, *användar namn* och *lösen ord* ska fyllas i baserat på de Azure Cosmos DB autentiseringsuppgifter som du samlade in tidigare. 
1. **Övervaka** terminalen utdata från *mongorestore*. Du bör se att den skriver ut rader till Terminal-uppdateringen av migrerings statusen:

    ![Skärm bild av mongorestore-anrop.](media/tutorial-mongotools-cosmos-db/mongo-restore-output.png)    

1. Granska slutligen Azure Cosmos DB för att **Verifiera** att migreringen lyckades. Öppna Azure Cosmos DB Portal och gå till Datautforskaren. Du bör se (1) att en *EDX* -databas med en *importedQuery* -samling har skapats och (2) *importedQuery* ska innehålla *hela* data uppsättningen från käll samlingen:

    ![Skärm bild av verifiering av Cosmos DB mongorestore-data.](media/tutorial-mongotools-cosmos-db/mongo-review-cosmos-restore.png)    

## <a name="post-migration-optimization"></a>Optimering efter migrering

När du har migrerat data som lagras i MongoDB-databasen till Azure Cosmos DB s API för MongoDB, kan du ansluta till Azure Cosmos DB och hantera data. Du kan också utföra andra optimerings steg efter migrering, till exempel optimera indexerings principen, uppdatera standard konsekvens nivån eller konfigurera global distribution för ditt Azure Cosmos DB-konto. Mer information finns i artikeln [optimering efter migreringen](../cosmos-db/mongodb-post-migration.md) .

## <a name="additional-resources"></a>Ytterligare resurser

* [Information om Cosmos DB-tjänsten](https://azure.microsoft.com/services/cosmos-db/)
* [Dokumentation om MongoDB-databas verktyg](https://docs.mongodb.com/database-tools/)

## <a name="next-steps"></a>Nästa steg

* Se fler scenarier i migreringsvägledningen i Microsofts [Guide för databasmigrering](https://datamigration.microsoft.com/).