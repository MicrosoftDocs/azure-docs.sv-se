---
title: Konfigurera en anslutning till ett Cosmos DB konto med en hanterad identitet
titleSuffix: Azure Cognitive Search
description: Lär dig hur du konfigurerar en Indexer-anslutning till ett Cosmos DB konto med hjälp av en hanterad identitet
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 2a1744feedc3e0ffae6cf2cd45cd090a6c2f06d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422101"
---
# <a name="set-up-an-indexer-connection-to-a-cosmos-db-database-using-a-managed-identity"></a>Konfigurera en Indexer-anslutning till en Cosmos DB-databas med hjälp av en hanterad identitet

Den här sidan beskriver hur du konfigurerar en Indexer-anslutning till en Azure Cosmos DB-databas med hjälp av en hanterad identitet i stället för att ange autentiseringsuppgifter i anslutnings strängen för data käll objekt.

Innan du lär dig mer om den här funktionen rekommenderar vi att du har en förståelse för vad en indexerare är och hur du konfigurerar en indexerare för din data källa. Mer information finns på följande länkar:

* [Översikt över indexeraren](search-indexer-overview.md)
* [Azure Cosmos DB-indexeraren](search-howto-index-cosmosdb.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>Konfigurera en anslutning med en hanterad identitet

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 – aktivera systemtilldelad hanterad identitet

När en systemtilldelad hanterad identitet är aktive rad skapar Azure en identitet för din Sök tjänst som kan användas för att autentisera till andra Azure-tjänster inom samma klient organisation och prenumeration. Du kan sedan använda den här identiteten i Azure-rollbaserade åtkomst kontrolls tilldelningar (Azure RBAC) som ger åtkomst till data under indexering.

![Aktivera systemtilldelad hanterad identitet](./media/search-managed-identities/turn-on-system-assigned-identity.png "Aktivera systemtilldelad hanterad identitet")

När du har valt **Spara** visas ett objekt-ID som har tilldelats till din Sök tjänst.

![Objekt-ID](./media/search-managed-identities/system-assigned-identity-object-id.png "Objekt-ID")
 
### <a name="2---add-a-role-assignment"></a>2 – Lägg till en roll tilldelning

I det här steget ska du ge Azure Kognitiv sökning-tjänstens behörighet att läsa data från Cosmos DB-databasen.

1. I Azure Portal navigerar du till det Cosmos DB-konto som innehåller de data som du vill indexera.
2. Välj **Åtkomstkontroll (IAM)**
3. Välj **Lägg till** och **Lägg till roll tilldelning**

    ![Lägg till rolltilldelning](./media/search-managed-identities/add-role-assignment-cosmos-db.png "Lägg till rolltilldelning")

4. Välj **rollen Cosmos DB konto läsare**
5. Lämna **tilldela åtkomst till** som **Azure AD-användare, grupp eller tjänstens huvud namn**
6. Sök efter Sök tjänsten, markera den och välj sedan **Spara**

    ![Lägg till roll tilldelning för läsare och data åtkomst](./media/search-managed-identities/add-role-assignment-cosmos-db-account-reader-role.png "Lägg till roll tilldelning för läsare och data åtkomst")

### <a name="3---create-the-data-source"></a>3 – skapa data källan

[REST API](/rest/api/searchservice/create-data-source), Azure Portal och [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) stöder anslutnings strängen för hanterad identitet. Nedan visas ett exempel på hur du skapar en data källa för att indexera data från Cosmos DB med hjälp av [REST API](/rest/api/searchservice/create-data-source) och en anslutnings sträng för hanterad identitet. Formatet för anslutnings strängen för hanterad identitet är detsamma för REST API, .NET SDK och Azure Portal.

När hanterade identiteter används för att autentisera, innehåller **autentiseringsuppgifterna** inte någon konto nyckel.

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "cosmos-db-datasource",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "Database=sql-test-db;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/cosmos-db-resource-group/providers/Microsoft.DocumentDB/databaseAccounts/my-cosmos-db-account/;"
    },
    "container": { "name": "myCollection", "query": null },
    "dataChangeDetectionPolicy": {
        "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName": "_ts"
    }
}
```    

Bröd texten i begäran innehåller definitionen av data källan, som ska innehålla följande fält:

| Fält   | Beskrivning |
|---------|-------------|
| **Namn** | Krävs. Välj ett namn som ska representera ditt data käll objekt. |
|**bastyp**| Krävs. Måste vara `cosmosdb` . |
|**klientautentiseringsuppgifter** | Krävs. <br/><br/>När du ansluter med en hanterad identitet ska formatet för **autentiseringsuppgifter** vara: *databas = [databas namn]; ResourceId = [resurs-ID-sträng];(ApiKind = [API-kind];)*<br/> <br/>ResourceId-formatet: *ResourceID =/Subscriptions/**ditt prenumerations-ID**/resourceGroups/**resurs gruppens namn**/providers/Microsoft.DocumentDB/databaseAccounts/**ditt Cosmos DB-kontonamn**/;*<br/><br/>Anslutnings strängen kräver ingen ApiKind för SQL-samlingar.<br/><br/>För MongoDB-samlingar lägger du till **ApiKind = MongoDB** i anslutnings strängen. <br/><br/>Registrera dig för för [hands versionen](https://aka.ms/azure-cognitive-search/indexer-preview) av Gremlin-diagram och Cassandra-tabeller för att få åtkomst till för hands versionen och information om hur du formaterar autentiseringsuppgifterna.<br/>|
| **fönster** | Innehåller följande element: <br/>**namn**: obligatoriskt. Ange ID för den databas samling som ska indexeras.<br/>**fråga**: valfritt. Du kan ange en fråga för att förenkla ett godtyckligt JSON-dokument till ett plant schema som Azure Kognitiv sökning kan indexera.<br/>För API: et för MongoDB, Gremlin API och API för Cassandra, stöds inte frågor. |
| **dataChangeDetectionPolicy** | Rekommenderas |
|**dataDeletionDetectionPolicy** | Valfritt |

### <a name="4---create-the-index"></a>4 – Skapa indexet

Indexet anger fält i ett dokument, attribut och andra konstruktioner som formar Sök funktionen.

Så här skapar du ett index med ett sökbart `booktitle` fält:

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

Mer information om hur du skapar index finns i [skapa index](/rest/api/searchservice/create-index)

### <a name="5---create-the-indexer"></a>5 – skapa indexeraren

En indexerare ansluter en data källa med ett mål Sök index och ger ett schema för att automatisera data uppdateringen.

När indexet och data källan har skapats är du redo att skapa indexeraren.

Exempel på indexerings definition:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "cosmos-db-indexer",
      "dataSourceName" : "cosmos-db-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Indexeraren körs varannan timme (schema intervall anges till "PT2H"). Om du vill köra en indexerare var 30: e minut anger du intervallet till "PT30M". Det kortaste intervall som stöds är 5 minuter. Schemat är valfritt – om det utelämnas körs en indexerare bara en gång när den skapas. Du kan dock köra en indexerare på begäran när du vill.   

Mer information om API för att skapa index finns i [skapa indexerare](/rest/api/searchservice/create-indexer).

Mer information om hur du definierar indexerare scheman finns i [så här schemalägger du indexerare för Azure kognitiv sökning](search-howto-schedule-indexers.md).

## <a name="troubleshooting"></a>Felsökning

Om du upptäcker att du inte kan indexera data från Cosmos DB bör du tänka på följande:

1. Om du nyligen har roterat dina Cosmos DB konto nycklar måste du vänta upp till 15 minuter för att anslutnings strängen för den hanterade identiteten ska fungera.

1. Kontrol lera om det Cosmos DB kontot har åtkomst begränsad till Välj nätverk. Om det gör det, se [indexerare åtkomst till innehåll som skyddas av Azure Network Security-funktioner](search-indexer-securing-resources.md).

## <a name="next-steps"></a>Nästa steg

* [Azure Cosmos DB-indexeraren](search-howto-index-cosmosdb.md)