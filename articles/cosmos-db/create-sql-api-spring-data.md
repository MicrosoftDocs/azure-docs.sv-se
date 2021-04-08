---
title: Snabb start – Använd våren data Azure Cosmos DB v3 för att skapa en dokument databas med hjälp av Azure Cosmos DB
description: I den här snabb starten presenteras ett data Azure Cosmos DB v3-kod exempel som du kan använda för att ansluta till och ställa frågor till Azure Cosmos DB SQL API
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/06/2020
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: f31eb0fa6dbb881f7a09b21b9dd4842fdfd291f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93090298"
---
# <a name="quickstart-build-a-spring-data-azure-cosmos-db-v3-app-to-manage-azure-cosmos-db-sql-api-data"></a>Snabb start: bygga en våren data Azure Cosmos DB v3-app för att hantera Azure Cosmos DB SQL API-data
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring-data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

I den här snabb starten skapar och hanterar du ett Azure Cosmos DB SQL API-konto från Azure Portal och genom att använda en våren data Azure Cosmos DB v3-app som klonas från GitHub. Först skapar du ett Azure Cosmos DB SQL-API-konto med hjälp av Azure Portal och skapar sedan en våren Boot-app med hjälp av våren-data Azure Cosmos DB v3-anslutning och lägger sedan till resurser till ditt Cosmos DB konto med hjälp av våren Boot-programmet. Azure Cosmos DB är en databas tjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckel värdes-och Graf-databaser med globala funktioner för distribution och horisontell skalning.

