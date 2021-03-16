---
title: Mass import av data till Azure Cosmos DB SQL API-konto med hjälp av .NET SDK
description: Lär dig hur du importerar eller matar in data till Azure Cosmos DB genom att skapa ett .NET-konsol program som optimerar allokerade data flöde (RU/s) som krävs för att importera data
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: tutorial
ms.date: 03/15/2021
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 1c178f57a31e02b3dac712a5425db226720200c5
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563631"
---
# <a name="bulk-import-data-to-azure-cosmos-db-sql-api-account-by-using-the-net-sdk"></a>Mass import av data till Azure Cosmos DB SQL API-konto med hjälp av .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här självstudien visar hur du skapar ett .NET-konsol program som optimerar det allokerade data flöde (RU/s) som krävs för att importera data till Azure Cosmos DB. I den här artikeln ska du läsa data från en exempel data källa och importera den till en Azure Cosmos-behållare.
I den här självstudien används [version 3.0 +](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) av Azure Cosmos dB .NET SDK, som kan vara avsedd för .NET Framework eller .net Core.

Den här självstudiekursen omfattar:

> [!div class="checklist"]
> * Skapa ett Azure Cosmos-konto
> * Konfigurera ditt projekt
> * Ansluta till ett Azure Cosmos-konto med Mass stöd aktiverat
> * Utföra en data import via samtidiga skapande åtgärder

## <a name="prerequisites"></a>Förutsättningar

Se till att du har följande resurser innan du följer anvisningarna i den här artikeln:

* Ett aktivt Azure-konto. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Net Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). Du kan kontrol lera vilken version som är tillgänglig i din miljö genom att köra `dotnet --version` .

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Steg 1: Skapa ett Azure Cosmos DB-konto

[Skapa ett Azure Cosmos DB SQL-API-konto](create-cosmosdb-resources-portal.md) från Azure Portal eller så kan du skapa kontot med hjälp av [Azure Cosmos DB-emulatorn](local-emulator.md).

## <a name="step-2-set-up-your-net-project"></a>Steg 2: Konfigurera ditt .NET-projekt

Öppna kommando tolken i Windows eller ett terminalfönster från den lokala datorn. Du kommer att köra alla kommandon i nästa avsnitt från kommando tolken eller terminalen. Kör följande dotNet New-kommando för att skapa en ny app med namnet *bulk-import-demo*. `--langVersion`Parametern anger egenskapen *LangVersion* i den skapade projekt filen.

   ```bash
   dotnet new console –langVersion:8 -n bulk-import-demo
   ```

Ändra katalogen till mappen nyligen skapade appar. Du kan bygga programmet med:

   ```bash
   cd bulk-import-demo
   dotnet build
   ```

Förväntade utdata från versionen bör se ut ungefär så här:

   ```bash
   Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo\bulk-import-demo.csproj.
     bulk -> C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo \bin\Debug\netcoreapp2.2\bulk-import-demo.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:34.17
   ```

## <a name="step-3-add-the-azure-cosmos-db-package"></a>Steg 3: Lägg till Azure Cosmos DB-paketet

När du fortfarande är i program katalogen installerar du Azure Cosmos DB klient biblioteket för .NET Core med kommandot dotNet Lägg till paket.

   ```bash
   dotnet add package Microsoft.Azure.Cosmos
   ```

## <a name="step-4-get-your-azure-cosmos-account-credentials"></a>Steg 4: Hämta dina autentiseringsuppgifter för Azure Cosmos-kontot

Exempel programmet måste autentisera till ditt Azure Cosmos-konto. För att autentisera bör du skicka autentiseringsuppgifter för Azure Cosmos-kontot till programmet. Hämta dina autentiseringsuppgifter för Azure Cosmos-kontot genom att följa dessa steg:

