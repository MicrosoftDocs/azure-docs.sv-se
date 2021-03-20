---
title: Definiera unika nycklar för en Azure Cosmos-behållare
description: 'Lär dig hur du definierar unika nycklar för en Azure Cosmos-behållare med hjälp av Azure Portal, PowerShell, .NET, Java och flera andra SDK: er.'
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 12/02/2019
ms.author: thweiss
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 55fc5222c1c245c56ba0a26caa816c5c845147c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93336647"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>Definiera unika nycklar för en Azure Cosmos-behållare
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här artikeln visar olika sätt att definiera [unika nycklar](unique-keys.md) när du skapar en Azure Cosmos-behållare. Det är för närvarande möjligt att utföra den här åtgärden antingen genom att använda Azure Portal eller via någon av SDK: erna.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-sql-api-dotnet.md#create-account) eller Välj ett befintligt.

1. Öppna fönstret **datautforskaren** och välj den behållare som du vill arbeta med.

1. Klicka på **ny behållare**.

1. I dialog rutan **Lägg till behållare** klickar du på **+ Lägg till unik nyckel** för att lägga till en unik nyckel post.

1. Ange sökvägen (erna) för den unika nyckel begränsningen

1. Om det behövs lägger du till fler unika nyckel poster genom att klicka på **+ Lägg till unik nyckel**

    :::image type="content" source="./media/how-to-define-unique-keys/unique-keys-portal.png" alt-text="Skärm bild av unik nyckel begränsnings post på Azure Portal":::

## <a name="use-powershell"></a>Använd PowerShell

Om du vill skapa en behållare med unika nycklar går du till [skapa en Azure Cosmos-behållare med unik nyckel och TTL](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="use-the-net-sdk"></a>Använda .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

När du skapar en ny behållare med hjälp av [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) `UniqueKeyPolicy` kan du använda ett objekt för att definiera unika nyckel begränsningar.

```csharp
client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("database"), new DocumentCollection
{
    Id = "container",
    PartitionKey = new PartitionKeyDefinition { Paths = new Collection<string>(new List<string> { "/myPartitionKey" }) },
    UniqueKeyPolicy = new UniqueKeyPolicy
    {
        UniqueKeys = new Collection<UniqueKey>(new List<UniqueKey>
        {
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/firstName", "/lastName", "/emailAddress" }) },
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/address/zipCode" }) }
        })
    }
});
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

När du skapar en ny behållare med [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)använder du SDK: s Fluent API för att deklarera unika nycklar på ett koncist och läsligt sätt.

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithUniqueKey()
        .Path("/firstName")
        .Path("/lastName")
        .Path("/emailAddress")
    .Attach()
    .WithUniqueKey()
        .Path("/address/zipCode")
    .Attach()
    .CreateIfNotExistsAsync();
```
---

## <a name="use-the-java-sdk"></a>Använda Java SDK

När du skapar en ny behållare med [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) `UniqueKeyPolicy` kan du använda ett-objekt för att definiera unika nyckel begränsningar.

```java
// create a new DocumentCollection object
DocumentCollection container = new DocumentCollection();
container.setId("container");

// create array of strings and populate them with the unique key paths
Collection<String> uniqueKey1Paths = new ArrayList<String>();
uniqueKey1Paths.add("/firstName");
uniqueKey1Paths.add("/lastName");
uniqueKey1Paths.add("/emailAddress");
Collection<String> uniqueKey2Paths = new ArrayList<String>();
uniqueKey2Paths.add("/address/zipCode");

// create UniqueKey objects and set their paths
UniqueKey uniqueKey1 = new UniqueKey();
UniqueKey uniqueKey2 = new UniqueKey();
uniqueKey1.setPaths(uniqueKey1Paths);
uniqueKey2.setPaths(uniqueKey2Paths);

// create a new UniqueKeyPolicy object and set its unique keys
UniqueKeyPolicy uniqueKeyPolicy = new UniqueKeyPolicy();
Collection<UniqueKey> uniqueKeys = new ArrayList<UniqueKey>();
uniqueKeys.add(uniqueKey1);
uniqueKeys.add(uniqueKey2);
uniqueKeyPolicy.setUniqueKeys(uniqueKeys);

// set the unique key policy
container.setUniqueKeyPolicy(uniqueKeyPolicy);

// create the container
client.createCollection(String.format("/dbs/%s", "database"), container, null);
```

## <a name="use-the-nodejs-sdk"></a>Använd Node.js SDK

När du skapar en ny behållare med hjälp av [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) `UniqueKeyPolicy` kan du använda ett objekt för att definiera unika nyckel begränsningar.

```javascript
client.database('database').containers.create({
    id: 'container',
    uniqueKeyPolicy: {
        uniqueKeys: [
            { paths: ['/firstName', '/lastName', '/emailAddress'] },
            { paths: ['/address/zipCode'] }
        ]
    }
});
```

## <a name="use-the-python-sdk"></a>Använda Python SDK

När du skapar en ny behållare med hjälp av [python SDK](https://pypi.org/project/azure-cosmos/)kan unika nyckel begränsningar anges som en del av ord listan som har skickats som parameter.

```python
client.CreateContainer('dbs/' + config['DATABASE'], {
    'id': 'container',
    'uniqueKeyPolicy': {
        'uniqueKeys': [
            {'paths': ['/firstName', '/lastName', '/emailAddress']},
            {'paths': ['/address/zipCode']}
        ]
    }
})
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [partitionering](partitioning-overview.md)
- Utforska [hur indexeringen fungerar](index-overview.md)
