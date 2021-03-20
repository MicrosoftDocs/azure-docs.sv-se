---
title: Hantera Azure Cosmos DB SQL API-resurser med hjälp av .NET v4 SDK
description: Snabb start för att bygga en konsol app med hjälp av .NET v4 SDK för att hantera Azure Cosmos DB SQL API-konto resurser.
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/22/2020
ms.custom: devx-track-dotnet
ms.openlocfilehash: 224a1b67ff0282c216763229593fcfed81d7567b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93090111"
---
# <a name="quickstart-build-a-console-app-using-the-net-v4-sdk-to-manage-azure-cosmos-db-sql-api-account-resources"></a>Snabb start: Bygg en konsol app med hjälp av .NET v4 SDK för att hantera Azure Cosmos DB SQL API-konto resurser.
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring-data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Kom igång med klient biblioteket Azure Cosmos DB SQL API för .NET. Följ stegen i det här dokumentet för att installera .NET v4-paketet (Azure. Cosmos), bygga en app och testa exempel koden för grundläggande CRUD-åtgärder för de data som lagras i Azure Cosmos DB. 

Azure Cosmos DB är Microsofts snabba NoSQL-databas med öppna API: er för alla skalor. Du kan använda Azure Cosmos DB för att snabbt skapa och fråga nyckel/värde-, dokument-och Graf-databaser. Använd klient biblioteket Azure Cosmos DB SQL-API för .NET för att:

* Skapa en Azure Cosmos-databas och en behållare
* Lägg till exempel data i behållaren
* Fråga efter data 
* Ta bort databasen