1.  Logga in på [Azure-portalen](https://portal.azure.com/).
1.  Navigera till ditt Azure Cosmos-konto.
1.  Öppna rutan **nycklar** och kopiera **URI: n** och **primär nyckeln** för ditt konto.

Om du använder Azure Cosmos DB emulatorn hämtar du autentiseringsuppgifterna för [emulatorn från den här artikeln](local-emulator.md#authenticate-requests).

## <a name="step-5-initialize-the-cosmosclient-object-with-bulk-execution-support"></a>Steg 5: initiera CosmosClient-objektet med stöd för Mass körning

Öppna den genererade `Program.cs` filen i en kod redigerare. Du skapar en ny instans av CosmosClient med aktive rad Mass körning och använder den för att utföra åtgärder mot Azure Cosmos DB. 

Vi börjar med att skriva över standard `Main` metoden och definiera de globala variablerna. Dessa globala variabler kommer att innehålla slut punkt och auktoriseringsregler, namnet på databasen, den behållare som du ska skapa och antalet objekt som du ska infoga i bulk. Se till att ersätta värdena för endpointURL och auktoriseringsvärdet enligt din miljö. 


   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Diagnostics;
   using System.IO;
   using System.Text.Json;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;

   public class Program
   {
        private const string EndpointUrl = "https://<your-account>.documents.azure.com:443/";
        private const string AuthorizationKey = "<your-account-key>";
        private const string DatabaseName = "bulk-tutorial";
        private const string ContainerName = "items";
        private const int AmountToInsert = 300000;

        static async Task Main(string[] args)
        {

        }
   }
   ```

I `Main` -metoden lägger du till följande kod för att initiera CosmosClient-objektet:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=CreateClient)]

När Mass körning har Aktiver ATS grupperas CosmosClient internt i enskilda tjänst anrop. På så sätt optimeras data flödes användningen genom att tjänste anropen distribueras mellan partitioner, och slutligen tilldelas individuella resultat till de ursprungliga anroparna.

Du kan sedan skapa en behållare för att lagra alla våra objekt.  Definiera `/pk` som partitionsnyckel, 50000 ru/s som ett tillhandahållet data flöde och en anpassad indexerings princip som kommer att undanta alla fält för att optimera Skriv data flödet. Lägg till följande kod efter CosmosClient-initierings instruktionen:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Initialize)]

## <a name="step-6-populate-a-list-of-concurrent-tasks"></a>Steg 6: Fyll i en lista över samtidiga aktiviteter

Om du vill dra nytta av stödet för Mass körning skapar du en lista över asynkrona uppgifter baserat på data källan och de åtgärder som du vill utföra och använder `Task.WhenAll` för att köra dem samtidigt.
Vi börjar med att använda "falsk" data för att generera en lista med objekt från vår data modell. I ett verkligt program kommer objekten från den önskade data källan.

Lägg först till det falsk-paketet i lösningen med kommandot dotNet Lägg till paket.

   ```bash
   dotnet add package Bogus
   ```

Definiera definitionen av de objekt som du vill spara. Du måste definiera `Item` klassen i `Program.cs` filen:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Model)]

Skapa sedan en hjälp funktion inuti `Program` klassen. Den här hjälp funktionen kommer att hämta antalet objekt som du har definierat för att infoga och generera slumpmässiga data:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Bogus)]

Använd hjälp funktionen för att initiera en lista över dokument att arbeta med:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Operations)]

Använd sedan listan med dokument för att skapa samtidiga uppgifter och fylla i uppgifts listan för att infoga objekten i behållaren. För att utföra den här åtgärden lägger du till följande kod i `Program` klassen:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=ConcurrentTasks)]

Alla dessa samtidiga punkt åtgärder utförs tillsammans (i bulk) enligt beskrivningen i introduktions avsnittet.

## <a name="step-7-run-the-sample"></a>Steg 7: kör exemplet

För att kunna köra exemplet kan du göra det helt enkelt med `dotnet` kommandot:

   ```bash
   dotnet run
   ```

## <a name="get-the-complete-sample"></a>Hämta det fullständiga exemplet

Om du inte har tid att slutföra stegen i den här självstudien eller bara vill ladda ned kodexemplen kan du hämta den från [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer).

När du har klonat projektet, se till att uppdatera önskade autentiseringsuppgifter i [program.cs](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer/blob/main/src/Program.cs#L25).

Exemplet kan köras genom att byta till databas katalogen och använda `dotnet` :

   ```bash
   cd cosmos-dotnet-bulk-import-throughput-optimizer
   dotnet run
   ```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du utfört följande steg:

> [!div class="checklist"]
> * Skapa ett Azure Cosmos-konto
> * Konfigurera ditt projekt
> * Ansluta till ett Azure Cosmos-konto med Mass stöd aktiverat
> * Utföra en data import via samtidiga skapande åtgärder

Nu kan du gå vidare till nästa självstudie:

> [!div class="nextstepaction"]
>[Köra frågor mot Azure Cosmos DB med hjälp av SQL API](tutorial-query-sql-api.md)