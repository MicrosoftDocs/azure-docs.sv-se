---
title: ta med fil
description: ta med fil
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 3693f46a602929f9d6e2f4e29083b071ae6a9e9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95972490"
---
Nu kan du använda datautforskarverktyget i Azure Portal för att skapa en grafdatabas. 

1. Välj **datautforskaren**  >  **nytt diagram**.

    Området **Lägg till diagram** visas längst till höger, du kan behöva bläddra åt höger för att se det.

    ![Datautforskaren i Azure Portal, sidan Lägg till diagram](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer-graph.png)

2. På sidan **Lägg till diagram** anger du inställningarna för den nya grafen.

    Inställning|Föreslaget värde|Beskrivning
    ---|---|---
    Databas-id|sample-database|Ange *sample-database* som namn på den nya databasen. Databasnamn måste innehålla mellan 1 och 255 tecken och får inte innehålla `/ \ # ?` eller avslutande blanksteg.
    Dataflöde|400 RU:er|Ändra genomflödet till 400 begäransenheter per sekund (RU/s). Du kan skala upp dataflödet senare om du vill minska svarstiden.
    Graf-id|sample-graph|Ange *sample-graph* som namnet på den nya samlingen. Samma teckenkrav gäller för diagramnamn som databas-ID:n.
    Partition Key (Partitionsnyckel)| /pk |Alla Cosmos DB-konton behöver en partitionsnyckel för att skalas vågrätt. Lär dig hur du väljer en lämplig partitionsnyckel i [artikeln diagram data partitionering](../articles/cosmos-db/graph-partitioning.md).

3. När formuläret har fyllts i väljer du **OK**.