[Biblioteks käll kod](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4)  |  [Paket (NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fri](https://azure.microsoft.com/free/) eller [prova Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration, utan kostnad och åtaganden. 
* [Net Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). Du kan kontrol lera vilken version som är tillgänglig i din miljö genom att köra `dotnet --version` .

## <a name="setting-up"></a>Konfigurera

Det här avsnittet beskriver hur du skapar ett Azure Cosmos-konto och konfigurerar ett projekt som använder Azure Cosmos DB SQL API-klient bibliotek för .NET för att hantera resurser. Exempel koden som beskrivs i den här artikeln skapar en `FamilyDatabase` databas och familje medlemmar (varje familje medlem är ett objekt) i databasen. Varje familje medlem har egenskaper som `Id, FamilyName, FirstName, LastName, Parents, Children, Address,` . `LastName`Egenskapen används som partitions nyckel för behållaren. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Skapa ett Azure Cosmos-konto

Om du använder alternativet [prova Azure Cosmos dB för kostnads fri](https://azure.microsoft.com/try/cosmosdb/) för att skapa ett Azure Cosmos-konto måste du skapa ett Azure Cosmos DB konto av typen **SQL API**. Ett Azure Cosmos DB test konto har redan skapats åt dig. Du behöver inte skapa kontot explicit, så du kan hoppa över det här avsnittet och gå vidare till nästa avsnitt.

Om du har en egen Azure-prenumeration eller skapat en prenumeration kostnads fritt bör du skapa ett Azure Cosmos-konto explicit. Följande kod skapar ett Azure Cosmos-konto med konsekvens i sessionen. Kontot replikeras i `South Central US` och `North Central US` .  

Du kan använda Azure Cloud Shell för att skapa ett Azure Cosmos-konto. Azure Cloud Shell är ett interaktivt, autentiserat skal för hantering av Azure-resurser via webbläsare. Den ger dig flexibilitet att välja den gränssnittsmiljö som passar bäst för ditt sätt att arbeta, antingen Bash eller PowerShell. I den här snabb starten väljer du **bash** läge. Azure Cloud Shell kräver också ett lagrings konto, så du kan skapa ett när du uppmanas till det.

Välj knappen **prova** bredvid följande kod och välj **bash** läge Välj **skapa ett lagrings konto** och logga in på Cloud Shell. Nästa kopiera och klistra in följande kod för att Azure Cloud Shell och köra den. Namnet på Azure Cosmos-kontot måste vara globalt unikt, se till att uppdatera `mysqlapicosmosdb` värdet innan du kör kommandot.

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'

# The Azure Cosmos account name must be globally unique, make sure to update the `mysqlapicosmosdb` value before you run the command
accountName='mysqlapicosmosdb'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-region writes enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

Det tar en stund att skapa ett Azure Cosmos-konto när åtgärden har slutförts. du kan se bekräftelse resultatet. När kommandot har slutförts loggar du in på [Azure Portal](https://portal.azure.com/) och kontrollerar att Azure Cosmos-kontot med det angivna namnet finns. Du kan stänga Azure Cloud Shells fönstret när resursen har skapats. 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>Skapa en ny .NET-app

Skapa ett nytt .NET-program i önskat redigerings program eller IDE. Öppna kommando tolken i Windows eller ett terminalfönster från den lokala datorn. Du kommer att köra alla kommandon i nästa avsnitt från kommando tolken eller terminalen.  Kör följande dotNet New-kommando för att skapa en ny app med namnet `todo` . Parametern--langVersion anger egenskapen LangVersion i den skapade projekt filen.

   ```bash
   dotnet new console –langVersion:8 -n todo
   ```

Ändra katalogen till mappen nyligen skapade appar. Du kan bygga programmet med:

   ```bash
   cd todo
   dotnet build
   ```

Förväntade utdata från versionen bör se ut ungefär så här:

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Installera Azure Cosmos DB-paketet

När du fortfarande är i program katalogen installerar du Azure Cosmos DB klient biblioteket för .NET Core med kommandot dotNet Lägg till paket.

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Kopiera dina autentiseringsuppgifter för Azure Cosmos-kontot från Azure Portal

Exempel programmet måste autentisera till ditt Azure Cosmos-konto. För att autentisera bör du skicka autentiseringsuppgifter för Azure Cosmos-kontot till programmet. Hämta dina autentiseringsuppgifter för Azure Cosmos-kontot genom att följa dessa steg:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Navigera till ditt Azure Cosmos-konto.

1. Öppna rutan **nycklar** och kopiera **URI: n** och **primär nyckeln** för ditt konto. Du lägger till värdena URI och nycklar i en miljö variabel i nästa steg.

## <a name="object-model"></a><a id="object-model"></a>Objekt modell

Innan du börjar skapa programmet ska vi titta närmare på hierarkin över resurser i Azure Cosmos DB och objekt modellen som används för att skapa och få åtkomst till dessa resurser. Azure Cosmos DB skapar resurser i följande ordning:

* Azure Cosmos-konto 
* Databaser 
* Containers 
* Poster

Mer information om hierarkin för olika entiteter finns i [arbeta med databaser, behållare och objekt i Azure Cosmos DB](account-databases-containers-items.md) artikel. Du kommer att använda följande .NET-klasser för att interagera med dessa resurser:

* CosmosClient – den här klassen tillhandahåller en logisk representation på klient sidan för Azure Cosmos DBs tjänsten. Klient objekt används för att konfigurera och köra begär Anden mot tjänsten.
* CreateDatabaseIfNotExistsAsync – den här metoden skapar (om den inte finns) eller hämtar (om redan finns) en databas resurs som en asynkron åtgärd. 
* CreateContainerIfNotExistsAsync – den här metoden skapar (om den inte finns) eller hämtar (om det redan finns) en behållare som en asynkron åtgärd. Du kan kontrol lera status koden från svaret för att avgöra om behållaren nyligen skapades (201) eller om en befintlig behållare returnerades (200). 
* CreateItemAsync – den här metoden skapar ett objekt i behållaren.
* UpsertItemAsync – den här metoden skapar ett objekt i behållaren om det inte redan finns eller ersätter objektet om det redan finns. 
* GetItemQueryIterator – den här metoden skapar en fråga för objekt under en behållare i en Azure Cosmos-databas med hjälp av ett SQL-uttryck med parametriserade värden. 
* DeleteAsync – tar bort den angivna databasen från ditt Azure Cosmos-konto. `DeleteAsync` metoden tar bara bort databasen.

 ## <a name="code-examples"></a><a id="code-examples"></a>Kodexempel

Exempel koden som beskrivs i den här artikeln skapar en familje databas i Azure Cosmos DB. Familje databasen innehåller familje information som namn, adress, plats, associerade föräldrar, barn och hus djur. Definiera egenskaperna för ett familje objekt innan du fyller i data på ditt Azure Cosmos-konto. Skapa en ny klass med namnet `Family.cs` på rot nivån i exempel programmet och Lägg till följande kod i den:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives--define-the-client-object"></a>Lägg till using-direktiv & definiera klient objekt

Öppna `Program.cs` filen i redigerings programmet från projekt katalogen och Lägg till följande med hjälp av direktiv överst i programmet:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


Lägg till följande globala variabler i din- `Program` klass. Dessa inkluderar slut punkt och auktoriseringsregler, namnet på databasen och den behållare som du ska skapa. Se till att ersätta värdena för slut punkten och auktoriseringsvärdet enligt din miljö. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

Ersätt till sist `Main` metoden:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>Skapa en databas 

Definiera `CreateDatabaseAsync` metoden i `program.cs` klassen. Den här metoden skapar `FamilyDatabase` om den inte redan finns.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>Skapa en container

Definiera `CreateContainerAsync` metoden i `Program` klassen. Den här metoden skapar `FamilyContainer` om den inte redan finns. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>Skapa ett objekt

Skapa ett familje objekt genom att lägga till- `AddItemsToContainerAsync` metoden med följande kod. Du kan använda `CreateItemAsync` metoderna eller `UpsertItemAsync` för att skapa ett objekt:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>Fråga objekten

När du har infogat ett objekt kan du köra en fråga för att hämta information om "Andersen"-serien. Följande kod visar hur du kör frågan med SQL-frågan direkt. SQL-frågan för att hämta "Anderson"-familje information är: `SELECT * FROM c WHERE c.LastName = 'Andersen'` . Definiera `QueryItemsAsync` metoden i `Program` klassen och Lägg till följande kod i den:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>Ersätta ett objekt 

Läs ett familje objekt och uppdatera det genom att lägga till- `ReplaceFamilyItemAsync` metoden med följande kod.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>Ta bort ett objekt 

Ta bort ett familje objekt genom att lägga till `DeleteFamilyItemAsync` metoden med följande kod.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>Ta bort databasen 

Slutligen kan du ta bort databasen genom att lägga till `DeleteDatabaseAndCleanupAsync` metoden med följande kod:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

När du har lagt till alla nödvändiga metoder sparar du `Program` filen. 

## <a name="run-the-code"></a>Kör koden

Skapa sedan Azure Cosmos DB-resurserna genom att skapa och köra programmet.

   ```bash
   dotnet run
   ```

Följande utdata skapas när du kör programmet. Du kan också logga in på Azure Portal och kontrol lera att resurserna har skapats:

   ```bash
   Created Database: FamilyDatabase
   
   Created Container: FamilyContainer
   
   Created item in database with id: Andersen.1
   
   Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'
   
           Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null   "FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets": [{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}
   
   Updated Family [Wakefield,Wakefield.7].
           Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"}   {"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6   "Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1   "Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}
   
   Deleted Family [Wakefield,Wakefield.7]
   
   Deleted Database: FamilyDatabase
   
   End of demo, press any key to exit.
   ```

Du kan verifiera att data har skapats genom att logga in på Azure Portal och se vilka objekt som krävs på ditt Azure Cosmos-konto. 

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda Azure CLI eller Azure PowerShell för att ta bort Azure Cosmos-kontot och motsvarande resurs grupp. Följande kommando visar hur du tar bort resurs gruppen med hjälp av Azure CLI:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Azure Cosmos-konto, skapar en databas och en behållare med hjälp av en .NET Core-app. Nu kan du importera ytterligare data till ditt Azure Cosmos-konto med hjälp av anvisningarna i avsnittet int följande artikel. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)
