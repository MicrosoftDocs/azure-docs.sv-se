---
title: 'Azure Cosmos DB: Skapa en att göra-app med Xamarin'
description: Presenterar ett Xamarin-kodexempel som du kan använda för att ansluta och ställa frågor till Azure Cosmos DB
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/07/2021
ms.author: anfeldma
ms.custom: devx-track-csharp
ms.openlocfilehash: 2a940f4bb519332e147577e4a9172406c401d152
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365747"
---
# <a name="quickstart-build-a-todo-app-with-xamarin-using-azure-cosmos-db-sql-api-account"></a>Snabbstart: Skapa en att göra-app med Xamarin med hjälp Azure Cosmos DB SQL API-konto
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring-data v3](create-sql-api-spring-data.md)
> * [Spark v3-anslutningsapp](create-sql-api-spark.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB.

> [!NOTE]
> Exempelkod för ett helt kanonisk Xamarin-exempelapp som visar flera Azure-erbjudanden, däribland CosmosDB, finns på GitHub [här](https://github.com/xamarinhq/app-geocontacts). Den här appen demonstrerar visning av geografiskt spridda kontakter, och gör att dessa kontakter kan uppdatera sin plats.

Den här snabbstarten visar hur du skapar ett SQL API-konto i Azure Cosmos DB, en dokumentdatabas och en container med Azure-portalen. Sedan skapar och distribuerar du en mobilapp för att göra-lista som bygger på [SQL .NET API](sql-api-sdk-dotnet.md) och [Xamarin](/xamarin/) med [Xamarin.Forms](/xamarin/) och [MVVM-arkitekturmönstret](/xamarin/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm).

:::image type="content" source="./media/create-sql-api-xamarin-dotnet/ios-todo-screen.png" alt-text="Att göra-app i Xamarin som körs på iOS":::

## <a name="prerequisites"></a>Förutsättningar

Om du utvecklar i Windows och inte redan har Visual Studio 2019 installerat  kan du ladda ned och använda den kostnadsfria [versionen Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Se till att du aktiverar arbetsbelastningarna **Azure-utveckling** och **Mobil utveckling med .NET** under installationen av Visual Studio.

Om du använder en Mac kan du ladda ned den **kostnadsfria** [Visual Studio för Mac](https://www.visualstudio.com/vs/mac/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Lägga till en container

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Lägga till exempeldata

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Fråga dina data

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona SQL API-appen i Xamarin från GitHub, granska koden, hämta API-nycklar och köra den. Du kommer att se hur lätt det är att arbeta med data programmässigt.

1. Öppna en kommandotolk, skapa en ny mapp som heter git-samples och stäng sedan kommandotolken.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till den nya mappen där du vill installera exempelappen.

    ```bash
    cd "C:\git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-xamarin-getting-started.git
    ```

4. I Visual Studio du **C:\git-samples\azure-cosmos-db-sql-xamarin-getting-started\src\ToDoItems.sln** 

## <a name="obtain-your-api-keys"></a>Hämta dina API-nycklar

Gå tillbaka till Azure Portal för att hämta API-nyckelinformationen och kopiera den till appen.

1. I [Azure Portal](https://portal.azure.com/) går du till ditt SQL API-konto i Azure Cosmos DB. Klicka på **Nycklar** i den vänstra navigeringen och sedan på **Läs- och skrivnycklar**. Använd kopieringsknapparna till höger på skärmen till att kopiera URI:n och primärnyckeln till filen APIKeys.cs i nästa steg.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/keys.png" alt-text="Visa och kopiera en åtkomstnyckel i Azure Portal, bladet Nycklar":::

2. I Visual Studio du **ToDoItems.Core/Helpers/APIKeys.cs.**

3. I Azure Portal kopieringsknappen kopierar du **URI-värdet** och gör det till värdet för `CosmosEndpointUrl` variabeln i APIKeys.cs.

    ```csharp
    //#error Enter the URL of your Azure Cosmos DB endpoint here
    public static readonly string CosmosEndpointUrl = "[URI Copied from Azure portal]";
    ```

4. I Azure Portal kopieringsknappen kopierar du värdet **för PRIMÄRNYCKEL** och gör det till värdet för `Cosmos Auth Key` i APIKeys.cs.

    ```csharp
    //#error Enter the read/write authentication key of your Azure Cosmos DB endpoint here
    public static readonly string CosmosAuthKey = "[PRIMARY KEY copied from Azure portal";
    ```

[!INCLUDE [cosmos-db-auth-key-info](../../includes/cosmos-db-auth-key-info.md)]

## <a name="review-the-code"></a>Granska koden

Den här lösningen visar hur du skapar en att göra-app med SQL API i Azure Cosmos DB och Xamarin.Forms. Appen har två flikar, den första fliken innehåller en listvy med att göra-objekt som inte har slutförts än. Den andra fliken visar att göra-objekt som har slutförts. Förutom att visa att göra-objekt som inte slutförts i den första fliken, kan du också lägga till nya att göra-objekt, redigera befintliga och markera objekt som slutförda.

:::image type="content" source="./media/create-sql-api-xamarin-dotnet/android-todo-screen.png" alt-text="Kopiera in json-data och klicka på Spara i Datautforskaren i Azure Portal":::

Koden i ToDoItems-lösningen innehåller:

* **ToDoItems.Core**
   * Detta är ett standardprojekt i .NET med ett Xamarin.Forms-projekt och delad programlogikkod som hanterar att göra-objekt i Azure Cosmos DB.
* **ToDoItems.Android**
  * Projektet innehåller Android-appen.
* **ToDoItems.iOS**
  * Projektet innehåller iOS-appen.

Nu ska vi ta en snabb titt på hur appen kommunicerar med Azure Cosmos DB.

* NuGet-paketet [Microsoft.Azure.DocumentDb.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/) måste läggas till i alla projekt.
* Klassen `ToDoItem` i mappen **ToDoItems.Core/Models modeller** modellerar dokumenten i objektcontainern som skapades ovan.  Observera att egenskapsnamnet är skiftlägeskänsligt.
* Klassen `CosmosDBService` i mappen **ToDoItems.Core/Services** kapslar in kommunikationen till Azure Cosmos DB.
* I `CosmosDBService`-klassen finns det en variabel av typen `DocumentClient`. `DocumentClient`används för att konfigurera och köra begäranden mot Azure Cosmos DB och instansieras:

    ```csharp
    docClient = new DocumentClient(new Uri(APIKeys.CosmosEndpointUrl), APIKeys.CosmosAuthKey);
    ```

* När du frågar en container efter dokument `DocumentClient.CreateDocumentQuery<T>` används metoden , som du ser här i funktionen `CosmosDBService.GetToDoItems` :

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=GetToDoItems)] 

    `CreateDocumentQuery<T>`tar en URI som pekar på containern som skapades i föregående avsnitt. Du kan också ange specifika LINQ-operatorer. som t.ex. en `Where`-sats. I detta fall returneras endast att göra-objekt som inte är slutförda.

    Funktionen `CreateDocumentQuery<T>` körs synkront och returnerar en `IQueryable<T>`. Metoden `AsDocumentQuery` konverterar dock till `IQueryable<T>` ett `IDocumentQuery<T>` -objekt, som kan köras asynkront. Därför blockeras inte UI-tråden för mobila program.

    Funktionen `IDocumentQuery<T>.ExecuteNextAsync<T>` hämtar sidan med resultat från Azure Cosmos DB, som undersöker för att se om ytterligare resultat fortfarande ska `HasMoreResults` returneras.

> [!TIP]
> Flera funktioner som fungerar på Azure Cosmos-containrar och -dokument använder en URI som en parameter som anger adressen till containern eller dokumentet. URI:n skapas med hjälp av `URIFactory`-klassen. URI:er för databaser, containrar och dokument kan skapas med den här klassen.

* Funktionen `ComsmosDBService.InsertToDoItem` visar hur du infogar ett nytt dokument:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=InsertToDoItem)] 

    Objekt-URI anges samt det objekt som ska infogas.

* Funktionen `CosmosDBService.UpdateToDoItem` visar hur du ersätter ett befintligt dokument med ett nytt:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=UpdateToDoItem)] 

    Här behövs en ny URI för att unikt identifiera dokumentet som ska ersättas och hämtas med hjälp av och skicka databasen och containernamnen och `UriFactory.CreateDocumentUri` ID:t för dokumentet.

    `DocumentClient.ReplaceDocumentAsync` ersätter dokumentet som identifieras av URI:n med det som anges som parameter.

