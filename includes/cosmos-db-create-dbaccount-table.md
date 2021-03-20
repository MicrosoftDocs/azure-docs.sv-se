---
title: ta med fil
description: ta med fil
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 01/24/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 941e2ee7b8eba957d970998324156c50a86439af
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94503870"
---
1. Logga in på [Azure Portal](https://portal.azure.com/) i ett nytt webbläsarfönster.

2. Välj **skapa en resurs** i den vänstra menyn.
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-0.png" alt-text="Skapa en resurs på Azure-portalen":::
   
3. Välj **databaser** Azure Cosmos DB på sidan **nytt**  >  .
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png" alt-text="Fönstret Databaser på Azure Portal":::
   
3. På sidan **skapa Azure Cosmos DB konto** anger du inställningarna för det nya Azure Cosmos DB-kontot. 
 
    Inställning|Värde|Beskrivning
    ---|---|---
    Prenumeration|Din prenumeration|Välj den Azure-prenumeration som ska användas för det här Azure Cosmos DB-kontot. 
    Resursgrupp|**Skapa nytt** och sedan konto namn|Välj **Skapa ny**. Ange sedan ett nytt resurs grupps namn för ditt konto. För enkelhetens skull använder du samma namn som ditt Azure Cosmos DB konto namn. 
    Account Name|Ett unikt namn|Ange ett unikt namn som identifierar ditt Azure Cosmos DB-konto.<br><br>Konto namnet får bara innehålla gemena bokstäver, siffror och bindestreck (-) och måste vara mellan 3 och 31 tecken långt.
    API|Tabell|API:et avgör vilken typ av konto som skapas. Azure Cosmos DB tillhandahåller fem API: er: Core (SQL) för dokument databaser, Gremlin för graf-databaser, MongoDB för dokument databaser, Azure Table och Cassandra. Du måste skapa ett separat konto för varje API. <br><br>Välj **Azure-tabell**, eftersom du i den här snabb starten skapar en tabell som fungerar med tabell-API. <br><br>[Läs mer om tabell-API](../articles/cosmos-db/table-introduction.md).|
    Location|Den region som är närmast dina användare|Välj en geografisk plats som värd för ditt Azure Cosmos DB-konto. Använd den plats som är närmast dina användare så att de får så snabb åtkomst till data som möjligt.
    Kapacitetsläge|Allokerat data flöde eller Server lös|Välj **tillhandahållet data flöde** för att skapa ett konto i ett [allokerat data flödes](../articles/cosmos-db/set-throughput.md) läge. Välj **Server** lös om du vill skapa ett konto i [Server](../articles/cosmos-db/serverless.md) fritt läge.

    Du kan lämna alternativen **GEO-redundans** och **flera regioner** vid **inaktive ring** för att undvika ytterligare kostnader och hoppa över avsnitten **nätverk** och **taggar** .

5. Välj **Granska + skapa**. När verifieringen är klar väljer du **skapa** för att skapa kontot. 
 
   :::image type="content" source="./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png" alt-text="Den nya kontosidan för Azure Cosmos DB":::

6. Det tar några minuter att skapa kontot. Ett meddelande visas som anger att **distributionen pågår**. Vänta tills distributionen är klar och välj sedan **gå till resurs**.

    :::image type="content" source="./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png" alt-text="Fönstret Azure Portal meddelanden":::

