---
title: Hantera Azure Cosmos DB SQL API-resurser med hjälp av .NET V4 SDK
description: Snabbstart för att skapa en konsolapp med .NET V4 SDK för att hantera Azure Cosmos DB SQL API-kontoresurser.
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/07/2021
ms.custom: devx-track-dotnet
ms.openlocfilehash: 559ace3e9cb9f6daeb6dc3da581bb99d3ff9145e
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365169"
---
# <a name="quickstart-build-a-console-app-using-the-net-v4-sdk-preview-to-manage-azure-cosmos-db-sql-api-account-resources"></a>Snabbstart: Skapa en konsolapp med .NET V4 SDK (förhandsversion) för att hantera Azure Cosmos DB SQL API-kontoresurser.
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

Kom igång med Azure Cosmos DB SQL API-klientbiblioteket för .NET. Följ stegen i det här dokumentet för att installera .NET V4-paketet (Azure.Cosmos), skapa en app och prova exempelkoden för grundläggande CRUD-åtgärder på data som lagras i Azure Cosmos DB.

> [!IMPORTANT]
> .NET V4 SDK för Azure Cosmos DB för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB är Microsofts snabba NoSQL-databas med öppna API:er för alla skalor. Du kan använda Azure Cosmos DB för att snabbt skapa och fråga efter nyckel/värde-, dokument- och grafdatabaser. Använd Azure Cosmos DB SQL API-klientbiblioteket för .NET för att:

* Skapa en Azure Cosmos-databas och en container
* Lägga till exempeldata i containern
* Fråga efter data 
* Ta bort databasen