> [!IMPORTANT]  
> Den här viktig information gäller för version 3 av vår data Azure Cosmos DB. [Viktig information om version 2 finns här](sql-api-sdk-java-spring-v2.md). 
>
> Våren data Azure Cosmos DB stöder bara SQL-API: et.
>
> I de här artiklarna finns information om våren-data på andra Azure Cosmos DB API: er:
> * [Våren-data för Apache Cassandra med Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Fjädra data MongoDB med Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Fjädra data Gremlin med Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Eller [prova Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration. Du kan också använda [Azure Cosmos DB emulatorn](https://aka.ms/cosmosdb-emulator) med en URI `https://localhost:8081` och nyckeln `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` .
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Peka `JAVA_HOME` miljö variabeln till den mapp där JDK är installerad.
- Ett [binärt maven-Arkiv](https://maven.apache.org/download.cgi). Kör `apt-get install maven` för att installera maven på Ubuntu.
- [Git](https://www.git-scm.com/downloads). Kör `sudo apt-get install git` för att installera git på Ubuntu.

## <a name="introductory-notes"></a>Inledande anmärkningar

*Strukturen för ett Cosmos DB konto.* Oavsett API eller programmeringsspråk innehåller ett Cosmos DB- *konto* noll eller flera *databaser*, en *databas* (dB) innehåller noll eller flera *behållare*, och en *behållare* innehåller noll eller flera objekt, som visas i diagrammet nedan:

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Azure Cosmos Account-entiteter" border="false":::

Du kan läsa mer om databaser, behållare och objekt [här.](account-databases-containers-items.md) Några viktiga egenskaper definieras på nivån för behållaren, bland dem med det *etablerade data flödet* och *partitionsnyckel*. 

Det etablerade data flödet mäts i *ru: er*(Request units) som har ett penning pris och är en väsentlig avgörande faktor för kontots drift kostnad. Det etablerade data flödet kan väljas per behållar kornig het eller granularitet per databas, men data flödes specifikationen på behållare nivå är vanligt vis lämplig. Du kan läsa mer om data flödes etablering [här.](set-throughput.md)

När objekt infogas i en Cosmos DB-behållare blir databasen vågrätt genom att lägga till mer lagrings utrymme och data bearbetning för att hantera begär Anden. Lagrings-och beräknings kapaciteten läggs till i diskreta enheter som kallas *partitioner*, och du måste välja ett fält i dina dokument för att vara den partitionsnyckel som mappar varje dokument till en partition. Hur partitioner hanteras är att varje partition tilldelas en ungefär lika stor sektor från intervallet av nyckel värden. Därför bör du välja en partitionsnyckel som är relativt slumpmässig eller jämnt distribuerad. Annars kommer vissa partitioner att se betydligt fler begär Anden (*het partition*) medan andra partitioner ser betydligt färre begär Anden (*kall partition*) och detta bör undvikas. Du kan lära dig mer om partitionering [här](partitioning-overview.md).

## <a name="create-a-database-account"></a>Skapa ett databaskonto

Innan du kan börja skapa en dokumentdatabas måste du skapa ett SQL-API-konto med Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Lägga till en container

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Lägga till exempeldata

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Fråga dina data

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi övergå till att arbeta med kod. Vi ska klona en SQL API-app från GitHub, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

```bash
git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started.git
```

## <a name="review-the-code"></a>Granska koden

Det här är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Annars kan du gå vidare till [Köra appen](#run-the-app). 

### <a name="application-configuration-file"></a>Program konfigurations fil

Här visas en demonstration av hur vår start-och våren-data förbättrar användar upplevelsen – processen att etablera en Cosmos-klient och ansluta till Cosmos-resurser är nu config i stället för kod. Vid start av start fjädern hanterar alla dessa exempel med inställningarna i **programmet. Properties**:

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

När du skapar ett Azure Cosmos DB konto, en databas och en behållare är det bara att fylla i de tomma i konfigurations filen och våren Boot/våren data gör följande: (1) skapa en underliggande Java SDK- `CosmosClient` instans med URI och nyckel och (2) Anslut till databasen och behållaren. Allt är klart – **inga fler resurs hanterings koder!**

### <a name="java-source"></a>Java-källa

Våren-datavärdet – Lägg till kommer också från det enkla, rena, standardiserade och plattforms oberoende gränssnittet för drift på data lager. Genom att bygga vidare på våren data GitHub-exemplet som är länkat ovan visas CRUD och fråge exempel för att ändra Azure Cosmos DB dokument med fjäder data Azure Cosmos DB.

* Skapa och uppdatera objekt med hjälp av- `save` metoden.

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]
   
* Punkt – läser med den härledda Frågeparametern som definierats i lagrings platsen. `findByIdAndLastName`Utför punkt-läsningar för `UserRepository` . De fält som anges i metod namnet gör att våren-data kan köra en punkt som definieras av `id` `lastName` fälten och:

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Read)]

* Objektet tas bort med `deleteAll` :

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

* Härledd fråga baserat på databas metod namn. Våren-data implementerar `UserRepository` `findByFirstName` metoden som en Java SDK SQL-fråga i `firstName` fältet (den här frågan kunde inte implementeras som en punkt-läsning):

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

## <a name="run-the-app"></a>Kör appen

Gå nu tillbaka till Azure-portalen för att hämta information om din anslutningssträng och starta appen med din slutpunktsinformation. På så vis kan appen kommunicera med den värdbaserade databasen.

1. I git-terminalfönstret `cd` till exempelkodsmappen.

    ```bash
    cd azure-spring-data-cosmos-java-sql-api-getting-started/azure-spring-data-cosmos-java-getting-started/
    ```

2. I git-terminalfönstret använder du följande kommando för att installera de nödvändiga Azure Cosmos DB-paketen för fjäder data.

    ```bash
    mvn clean package
    ```

3. I git-terminalfönstret använder du följande kommando för att starta programmet våren data Azure Cosmos DB:

    ```bash
    mvn spring-boot:run
    ```
    
4. Appen läser in **Application. Properties** och ansluter resurserna i ditt Azure Cosmos DB-konto.
5. Appen utför punkt CRUD åtgärder som beskrivs ovan.
6. Appen utför en härledd fråga.
7. Appen tar inte bort dina resurser. Gå tillbaka till portalen för att [Rensa resurserna](#clean-up-resources) från ditt konto om du vill undvika kostnader.

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Azure Cosmos DB SQL API-konto, skapar en dokument databas och behållare med hjälp av Datautforskaren och kör en våren data-app för att göra samma sak program mässigt. Nu kan du importera ytterligare data till ditt Azure Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)