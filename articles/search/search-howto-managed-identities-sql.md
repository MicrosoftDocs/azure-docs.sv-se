---
title: Konfigurera en anslutning till Azure SQL Database med hjälp av en hanterad identitet (förhands granskning)
titleSuffix: Azure Cognitive Search
description: Lär dig hur du konfigurerar en Indexer-anslutning till Azure SQL Database med hjälp av en hanterad identitet (förhands granskning)
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 321c13e88cb09c7078a169c3e1666cf781ec7787
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553146"
---
# <a name="set-up-an-indexer-connection-to-azure-sql-database-using-a-managed-identity-preview"></a>Konfigurera en Indexer-anslutning till Azure SQL Database med hjälp av en hanterad identitet (förhands granskning)

> [!IMPORTANT] 
> Stöd för att konfigurera en anslutning till en data källa med hjälp av en hanterad identitet finns för närvarande i offentlig för hands version. För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar.

På den här sidan beskrivs hur du konfigurerar en Indexer-anslutning till Azure SQL Database med hjälp av en hanterad identitet i stället för att ange autentiseringsuppgifter i anslutnings strängen för data käll objekt.

Innan du lär dig mer om den här funktionen rekommenderar vi att du har en förståelse för vad en indexerare är och hur du konfigurerar en indexerare för din data källa. Mer information finns på följande länkar:

* [Översikt över indexeraren](search-indexer-overview.md)
* [Azure SQL-indexeraren](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>Konfigurera en anslutning med en hanterad identitet

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 – aktivera systemtilldelad hanterad identitet

När en systemtilldelad hanterad identitet är aktive rad skapar Azure en identitet för din Sök tjänst som kan användas för att autentisera till andra Azure-tjänster inom samma klient organisation och prenumeration. Du kan sedan använda den här identiteten i RBAC-tilldelningar (rollbaserad åtkomst kontroll) som tillåter åtkomst till data vid indexering.

![Aktivera systemtilldelad hanterad identitet](./media/search-managed-identities/turn-on-system-assigned-identity.png "Aktivera systemtilldelad hanterad identitet")

När du har valt **Spara** visas ett objekt-ID som har tilldelats till din Sök tjänst.

![Objekt-ID](./media/search-managed-identities/system-assigned-identity-object-id.png "Objekt-ID")

### <a name="2---provision-azure-active-directory-admin-for-sql-server"></a>2-etablera Azure Active Directory administratör för SQL Server

När du ansluter till databasen i nästa steg måste du ansluta till ett Azure Active Directory (Azure AD)-konto som har administratörs åtkomst till databasen för att ge din Sök tjänst behörighet att komma åt databasen.

Följ anvisningarna [här](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) för att ge din Azure AD-konto administratör åtkomst till databasen.

### <a name="3---assign-the-search-service-permissions"></a>3 – tilldela Sök tjänst behörigheter

Följ stegen nedan för att tilldela Sök tjänst behörighet för att läsa databasen.

1. Anslut till Visual Studio

    ![Anslut till Visual Studio](./media/search-managed-identities/connect-with-visual-studio.png "Anslut till Visual Studio")

2. Autentisera med ditt Azure AD-konto

    ![Autentisera](./media/search-managed-identities/visual-studio-authentication.png "Autentisera")

3. Kör följande kommandon:

    Inkludera hakparenteser runt Sök tjänstens namn.
    
    ```
    CREATE USER [your search service name here] FROM EXTERNAL PROVIDER;
    EXEC sp_addrolemember 'db_datareader', [your search service name here];
    ```

    ![Ny fråga](./media/search-managed-identities/visual-studio-new-query.png "Ny fråga")

    ![Kör fråga](./media/search-managed-identities/visual-studio-execute-query.png "Kör fråga")

>[!NOTE]
> Om Sök tjänst identiteten från steg 1 ändras när du har slutfört det här steget måste du ta bort roll medlemskapet och ta bort användaren i SQL-databasen och sedan lägga till behörigheterna igen genom att slutföra steg 3 igen.
> Du tar bort roll medlemskapet och användaren kan åstadkomma detta genom att köra följande kommandon:
> ```
> sp_droprolemember 'db_datareader', [your search service name];
> DROP USER IF EXISTS [your search service name];
> ```

### <a name="4---add-a-role-assignment"></a>4 – Lägg till en roll tilldelning

I det här steget ska du ge Azure Kognitiv sökning-tjänstens behörighet att läsa data från din SQL Server.

1. I Azure Portal navigerar du till din Azure SQL Server-sida.
2. Välj **åtkomst kontroll (IAM)**
3. Välj **Lägg till** och **Lägg till roll tilldelning**

    ![Lägg till rolltilldelning](./media/search-managed-identities/add-role-assignment-sql-server.png "Lägg till rolltilldelning")

4. Välj lämplig **läsar** roll.
5. Lämna **tilldela åtkomst till** som **Azure AD-användare, grupp eller tjänstens huvud namn**
6. Sök efter Sök tjänsten, markera den och välj sedan **Spara**

    ![Lägg till roll tilldelning för läsare](./media/search-managed-identities/add-role-assignment-sql-server-reader-role.png "Lägg till roll tilldelning för läsare")

### <a name="5---create-the-data-source"></a>5 – skapa data källan

[REST API](https://docs.microsoft.com/rest/api/searchservice/create-data-source), Azure Portal och [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet) stöder anslutnings strängen för hanterad identitet. Nedan visas ett exempel på hur du skapar en data källa för att indexera data från en Azure SQL Database med hjälp av [REST API](https://docs.microsoft.com/rest/api/searchservice/create-data-source) och en anslutnings sträng för hanterad identitet. Formatet för anslutnings strängen för hanterad identitet är detsamma för REST API, .NET SDK och Azure Portal.

När du skapar en data källa med hjälp av [REST API](https://docs.microsoft.com/rest/api/searchservice/create-data-source)måste data källan ha följande obligatoriska egenskaper:

* **Name** är det unika namnet på data källan i Sök tjänsten.
* **typ** är `azuresql`
* **klientautentiseringsuppgifter**
    * När du använder en hanterad identitet för att autentisera, skiljer sig formatet på **autentiseringsuppgifter** till om du inte använder en hanterade-identitet. Här anger du en initial katalog eller ett databas namn och ett ResourceId som inte har någon konto nyckel eller lösen ord. ResourceId måste innehålla prenumerations-ID: t för Azure SQL Database, resurs gruppen för SQL Database och namnet på SQL-databasen. 
    * Format för anslutnings sträng för hanterad identitet:
        * *Initial katalog | Databas =**databas namn**; ResourceId =/Subscriptions/**ditt prenumerations-ID**/resourceGroups/**resurs gruppens namn**/providers/Microsoft.SQL/Servers/**ditt SQL Server namn**/; Timeout för anslutning =**anslutningens timeout-längd**;*
* **container** anger namnet på den tabell eller vy som du vill indexera.

Exempel på hur du skapar ett Azure SQL Data Source-objekt med hjälp av [REST API](https://docs.microsoft.com/rest/api/searchservice/create-data-source):

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-datasource",
    "type" : "azuresql",
    "credentials" : { "connectionString" : "Database=sql-database;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azure-sql-resource-group/providers/Microsoft.Sql/servers/sql-server-search-demo;Connection Timeout=30;" },
    "container" : { "name" : "my-table" }
} 
```

### <a name="6---create-the-index"></a>6 – skapa indexet

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

Mer information om hur du skapar index finns i [skapa index](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="7---create-the-indexer"></a>7 – skapa indexeraren

En indexerare ansluter en data källa med ett mål Sök index och innehåller ett schema för att automatisera data uppdateringen.

När indexet och data källan har skapats är du redo att skapa indexeraren.

Exempel på indexerings definition för en Azure SQL-indexerare:

```
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-indexer",
    "dataSourceName" : "sql-datasource",
    "targetIndexName" : "my-target-index",
    "schedule" : { "interval" : "PT2H" }
}
```    

Indexeraren körs varannan timme (schema intervall anges till "PT2H"). Om du vill köra en indexerare var 30: e minut anger du intervallet till "PT30M". Det kortaste intervall som stöds är 5 minuter. Schemat är valfritt – om det utelämnas körs en indexerare bara en gång när den skapas. Du kan dock köra en indexerare på begäran när du vill.   

Mer information om API för att skapa index finns i [skapa indexerare](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Mer information om hur du definierar indexerare scheman finns i [så här schemalägger du indexerare för Azure kognitiv sökning](search-howto-schedule-indexers.md).

## <a name="troubleshooting"></a>Felsökning

Om du får ett fel meddelande när indexeraren försöker ansluta till data källan som säger att klienten inte har behörighet att komma åt servern, kan du ta en titt på [vanliga index fel](https://docs.microsoft.com/azure/search/search-indexer-troubleshooting).

## <a name="see-also"></a>Se även

Läs mer om Azure SQL-indexeraren:
* [Azure SQL-indexeraren](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