* Borttagning av ett objekt visas med `CosmosDBService.DeleteToDoItem` funktionen :

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=DeleteToDoItem)] 

    Observera återigen det unika dokument-URI som skapas och skickas till funktionen `DocumentClient.DeleteDocumentAsync`.

## <a name="run-the-app"></a>Kör appen

Du har nu uppdaterat appen med all information som behövs för kommunikation med Azure Cosmos DB.

Följande steg visar hur du kör appen med felsökningsprogrammet i Visual Studio för Mac.

> [!NOTE]
> Att använda Android-appen går till på samma sätt, eventuella skillnader beskrivs i stegen nedan. Om du vill felsöka med Visual Studio i Windows finns dokumentation för [iOS här](/xamarin/ios/deploy-test/debugging-in-xamarin-ios?tabs=vswin) och [Android här](/xamarin/android/deploy-test/debugging/).

1. Först väljer du plattform genom att klicka på den markerade listrutan och välja antingen ToDoItems.iOS för iOS eller ToDoItems.Android för Android.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ide-select-platform.png" alt-text="Välja en plattform för felsökning i Visual Studio för Mac":::

2. Tryck på cmd+Retur eller klicka på uppspelningsknappen för att starta felsökningen av appen.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ide-start-debug.png" alt-text="Börja felsöka i Visual Studio för Mac":::

3. När iOS-simulatorn eller Android-emulatorn har startas visas två flikar längst ned på skärmen för iOS och överst på skärmen för Android. Den första visar att göra-objekt, som inte har slutförts, den andra visar att göra-objekt, som har slutförts.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ios-droid-started.png" alt-text="Starta skärmen i Att göra-appen":::

4. Om du vill slutföra ett att göra-objekt i iOS drar du det till vänster > och trycker på knappen **Slutför**. Om du vill slutföra ett att göra-objekt i Android trycker du länge på objektet > tryck sedan på knappen Slutför.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-complete.png" alt-text="Slutföra ett att göra-objekt":::

5. Om du vill redigera ett att göra-objekt > trycker du på objektet > en ny skärm visas där du kan ange nya värden. Tryck på Spara-knappen för att behålla ändringarna i Azure Cosmos DB.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-edit.png" alt-text="Redigera att göra-objekt":::

6. Om du vill lägga till ett att göra-objekt > trycker du på knappen **Lägg till** längst upp till höger på startsidan > en ny tom redigeringssida visas.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-add.png" alt-text="Lägga till att göra-objekt":::

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Azure Cosmos-konto, skapar en container med Datautforskaren och skapar och distribuerar en Xamarin-app. Nu kan du importera mer data till ditt Azure Cosmos-konto.

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)