[Bibliotekskällkod](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4)  |  [Paket (NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration [– skapa en](https://azure.microsoft.com/free/) kostnadsfritt eller så kan du prova [Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) kostnadsfritt utan en Azure-prenumeration, utan kostnad och åtaganden. 
* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). Du kan kontrollera vilken version som är tillgänglig i din miljö genom att köra `dotnet --version` .

## <a name="setting-up"></a>Inrätta

Det här avsnittet vägled dig genom att skapa ett Azure Cosmos-konto och konfigurera ett projekt som använder Azure Cosmos DB SQL API-klientbibliotek för .NET för att hantera resurser. Exempelkoden som beskrivs i den här artikeln skapar `FamilyDatabase` en databas och familjemedlemmar (varje medlem i familjen är ett objekt) i databasen. Varje medlem i familjen har egenskaper som `Id, FamilyName, FirstName, LastName, Parents, Children, Address,` . Egenskapen `LastName` används som partitionsnyckel för containern. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Skapa ett Azure Cosmos-konto

Om du använder alternativet [Prova Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) kostnadsfritt för att skapa ett Azure Cosmos-konto måste du skapa ett Azure Cosmos DB-konto av typen **SQL API.** Ett Azure Cosmos DB testkonto har redan skapats åt dig. Du behöver inte skapa kontot uttryckligen, så du kan hoppa över det här avsnittet och gå vidare till nästa avsnitt.

Om du har en egen Azure-prenumeration eller om du har skapat en prenumeration kostnadsfritt bör du uttryckligen skapa ett Azure Cosmos-konto. Följande kod skapar ett Azure Cosmos-konto med sessionskonsekvens. Kontot replikeras i `South Central US` och `North Central US` .  

Du kan använda Azure Cloud Shell för att skapa Azure Cosmos-kontot. Azure Cloud Shell är ett interaktivt, autentiserat skal för hantering av Azure-resurser via webbläsare. Den ger dig flexibilitet att välja den gränssnittsmiljö som passar bäst för ditt sätt att arbeta, antingen Bash eller PowerShell. För den här snabbstarten väljer du **Bash-läge.** Azure Cloud Shell kräver ett lagringskonto kan du skapa ett när du uppmanas att göra det.

Välj knappen **Prova bredvid** följande kod, välj **Bash-läge,** välj Skapa ett **lagringskonto** och logga in på Cloud Shell. Kopiera och klistra in följande kod för att Azure Cloud Shell och köra den. Azure Cosmos-kontonamnet måste vara globalt unikt. Se till att uppdatera `mysqlapicosmosdb` värdet innan du kör kommandot.

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

Det tar en stund att skapa Azure Cosmos-kontot. När åtgärden har lyckats kan du se bekräftelseutdata. När kommandot har slutförts loggar du in på [Azure Portal](https://portal.azure.com/) och kontrollerar att Azure Cosmos-kontot med det angivna namnet finns. Du kan stänga Azure Cloud Shell-fönstret när resursen har skapats. 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>Skapa en ny .NET-app

Skapa ett nytt .NET-program i önskad redigerare eller IDE. Öppna Kommandotolken i Windows eller ett terminalfönster från den lokala datorn. Du kommer att köra alla kommandon i nästa avsnitt från kommandotolken eller terminalen.  Kör följande nya dotnet-kommando för att skapa en ny app med namnet `todo` . Parametern --langVersion anger egenskapen LangVersion i den skapade projektfilen.

   ```bash
   dotnet new console –langVersion:8 -n todo
   ```

Ändra din katalog till den nyligen skapade appmappen. Du kan skapa programmet med:

   ```bash
   cd todo
   dotnet build
   ```

Förväntade utdata från bygget bör se ut ungefär så här:

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Installera Azure Cosmos DB paketet

När du fortfarande är i programkatalogen installerar du Azure Cosmos DB för .NET Core med hjälp av kommandot dotnet add package.

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Kopiera autentiseringsuppgifterna för ditt Azure Cosmos-konto från Azure Portal

Exempelprogrammet måste autentisera till ditt Azure Cosmos-konto. För att autentisera bör du skicka autentiseringsuppgifterna för Azure Cosmos-kontot till programmet. Hämta autentiseringsuppgifterna för ditt Azure Cosmos-konto genom att följa dessa steg:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Gå till ditt Azure Cosmos-konto.

1. Öppna fönstret **Nycklar** och kopiera **URI och** **PRIMÄRNYCKEL för** ditt konto. Du lägger till värdena för URI och nycklar i en miljövariabel i nästa steg.

## <a name="object-model"></a><a id="object-model"></a>Objektmodell

Innan du börjar skapa programmet ska vi titta på hierarkin med resurser i Azure Cosmos DB och objektmodellen som används för att skapa och komma åt dessa resurser. Den Azure Cosmos DB skapar resurser i följande ordning:

* Azure Cosmos-konto 
* Databaser 
* Containers 
* Poster

Mer information om hierarkin för olika entiteter finns i artikeln arbeta med [databaser,](account-databases-containers-items.md) containrar och objekt i Azure Cosmos DB entiteter. Du kommer att använda följande .NET-klasser för att interagera med dessa resurser:

* CosmosClient – den här klassen tillhandahåller en logisk representation på klientsidan för Azure Cosmos DB tjänsten. Klientobjektet används för att konfigurera och köra begäranden mot tjänsten.
* CreateDatabaseIfNotExistsAsync – Den här metoden skapar (om den inte finns) eller hämtar (om det redan finns) en databasresurs som en asynkron åtgärd. 
* CreateContainerIfNotExistsAsync – Den här metoden skapar (om den inte finns) eller hämtar (om den redan finns) en container som en asynkron åtgärd. Du kan kontrollera statuskoden från svaret för att avgöra om containern skapades nyligen (201) eller om en befintlig container returnerades (200). 
* CreateItemAsync – Den här metoden skapar ett objekt i containern.
* UpsertItemAsync – Den här metoden skapar ett objekt i containern om det inte redan finns eller ersätter objektet om det redan finns. 
* GetItemQueryIterator – Den här metoden skapar en fråga för objekt under en container i en Azure Cosmos-databas med hjälp av en SQL-instruktion med parametriserade värden. 
* DeleteAsync – Tar bort den angivna databasen från ditt Azure Cosmos-konto. `DeleteAsync` -metoden tar bara bort databasen.

 ## <a name="code-examples"></a><a id="code-examples"></a>Kodexempel

Exempelkoden som beskrivs i den här artikeln skapar en familjedatabas i Azure Cosmos DB. Familjedatabasen innehåller familjeinformation som namn, adress, plats, associerade föräldrar, barn och husdjur. Innan du fyller i data till ditt Azure Cosmos-konto definierar du egenskaperna för ett familjeobjekt. Skapa en ny klass med `Family.cs` namnet på rotnivån för exempelprogrammet och lägg till följande kod i den:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives--define-the-client-object"></a>Lägg till using-direktiv & definiera klientobjektet

Öppna filen i redigeringsprogrammet `Program.cs` från projektkatalogen och lägg till följande using-direktiv överst i programmet:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


Lägg till följande globala variabler i klassen `Program` . Dessa inkluderar slutpunkten och auktoriseringsnycklarna, namnet på databasen och containern som du skapar. Ersätt värdena för slutpunkten och auktoriseringsnycklarna enligt din miljö. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

Ersätt slutligen `Main` metoden :

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>Skapa en databas 

Definiera `CreateDatabaseAsync` metoden i `program.cs` klassen . Den här metoden `FamilyDatabase` skapar om den inte redan finns.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>Skapa en container

Definiera `CreateContainerAsync` metoden i `Program` klassen . Den här metoden `FamilyContainer` skapar om den inte redan finns. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>Skapa ett objekt

Skapa ett familjeobjekt genom att lägga `AddItemsToContainerAsync` till metoden med följande kod. Du kan använda metoderna `CreateItemAsync` eller för att skapa ett `UpsertItemAsync` objekt:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>Fråga objekten

När du har infogat ett objekt kan du köra en fråga för att få information om familjen "Andersen". Följande kod visar hur du kör frågan direkt med SQL-frågan. SQL-frågan för att hämta information om "Anderson"-familjen är: `SELECT * FROM c WHERE c.LastName = 'Andersen'` . Definiera `QueryItemsAsync` metoden i klassen `Program` och lägg till följande kod i den:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>Ersätta ett objekt 

Läs ett familjeobjekt och uppdatera det genom att lägga `ReplaceFamilyItemAsync` till metoden med följande kod.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>Ta bort ett objekt 

Ta bort ett familjeobjekt genom att `DeleteFamilyItemAsync` lägga till metoden med följande kod.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>Ta bort databasen 

Slutligen kan du ta bort databasen genom att `DeleteDatabaseAndCleanupAsync` lägga till metoden med följande kod:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

När du har lagt till alla metoder som krävs sparar du `Program` filen. 

## <a name="run-the-code"></a>Kör koden

Skapa och kör sedan programmet för att skapa Azure Cosmos DB resurser.

   ```bash
   dotnet run
   ```

Följande utdata genereras när du kör programmet. Du kan också logga in Azure Portal och verifiera att resurserna har skapats:

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

Du kan verifiera att data skapas genom att logga in på Azure Portal och se de nödvändiga objekten i ditt Azure Cosmos-konto. 

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda Azure CLI eller Azure PowerShell för att ta bort Azure Cosmos-kontot och motsvarande resursgrupp. Följande kommando visar hur du tar bort resursgruppen med hjälp av Azure CLI:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Azure Cosmos-konto, skapar en databas och en container med hjälp av en .NET Core-app. Du kan nu importera ytterligare data till ditt Azure Cosmos-konto med hjälp av anvisningarna i följande artikel. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)
