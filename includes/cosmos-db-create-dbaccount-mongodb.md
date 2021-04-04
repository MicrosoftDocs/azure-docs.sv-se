---
title: ta med fil
description: ta med fil
services: cosmos-db
ms.custom: include file
ms.openlocfilehash: ad4445cbea6553a7a96299e1276dbe8f3816e166
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994574"
---
1. Logga in på [Azure Portal](https://portal.azure.com/) i ett nytt webbläsarfönster.

2. Välj **skapa en resurs** i den vänstra menyn.
   
   ![Skapa en resurs på Azure-portalen](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-0.png)
   
3. Välj **databaser** Azure Cosmos DB på sidan **nytt**  >  .
   
   ![Fönstret Databaser på Azure Portal](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)
   
3. På sidan **skapa Azure Cosmos DB konto** anger du inställningarna för det nya Azure Cosmos DB-kontot. 
 
    Inställning|Värde|Beskrivning
    ---|---|---
    Prenumeration|Din prenumeration|Välj den Azure-prenumeration som ska användas för det här Azure Cosmos DB-kontot. 
    Resursgrupp|Skapa ny<br><br>Ange sedan samma namn som konto namnet|Välj **Skapa ny**. Ange sedan ett nytt resurs grupps namn för ditt konto. För enkelhetens skull använder du samma namn som ditt Azure Cosmos DB konto namn. 
    Account Name|Ange ett unikt namn|Ange ett unikt namn som identifierar ditt Azure Cosmos DB-konto. Din konto-URI kommer att *Mongo.Cosmos.Azure.com* till ditt unika konto namn.<br><br>Konto namnet får bara innehålla gemena bokstäver, siffror och bindestreck (-) och måste vara mellan 3 och 31 tecken långt.
    API|Azure Cosmos DB för mongo DB-API|API:et avgör vilken typ av konto som skapas. Azure Cosmos DB tillhandahåller fem API: er: Core (SQL) för dokument databaser, Gremlin för graf-databaser, Azure Cosmos DB för mongo DB API för dokument databaser, Azure Table och Cassandra. För närvarande måste du skapa ett separat konto för varje API. <br><br>Välj **Azure Cosmos dB för MONGO DB-API**  eftersom du skapar en samling som fungerar med MongoDB i den här snabb starten.<br><br>[Läs mer om Azure Cosmos dB för MongoDB-API: et](../articles/cosmos-db/mongodb-introduction.md).|
    Location|Välj den region som är närmast dina användare|Välj en geografisk plats som värd för ditt Azure Cosmos DB-konto. Använd den plats som är närmast dina användare så att de får så snabb åtkomst till data som möjligt.|
    Kapacitetsläge|Allokerat data flöde eller Server lös|Välj **tillhandahållet data flöde** för att skapa ett konto i ett [allokerat data flödes](../articles/cosmos-db/set-throughput.md) läge. Välj **Server** lös om du vill skapa ett konto i [Server](../articles/cosmos-db/serverless.md) fritt läge.<br><br>**Obs!** endast MongoDB API version 3,6 stöds av Server lös konton. Om du väljer 3,2 som version kommer kontot att tvingas i det etablerade data flödes läget.

    Välj **Granska + skapa**. Du kan hoppa över avsnittet **Nätverk** och **Taggar**. 

    ![Den nya kontosidan för Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. Det tar några minuter att skapa kontot. Vänta tills portalen visar **Grattis! Din Azure Cosmos DB för mongo DB API-konto är klar** .

    ![Meddelandefönstret på Azure-portalen](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
