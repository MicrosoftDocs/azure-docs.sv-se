---
title: Ansluta till Azure Synapse Link för Azure Cosmos DB
description: Lär dig hur du ansluter en Azure Cosmos DB-databas till en Azure dataSynapses-arbetsyta med Azure Synapse-länken.
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 03/02/2021
ms.author: acomet
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 1c2dedfcbfd937215320c90d63c60d2e868939b7
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868604"
---
# <a name="connect-to-azure-synapse-link-for-azure-cosmos-db"></a>Ansluta till Azure Synapse Link för Azure Cosmos DB

Den här artikeln beskriver hur du kommer åt en Azure Cosmos DB databas från Azure Synapse Analytics Studio med Azure Synapse-länken.

## <a name="prerequisites"></a>Förutsättningar

Innan du ansluter en Azure Cosmos DB databas till din arbets yta behöver du:

* Befintliga Azure Cosmos DB databasen eller skapa ett nytt konto genom att följa stegen i [snabb start: hantera ett Azure Cosmos DB konto](../../cosmos-db/how-to-manage-database-account.md).
* Befintlig Azure Synapse-arbetsyta eller skapa en ny arbets yta genom att följa stegen i [snabb start: skapa en Synapse-arbetsyta](../quickstart-create-workspace.md).

## <a name="enable-synapse-link-on-an-azure-cosmos-db-database-account"></a>Aktivera Synapse-länk på ett Azure Cosmos DB databas konto

Om du vill köra storskalig analys i Azure Cosmos DB utan att påverka drifts prestanda rekommenderar vi att du aktiverar Synapse-länken för Azure Cosmos DB. Synapse-länken ger HTAP-kapacitet till en behållare och inbyggt stöd i Azure Synapse.

## <a name="go-to-synapse-studio"></a>Gå till Synapse Studio

Från din Azure Synapse-arbetsyta väljer du **Starta Synapse Studio**. På Start sidan för Synapse Studio väljer du **data**, som tar dig till data Object Explorer.

## <a name="connect-an-azure-cosmos-db-database-to-an-azure-synapse-workspace"></a>Ansluta en Azure Cosmos DB-databas till en Azure Synapse-arbetsyta

Anslutning av en Azure Cosmos DB databas görs som en länkad tjänst. Med en Azure Cosmos DB länkad tjänst kan du söka efter och utforska data, läsa och skriva från Apache Spark för Azure Synapse Analytics eller SQL till Azure Cosmos DB.

Från data Object Explorer kan du ansluta direkt till en Azure Cosmos DB-databas genom att följa dessa steg:

1. Välj **+** ikonen nära **data**.
1. Välj **Anslut till externa data**.
1. Välj det API som du vill ansluta till, till exempel **SQL API** eller **API för MongoDB**.
1. Välj **Fortsätt**.
1. Använd ett eget namn för att namnge den länkade tjänsten. Namnet kommer att visas i data Object Explorer och används av Azure Synapse-körningar för att ansluta till databasen och behållarna.
1. Välj det **Azure Cosmos DB konto namnet** och **databas namnet**.
1. Valfritt Om ingen region anges dirigeras Azure Synapse runtime-åtgärder mot den närmaste region där analys lagret är aktiverat. Du kan också manuellt ange den region som du vill att användarna ska använda för att få åtkomst till den Azure Cosmos DB analytiska butiken. Välj **Ytterligare egenskaper för anslutning** och välj sedan **ny**. Under **egenskaps namn**, anger du **PreferredRegions**. Ange **värdet** till den region som du vill ha, till exempel **WestUS2**. (Det finns inga blank steg mellan orden och talet.)
1. Välj **Skapa**.

Azure Cosmos DB databaser visas på fliken **länkad** under avsnittet **Azure Cosmos DB** . Med Azure Cosmos DB kan du särskilja en HTAP-aktiverad behållare från en OLTP-behållare via följande ikoner:

**Endast OLTP-behållare**:

![Visualisering som visar ikonen för OLTP-behållare.](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**HTAP-aktive rad behållare**:

![Visualisering som visar HTAP container-ikonen.](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Interagera snabbt med kod genererade åtgärder

Genom att högerklicka på en behållare har du en lista med gester som utlöser en spark-eller SQL-körning. Att skriva till en behållare sker via transaktions lagringen för Azure Cosmos DB och kommer att förbruka enheter för programbegäran.  

## <a name="next-steps"></a>Nästa steg

* [Lär dig vad som stöds mellan Azure Synapse och Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Konfigurera privata slut punkter för Azure Cosmos DB analys lager](../../cosmos-db/analytical-store-private-endpoints.md)
* [Lär dig hur du frågar analys lagret med Spark](./how-to-query-analytical-store-spark.md)