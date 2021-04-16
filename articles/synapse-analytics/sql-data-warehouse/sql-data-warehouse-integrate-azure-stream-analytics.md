---
title: Använda Azure Stream Analytics i dedikerad SQL-pool
description: Tips för att Azure Stream Analytics med dedikerad SQL-pool i Azure Synapse för att utveckla realtidslösningar.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 9/25/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 0c7f139b50cd43e3e8862fda3f5401a853ced8d0
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566587"
---
# <a name="use-azure-stream-analytics-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>Använda Azure Stream Analytics med dedikerad SQL-pool i Azure Synapse Analytics

Azure Stream Analytics är en fullständigt hanterad tjänst som tillhandahåller skalbar, skalbar, komplex händelsebearbetning med korta svarstider över strömmande data i molnet. Du kan lära dig grunderna genom att läsa [Introduktion till Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Du kan sedan lära dig hur du skapar en lösning från början till slut med Stream Analytics genom att följa självstudien Kom igång [med Azure Stream Analytics.](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

I den här artikeln får du lära dig hur du använder din dedikerade SQL-pool som utdata mottagare för datainmatning med högt dataflöde med Azure Stream Analytics jobb.

## <a name="prerequisites"></a>Förutsättningar

* Azure Stream Analytics Jobb – Skapa ett Azure Stream Analytics jobb genom att följa stegen i självstudien Kom igång [med Azure Stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) för att:  

    1. Skapa en händelsehubbindata
    2. Konfigurera och starta händelsegeneratorprogram
    3. Etablera ett Stream Analytics jobb
    4. Ange jobbindata och fråga
* Dedikerad SQL-pool – Om du vill skapa en ny dedikerad SQL-pool följer du stegen i [Snabbstart: Skapa en dedikerad SQL-pool.](../quickstart-create-sql-pool-portal.md)

## <a name="specify-streaming-output-to-point-to-your-dedicated-sql-pool"></a>Ange strömmande utdata som pekar på din dedikerade SQL-pool

### <a name="step-1"></a>Steg 1

Från menyn Azure Portal du till ditt Stream Analytics och klickar på **Utdata** under menyn **Jobbtopologi.**

### <a name="step-2"></a>Steg 2

Klicka på knappen **Lägg** till och **välj Azure Synapse Analytics** från den nedrullningsna menyn.

![Välj Azure Synapse Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output.png)

### <a name="step-3"></a>Steg 3

Ange följande värden:

* *Utdataalias:* Ange ett eget namn för jobbets utdata.
* *Prenumeration:*
  * Om din dedikerade SQL-pool finns i samma prenumeration som Stream Analytics klickar du på ***Välj Azure Synapse Analytics från dina prenumerationer.***
  * Om din dedikerade SQL-pool finns i en annan prenumeration klickar du på Ange Azure Synapse Analytics inställningar manuellt.
* *Databas:* Välj måldatabasen i listrutan.
* *Användarnamn:* Ange användarnamnet för ett konto som har skrivbehörighet för databasen.
* *Lösenord:* Ange lösenordet för det angivna användarkontot.
* *Tabell:* Ange namnet på måltabellen i databasen.
* klicka på **knappen** Spara

![Ifyllda Azure Synapse Analytics formulär](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output-db-settings.png)

### <a name="step-4"></a>Steg 4

Innan du kan köra ett test måste du skapa tabellen i din dedikerade SQL-pool.  Kör följande skript för tabellskapande med SQL Server Management Studio (SSMS) eller val av frågeverktyg.

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

På sidan Azure Portal för Stream Analytics jobb klickar du på jobbnamnet.  Klicka på knappen ***Test** _ i fönstret _ *_Utdatainformation_** .

![Testknapp i Outpout-information När anslutningen till databasen lyckas ](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) visas ett meddelande i portalen.

### <a name="step-6"></a>Steg 6

Klicka på menyn ***Fråga** _ under _*_Jobbtopologi och ändra_*_ frågan för att infoga data i Stream-utdata som du skapade.  Klicka på knappen _*_Testa vald fråga_*_ för att testa frågan.  Klicka på *_knappen _ Spara_* fråga * när frågetestet lyckas.

![Spara fråga](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>Steg 7

Starta Azure Stream Analytics jobbet.  Klicka på knappen ***Start** _ på menyn _ *_Översikt_** .

![Starta Stream Analytics-jobb](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

Klicka på ***knappen*** Start i fönstret Starta jobb.

![Klicka på Starta](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Nästa steg

En översikt över integrering finns i [Integrera andra tjänster.](sql-data-warehouse-overview-integrate.md)
Fler utvecklingstips finns i [Designbeslut och kodningstekniker för dedikerad SQL-pool.](sql-data-warehouse-overview-develop.md)
