---
title: Konfigurera en anslutning till ett lagrings konto med hjälp av en hanterad identitet (förhands granskning)
titleSuffix: Azure Cognitive Search
description: Lär dig hur du konfigurerar en Indexer-anslutning till ett Azure Storage konto med hjälp av en hanterad identitet (förhands granskning)
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: dacfeeff06d58a084d4313ca50b51f262cf61381
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553088"
---
# <a name="set-up-a-connection-to-an-azure-storage-account-using-a-managed-identity-preview"></a>Konfigurera en anslutning till ett Azure Storage konto med hjälp av en hanterad identitet (förhands granskning)

> [!IMPORTANT] 
> Stöd för att konfigurera en anslutning till en data källa med hjälp av en hanterad identitet finns för närvarande i offentlig för hands version. För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar.

Den här sidan beskriver hur du konfigurerar en Indexer-anslutning till ett Azure Storage-konto med hjälp av en hanterad identitet i stället för att ange autentiseringsuppgifter i anslutnings strängen för data käll objekt.

Innan du lär dig mer om den här funktionen rekommenderar vi att du har en förståelse för vad en indexerare är och hur du konfigurerar en indexerare för din data källa. Mer information finns på följande länkar:
* [Översikt över indexeraren](search-indexer-overview.md)
* [Azure Blob-indexeraren](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2 indexerare](search-howto-index-azure-data-lake-storage.md)
* [Azure Table-indexeraren](search-howto-indexing-azure-tables.md)

## <a name="set-up-the-connection"></a>Konfigurera anslutningen

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 – aktivera systemtilldelad hanterad identitet

När en systemtilldelad hanterad identitet är aktive rad skapar Azure en identitet för din Sök tjänst som kan användas för att autentisera till andra Azure-tjänster inom samma klient organisation och prenumeration. Du kan sedan använda den här identiteten i RBAC-tilldelningar (rollbaserad åtkomst kontroll) som tillåter åtkomst till data vid indexering.

![Aktivera systemtilldelad hanterad identitet](./media/search-managed-identities/turn-on-system-assigned-identity.png "Aktivera systemtilldelad hanterad identitet")

När du har valt **Spara** visas ett objekt-ID som har tilldelats till din Sök tjänst.

![Objekt-ID](./media/search-managed-identities/system-assigned-identity-object-id.png "Objekt-ID")
 
### <a name="2---add-a-role-assignment"></a>2 – Lägg till en roll tilldelning

I det här steget ska du ge Azure Kognitiv sökning-tjänstens behörighet att läsa data från ditt lagrings konto.

1. I Azure Portal navigerar du till det lagrings konto som innehåller de data som du vill indexera.
2. Välj **åtkomst kontroll (IAM)**
3. Välj **Lägg till** och **Lägg till roll tilldelning**

    ![Lägg till rolltilldelning](./media/search-managed-identities/add-role-assignment-storage.png "Lägg till rolltilldelning")

4. Välj lämpliga roller baserat på lagrings konto typen som du vill indexera:
    1. Azure Blob Storage kräver att du lägger till din Sök tjänst i rollen **Storage BLOB data Reader** .
    1. Azure Data Lake Storage Gen2 kräver att du lägger till din Sök tjänst i rollen **Storage BLOB data Reader** .
    1. Azure Table Storage kräver att du lägger till din Sök tjänst i **läsaren och rollen för data åtkomst** .
5.  Lämna **tilldela åtkomst till** som **Azure AD-användare, grupp eller tjänstens huvud namn**
6.  Sök efter Sök tjänsten, markera den och välj sedan **Spara**

    Exempel för Azure Blob Storage och Azure Data Lake Storage Gen2:

    ![Lägg till roll tilldelning för Storage BLOB data Reader](./media/search-managed-identities/add-role-assignment-storage-blob-data-reader.png "Lägg till roll tilldelning för Storage BLOB data Reader")

    Exempel på Azure Table Storage:

    ![Lägg till roll tilldelning för läsare och data åtkomst](./media/search-managed-identities/add-role-assignment-reader-and-data-access.png "Lägg till roll tilldelning för läsare och data åtkomst")

