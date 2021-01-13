---
title: Använda Azure Stream Analytics i dedikerad SQL-pool
description: Tips för att använda Azure Stream Analytics med dedikerad SQL-pool i Azure Synapse för utveckling av real tids lösningar.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 9/25/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 8fbe546beb1004214e544f8eb160884c0f64ef9e
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2021
ms.locfileid: "96458230"
---
# <a name="use-azure-stream-analytics-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>Använda Azure Stream Analytics med dedikerad SQL-pool i Azure Synapse Analytics

Azure Stream Analytics är en helt hanterad tjänst som tillhandahåller låg latens, skalbar komplex händelse behandling med hög tillgänglighet för strömning av data i molnet. Du kan lära dig grunderna genom [att läsa introduktion till Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Du kan sedan lära dig hur du skapar en lösning från slut punkt till slut punkt med Stream Analytics genom att följa själv studie kursen [komma igång med Azure Stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

I den här artikeln får du lära dig hur du använder din dedikerade SQL-pool som utgående mottagare för data inmatning med hög data flöde med Azure Stream Analytics-jobb.

## <a name="prerequisites"></a>Krav

* Azure Stream Analytics jobb – om du vill skapa ett Azure Stream Analytics jobb följer du stegen i självstudien [komma igång med Azure Stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) för att:  

    1. Skapa en Event Hub-inmatare
    2. Konfigurera och starta Event Generator-programmet
    3. Etablera ett Stream Analytics jobb
    4. Ange inmatade jobb och fråga
* Dedikerad SQL-pool – om du vill skapa en ny dedikerad SQL-pool följer du stegen i [snabb start: skapa en dedikerad SQL-pool](../quickstart-create-sql-pool-portal.md).

## <a name="specify-streaming-output-to-point-to-your-dedicated-sql-pool"></a>Ange strömmande utdata så att de pekar på din dedikerade SQL-pool

### <a name="step-1"></a>Steg 1

Gå till ditt Stream Analytics jobb från Azure Portal och klicka på **utdata** på menyn **jobb sto pol Ogin** .

### <a name="step-2"></a>Steg 2

Klicka på knappen **Lägg till** och välj **Azure Synapse Analytics** på den nedrullningsbara menyn.

![Välj Azure Synapse Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output.png)

### <a name="step-3"></a>Steg 3

Ange följande värden:

* *Alias för utdata*: Ange ett eget namn för jobbets utdata.
* *Prenumeration*:
  * Om din dedikerade SQL-pool är i samma prenumeration som Stream Analytics jobb klickar du på ***Välj Azure Synapse Analytics från dina prenumerationer** _.
  _ Om din dedikerade SQL-pool finns i en annan prenumeration klickar du på Ange inställningar för Azure Synapse Analytics manuellt.
* *Databas*: Välj mål databas i list rutan.
* *Användar namn*: Ange användar namnet för ett konto som har Skriv behörighet för databasen.
* *Lösen ord*: Ange lösen ordet för det angivna användar kontot.
* *Tabell*: Ange namnet på mål tabellen i databasen.
* Klicka på knappen **Spara**

![Slutfört Azure Synapse Analytics-formulär](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output-db-settings.png)

### <a name="step-4"></a>Steg 4

Innan du kan köra ett test måste du skapa tabellen i din dedikerade SQL-pool.  Kör följande skript för att skapa tabell med SQL Server Management Studio (SSMS) eller ditt val av frågeinställningar.

```sql
CREATE TABLE SensorLog
(
    RecordType VARCHAR(2)
    , SystemIdentity VARCHAR(2)
    , FileNum INT
    , SwitchNum VARCHAR(50)
    , CallingNum VARCHAR(25)
    , CallingIMSI VARCHAR(25)
    , CalledNum VARCHAR(25)
    , CalledIMSI VARCHAR(25)
    , DateS VARCHAR(25)
    , TimeS VARCHAR(25)
    , TimeType INT
    , CallPeriod INT
    , CallingCellID VARCHAR(25)
    , CalledCellID VARCHAR(25)
    , ServiceType VARCHAR(25)
    , [Transfer] INT
    , IncomingTrunk VARCHAR(25)
    , OutgoingTrunk VARCHAR(25)
    , MSRN VARCHAR(25)
    , CalledNum2 VARCHAR(25)
    , FCIFlag VARCHAR(25)
    , callrecTime VARCHAR(50)
    , EventProcessedUtcTime VARCHAR(50)
    , PartitionId int
    , EventEnqueuedUtcTime VARCHAR(50)
    )
WITH (DISTRIBUTION = ROUND_ROBIN)
```

### <a name="step-5"></a>Steg 5

Klicka på jobb namnet på Azure Portal för Stream Analytics jobb.  Klicka på knappen **_test_* _ i fönstret _*_utdata_*_ .

![Test knapp på Outpout information ](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) när anslutningen till databasen lyckas visas ett meddelande i portalen.

### <a name="step-6"></a>Steg 6

Klicka på menyn _*_fråga_*_ under _*_jobb sto pol Ogin_*_ och ändra frågan om du vill infoga data i Stream-utdata som du skapade.  Testa frågan genom att klicka på knappen _*_testa vald fråga_*_ .  Klicka på knappen _*_Spara fråga_*_ när du har lyckats testa frågan.

![Spara fråga](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>Steg 7

Starta Azure Stream Analytics jobbet.  Klicka på _*_Start_*_ -knappen på _*_översikts_*_ menyn.

![Starta Stream Analytics-jobb](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

Klicka på knappen _ *_Start_** i fönstret Starta jobb.

![Klicka på Start](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Nästa steg

En översikt över integrering finns i [integrera andra tjänster](sql-data-warehouse-overview-integrate.md).
Mer utvecklings tips finns i [design beslut och kodnings tekniker för dedikerad SQL-pool](sql-data-warehouse-overview-develop.md).
