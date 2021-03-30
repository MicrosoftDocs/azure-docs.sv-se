---
title: 'Snabb start: API för Cassandra med .NET Core – Azure Cosmos DB'
description: Den här snabb starten visar hur du använder Azure Cosmos DB API för Cassandra för att skapa ett profil program med Azure Portal och .NET Core
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
author: TheovanKraay
ms.author: thvankra
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/01/2020
ms.custom: devx-track-dotnet
ms.openlocfilehash: f228386d8cf0e708080b7f6c5f6cef7258b2eafb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93099988"
---
# <a name="quickstart-build-a-cassandra-app-with-net-core-and-azure-cosmos-db"></a>Snabb start: Bygg en Cassandra-app med .NET Core och Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

Den här snabb starten visar hur du använder .NET Core och Azure Cosmos DB [API för Cassandra](cassandra-introduction.md) för att bygga en profil-app genom att klona ett exempel från GitHub. Den här snabbstarten visar även hur du använder den webbaserade Azure-portalen för att skapa ett Azure Cosmos DB-konto.

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, tabeller, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Du kan även [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration, utan kostnad och åtaganden.

Dessutom behöver du: 
* Om du inte redan har Visual Studio 2019 installerat kan du hämta och använda den **kostnads fria** [versionen av Visual Studio 2019 community](https://www.visualstudio.com/downloads/). Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.
* Installera [Git](https://www.git-scm.com/) för att klona exemplet.

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]


## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi övergå till att arbeta med kod. Nu ska vi klona en Cassandra API-app från GitHub, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna en kommandotolk. Skapa en ny mapp med namnet `git-samples`. Stäng kommandotolken.

    ```bash
    md "C:\git-samples"
    ```

2. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till den nya mappen där du vill installera exempelappen.

    ```bash
    cd "C:\git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-dotnet-core-getting-started.git
    ```

4. Öppna sedan lösningsfilen CassandraQuickStartSample i Visual Studio. 

## <a name="review-the-code"></a>Granska koden

Det här är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Alla kod avsnitt hämtas från `Program.cs` filen i `async Task ProcessAsync()` metoden, som installeras i `C:\git-samples\azure-cosmos-db-cassandra-dotnet-core-getting-started\CassandraQuickStart` mappen. Annars kan du gå vidare till [Uppdatera din anslutningssträng](#update-your-connection-string).

* Initiera sessionen genom att ansluta till en Cassandra-klusterslutpunkt. Cassandra-API:et på Azure Cosmos DB stöder endast TLSv1.2. 

  ```csharp
      var options = new Cassandra.SSLOptions(SslProtocols.Tls12, true, ValidateServerCertificate);
      options.SetHostNameResolver((ipAddress) => CASSANDRACONTACTPOINT);
      Cluster cluster = Cluster
          .Builder()
          .WithCredentials(USERNAME, PASSWORD)
          .WithPort(CASSANDRAPORT)
          .AddContactPoint(CASSANDRACONTACTPOINT)
          .WithSSL(options)
          .Build()
      ;
      ISession session = await cluster.ConnectAsync();
   ```

* Ta bort befintligt blank steg om det redan finns.

    ```csharp
    await session.ExecuteAsync(new SimpleStatement("DROP KEYSPACE IF EXISTS uprofile")); 
    ```

* Skapa ett nytt keyspace.

    ```csharp
    await session.ExecuteAsync(new SimpleStatement("CREATE KEYSPACE uprofile WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 };"));
    ```

* Skapa en ny tabell.

   ```csharp
  await session.ExecuteAsync(new SimpleStatement("CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)"));
   ```

* Infoga användarentiteter med hjälp av IMapper-objektet med en ny session som ansluter till uprofile keyspace.

    ```csharp
    await mapper.InsertAsync<User>(new User(1, "LyubovK", "Dubai"));
    ```

* Fråga för att hämta alla användares information.

    ```csharp
    foreach (User user in await mapper.FetchAsync<User>("Select * from user"))
    {
        Console.WriteLine(user);
    }
    ```

* Fråga för att hämta en användares information.

    ```csharp
    mapper.FirstOrDefault<User>("Select * from user where user_id = ?", 3);
    ```

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure Portal för att hämta information om din anslutningssträng och kopiera den till appen. Informationen i anslutningssträngen gör det möjligt för appen att kommunicera med den värdbaserade databasen.

1. På [Azure-portalen](https://portal.azure.com/) väljer du **Anslutningssträng**.

1. Använd :::image type="icon" source="./media/create-cassandra-dotnet/copy.png"::: knappen på höger sida av skärmen för att kopiera värdet username.

   :::image type="content" source="./media/create-cassandra-dotnet/keys.png" alt-text="Visa och kopiera en åtkomstnyckel från Azure-portalen, sidan Anslutningssträng":::

1. Öppna filen program. cs i Visual Studio. 

1. Klistra in värdet för ANVÄNDARNAMN från portalen över `<PROVIDE>` på rad 13.

    Rad 13 i Program.cs bör nu se ut ungefär så här 

    `private const string UserName = "cosmos-db-quickstart";`

    Du kan också klistra in samma värde över `<PROVIDE>` på rad 15 för värdet för kontakt punkten:

    `private const string CassandraContactPoint = "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com"; //  DnsName`

1. Gå tillbaka till portalen och kopiera värdet LÖSENORD. Klistra in värdet för LÖSENORD från portalen över `<PROVIDE>` på rad 14.

    Rad 14 i Program.cs bör nu se ut ungefär så här 

    `private const string Password = "2Ggkr662ifxz2Mg...==";`

1. Gå tillbaka till portalen och kopiera värdet KONTAKTPUNKT. Klistra in värdet för kontakt punkt från portalen över `<PROVIDE>` på rad 16.

    Rad 16 i program. cs bör nu se ut ungefär så här 

    `private const string CASSANDRACONTACTPOINT = "quickstart-cassandra-api.cassandra.cosmos.azure.com";`

1. Spara filen Program.cs.
    
## <a name="run-the-net-core-app"></a>Kör .NET Core-appen

1. I Visual Studio väljer du **verktyg**  >  **NuGet Package Manager**  >  **Package Manager-konsolen**.

2. I kommandotolken installerar du .NET-drivrutinens NuGet-paket genom att köra följande kommando. 

    ```cmd
    Install-Package CassandraCSharpDriver
    ```
3. Tryck på Ctrl + F5 för att köra programmet. Appen visas i konsolfönstret. 

    :::image type="content" source="./media/create-cassandra-dotnet/output.png" alt-text="Visa och verifiera utdata":::

    Tryck på CTRL + C om du vill stoppa körningen av programmet och stänga konsolfönstret. 
    
4. I **Datautforskaren** på Azure-portalen kan du fråga, ändra och arbeta med dessa nya data.

    :::image type="content" source="./media/create-cassandra-dotnet/data-explorer.png" alt-text="Visa data i Datautforskaren":::

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Azure Cosmos DB-konto, hur du skapar en container med hjälp av Datautforskaren och hur du kör en webbapp. Du kan nu importera ytterligare data till ditt Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera Cassandra-data till Azure Cosmos DB](cassandra-import-data.md)
