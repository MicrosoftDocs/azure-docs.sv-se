---
title: Snabb start – skapa Azure Cosmos DB resurser från Azure Portal
description: Den här snabb starten visar hur du skapar en Azure Cosmos-databas, behållare och objekt med hjälp av Azure Portal.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/10/2020
ms.openlocfilehash: 041b930a0c2fa82727216b4ca0c8367387d0d801
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367271"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Snabb start: skapa ett Azure Cosmos-konto, databas, behållare och objekt från Azure Portal
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Azure-portalen](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan använda Azure Cosmos DB för att snabbt skapa och fråga nyckel/värde-databaser, dokument databaser och Graf-databaser, som alla drar nytta av den globala distributionen och den horisontella skalnings kapaciteten i kärnan av Azure Cosmos DB. 

Den här snabb starten visar hur du använder Azure Portal för att skapa ett Azure Cosmos DB [SQL API](./introduction.md) -konto, skapa en dokument databas och behållare och lägga till data i behållaren. 

## <a name="prerequisites"></a>Förutsättningar

En Azure-prenumeration eller ett kostnads fritt Azure Cosmos DB utvärderings konto
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Skapa ett Azure Cosmos DB-konto

Gå till [Microsoft Azure-portalen](https://portal.azure.com/) för att skapa ett Azure Cosmos DB-konto. Sök efter och välj **Azure Cosmos DB**.

   :::image type="content" source="./media/create-cosmosdb-resources-portal/find-nosql-cosmosdb-marketplace.png" alt-text="Fönstret Databaser på Azure Portal":::

1. Välj **Lägg till**.
1. Ange de grundläggande inställningarna för det nya Azure Cosmos-kontot på sidan **Skapa Azure Cosmos DB-konto**. 

    |Inställning|Värde|Beskrivning |
    |---|---|---|
    |Prenumeration|Prenumerationens namn|Välj den Azure-prenumeration som ska användas för det här Azure Cosmos-kontot. |
    |Resursgrupp|Namn på resursgrupp|Välj en resursgrupp eller välj **Skapa ny** och ange sedan ett unikt namn för den nya resursgruppen. |
    |Kontonamn|Ett unikt namn|Ange ett namn som identifierar ditt Azure Cosmos-konto. Eftersom *documents.azure.com* läggs till det namn du anger för att skapa din URI måste du använda ett unikt namn.<br><br>Namnet får endast innehålla gemener, siffror och bindestreck (-). Det måste innehålla 3–31 tecken.|
    |API|Typ av konto som skapas|Välj **Core (SQL)** för att skapa en dokumentdatabas och kör frågor med hjälp av SQL-syntax. <br><br>API:et avgör vilken typ av konto som skapas. Azure Cosmos DB tillhandahåller fem API: er: Core (SQL) och MongoDB för dokument data, Gremlin för graf-data, Azure Table och Cassandra. För närvarande måste du skapa ett separat konto för varje API. <br><br>[Läs mer om SQL-API: et](introduction.md).|
    |Kapacitetsläge|Allokerat data flöde eller Server lös|Välj **tillhandahållet data flöde** för att skapa ett konto i ett [allokerat data flödes](set-throughput.md) läge. Välj **Server** lös om du vill skapa ett konto i [Server](serverless.md) fritt läge.|
    |Använd Azure Cosmos DB kostnads fri nivå rabatt|Tillämpa eller Verkställ inte|Med Azure Cosmos DB kostnads fri nivå får du de första 400 RU/s och 5 GB lagring kostnads fritt i ett konto. Läs mer om den [kostnads fria nivån](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Location|Den region som är närmast dina användare|Välj en geografisk plats som värd för ditt Azure Cosmos DB-konto. Använd den plats som är närmast dina användare för att ge dem så snabb åtkomst till data som möjligt.|
    |Kontotyp|Produktion eller icke-produktion|Välj **produktion** om kontot ska användas för en produktions arbets belastning. Välj **icke-produktion** om kontot ska användas för icke-produktion, t. ex. utveckling, testning, frågor och svar eller mellanlagring. Det här är en Azure-resurs tag-inställning som justerar Portal upplevelsen, men som inte påverkar det underliggande Azure Cosmos DB kontot. Du kan ändra det här värdet när som helst.|
    |Geo-redundans|Aktivera eller inaktivera|Aktivera eller inaktivera global distribution på ditt konto genom att para ihop din region med en par region. Du kan lägga till fler regioner i kontot senare.|
    |Skrivåtgärder för flera regioner|Aktivera eller inaktivera|Med kapacitet för flera regioner kan du dra nytta av det etablerade data flödet för dina databaser och behållare över hela världen.|
    |Tillgänglighetszoner|Aktivera eller inaktivera|Tillgänglighetszoner hjälpa dig att förbättra tillgängligheten och återhämtnings förmågan för ditt program.|

> [!NOTE]
> Du kan ha upp till en kostnads fri nivå Azure Cosmos DB ett konto per Azure-prenumeration och måste välja när du skapar kontot. Om du inte ser alternativet för att tillämpa rabatten på den kostnads fria nivån innebär det att ett annat konto i prenumerationen redan har Aktiver ATS med den kostnads fria nivån.

> [!NOTE]
> Följande alternativ är inte tillgängliga om du väljer **Server** lös som **kapacitets läge**:
> - Tillämpa rabatt för kostnadsfri nivå
> - Geo-redundans
> - Skrivåtgärder för flera regioner
   
   :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail-2.png" alt-text="Den nya kontosidan för Azure Cosmos DB":::

1. Välj **Granska + skapa**. Du kan hoppa över avsnitten **Nätverk** och **Taggar**.

1. Granska kontoinställningarna och välj sedan **Skapa**. Det tar några minuter att skapa kontot. Vänta tills portalsidan visar meddelandet **Distributionen är klar**. 

    :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png" alt-text="Meddelandefönstret på Azure-portalen":::

1. Välj **Gå till resurs** för att gå till sidan för Azure Cosmos DB-kontot. 

    :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-quickstart-pane.png" alt-text="Sidan för Azure Cosmos DB-kontot":::

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Lägg till en databas och en behållare 

Du kan använda Datautforskaren i Azure Portal för att skapa en databas och behållare. 

1.  Välj **datautforskaren** i det vänstra navigerings fältet på ditt Azure Cosmos DB konto och välj sedan **ny behållare**. 
    
    Du kan behöva rulla åt höger för att se fönstret **Lägg till behållare** .
    
    :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png" alt-text="Datautforskaren på Azure-portalen, fönstret Lägg till container":::
    
1.  I fönstret **Lägg till behållare** anger du inställningarna för den nya behållaren.
    
    |Inställning|Föreslaget värde|Beskrivning
    |---|---|---|
    |**Databas-ID**|ToDoList|Ange *ToDoList* som namn på den nya databasen. Databasnamn måste innehålla 1–255 tecken och får inte innehålla `/, \\, #, ?`, eller avslutande blanksteg. Kontrol lera **data flödes alternativet etablera databas** så att du kan dela det data flöde som har etablerats till databasen över alla behållare i databasen. Det här alternativet hjälper också till med kostnads besparingar. |
    |**Dataflöde**|400|Lämna data flödet på 400 enheter för programbegäran per sekund (RU/s). Du kan skala upp dataflödet senare om du vill minska svarstiden.<br><br>**Obs!** den här inställningen är inte tillgänglig när du skapar en ny behållare i ett Server lös konto.| 
    |**Container-ID**|Poster|Ange *objekt* som namn på den nya behållaren. För container-ID:n gäller samma teckenkrav som för databasnamn.|
    |**Partitionsnyckel**| /category| Exemplet som beskrivs i den här artikeln använder */Category* som partitionsnyckel.|

    
    Lägg inte till **unika nycklar** i det här exemplet. Med unika nycklar kan du lägga till ett lager med data integritet i databasen genom att se till att det är unikt för ett eller flera värden per partitionsnyckel. Mer information finns i [unika nycklar i Azure Cosmos DB](unique-keys.md).
    
1.  Välj **OK**. Datautforskaren visar den nya databasen och den behållare som du skapade.

## <a name="add-data-to-your-database"></a>Lägg till data i databasen

Lägg till data i den nya databasen med hjälp av Datautforskaren.

1. Expandera **ToDoList** -databasen i **datautforskaren** och expandera behållaren **objekt** . Välj sedan **objekt** och välj sedan **nytt objekt**. 
   
   :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png" alt-text="Skapa nya dokument i datautforskaren i Azure Portal":::
   
1. Lägg till följande struktur till dokumentet till höger i fönstret **dokument** :

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

1. Välj **Spara**.
   
   :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png" alt-text="Kopiera i JSON-data och välj Spara i Datautforskaren i Azure Portal":::
   
1. Välj **nytt dokument** igen och skapa och spara ett annat dokument med en unik `id` och andra egenskaper och värden som du vill ha. Dokumenten kan ha vilken struktur som helst, eftersom Azure Cosmos DB inte har något schema för dina data.

## <a name="query-your-data"></a>Fråga dina data

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

Om du bara vill ta bort databasen och använda Azure Cosmos-kontot i framtiden kan du ta bort databasen med följande steg:

* Fick ditt Azure Cosmos-konto.
* Öppna **datautforskaren**, högerklicka på den databas som du vill ta bort och välj **ta bort databas**.
* Ange databas-ID/databas namn för att bekräfta borttagnings åtgärden. 

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Azure Cosmos DB-konto, skapar en databas och behållare med hjälp av Datautforskaren. Du kan nu importera ytterligare data till ditt Azure Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)