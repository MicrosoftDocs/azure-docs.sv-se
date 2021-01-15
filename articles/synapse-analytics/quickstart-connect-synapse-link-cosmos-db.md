---
title: 'Snabb start: ansluta till Azure Synapse-länken för Azure Cosmos DB'
description: Så här ansluter du en Azure Cosmos DB till en Synapse-arbetsyta med Synapse-länk
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.subservice: synapse-link
ms.topic: quickstart
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 7462c78ecad718c30980a98ff7659a2a4d2ea020
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222164"
---
# <a name="quickstart-connect-to-azure-synapse-link-for-azure-cosmos-db"></a>Snabb start: ansluta till Azure Synapse-länken för Azure Cosmos DB

Den här artikeln beskriver hur du kommer åt en Azure Cosmos DB databas från Azure Synapse Analytics Studio med Synapse-länken. 

## <a name="prerequisites"></a>Förutsättningar

Innan du ansluter ett Azure Cosmos DB konto till din arbets yta finns det några saker du behöver.

* Befintligt Azure Cosmos DB konto eller skapa ett nytt konto efter den här [snabb](../cosmos-db/how-to-manage-database-account.md) starten
* Befintlig arbets yta i Synapse eller skapa en ny arbets yta efter den här [snabb](./quickstart-create-workspace.md) starten 

## <a name="enable-azure-cosmos-db-analytical-store"></a>Aktivera Azure Cosmos DB analys lager

Om du vill köra storskalig analys i Azure Cosmos DB utan att påverka din drifts prestanda rekommenderar vi att du aktiverar Synapse-länken för Azure Cosmos DB. Den här funktionen ger HTAP-funktioner till en behållare och inbyggt stöd i Azure Synapse. Följ den här snabb starten om du vill aktivera Synapse-länken för Cosmos DB behållare.

## <a name="navigate-to-synapse-studio"></a>Gå till Synapse Studio

Från din Synapse-arbetsyta väljer du **Starta Synapse Studio**. På Start sidan för Synapse Studio väljer du * * data, vilket leder dig till **Data Object Explorer**.

## <a name="connect-an-azure-cosmos-db-database-to-a-synapse-workspace"></a>Ansluta en Azure Cosmos DB-databas till en Synapse-arbetsyta

Anslutning av en Azure Cosmos DB databas görs som en länkad tjänst. Med en Cosmos DB länkad tjänst kan användare söka efter och utforska data, läsa och skriva från Apache Spark för Azure Synapse Analytics eller SQL till Azure Cosmos DB.

Från data Object Explorer kan du ansluta en Azure Cosmos DB databas direkt genom att utföra följande steg:

1. Välj * *_+_* _ ikon nära data
2. Välj _ *Anslut till externa data**
3. Välj det API som du vill ansluta till: SQL eller MongoDB
4. Välj **_Fortsätt_* _
5. Namnge den länkade tjänsten. Namnet visas i Object Explorer och används av Synapse körnings tider för att ansluta till databasen och behållarna. Vi rekommenderar att du använder ett eget namn.
6. Välj _ *Cosmos DB konto namn** och **databas namn**
7. Valfritt Om ingen region anges dirigeras Synapse kör tids åtgärder till den närmaste region där analys lagret är aktiverat. Du kan dock ange manuellt vilken region du vill att användarna ska ha åtkomst till Cosmos DB analys lager. Välj **ytterligare anslutnings egenskaper** och sedan **ny**. Under **egenskaps namn** skriver du **_PreferredRegions_*_ och anger _*-värdet** till den region som du vill ha (exempel: WestUS2, det finns inget blank steg mellan ord och siffror)
8. Välj **_skapa_* _

Azure Cosmos DB-databaser visas under fliken _ *länkad** i avsnittet Azure Cosmos dB. Du kan skilja en HTAP-aktiverad Azure Cosmos DB-behållare från en skrivskyddad OLTP-behållare med följande ikoner:

**Synapse-behållare**:

![HTAP-behållare](./media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

**Endast behållare för OLTP**:

![OLTP-behållare](./media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Interagera snabbt med kod genererade åtgärder

När du högerklickar på en behållare har du en lista med gester som utlöser en spark-eller SQL-körning. Att skriva till en behållare sker via transaktions lagringen för Azure Cosmos DB och kommer att förbruka enheter för programbegäran.  

## <a name="next-steps"></a>Nästa steg

* [Lär dig vad som stöds mellan Synapse och Azure Cosmos DB](./synapse-link/concept-synapse-link-cosmos-db-support.md)
* [Lär dig hur du frågar ett analys lager med Apache Spark för Azure Synapse Analytics](synapse-link/how-to-query-analytical-store-spark.md)