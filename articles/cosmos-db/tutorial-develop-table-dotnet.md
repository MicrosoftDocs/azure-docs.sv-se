---
title: Azure Cosmos DB Tabell-API med .NET standard SDK
description: Lär dig hur du lagrar och frågar strukturerade data i Azure Cosmos DB Tabell-API konto
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/03/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: c641e24a498a6263d6a7c2325eed099b75a82caa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102426441"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Kom igång med tabell-API för Azure Cosmos DB och Azure Table Storage med .NET SDK
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Du kan använda Azure Cosmos DB Tabell-API eller Azure Table Storage för att lagra strukturerade NoSQL-data i molnet, vilket ger ett Arkiv för nyckel/attribut med ett schema som är mindre design. Eftersom Azure Cosmos DB Tabell-API-och table-lagring är ett schema som är mindre, är det enkelt att anpassa dina data när dina program behöver utvecklas. Du kan använda Azure Cosmos DB Tabell-API eller tabell lagringen för att lagra flexibla data uppsättningar, till exempel användar data för webb program, adress böcker, enhets information eller andra typer av metadata som din tjänst kräver. 

I den här självstudien beskrivs ett exempel som visar hur du använder [Microsoft Azure Cosmos DB tabell bibliotek för .net](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) med Azure Cosmos DB tabell-API och Azure Table Storage-scenarier. Du måste använda den anslutning som är speciell för Azure-tjänsten. De här scenarierna är bekanta med C#-exempel som illustrerar hur du skapar tabeller, infogar/uppdaterar data, frågedata och tar bort tabeller.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att kunna följa med i det här exemplet:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Microsoft Azure CosmosDB-tabell bibliotek för .net](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) – det här biblioteket är för närvarande tillgängligt för .net standard och .NET Framework. 

