---
title: Anslut Qlik Sense till Azure Cosmos DB och visualisera dina data
description: I den här artikeln beskrivs de steg som krävs för att ansluta Azure Cosmos DB till Qlik Sense och visualisera dina data.
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
author: SnehaGunda
ms.author: sngun
ms.topic: how-to
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 2401786a82b6a3e5bf6c6a893a8e7cd3656f3402
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996973"
---
# <a name="connect-qlik-sense-to-azure-cosmos-db-and-visualize-your-data"></a>Anslut Qlik Sense till Azure Cosmos DB och visualisera dina data
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Qlik Sense är ett verktyg för data visualisering som kombinerar data från olika källor i en enda vy. Qlik Sense indexerar varje möjlig relation i dina data så att du kan få omedelbara insikter om data. Du kan visualisera Azure Cosmos DB data med hjälp av Qlik Sense. I den här artikeln beskrivs de steg som krävs för att ansluta Azure Cosmos DB till Qlik Sense och visualisera dina data. 

> [!NOTE]
> Att ansluta Qlik Sense till Azure Cosmos DB stöds för närvarande endast för SQL API och Azure Cosmos DB s API för MongoDB-konton.

Du kan ansluta Qlik Sense till att Azure Cosmos DB med:

* Cosmos DB SQL-API med hjälp av ODBC-anslutaren.

* Azure Cosmos DB s API för MongoDB med hjälp av Qlik Sense MongoDB Connector (för närvarande i för hands version).

* Azure Cosmos DB s API för MongoDB och SQL API genom att använda REST API Connector i Qlik Sense.

* Cosmos DB Mongo DB-API med hjälp av gRPC-anslutaren för Qlik Core.
I den här artikeln beskrivs hur du ansluter till Cosmos DB SQL-API med hjälp av ODBC-anslutningen.

I den här artikeln beskrivs hur du ansluter till Cosmos DB SQL-API med hjälp av ODBC-anslutningen.

## <a name="prerequisites"></a>Förutsättningar

Innan du följer anvisningarna i den här artikeln ser du till att du har följande resurser klara:

* Hämta [Qlik Sense Desktop](https://www.qlik.com/us/try-or-buy/download-qlik-sense) eller konfigurera Qlik Sense i Azure genom [att installera Qlik Sense Marketplace-objektet](https://azuremarketplace.microsoft.com/marketplace/apps/qlik.qlik-sense).

* Hämta [video spel data](https://www.kaggle.com/gregorut/videogamesales), det här exempel data är i CSV-format. Du kommer att lagra dessa data i ett Cosmos DB konto och visualisera dem i Qlik Sense.

* Skapa ett Azure Cosmos DB SQL API-konto med hjälp av stegen som beskrivs i avsnittet [skapa ett konto](create-sql-api-dotnet.md#create-account) i snabb starts artikeln.

* [Skapa en databas och en samling](create-sql-api-java.md#add-a-container) – du kan använda Ställ in värdet för Collection-genomflödet på 1000 ru/s. 

* Läs in försäljnings data för exempel video spel till ditt Cosmos DB-konto. Du kan importera data med hjälp av Azure Cosmos DB verktyget datamigrering. du kan göra en [sekventiell](import-data.md#SQLSeqTarget) eller [Mass import](import-data.md#SQLBulkTarget) av data. Det tar cirka 3-5 minuter för data att importeras till Cosmos DB-kontot.

* Hämta, installera och konfigurera ODBC-drivrutinen genom att följa anvisningarna i artikeln [Anslut till Cosmos dB med ODBC-drivrutin](odbc-driver.md) . Video spelets data är en enkel data uppsättning och du behöver inte redigera schemat, Använd inte standard schemat för samlings mappning.

## <a name="connect-qlik-sense-to-cosmos-db"></a>Anslut Qlik Sense till Cosmos DB

1. Öppna Qlik Sense och välj **Skapa ny app**. Ange ett namn för din app och välj **skapa**.

   :::image type="content" source="./media/visualize-qlik-sense/create-new-qlik-sense-app.png" alt-text="Skapa en ny Qlik Sense-app":::

2. När den nya appen har skapats väljer du **Öppna App** och väljer **Lägg till data från filer och andra källor**. 

3. Från data källorna väljer du **ODBC** för att öppna fönstret ny anslutnings konfiguration. 

4. Växla till **användar-DSN** och välj den ODBC-anslutning som du skapade tidigare. Ange ett namn för anslutningen och välj **skapa**. 

   :::image type="content" source="./media/visualize-qlik-sense/create-new-connection.png" alt-text="Skapa en ny anslutning":::

5. När du har skapat anslutningen kan du välja databasen, samlingen där video spelets data finns och sedan förhandsgranska den.

   :::image type="content" source="./media/visualize-qlik-sense/choose-database-and-collection.png" alt-text="Välj databas och samling"::: 

6. Välj sedan **Lägg till data** för att läsa in data till Qlik Sense. När du har läst in data till Qlik Sense kan du generera insikter och analysera data. Du kan antingen använda insikter eller bygga din egen app för att utforska video spels försäljningen. Följande bild visar 

   :::image type="content" source="./media/visualize-qlik-sense/visualize-data.png" alt-text="Visualisera data":::

### <a name="limitations-when-connecting-with-odbc"></a>Begränsningar vid anslutning med ODBC 

Cosmos DB är en schema fri distribuerad databas med driv rutiner som är kopplade till utvecklings behoven. ODBC-drivrutinen kräver en databas med schema för att härleda kolumner, deras data typer och andra egenskaper. Den vanliga SQL-frågan eller DML-syntaxen med relations funktioner kan inte användas för Cosmos DB SQL API eftersom SQL API inte är ANSI SQL. På grund av det här skälet översätts SQL-uttrycken som utfärdats genom ODBC-drivrutinen till Cosmos DB-Specific SQL-syntax som inte har motsvarigheter till alla konstruktioner. För att förhindra dessa översättnings problem måste du använda ett schema när du konfigurerar ODBC-anslutningen. Artikeln [Anslut till ODBC-drivrutinen](odbc-driver.md) innehåller förslag och metoder som hjälper dig att konfigurera schemat. Se till att skapa den här mappningen för varje databas/samling i Cosmos DB-kontot.

## <a name="next-steps"></a>Nästa steg

Om du använder ett annat visualiserings verktyg, till exempel Power BI, kan du ansluta till det med hjälp av instruktionerna i följande dokument:

* [Visualisera Cosmos DB data med hjälp av Power BI koppling](powerbi-visualize.md)
