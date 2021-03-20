---
title: Använd Azure Cosmos DB-Gremlin med SDK för
description: Lär dig hur du skapar resurs-tokens och använder dem för att få åtkomst till graf-databasen.
author: christopheranderson
ms.author: chrande
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/06/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 22c048b748806404ccfa580e660552a1744f3781
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93361701"
---
# <a name="use-azure-cosmos-db-resource-tokens-with-the-gremlin-sdk"></a>Använd Azure Cosmos DB-Gremlin med SDK för
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Den här artikeln förklarar hur du använder [Azure Cosmos DB-resursfiler](secure-access-to-data.md) för att komma åt graf-databasen via Gremlin SDK.

## <a name="create-a-resource-token"></a>Skapa en resurs-token

Apache TinkerPop Gremlin SDK har inget API som används för att skapa resurs-token. Termens *resurs-token* är ett Azure Cosmos DB koncept. Hämta [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)för att skapa resurs-token. Om ditt program behöver skapa resursfiler och använda dem för att få åtkomst till graf-databasen, krävs två separata SDK: er.

Objekt modellens hierarki över resurs-tokens visas i följande disposition:

- **Azure Cosmos DB konto** – den översta entiteten som har en DNS som är kopplad till den (till exempel `contoso.gremlin.cosmos.azure.com` ).
  - **Azure Cosmos DB databas**
    - **Användare**
      - **Behörighet**
        - **Token** – en behörighets objekt egenskap som anger vilka åtgärder som tillåts eller nekas.

En resurs-token använder följande format: `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"` . Den här strängen är ogenomskinlig för klienterna och bör användas som den är, utan modifiering eller tolkning.

```csharp
// Notice that document client is created against .NET SDK endpoint, rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<primary key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // The token isn't returned during the ReadPermissionReedAsync() call.
  // The call succeeds only if database id, user id, and permission id already exist. 
  // Note that <database id> is not a database name. It is a base64 string that represents the database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>.
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>Använd en Resource-token
Du kan använda resurs-token direkt som "Password"-egenskap när du skapar klassen GremlinServer.

```csharp
// The Gremlin application needs to be given a resource token. It can't discover the token on its own.
// You can obtain the token for a given permission by using the Azure Cosmos DB SDK, or you can pass it into the application as a command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure the Gremlin server to use a resource token rather than a primary key.
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

Samma metod fungerar i alla TinkerPop Gremlin SDK: er.

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>Gräns

Med ett enda Gremlin-konto kan du utfärda ett obegränsat antal tokens. Du kan dock använda upp till 100 token samtidigt inom en timme. Om ett program överskrider kvoten för token per timme nekas en autentiseringsbegäran och du får följande fel meddelande: "gränsen för tillåten resurs-token på 100 som kan användas samtidigt". Det fungerar inte att stänga aktiva anslutningar som använder vissa tokens för att frigöra platser för nya token. Azure Cosmos DB Gremlin-databasmotorn håller reda på unika tokens under timmen omedelbart före autentiseringsbegäran.

## <a name="permission"></a>Behörighet

Ett vanligt fel som uppstår när de använder resurs-token är "otillräcklig behörighet i Authorization-huvudet för motsvarande begäran. Försök igen med ett annat Authorization-huvud. " Det här felet returneras när en Gremlin-överträdelse försöker skriva en gräns eller ett hörn, men resurs-token beviljar endast *Läs* behörighet. Granska din genom gång för att se om den innehåller något av följande steg: *. addV ()*, *. addE ()*, *. drop ()* eller *. Property ()*.

## <a name="next-steps"></a>Nästa steg
* [Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](role-based-access-control.md) i Azure Cosmos DB
* [Lär dig hur du skyddar åtkomsten till data](secure-access-to-data.md) i Azure Cosmos DB