* [Azure Cosmos DB tabell-API konto](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Skapa ett Azure Cosmos DB Table API-konto

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Skapa ett .NET-konsol projekt

Skapa ett nytt .NET-konsol program i Visual Studio. Följande steg visar hur du skapar ett konsol program i Visual Studio 2019. Du kan använda Azure Cosmos DB tabell bibliotek i vilken typ av .NET-program som helst, inklusive en Azure-moln tjänst eller webbapp, samt Skriv bords-och mobil program. I den här guiden använder vi oss av en konsolapp för enkelhetens skull.

1. Välj **Arkiv**  >  **nytt**  >  **projekt**.

1. Välj **konsol program (.net Core)** och välj sedan **Nästa**.

1. I fältet **projekt namn** anger du ett namn för ditt program, till exempel **CosmosTableSamples**. (Du kan ange ett annat namn efter behov.)

1. Välj **Skapa**.

Alla kod exempel i det här exemplet kan läggas till i Main ()-metoden i konsol programmets **program. cs** -fil.

## <a name="install-the-required-nuget-package"></a>Installera det nödvändiga NuGet-paketet

Följ dessa steg för att hämta NuGet-paketet:

1. Högerklicka på ditt projekt i **Solution Explorer** och välj **Hantera NuGet-paket**.

1. Sök online efter [`Microsoft.Azure.Cosmos.Table`](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) , [`Microsoft.Extensions.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) , [`Microsoft.Extensions.Configuration.Json`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) [`Microsoft.Extensions.Configuration.Binder`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder) och välj **Installera** för att installera Microsoft Azure Cosmos DB tabell bibliotek.

## <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring

1. Från [Azure Portal](https://portal.azure.com/)navigerar du till ditt Azure Cosmos-konto eller Table Storage-kontot. 

1. Öppna fönstret **anslutnings sträng** eller **åtkomst nycklar** . Använd knapparna på höger sida av fönstret för att kopiera **PRIMÄR ANSLUTNINGSSTRÄNG**.

   :::image type="content" source="./media/create-table-dotnet/connection-string.png" alt-text="Visa och kopiera PRIMÄR ANSLUTNINGSSTRÄNG i fönstret Anslutningssträng":::
   
1. Om du vill konfigurera anslutnings strängen från Visual Studio högerklickar du på projektet **CosmosTableSamples**.

1. Välj **Lägg till** och sedan **nytt objekt**. Skapa en ny fil **Settings.jspå** med filtypen **typescript JSON-konfigurationsfil** . 

1. Ersätt koden i Settings.jspå filen med följande kod och tilldela din primära anslutnings sträng:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Högerklicka på din Project- **CosmosTableSamples**. Välj **Lägg till**, **nytt objekt** och Lägg till en klass med namnet **appSettings. cs**.

1. Lägg till följande kod i filen AppSettings. cs. Den här filen läser anslutnings strängen från Settings.jspå filen och tilldelar den till konfigurations parametern:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/AppSettings.cs":::

## <a name="parse-and-validate-the-connection-details"></a>Analysera och verifiera anslutnings informationen

1. Högerklicka på din Project- **CosmosTableSamples**. Välj **Lägg till**, **nytt objekt** och Lägg till en klass med namnet **common. cs**. Du kommer att skriva kod för att verifiera anslutnings informationen och skapa en tabell i den här klassen.

1. Definiera en metod `CreateStorageAccountFromConnectionString` som visas nedan. Med den här metoden tolkas anslutnings Strängs informationen och du kan kontrol lera att konto namnet och konto nyckel informationen i filen "Settings.jspå" är giltig.

   :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Common.cs" id="createStorageAccount":::

## <a name="create-a-table"></a>Skapa en tabell 

Med klassen [CloudTableClient](/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) kan du hämta tabeller och de entiteter som lagras i Table Storage. Eftersom vi inte har några tabeller i Cosmos DB Tabell-API-kontot lägger vi till `CreateTableAsync` metoden i klassen **common. cs** för att skapa en tabell:

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Common.cs" id="CreateTable":::

Om du får fel meddelandet "503 Service Unavailable Exception" är det möjligt att de portar som krävs för anslutnings läget blockeras av en brand vägg. Du kan åtgärda det här problemet genom att antingen öppna de portar som krävs eller använda anslutnings tjänsten för gateway-läge som visas i följande kod:

```csharp
tableClient.TableClientConfiguration.UseRestExecutorForCosmosEndpoint = true;
```

## <a name="define-the-entity"></a>Definiera entiteten 

Entiteter mappar till C#-objekt med hjälp av en anpassad klass som härletts från [TableEntity](/dotnet/api/microsoft.azure.cosmos.table.tableentity). Om du vill lägga till en entitet i en tabell skapar du en klass som definierar egenskaperna för entiteten.

Högerklicka på din Project- **CosmosTableSamples**. Välj **Lägg till**, **ny mapp** och ge den namnet som **modell**. I mappen modell lägger du till en klass med namnet **CustomerEntity. cs** och lägger till följande kod i den.

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Model/CustomerEntity.cs":::

Den här koden definierar en entitets klass som använder kundens förnamn som rad nyckel och efter namn som partitionsnyckel. Tillsammans identifierar en entitets partition och radnyckel den unikt i tabellen. Entiteter med samma partitionsnyckel kan efter frågas snabbare än entiteter med olika partitionsnyckel, men med hjälp av olika partitionstyper kan du få bättre skalbarhet för parallella åtgärder. Enheter som ska lagras i tabeller måste ha en typ som stöds, till exempel härledda från klassen [TableEntity](/dotnet/api/microsoft.azure.cosmos.table.tableentity). Entitetsegenskaper som du vill lagra i en tabell måste vara offentliga egenskaper av den typen och ha stöd för att både hämta och ange värden. Dessutom måste entitetstypen exponera en parameterlös konstruktor.

## <a name="insert-or-merge-an-entity"></a>Infoga eller sammanfoga en entitet

Följande kod exempel skapar ett entitet-objekt och lägger till det i tabellen. Metoden InsertOrMerge i klassen [TableOperation](/dotnet/api/microsoft.azure.cosmos.table.tableoperation) används för att infoga eller slå samman en entitet. Metoden [CloudTable.ExecuteAsync](/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync) anropas för att utföra åtgärden. 

Högerklicka på din Project- **CosmosTableSamples**. Välj **Lägg till**, **nytt objekt** och Lägg till en klass med namnet **SamplesUtils. cs**. Den här klassen lagrar all kod som krävs för att utföra CRUD-åtgärder på entiteterna. 

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="InsertItem":::

## <a name="get-an-entity-from-a-partition"></a>Hämta en entitet från en partition

Du kan hämta entiteten från en partition med hjälp av metoden GET under klassen [TableOperation](/dotnet/api/microsoft.azure.cosmos.table.tableoperation) . I följande kod exempel hämtas rad nyckel, e-postadress och telefonnummer för en kundentitet. I det här exemplet skrivs även de enheter för programbegäran som används för att fråga efter entiteten. Om du vill fråga efter en entitet lägger du till följande kod i filen **SamplesUtils. cs** :

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="QueryData":::

## <a name="delete-an-entity"></a>Ta bort en entitet

Du kan enkelt ta bort en enhet när du har hämtat den genom att använda samma mönster som när du uppdaterar en entitet. Följande kod hämtar och tar bort en kundentitet. Om du vill ta bort en entitet lägger du till följande kod i filen **SamplesUtils. cs** : 

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="DeleteItem":::

## <a name="execute-the-crud-operations-on-sample-data"></a>Kör CRUD-åtgärder på exempel data

När du har definierat metoder för att skapa tabeller, infoga eller sammanfoga entiteter kör du dessa metoder i exempel data. Det gör du genom att högerklicka på projektet **CosmosTableSamples**. Välj **Lägg till**, **nytt objekt** och Lägg till en klass med namnet **BasicSamples. cs** och Lägg till följande kod i den. Den här koden skapar en tabell, lägger till entiteter i den.

Om du inte vill ta bort entiteten och tabellen i slutet av projektet, kommenterar du `await table.DeleteIfExistsAsync()` `SamplesUtils.DeleteEntityAsync(table, customer)` metoderna och från följande kod. Det är bäst att kommentera ut dessa metoder och verifiera data innan du tar bort tabellen.

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/BasicSamples.cs":::

Föregående kod skapar en tabell som börjar med "demo" och det genererade GUID: t läggs till i tabell namnet. Sedan läggs en kundentitet med för-och efter namn till "Harp Walter" och senare uppdateras telefonnumret för den här användaren. 

I den här självstudien har du skapat kod för att utföra grundläggande CRUD-åtgärder på de data som lagras i Tabell-API-kontot. Du kan också utföra avancerade åtgärder, till exempel – batch infoga data, fråga alla data i en partition, fråga efter data intervall i en partition, listar tabeller i kontot vars namn börjar med det angivna prefixet. Du kan hämta det fullständiga exempel formuläret [Azure-Cosmos-Table-dotNet-Core-komma igång-GitHub-](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) lagringsplats. Klassen [AdvancedSamples. cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/main/CosmosTableSamples/AdvancedSamples.cs) innehåller fler åtgärder som du kan utföra på data.  

## <a name="run-the-project"></a>Kör projektet

Från din Project- **CosmosTableSamples**. Öppna klassen med namnet **program. cs** och Lägg till följande kod i den för att anropa BasicSamples när projektet körs.

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Program.cs":::

Nu ska du skapa lösningen och trycka på F5 för att köra projektet. När projektet körs visas följande utdata i kommando tolken:

:::image type="content" source="./media/tutorial-develop-table-standard/output-from-sample.png" alt-text="Utdata från kommando tolken":::

Om du får ett fel meddelande om att det inte går att hitta Settings.jspå filen när du kör projektet kan du lösa det genom att lägga till följande XML-post i projekt inställningarna. Högerklicka på CosmosTableSamples, Välj Redigera CosmosTableSamples. CSPROJ och Lägg till följande itemGroup: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Nu kan du logga in på Azure Portal och kontrol lera att data finns i tabellen. 

:::image type="content" source="./media/tutorial-develop-table-standard/results-in-portal.png" alt-text="Resultat i portalen":::

## <a name="next-steps"></a>Nästa steg

Nu kan du fortsätta till nästa självstudie och lära dig hur du migrerar data till Azure Cosmos DB Tabell-API-kontot. 

> [!div class="nextstepaction"]
>[Migrera data till Azure Comsos DB Tabell-API](../cosmos-db/table-import.md)