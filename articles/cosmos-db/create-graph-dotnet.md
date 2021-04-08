---
title: 'Bygg en Azure Cosmos DB .NET Framework, kärn program med hjälp av Gremlin-API: et'
description: Anger ett .NET Framework/Core-kodexempel som du kan använda för att ansluta till och fråga en Azure Cosmos DB
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 02/21/2020
ms.author: chrande
ms.custom: devx-track-dotnet
ms.openlocfilehash: 1953f4a21df6f550320592fbe009834a7b573887
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93360538"
---
# <a name="quickstart-build-a-net-framework-or-core-application-using-the-azure-cosmos-db-gremlin-api-account"></a>Snabb start: bygga ett .NET Framework-eller kärn program med hjälp av Azure Cosmos DB Gremlin API-konto
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

> [!div class="op_single_selector"]
> * [Gremlin-konsol](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

Den här snabb starten visar hur du skapar ett Azure Cosmos DB [GREMLIN API](graph-introduction.md) -konto, databas och Graf (behållare) med hjälp av Azure Portal. Sedan skapar och kör du en konsolapp med drivrutinen [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) (öppen källkod).  

## <a name="prerequisites"></a>Förutsättningar

Om du inte redan har Visual Studio 2019 installerat kan du hämta och använda den **kostnads fria** [versionen av Visual Studio 2019 community](https://www.visualstudio.com/downloads/). Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Lägga till en graf

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en Gremlin-API-app från GitHub, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna en kommandotolk, skapa en ny mapp som heter git-samples och stäng sedan kommandotolken.

    ```bash
    md "C:\git-samples"
    ```

2. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till den nya mappen där du vill installera exempelappen.

    ```bash
    cd "C:\git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-gremlindotnet-getting-started.git
    ```

4. Öppna sedan Visual Studio och öppna lösningsfilen.

5. Återställ NuGet-paketen i projektet. Det bör inkludera Gremlin.Net-drivrutinen och Newtonsoft.Json-paketet.


6. Du kan också installera Gremlin.Net-drivrutinen manuellt med Nuget-pakethanteraren eller [nuget-kommandoradsverktyget](/nuget/install-nuget-client-tools): 

    ```bash
    nuget install Gremlin.Net
    ```

## <a name="review-the-code"></a>Granska koden

Det här är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Annars kan du gå vidare till [Uppdatera din anslutningssträng](#update-your-connection-string). 

Följande kodfragment är alla hämtade från filen Program.cs.

* Ange anslutnings parametrar baserat på det konto som du skapade ovan: 

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="configureConnectivity":::

* De Gremlin-kommandon som ska köras visas i en ord lista:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineQueries":::

* Skapa ett nytt `GremlinServer` och `GremlinClient` anslutnings objekt med de parametrar som anges ovan:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineClientandServerObjects":::

* Kör varje Gremlin-fråga med hjälp av `GremlinClient` objektet med en asynkron uppgift. Du kan läsa Gremlin-frågorna från ord listan som definierades i föregående steg och köra dem. Hämta sedan resultatet och Läs värdena, som är formaterade som en ord lista, med hjälp av `JsonSerializer` klassen från Newtonsoft.Jsi paketet:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="executeQueries":::

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure Portal för att hämta information om din anslutningssträng och kopiera den till appen.

1. Öppna [Azure Portal](https://portal.azure.com/) och navigera till ditt Graph-databaskonto. På fliken **Översikt** ser du två slutpunkter: 
 
   **.NET SDK-URI** – det här värdet används när du ansluter till diagrammets konto med hjälp av Microsoft. Azure. graphs-biblioteket. 

   **Gremlin-slutpunkt** – Det här värdet används när du ansluter till Graph-kontot via biblioteket Gremlin.Net.

    :::image type="content" source="./media/create-graph-dotnet/endpoint.png" alt-text="Kopiera slutpunkten":::

   Om du vill köra det här exemplet kopierar du **slut punkt** svärdet för Gremlin, tar bort port numret i slutet, vilket är URI: n blir `https://<your cosmos db account name>.gremlin.cosmosdb.azure.com` . Slut punkt svärdet bör se ut så här `testgraphacct.gremlin.cosmosdb.azure.com`

1. Gå sedan till fliken **nycklar** och kopiera värdet för **primär nyckel** från Azure Portal. 

1. När du har kopierat URI: n och primär nyckeln för ditt konto sparar du dem i en ny miljö variabel på den lokala datorn som kör programmet. Ange miljövariabeln genom att öppna ett kommando tolks fönster och köra följande kommando. Se till att ersätta <Your_Azure_Cosmos_account_URI> och <Your_Azure_Cosmos_account_PRIMARY_KEY> värden.

   ```console
   setx Host "<your Azure Cosmos account name>.gremlin.cosmosdb.azure.com"
   setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
   ```

1. Öppna filen *program. cs* och uppdatera variablerna "databas och container" med databasen och behållaren (som också är graf-namn) som du skapade ovan.

    `private static string database = "your-database-name";` `private static string container = "your-container-or-graph-name";`

1. Spara filen Program.cs. 

Du har nu uppdaterat appen med all information som behövs för kommunikation med Azure Cosmos DB. 

## <a name="run-the-console-app"></a>Kör konsolappen

Tryck på Ctrl + F5 för att köra programmet. Programmet skriver ut både Gremlin-frågekommandona och resultatet i konsolen.

   Hörn och gränser som läggs till i tabellen visas i konsolfönstret. Stäng konsolfönstret genom att trycka på RETUR när skriptet har körts.

## <a name="browse-using-the-data-explorer"></a>Bläddra med datautforskaren

Nu kan du gå tillbaka till datautforskaren i Azure Portal och bläddra bland och ställa frågor mot dina nya grafdata.

1. Den nya databasen visas i fönstret Graphs (Diagram) i Datautforskaren. Expandera databasen och containernoderna och klicka sedan på **Diagram**.

2. Klicka på knappen **Använd filter** för att använda standardfrågan och visa alla hörn i diagrammet. Data som genereras av den här exempelappen visas i fönstret Graphs (Grafer).

    Du kan zooma in och ut i diagrammet, du kan expandera diagrammets skärmutrymme, lägga till ytterligare hörn och flytta hörn på skärmytan.

    :::image type="content" source="./media/create-graph-dotnet/graph-explorer.png" alt-text="Visa diagrammet i Datautforskaren på Azure-portalen":::

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du fått lära dig att skapa ett Azure Cosmos DB-konto, skapa en graf med datautforskaren och att köra en app. Nu kan du skapa mer komplexa frågor och implementera kraftfull logik för grafbläddring med Gremlin. 

> [!div class="nextstepaction"]
> [Fråga med hjälp av Gremlin](tutorial-query-graph.md)