### <a name="3---create-the-data-source"></a>3 – skapa data källan

[REST API](https://docs.microsoft.com/rest/api/searchservice/create-data-source), Azure Portal och [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet) stöder anslutnings strängen för hanterad identitet. Nedan visas ett exempel på hur du skapar en data källa för att indexera data från ett lagrings konto med hjälp av [REST API](https://docs.microsoft.com/rest/api/searchservice/create-data-source) och en anslutnings sträng för hanterad identitet. Formatet för anslutnings strängen för hanterad identitet är detsamma för REST API, .NET SDK och Azure Portal.

När du indexerar från ett lagrings konto måste data källan ha följande obligatoriska egenskaper:

* **Name** är det unika namnet på data källan i Sök tjänsten.
* **bastyp**
    * Azure Blob-lagring: `azureblob`
    * Azure Table-lagring: `azuretable`
    * Azure Data Lake Storage Gen2: **typ** anges när du registrerar dig för för hands versionen med [det här formuläret](https://aka.ms/azure-cognitive-search/mi-preview-request).
* **klientautentiseringsuppgifter**
    * När du använder en hanterad identitet för att autentisera, skiljer sig formatet på **autentiseringsuppgifter** till om du inte använder en hanterad identitet. Här kommer du att ange ett ResourceId som saknar konto nyckel eller lösen ord. ResourceId måste innehålla prenumerations-ID för lagrings kontot, resurs gruppen för lagrings kontot och lagrings kontots namn.
    * Hanterat identitets format: 
        * *ResourceId =/Subscriptions/**ditt prenumerations-ID**/resourceGroups/**resurs gruppens namn**/providers/Microsoft.Storage/storageAccounts/**ditt lagrings konto namn**/;*
* **container** anger ett behållar-eller tabell namn i ditt lagrings konto. Som standard kan alla blobar i behållaren hämtas. Om du bara vill indexera blobbar i en viss virtuell katalog kan du ange den katalogen med hjälp av parametern valfri **fråga** .

Exempel på hur du skapar ett BLOB-källdokument med hjälp av [REST API](https://docs.microsoft.com/rest/api/searchservice/create-data-source):

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name/;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}   
```

### <a name="4---create-the-index"></a>4 – Skapa indexet

Indexet anger fält i ett dokument, attribut och andra konstruktioner som formar Sök funktionen.

Så här skapar du ett index med ett sökbart `content` fält för att lagra texten som extraheras från blobbar:   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

Mer information om hur du skapar index finns i [skapa index](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="5---create-the-indexer"></a>5 – skapa indexeraren

En indexerare ansluter en data källa med ett mål Sök index och innehåller ett schema för att automatisera data uppdateringen.

När indexet och data källan har skapats är du redo att skapa indexeraren.

Exempel på indexerings definition för en BLOB-indexerare:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Indexeraren körs varannan timme (schema intervall anges till "PT2H"). Om du vill köra en indexerare var 30: e minut anger du intervallet till "PT30M". Det kortaste intervall som stöds är 5 minuter. Schemat är valfritt – om det utelämnas körs en indexerare bara en gång när den skapas. Du kan dock köra en indexerare på begäran när du vill.   

Mer information om API för att skapa index finns i [skapa indexerare](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Mer information om hur du definierar indexerare scheman finns i [så här schemalägger du indexerare för Azure kognitiv sökning](search-howto-schedule-indexers.md).

## <a name="see-also"></a>Se även

Läs mer om Azure Storage indexerare:
* [Azure Blob-indexeraren](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2 indexerare](search-howto-index-azure-data-lake-storage.md)
* [Azure Table-indexeraren](search-howto-indexing-azure-tables.md)
