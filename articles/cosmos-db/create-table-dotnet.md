---
title: 'Snabbstart: Tabell-API med .NET – Azure Cosmos DB'
description: Den här snabbstarten visar hur du använder tabell-API i Azure Cosmos DB för att skapa ett program med Azure-portalen och .NET
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/28/2020
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 513af71384fd1fa5d38ee3ec367a42892a662444
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93096945"
---
# <a name="quickstart-build-a-table-api-app-with-net-sdk-and-azure-cosmos-db"></a>Snabb start: Bygg en Tabell-API-app med .NET SDK och Azure Cosmos DB 
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](./table-storage-how-to-use-python.md)
>  

Den här snabbstarten visar hur du använder [tabell-API](table-introduction.md) i .NET och Azure Cosmos DB för att skapa en app genom att klona ett exempel från GitHub. Den här snabbstarten visar även hur du skapar ett Azure Cosmos DB-konto och hur du använder Datautforskaren för att skapa tabeller och entiteter i den webbaserade Azure-portalen.

## <a name="prerequisites"></a>Förutsättningar

Om du inte redan har Visual Studio 2019 installerat kan du hämta och använda den **kostnads fria** [versionen av Visual Studio 2019 community](https://www.visualstudio.com/downloads/). Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Lägg till en tabell

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Lägga till exempeldata

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en Table-app från GitHub, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

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
   git clone https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started.git
   ```

> [!TIP]
> En mer detaljerad genom gång av liknande kod finns i artikeln [Cosmos DB tabell-API exempel](./tutorial-develop-table-dotnet.md) .

## <a name="open-the-sample-application-in-visual-studio"></a>Öppna exempelappen i Visual Studio

1. I Visual Studio: Välj **Öppna** på **Arkiv**-menyn och välj sedan **Projekt/lösning**. 

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-open-solution.png" alt-text="Öppna lösningen"::: 

2. Navigera till den mapp där du klonade exempel programmet och öppna filen TableStorage. SLN.

## <a name="review-the-code"></a>Granska koden

Det här är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Annars kan du gå vidare till [Uppdatera anslutnings sträng](#update-your-connection-string) avsnittet i det här dokumentet.

* Följande kod visar hur du skapar en tabell i Azure Storage:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Common.cs" id="CreateTable":::

* Följande kod visar hur du infogar data i tabellen:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="InsertItem":::

* Följande kod visar hur du frågar efter data från tabellen:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="QueryData":::

* Följande kod visar hur du tar bort data från tabellen:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="DeleteItem":::

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure Portal för att hämta information om din anslutningssträng och kopiera den till appen. På så vis kan appen kommunicera med den värdbaserade databasen. 

1. I [Azure-portalen](https://portal.azure.com/) klickar du på **Anslutningssträng**. Använd knapparna på höger sida av fönstret för att kopiera **PRIMÄR ANSLUTNINGSSTRÄNG**.

   :::image type="content" source="./media/create-table-dotnet/connection-string.png" alt-text="Visa och kopiera PRIMÄR ANSLUTNINGSSTRÄNG i fönstret Anslutningssträng":::

2. Öppna filen **Settings.js** i Visual Studio. 

3. Klistra in den **primära ANSLUTNINGS strängen** från portalen i StorageConnectionString-värdet. Klistra in strängen innanför citattecknen.

   ```csharp
   {
      "StorageConnectionString": "<Primary connection string from Azure portal>"
   }
   ```

4. Tryck på CTRL + S för att spara **Settings.jspå** filen.

Du har nu uppdaterat appen med all information som behövs för kommunikation med Azure Cosmos DB. 

## <a name="build-and-deploy-the-app"></a>Skapa och distribuera appen

1. I Visual Studio högerklickar du på projektet **CosmosTableSamples** i **Solution Explorer** och klickar sedan på **Hantera NuGet-paket**. 

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-manage-nuget.png" alt-text="Hantera NuGet-paket":::

2. Skriv Microsoft. Azure. Cosmos. table i rutan NuGet **Browse** . Därmed hittas klientbiblioteket Cosmos DB Table API. Observera att det här biblioteket för närvarande är tillgängligt för .NET Framework och .NET standard. 
   
   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-nuget-browse.png" alt-text="Fliken Bläddra i NuGet":::

3. Klicka på **Installera** för att installera **Microsoft. Azure. Cosmos. table** -biblioteket. Därmed installeras Azure Cosmos DB Table API-paketet och alla beroenden.

4. När du kör hela appen infogas exempel data i entiteten tabell och tas bort i slutet så att du inte ser några data som infogas om du kör hela exemplet. Du kan dock infoga vissa Bryt punkter för att visa data. Öppna BasicSamples. cs-filen och högerklicka på rad 52, Välj **Bryt punkt** och välj sedan **Infoga Bryt punkt**. Infoga en annan brytpunkt på rad 55.

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-breakpoint.png" alt-text="Lägga till en brytpunkt"::: 

5. Tryck på F5 för att köra appen. Konsol fönstret visar namnet på den nya tabell databasen (i det här fallet demoa13b1) i Azure Cosmos DB. 
    
   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-console.png" alt-text="Konsolutdata":::

   När du kommer till den första brytpunkten går du tillbaka till Datautforskaren i Azure Portal. Klicka på **Uppdatera**, expandera demotabellen* och klicka på **Entiteter**. Fliken **Entiteter** till höger visar den nya entiteten som har lagts till för Walter Harp. Lägg märke till att telefonnumret för den nya entiteten är 425-555-0101.

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-entity.png" alt-text="Ny entitet":::
    
   Om du får ett fel meddelande om att det inte går att hitta Settings.jspå filen när du kör projektet kan du lösa det genom att lägga till följande XML-post i projekt inställningarna. Högerklicka på CosmosTableSamples, Välj Redigera CosmosTableSamples. CSPROJ och Lägg till följande itemGroup: 

   ```csharp
     <ItemGroup>
       <None Update="Settings.json">
         <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
       </None>
     </ItemGroup>
   ```

6. Stäng fliken **entiteter** i datautforskaren.
    
7. Tryck på F5 för att köra appen till nästa brytpunkt. 

    När du kommer till brytpunkten växlar du tillbaka till Azure Portal och klickar på **Entiteter** igen för att öppna fliken **Entiteter**. Du kan nu se att telefonnumret har uppdaterats till 425-555-0105.

8. Tryck på F5 för att köra appen. 
 
   Appen lägger till entiteter som kan användas i en avancerad exempelapp som tabell-API:t inte stöder för närvarande. Appen tar sedan bort tabellen som skapats av exempelappen.

9. I konsolfönstret trycker du på Retur för att avsluta körningen av appen. 
  

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du fått lära dig att skapa ett Azure Cosmos DB-konto, skapa en tabell med datautforskaren och att köra en app.  Du kan nu ställa frågor mot dina data med tabell-API:t.  

> [!div class="nextstepaction"]
> [Importera tabelldata till tabell-API](table-import.md)