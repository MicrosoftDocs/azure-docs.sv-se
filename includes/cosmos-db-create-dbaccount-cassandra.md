---
title: ta med fil
description: ta med fil
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 10/01/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 30a7f3ae878cebcd1e58287fc59241651dac2bfd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "94504135"
---
1. Logga in på [Azure Portal](https://portal.azure.com/) i ett nytt webbläsarfönster.

2. Välj **skapa en resurs** i den vänstra menyn.
   
   ![Skapa en resurs på Azure-portalen](./media/cosmos-db-create-dbaccount-cassandra/create-nosql-db-databases-json-tutorial-0.png)
   
3. Välj **databaser** Azure Cosmos DB på sidan **nytt**  >  .
   
   ![Fönstret Databaser på Azure Portal](./media/cosmos-db-create-dbaccount-cassandra/create-nosql-db-databases-json-tutorial-1.png)
   
3. På sidan **skapa Azure Cosmos DB konto** anger du inställningarna för det nya Azure Cosmos DB-kontot. 
 
    Inställning|Värde|Beskrivning
    ---|---|---
    Prenumeration|Din prenumeration|Välj den Azure-prenumeration som ska användas för det här Azure Cosmos DB-kontot. 
    Resursgrupp|Skapa ny<br><br>Ange sedan samma namn som konto namnet|Välj **Skapa ny**. Ange sedan ett nytt resurs grupps namn för ditt konto. För enkelhetens skull använder du samma namn som namnet på ditt Azure Cosmos-konto. 
    Account Name|Ange ett unikt namn|Ange ett unikt namn som identifierar ditt Azure Cosmos DB-konto. Din konto-URI kommer att *Cassandra.Cosmos.Azure.com* till ditt unika konto namn.<br><br>Konto namnet får bara innehålla gemena bokstäver, siffror och bindestreck (-) och måste vara mellan 3 och 31 tecken långt.
    API|Cassandra|API:et avgör vilken typ av konto som skapas. Azure Cosmos DB tillhandahåller fem API: er: Core (SQL) för dokument databaser, Gremlin för graf-databaser, MongoDB för dokument databaser, Azure Table och Cassandra. Du måste skapa ett separat konto för varje API. <br><br>Välj **Cassandra**, eftersom du i den här snabb starten skapar en tabell som fungerar med API för Cassandra. <br><br>[Läs mer om Cassandra-API:n](../articles/cosmos-db/cassandra-introduction.md).|
    Location|Välj den region som är närmast dina användare|Välj en geografisk plats som värd för ditt Azure Cosmos DB-konto. Använd den plats som är närmast dina användare så att de får så snabb åtkomst till data som möjligt.
    Kapacitetsläge|Allokerat data flöde eller Server lös|Välj **tillhandahållet data flöde** för att skapa ett konto i ett [allokerat data flödes](../articles/cosmos-db/set-throughput.md) läge. Välj **Server** lös om du vill skapa ett konto i [Server](../articles/cosmos-db/serverless.md) fritt läge.

    Välj **Granska + skapa**. Du kan hoppa över avsnittet **nätverk**, **säkerhets kopiering**, **kryptering** och **taggar** . 

    ![Den nya kontosidan för Azure Cosmos DB](./media/cosmos-db-create-dbaccount-cassandra/azure-cosmos-db-create-new-account.png)

4. Det tar några minuter att skapa kontot. Vänta tills portalen visar sidan som säger **Grattis! Ditt Azure Cosmos DBs konto har skapats**.

