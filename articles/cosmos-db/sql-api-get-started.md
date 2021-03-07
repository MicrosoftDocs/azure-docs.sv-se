---
title: 'Självstudie: Bygg en .NET-konsol-app för att hantera data i Azure Cosmos DB SQL API-konto'
description: 'Självstudie: Lär dig hur du skapar Azure Cosmos DB SQL API-resurser med hjälp av ett C#-konsol program.'
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: kirankk
ms.custom: devx-track-csharp
ms.openlocfilehash: 2ece9ed5cb6eb6ea706db1ed615a64d001f2b17c
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102423963"
---
# <a name="tutorial-build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Självstudie: Bygg en .NET-konsol-app för att hantera data i Azure Cosmos DB SQL API-konto
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](./create-sql-api-java.md)
> * [Async Java](./create-sql-api-java.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Välkommen till själv studie kursen om att komma igång med Azure Cosmos DB SQL API. När du har genomfört den här självstudiekursen har du ett konsolprogram som skapar och skickar frågor till Azure Cosmos DB-resurser.

I den här självstudien används version 3,0 eller senare av [Azure Cosmos dB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos). Du kan arbeta med [.NET Framework eller .net Core](https://dotnet.microsoft.com/download).

Den här självstudiekursen omfattar:

> [!div class="checklist"]
>
> * Skapa och ansluta till ett Azure Cosmos-konto
> * Konfigurera ditt projekt i Visual Studio
> * Skapa en databas och en container
> * Lägga till objekt i containern
> * Fråga containern
> * Utföra åtgärder för att skapa, läsa, uppdatera och ta bort (CRUD) på objektet
> * Ta bort databasen

Har du inte tid? Men oroa dig inte! Den kompletta lösningen finns på [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). En snabbguide finns i avsnittet [Hämta den fullständiga lösningen till självstudien](#GetSolution).

Nu sätter vi igång!

## <a name="prerequisites"></a>Förutsättningar

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Steg 1: Skapa ett Azure Cosmos DB-konto

Nu ska vi skapa ett Azure Cosmos DB-konto. Hoppa över det här avsnittet om du redan har ett konto som du vill använda. Om du vill använda Azure Cosmos DB emulatorn följer du stegen i [Azure Cosmos DB emulatorn](local-emulator.md) för att konfigurera emulatorn. Gå sedan vidare till [steg 2: Konfigurera ditt Visual Studio-projekt](#SetupVS).

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="step-2-set-up-your-visual-studio-project"></a><a id="SetupVS"></a>Steg 2: Konfigurera ditt Visual Studio-projekt

1. Öppna Visual Studio och välj **skapa ett nytt projekt**.
1. I **skapa ett nytt projekt** väljer du **konsol program (.NET Framework)** för C# och väljer sedan **Nästa**.
1. Namnge projektet *CosmosGettingStartedTutorial* och välj sedan **skapa**.

    :::image type="content" source="./media/sql-api-get-started/configure-cosmos-getting-started-2019.png" alt-text="Konfigurera projektet":::

1. I **Solution Explorer** högerklickar du på det nya konsol programmet, som finns under din Visual Studio-lösning, och väljer **Hantera NuGet-paket**.
1. I **NuGet Package Manager** väljer du **Bläddra** och söker efter *Microsoft. Azure. Cosmos*. Välj **Microsoft. Azure. Cosmos** och välj **Installera**.

   :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-manage-nuget-2019.png" alt-text="Installera NuGet för Azure Cosmos DB-klient-SDK":::

   Paket-ID:t för Azure Cosmos DB-klientbiblioteket är [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).

Toppen! Konfigurationen är slutförd, så vi kan börja skriva kod. Ett slutfört projekt av den här självstudien finns i [utveckla en .net-konsol program med hjälp av Azure Cosmos DB](https://github.com/Azure-Samples/cosmos-dotnet-getting-started).

## <a name="step-3-connect-to-an-azure-cosmos-db-account"></a><a id="Connect"></a>Steg 3: Ansluta till ett Azure Cosmos DB-konto

1. Ersätt referenserna i början av ditt C#-program i filen *program.cs* med följande referenser:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Lägg till dessa konstanter och variabler i `Program` klassen.

    ```csharp
    public class Program
    {
        // ADD THIS PART TO YOUR CODE

        // The Azure Cosmos DB endpoint for running this sample.
        private static readonly string EndpointUri = "<your endpoint here>";
        // The primary key for the Azure Cosmos account.
        private static readonly string PrimaryKey = "<your primary key>";

        // The Cosmos client instance
        private CosmosClient cosmosClient;

        // The database we will create
        private Database database;

        // The container we will create.
        private Container container;

        // The name of the database and container we will create
        private string databaseId = "FamilyDatabase";
        private string containerId = "FamilyContainer";
    }
    ```

   > [!NOTE]
   > Om du är bekant med den tidigare versionen av .NET SDK kan du vara bekant med villkors *samlingen* och *dokumentet*. Eftersom Azure Cosmos DB stöder flera API-modeller, använder version 3,0 av .NET SDK de allmänna termerna *container* och *item*. En *behållare* kan vara en samling, Graf eller tabell. Ett *objekt* kan vara ett dokument, en kant/ett hörn eller en rad och är innehållet i en behållare. Mer information finns i [arbeta med databaser, behållare och objekt i Azure Cosmos DB](account-databases-containers-items.md).

1. Öppna [Azure-portalen](https://portal.azure.com). Hitta ditt Azure Cosmos DB konto och välj sedan **nycklar**.

   :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-portal-keys.png" alt-text="Hämta Azure Cosmos DB nycklar från Azure Portal":::

1. Ersätt  `<your endpoint URL>` med värdet för **URI** i program.cs. Ersätt `<your primary key>` med värdet för **primär nyckel**.

1. Under **main** -metoden lägger du till en ny asynkron aktivitet som heter **GetStartedDemoAsync**, som skapar en instans av vår nya `CosmosClient` .

    ```csharp
    public static async Task Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    /*
        Entry point to call methods that operate on Azure Cosmos DB resources in this sample
    */
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    }
    ```

    Vi använder **GetStartedDemoAsync** som start punkt som anropar metoder som körs på Azure Cosmos DB resurser.

1. Lägg till följande kod för att köra den asynkrona uppgiften **GetStartedDemoAsync** via **Main**-metoden. Metoden **Main** fångar upp undantag och skriver dem till konsolen.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Välj F5 för att köra programmet.

    Konsolen visar meddelandet: **slut på demo, tryck på valfri tangent för att avsluta.** Det här meddelandet bekräftar att programmet har skapat en anslutning till Azure Cosmos DB. Därefter kan du stänga konsolfönstret.

Grattis! Du har anslutit till ett Azure Cosmos DB-konto.

## <a name="step-4-create-a-database"></a>Steg 4: Skapa en databas

En databas är en logisk container med objekt som är partitionerade över containrar. Antingen- `CreateDatabaseIfNotExistsAsync` eller- `CreateDatabaseAsync` metoden för [CosmosClient](/dotnet/api/microsoft.azure.cosmos.cosmosclient) -klassen kan skapa en databas.

1. Kopiera och klistra in `CreateDatabaseAsync` metoden under `GetStartedDemoAsync` metoden.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

    `CreateDatabaseAsync` skapar en ny databas med ID `FamilyDatabase` om den inte redan finns, som har det ID som anges från `databaseId` fältet.

1. Kopiera och klistra in koden nedan där du instansierar CosmosClient för att anropa **metoderna createdatabaseasync** -metoden som du nyss lade till.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    Din *program.cs* bör nu se ut så här, med din slut punkt och primär nyckel ifylld.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStartedTutorial
    {
        class Program
        {
            // The Azure Cosmos DB endpoint for running this sample.
            private static readonly string EndpointUri = "<your endpoint here>";
            // The primary key for the Azure Cosmos account.
            private static readonly string PrimaryKey = "<your primary key>";

            // The Cosmos client instance
            private CosmosClient cosmosClient;

            // The database we will create
            private Database database;

            // The container we will create.
            private Container container;

            // The name of the database and container we will create
            private string databaseId = "FamilyDatabase";
            private string containerId = "FamilyContainer";

            public static async Task Main(string[] args)
            {
                try
                {
                    Console.WriteLine("Beginning operations...");
                    Program p = new Program();
                    await p.GetStartedDemoAsync();
                }
                catch (CosmosException de)
                {
                    Exception baseException = de.GetBaseException();
                    Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: {0}\n", e);
                }
                finally
                {
                    Console.WriteLine("End of demo, press any key to exit.");
                    Console.ReadKey();
                }
            }

            /// <summary>
            /// Entry point to call methods that operate on Azure Cosmos DB resources in this sample
            /// </summary>
            public async Task GetStartedDemoAsync()
            {
                // Create a new instance of the Cosmos Client
                this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
                await this.CreateDatabaseAsync();
            }

            /// <summary>
            /// Create the database if it does not exist
            /// </summary>
            private async Task CreateDatabaseAsync()
            {
                // Create a new database
                this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
                Console.WriteLine("Created Database: {0}\n", this.database.Id);
            }
        }
    }
    ```

1. Välj F5 för att köra programmet.

   > [!NOTE]
   > Om du får fel meddelandet "503 Service Unavailable Exception" är det möjligt att de [portar](sql-sdk-connection-modes.md#service-port-ranges) som krävs för direkt anslutnings läge blockeras av en brand vägg. Du kan åtgärda det här problemet genom att antingen öppna de portar som krävs eller använda anslutnings tjänsten för gateway-läge som visas i följande kod:
   ```csharp
     // Create a new instance of the Cosmos Client in Gateway mode
     this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey, new CosmosClientOptions()
            {
                ConnectionMode = ConnectionMode.Gateway
            });
   ```

Grattis! Du har skapat en Azure Cosmos-databas.  

## <a name="step-5-create-a-container"></a><a id="CreateColl"></a>Steg 5: Skapa en container

> [!WARNING]
> Metoden `CreateContainerIfNotExistsAsync` skapar en ny behållare som har pris effekter. Mer information finns på vår [prissättnings sida](https://azure.microsoft.com/pricing/details/cosmos-db/).
>
>

En behållare kan skapas med hjälp av antingen [**CreateContainerIfNotExistsAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync#Microsoft_Azure_Cosmos_Database_CreateContainerIfNotExistsAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) -eller [**CreateContainerAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerasync#Microsoft_Azure_Cosmos_Database_CreateContainerAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) -metoden i `CosmosDatabase` klassen. En behållare består av objekt (JSON-dokument om SQL API) och tillhör ande program logik på Server sidan i Java Script, till exempel lagrade procedurer, användardefinierade funktioner och utlösare.

1. Kopiera och klistra in `CreateContainerAsync` metoden under `CreateDatabaseAsync` metoden. `CreateContainerAsync`  skapar en ny behållare med ID `FamilyContainer` om den inte redan finns, genom att använda det ID som anges från `containerId` fältet partitionerad av `LastName` egenskap.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. Kopiera och klistra in koden nedan där du instansierade CosmosClient för att anropa metoden **CreateContainer** som du precis lade till.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();

        //ADD THIS PART TO YOUR CODE
        await this.CreateContainerAsync();
    }
    ```

1. Välj F5 för att köra programmet.

Grattis! Du har skapat en Azure Cosmos-behållare.  

## <a name="step-6-add-items-to-the-container"></a><a id="CreateDoc"></a>Steg 6: Lägg till objekt i behållaren

[**CreateItemAsync**](/dotnet/api/microsoft.azure.cosmos.container.createitemasync#Microsoft_Azure_Cosmos_Container_CreateItemAsync__1___0_System_Nullable_Microsoft_Azure_Cosmos_PartitionKey__Microsoft_Azure_Cosmos_ItemRequestOptions_System_Threading_CancellationToken_) -metoden för `CosmosContainer` klassen kan skapa ett objekt. När du använder SQL-API: t projiceras objekt som dokument, vilket är användardefinierat godtyckligt JSON-innehåll. Nu kan du infoga ett objekt i din Azure Cosmos-behållare.

Först ska vi skapa en `Family` klass som representerar objekt som lagras i Azure Cosmos db i det här exemplet. Vi ska också skapa `Parent` , `Child` , `Pet` , `Address` underklasser som används i `Family` . Objektet måste ha en `Id` egenskap som är serialiserad som `id` i JSON.

1. Välj Ctrl + Skift + A för att öppna **Lägg till nytt objekt**. Lägg till en ny klass `Family.cs` i projektet.

    :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-add-family-class-2019.png" alt-text="Skärm bild som visar hur du lägger till en ny Family.cs-klass i projektet":::

1. Kopiera och klistra in `Family` `Parent` klassen,,, `Child` `Pet` och `Address` i `Family.cs` .

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]


1. Gå tillbaka till *program.cs* och Lägg till `AddItemsToContainerAsync` metoden efter din `CreateContainerAsync` metod.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]


    Koden kontrollerar att det inte redan finns ett objekt med samma ID. Vi infogar två objekt, ett för *Andersen-serien* och *Wakefield-serien*.

1. Lägg till ett anrop till `AddItemsToContainerAsync` i metoden `GetStartedDemoAsync`.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainerAsync();
    }
    ```

1. Välj F5 för att köra programmet.

Grattis! Du har skapat två Azure Cosmos-objekt.  

## <a name="step-7-query-azure-cosmos-db-resources"></a><a id="Query"></a>Steg 7: Skicka frågor mot Azure Cosmos DB-resurser

Azure Cosmos DB stöder komplexa frågor mot JSON-dokument som lagras i varje container. Mer information finns i [komma igång med SQL-frågor](./sql-query-getting-started.md). I följande exempelkod visas hur du kör en fråga mot de objekt som vi infogade i det föregående steget.

1. Kopiera och klistra in `QueryItemsAsync` metoden efter din `AddItemsToContainerAsync` metod.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. Lägg till ett anrop till ``QueryItemsAsync`` i metoden ``GetStartedDemoAsync``.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.QueryItemsAsync();
    }
    ```

1. Välj F5 för att köra programmet.

Grattis! Du har efterfrågat en Azure Cosmos-behållare.

## <a name="step-8-replace-a-json-item"></a><a id="ReplaceItem"></a>Steg 8: Ersätta ett JSON-objekt

Nu ska vi uppdatera ett objekt i Azure Cosmos DB. Vi kommer att ändra `IsRegistered` egenskapen för `Family` `Grade` en av de underordnade objekten.

1. Kopiera och klistra in `ReplaceFamilyItemAsync` metoden efter din `QueryItemsAsync` metod.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Lägg till ett anrop till `ReplaceFamilyItemAsync` i metoden `GetStartedDemoAsync`.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();

        //ADD THIS PART TO YOUR CODE
        await this.ReplaceFamilyItemAsync();
    }
    ```

1. Välj F5 för att köra programmet.

Grattis! Du har ersatt ett Azure Cosmos-objekt.

## <a name="step-9-delete-item"></a><a id="DeleteDocument"></a>Steg 9: ta bort objekt

Nu ska vi ta bort ett objekt i Azure Cosmos DB.

1. Kopiera och klistra in `DeleteFamilyItemAsync` metoden efter din `ReplaceFamilyItemAsync` metod.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Lägg till ett anrop till `DeleteFamilyItemAsync` i metoden `GetStartedDemoAsync`.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();
        await this.ReplaceFamilyItemAsync();

        //ADD THIS PART TO YOUR CODE
        await this.DeleteFamilyItemAsync();
    }
    ```

1. Välj F5 för att köra programmet.

Grattis! Du har tagit bort ett Azure Cosmos-objekt.

## <a name="step-10-delete-the-database"></a><a id="DeleteDatabase"></a>Steg 10: Ta bort databasen

Nu ska vi ta bort vår databas. Om du tar bort den skapade databasen tas både databasen och alla underordnade resurser bort. Resurserna omfattar behållare, objekt och alla lagrade procedurer, användardefinierade funktioner och utlösare. Vi ska också ta bort `CosmosClient` instansen.

1. Kopiera och klistra in `DeleteDatabaseAndCleanupAsync` metoden efter din `DeleteFamilyItemAsync` metod.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Lägg till ett anrop till ``DeleteDatabaseAndCleanupAsync`` i metoden ``GetStartedDemoAsync``.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

1. Välj F5 för att köra programmet.

Grattis! Du har tagit bort en Azure Cosmos-databas.

## <a name="step-11-run-your-c-console-application-all-together"></a><a id="Run"></a>Steg 11: Kör C#-konsolappen i sin helhet!

Välj F5 i Visual Studio för att bygga och köra appen i felsökningsläge.

Du bör nu se utdata från hela appen i ett konsolfönster. Resultatet visar resultatet av de frågor som vi har lagt till. Den ska överensstämma med exempel texten nedan.

```cmd
Beginning operations...

Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.43 RUs.

Created item in database with id: Wakefield.7 Operation consumed 14.29 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

Updated Family [Wakefield,Wakefield.7].
        Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"},{"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6,"Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1,"Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}

Deleted Family [Wakefield,Wakefield.7]

Deleted Database: FamilyDatabase

End of demo, press any key to exit.
```

Grattis! Du har slutfört självstudiekursen och har ett fungerande C#-konsolprogram!

## <a name="get-the-complete-tutorial-solution"></a><a id="GetSolution"></a>Hämta den fullständiga lösningen för självstudiekursen

Om du inte har tid att slutföra stegen i den här självstudien eller bara vill hämta kod exemplen kan du ladda ned det.

För att bygga `GetStarted` lösningen behöver du följande förutsättningar:

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/).
* Ett [Azure Cosmos DB-konto][cosmos-db-create-account].
* [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started)-lösningen som finns på GitHub.

Om du vill återställa referenserna till Azure Cosmos DB .NET SDK i Visual Studio högerklickar du på lösningen i **Solution Explorer** och väljer sedan **Återställ NuGet-paket**. Sedan uppdaterar du och-värdena i *App.config* - `EndPointUri` filen `PrimaryKey` enligt beskrivningen i [steg 3: Anslut till ett Azure Cosmos DB konto](#Connect).

Då är det bara att bygga den, så är du på väg!

## <a name="next-steps"></a>Nästa steg

* Behöver du en mer komplex ASP.NET MVC-självstudiekurs? Se [självstudie: utveckla ett ASP.net Core MVC-webbprogram med Azure Cosmos dB med hjälp av .NET SDK](sql-api-dotnet-application.md).
* Vill du skala och testa prestanda med Azure Cosmos DB? Se [prestanda-och skalnings testning med Azure Cosmos DB](performance-testing.md).
* Information om hur du övervakar Azure Cosmos DB begär Anden, användning och lagring finns i [övervaka prestanda och lagrings mått i Azure Cosmos DB](./monitor-cosmos-db.md).
* Information om hur du kör frågor mot vår exempel data uppsättning finns i [testplats för databasfrågor](https://www.documentdb.com/sql/demo).
* Läs mer om Azure Cosmos DB i [Välkommen till Azure Cosmos DB](./introduction.md).

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account