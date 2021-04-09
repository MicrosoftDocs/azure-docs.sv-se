---
title: Databas bearbetning utan server med Azure Cosmos DB och Azure Functions
description: Lär dig hur Azure Cosmos DB och Azure Functions kan användas tillsammans för att skapa program med händelse drivna Server lös data behandling.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/17/2019
ms.author: sngun
ms.openlocfilehash: 73a34cc27eaba33d04f4d31585c7f494f58e7274
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93334085"
---
# <a name="serverless-database-computing-using-azure-cosmos-db-and-azure-functions"></a>Databas bearbetning utan server med hjälp av Azure Cosmos DB och Azure Functions
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Data behandling utan server är allt om möjligheten att fokusera på enskilda delar av logiken som är repeterbara och tillstånds lösa. Dessa delar kräver ingen infrastruktur hantering och de använder bara resurser för de sekunder eller millisekunder som de körs för. I kärnan av den server fria data bearbetningen är funktioner som är tillgängliga i Azures eko system genom att [Azure Functions](https://azure.microsoft.com/services/functions). Om du vill veta mer om andra miljöer utan server i Azure ser du [Server lösa på Azure](https://azure.microsoft.com/solutions/serverless/) -sidan. 

Med den interna integrationen mellan [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) och Azure Functions kan du skapa databas utlösare, indata bindningar och utgående bindningar direkt från ditt Azure Cosmos DB-konto. Med hjälp av Azure Functions och Azure Cosmos DB kan du skapa och distribuera händelse drivna appar utan server med låg latens åtkomst till RTF-data för en global användar bas.

## <a name="overview"></a>Översikt

Azure Cosmos DB och Azure Functions kan du integrera dina databaser och appar utan server på följande sätt:

* Skapa en händelse driven **Azure Functions-utlösare för Cosmos DB**. Den här utlösaren förlitar sig på att [ändra feed](change-feed.md) -strömmar för att övervaka dina Azure Cosmos-behållare. När ändringar görs i en behållare skickas ändrings flödes strömmen till utlösaren, som anropar Azure-funktionen.
* Du kan också binda en Azure-funktion till en Azure Cosmos-behållare med hjälp av en **Indatamask**. Data bindningar läser data från en behållare när en funktion körs.
* Bind en funktion till en Azure Cosmos-behållare med hjälp av en **utgående bindning**. Utgående bindningar skriver data till en behållare när en funktion slutförs.

> [!NOTE]
> För närvarande stöds Azure Functions utlösare, indata bindningar och utgående bindningar för Cosmos DB endast för användning med SQL-API: et. För alla andra Azure Cosmos DB-API: er ska du komma åt databasen från din funktion med hjälp av den statiska klienten för ditt API.


Följande diagram illustrerar var och en av dessa tre integreringar: 

:::image type="content" source="./media/serverless-computing-database/cosmos-db-azure-functions-integration.png" alt-text="Hur Azure Cosmos DB och Azure Functions integrera" border="false":::

Azure Functions utlösare, indata bindning och utgående bindning för Azure Cosmos DB kan användas i följande kombinationer:

* En Azure Functions-utlösare för Cosmos DB kan användas med en utgående bindning till en annan Azure Cosmos-behållare. När en funktion utför en åtgärd på ett objekt i ändrings flödet kan du skriva den till en annan behållare (och skriva den till samma behållare som den kom ifrån skulle skapa en rekursiv loop). Du kan också använda en Azure Functions-utlösare för Cosmos DB för att effektivt migrera alla ändrade objekt från en behållare till en annan behållare, med hjälp av en utgående bindning.
* Data bindningar och utgående bindningar för Azure Cosmos DB kan användas i samma Azure-funktion. Detta fungerar bra om du vill hitta vissa data med den angivna bindningen, ändra den i Azure-funktionen och sedan spara den i samma behållare eller en annan behållare efter ändringen.
* En indata-bindning till en Azure Cosmos-behållare kan användas i samma funktion som en Azure Functions-utlösare för Cosmos DB, och kan även användas med eller utan en utgående bindning. Du kan använda den här kombinationen för att tillämpa uppdaterad valuta utbytes information (som hämtas med en indata-bindning till en Exchange-behållare) till ändrings flödet för nya beställningar i shopping vagns tjänsten. Den uppdaterade shopping vagns summan, med den aktuella valuta omvandlingen som används, kan skrivas till en tredje behållare med hjälp av en utgående bindning.

## <a name="use-cases"></a>Användningsfall

Följande användnings fall visar några sätt som du kan använda för att få ut mesta möjliga av dina Azure Cosmos DB data – genom att ansluta dina data till händelse drivna Azure Functions.

### <a name="iot-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>Användnings fall för IoT – Azure Functions utlösare och utgående bindning för Cosmos DB

I IoT-implementeringar kan du anropa en funktion när kontroll motor lampan visas i en ansluten bil.

**Implementering:** Använd en Azure Functions utlösare och utgående bindning för Cosmos DB

1. En **Azure Functions-utlösare för Cosmos DB** används för att utlösa händelser som rör Car-aviseringar, t. ex. kontroll motor lampan som kommer på en ansluten bil.
2. När kontroll motorn lyser skickas sensor data till Azure Cosmos DB.
3. Azure Cosmos DB skapar eller uppdaterar nya sensor data dokument. dessa ändringar strömmas sedan till Azure Functions-utlösaren för Cosmos DB.
4. Utlösaren anropas vid varje data ändring till sensor data insamlingen, eftersom alla ändringar strömmas via ändrings flödet.
5. Ett tröskel villkor används i funktionen för att skicka sensor data till garanti avdelningen.
6. Om temperaturen också är över ett visst värde skickas även en avisering till ägaren.
7. Den **utgående bindningen** för funktionen uppdaterar Car-posten i en annan Azure Cosmos-behållare för att lagra information om kontroll motor händelsen.

Följande bild visar koden som skrivits i Azure Portal för den här utlösaren.

:::image type="content" source="./media/serverless-computing-database/cosmos-db-trigger-portal.png" alt-text="Skapa en Azure Functions-utlösare för Cosmos DB i Azure Portal":::

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Ekonomiskt användnings fall – timer-utlösare och ingående bindning

I finansiella implementeringar kan du anropa en funktion när ett bank kontos saldo faller under en viss mängd.

**Implementering:** En timer-utlösare med en Azure Cosmos DB inleveranstransport

1. Med hjälp av en [timer-utlösare](../azure-functions/functions-bindings-timer.md)kan du hämta bank kontots saldobelopp som lagras i en Azure Cosmos-behållare vid tidsinställda intervall med hjälp av en **data bindning**.
2. Om saldot är lägre än tröskelvärdet för den låga balansen som användaren har angett följer du upp med en åtgärd från Azure-funktionen.
3. Utgående bindning kan vara en [SendGrid-integrering](../azure-functions/functions-bindings-sendgrid.md) som skickar ett e-postmeddelande från ett tjänst konto till de e-postadresser som identifieras för var och en av de konton som är låga.

Följande bilder visar koden i Azure Portal för det här scenariot.

:::image type="content" source="./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png" alt-text="Index.js fil för en timer-utlösare för ett finansiellt scenario":::

:::image type="content" source="./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png" alt-text="Kör. CSX-fil för en timer-utlösare för ett finansiellt scenario":::

### <a name="gaming-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>Användnings fall för spel – Azure Functions utlösare och utgående bindning för Cosmos DB 

När en ny användare skapas i spel kan du söka efter andra användare som kan känna till dem med hjälp av [API: et för Azure Cosmos DB Gremlin](graph-introduction.md). Du kan sedan skriva resultatet till en Azure Cosmos DB eller SQL-databas för enkel hämtning.

**Implementering:** Använd en Azure Functions utlösare och utgående bindning för Cosmos DB

1. Genom att använda en Azure Cosmos DB [diagram databas](graph-introduction.md) för att lagra alla användare kan du skapa en ny funktion med en Azure Functions-utlösare för Cosmos dB. 
2. När en ny användare infogas, anropas funktionen och sedan lagras resultatet med hjälp av en **utgående bindning**.
3. Funktionen frågar graf-databasen för att söka efter alla användare som är direkt relaterade till den nya användaren och returnerar den data uppsättningen till funktionen.
4. Dessa data lagras sedan i en Azure Cosmos DB som sedan enkelt kan hämtas av alla klient program som visar den nya användaren.

### <a name="retail-use-case---multiple-functions"></a>Exempel på detalj handels användning – flera funktioner

När en användare lägger till ett objekt i sin varukorg i detalj handels implementeringar har du nu möjlighet att skapa och anropa funktioner för valfria affärs pipeline-komponenter.

**Implementering:** Flera Azure Functions-utlösare för Cosmos DB lyssnar på en behållare

1. Du kan skapa flera Azure Functions genom att lägga till Azure Functions utlösare för Cosmos DB till varje, som lyssnar på samma ändrings flöde för shopping vagns data. Observera att när flera funktioner lyssnar på samma ändrings flöde krävs en ny låne samling för varje funktion. Mer information om låne samlingar finns i [förstå bibliotek för ändrings flödes processor](change-feed-processor.md).
2. Varje gång ett nytt objekt läggs till i en användares Shopping vagn, anropas varje funktion oberoende av ändrings flödet från behållaren kundvagn.
   * En funktion kan använda innehållet i den aktuella korgen för att ändra visningen av andra objekt som användaren kan vara intresse rad av.
   * En annan funktion kan uppdatera inventerings summor.
   * En annan funktion kan skicka kund information för vissa produkter till marknadsförings avdelningen, som skickar dem en kampanj post. 

     Alla avdelningar kan skapa en Azure Functions för Cosmos DB genom att lyssna på ändrings flödet och se till att de inte fördröjer kritiska bearbetnings händelser i processen.

I alla dessa användnings fall, eftersom funktionen har frikopplat själva appen, behöver du inte skapa nya app-instanser hela tiden. I stället skapar Azure Functions enskilda funktioner för att slutföra diskreta processer vid behov.

## <a name="tooling"></a>Verktyg

Intern integrering mellan Azure Cosmos DB och Azure Functions finns i Azure Portal och i Visual Studio 2019.

* I Azure Functions-portalen kan du skapa en utlösare. Instruktioner för snabb start finns i [skapa en Azure Functions-utlösare för Cosmos db i Azure Portal](../azure-functions/functions-create-cosmos-db-triggered-function.md).
* I Azure Cosmos DB Portal kan du lägga till en Azure Functions-utlösare för Cosmos DB till en befintlig Azure Function-app i samma resurs grupp.
* I Visual Studio 2019 kan du skapa utlösaren med hjälp av [Azure Functions verktyg](../azure-functions/functions-develop-vs.md):

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Varför ska du välja Azure Functions-integrering för Server lös data behandling?

Azure Functions ger möjlighet att skapa skalbara arbets enheter eller korta delar av logik som kan köras på begäran, utan etablering eller hantering av infrastruktur. Genom att använda Azure Functions behöver du inte skapa en fullständig app för att svara på ändringar i din Azure Cosmos-databas, men du kan skapa små återanvändbara funktioner för vissa aktiviteter. Dessutom kan du också använda Azure Cosmos DB data som indata eller utdata till en Azure-funktion som svar på händelse som till exempel en HTTP-begäran eller en tids aktive rad utlösare.

Azure Cosmos DB är den rekommenderade databasen för din server lös data behandlings arkitektur av följande anledningar:

* **Omedelbar åtkomst till alla dina data**: du har detaljerad åtkomst till alla värden som lagras eftersom Azure Cosmos DB [automatiskt indexerar](index-policy.md) alla data som standard och gör dessa index omedelbart tillgängliga. Det innebär att du kan fråga, uppdatera och lägga till nya objekt i databasen och få omedelbar åtkomst via Azure Functions.

* **Schema** lös. Azure Cosmos DB är schema lös – så att det är unikt att hantera data utdata från en Azure-funktion. Detta "hanterar alla"-tillvägagångs sätt gör det enkelt att skapa en rad funktioner som alla utdata till Azure Cosmos DB.

* **Skalbart data flöde**. Data flödet kan skalas upp och ned direkt i Azure Cosmos DB. Om du har hundratals eller tusentals funktioner som frågar och skriver till samma behållare kan du skala upp dina [ru/s](request-units.md) för att hantera belastningen. Alla funktioner kan arbeta parallellt med din allokerade RU/s och dina data garanteras vara [konsekventa](consistency-levels.md).

* **Global replikering**. Du kan replikera Azure Cosmos DB data över [hela världen](distribute-data-globally.md) för att minska svars tiden, så att du kan hitta data som ligger närmast var användarna finns. Precis som med alla Azure Cosmos DB-frågor läser data från händelse drivna utlösare data från den Azure Cosmos DB som är närmast användaren.

Om du vill integrera med Azure Functions för att lagra data och inte behöver djup indexering eller om du behöver lagra bifogade filer och mediefiler, kan [Azure Blob Storage-utlösaren](../azure-functions/functions-bindings-storage-blob.md) vara ett bättre alternativ.

Fördelar med Azure Functions: 

* **Händelse driven**. Azure Functions är händelse drivna och kan lyssna på en ändrings flöde från Azure Cosmos DB. Det innebär att du inte behöver skapa lyssnings logik. du behåller bara ett öga för de ändringar du lyssnar på. 

* **Inga gränser**. Funktionerna körs parallellt och tjänsten ökar så många som du behöver. Du anger parametrarna.

* **Lämpligt för snabba uppgifter**. Tjänsten ökar nya instanser av funktioner när en händelse utlöses och stängs så snart funktionen slutförs. Du betalar bara för den tid som dina funktioner körs.

Om du inte är säker på om Flow, Logic Apps, Azure Functions eller WebJobs är bäst för din implementering, se [Välj mellan Flow, Logic Apps, Functions och WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="next-steps"></a>Nästa steg

Nu ska vi ansluta Azure Cosmos DB och Azure Functions för verkliga: 

* [Skapa en Azure Functions-utlösare för Cosmos DB i Azure Portal](../azure-functions/functions-create-cosmos-db-triggered-function.md)
* [Skapa en HTTP-utlösare i Azure Functions med en Azure Cosmos DB-indatabindning](../azure-functions/functions-bindings-cosmosdb.md?tabs=csharp)
* [Azure Cosmos DB bindningar och utlösare](../azure-functions/functions-bindings-cosmosdb-v2.md)
