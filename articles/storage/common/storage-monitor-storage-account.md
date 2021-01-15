---
title: Så här övervakar du ett Azure Storage konto i Azure Portal | Microsoft Docs
description: Lär dig hur du övervakar ett lagrings konto i Azure med hjälp av Azure Portal och Azure-lagringsanalys.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: e5495b466bf9b16319b788ec32c7b3a03100f505
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218150"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Övervaka ett lagringskonto i Azure-portalen

[Azure-lagringsanalys](storage-analytics.md) tillhandahåller mått för alla lagrings tjänster och loggar för blobbar, köer och tabeller. Du kan använda [Azure Portal](https://portal.azure.com) för att konfigurera vilka mått och loggar som registreras för ditt konto och konfigurera diagram som ger visuella representationer av dina mått data. 

Vi rekommenderar att du läser [Azure Monitor för lagring](../../azure-monitor/insights/storage-insights-overview.md) (för hands version). Det är en funktion i Azure Monitor som erbjuder omfattande övervakning av dina Azure Storage-konton genom att leverera en enhetlig vy över dina Azure Storage tjänsters prestanda, kapacitet och tillgänglighet. Du behöver inte aktivera eller konfigurera något, och du kan direkt Visa dessa mått från de fördefinierade interaktiva diagrammen och andra visualiseringar som ingår.

> [!NOTE]
> Det finns kostnader för att undersöka övervaknings data i Azure Portal. Mer information finns i [Lagringsanalys](storage-analytics.md).
>
> Azure Files stöder för närvarande Lagringsanalyss mått, men har ännu inte stöd för loggning.
>
> Förstklassiga prestanda Block Blob Storage-konton stöder inte lagring av analys mått, men de stöder loggning. Du kan aktivera loggning via programmering via REST API eller klient biblioteket. Överväg att använda [Azure Storage mått i Azure Monitor](../blobs/monitor-blob-storage.md)om du vill visa mått med förstklassiga prestanda för Blob Storage-konton.
>
> En djupgående guide om hur du använder Lagringsanalys och andra verktyg för att identifiera, diagnostisera och felsöka Azure Storage-relaterade problem finns i [övervaka, diagnostisera och felsöka Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).
>

<a id="modify-retention-policy"></a>

## <a name="configure-monitoring-for-a-storage-account"></a>Konfigurera övervakning för ett lagrings konto

1. I [Azure Portal](https://portal.azure.com)väljer du **lagrings konton** och sedan namnet på lagrings kontot för att öppna instrument panelen för kontot.
1. Välj **diagnostik** i avsnittet **övervakning** på Meny bladet.

    ![Skärm bild som markerar alternativet diagnostikinställningar (klassisk) under avsnittet övervakning (klassisk).](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Välj **typ** av mått data för varje **tjänst** som du vill övervaka och **bevarande principen** för data. Du kan också inaktivera övervakning genom att ange **status** till **av**.

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-01.png)

   Om du vill ange data bevarande principen flyttar du skjutreglaget för **kvarhållning (dagar)** eller anger hur många dagars data som ska behållas, från 1 till 365. Standardvärdet för nya lagrings konton är sju dagar. Om du inte vill ange en bevarande princip anger du noll. Om det inte finns någon bevarande princip är det upp till dig att ta bort övervaknings data.

   > [!WARNING]
   > Du debiteras när du tar bort mått data manuellt. Inaktuella analys data (data som är äldre än din bevarande princip) tas bort av systemet utan kostnad. Vi rekommenderar att du anger en bevarande princip baserat på hur länge du vill behålla lagrings analys data för ditt konto. Mer information finns i [fakturering av lagrings mått](storage-analytics-metrics.md#billing-on-storage-metrics) .
   >

1. När du är klar med övervaknings konfigurationen väljer du **Spara**.

En standard uppsättning mått visas i diagram på bladet lagrings konto, samt enskilda tjänst blad (BLOB, kö, tabell och fil). När du har aktiverat mått för en tjänst kan det ta upp till en timme innan data visas i diagrammen. Du kan välja **Redigera** i ett mått diagram om du vill konfigurera vilka mått som visas i diagrammet.

Du kan inaktivera insamling av mått och loggning genom att ange **status** till **av**.

> [!NOTE]
> Azure Storage använder [Table Storage](storage-introduction.md#table-storage) för att lagra måtten för ditt lagrings konto och lagrar måtten i tabeller i ditt konto. Mer information finns här: [Hur mått lagras](storage-analytics-metrics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>Anpassa mått diagram

Använd följande procedur för att välja vilka lagrings mått som ska visas i ett mått diagram.

1. Börja med att visa ett mått för lagrings mått i Azure Portal. Du kan hitta diagram på **bladet lagrings konto** och på **mått** bladet för en enskild tjänst (BLOB, kö, tabell, fil).

   I det här exemplet använder följande diagram som visas på **bladet lagrings konto**:

   ![Diagram val i Azure Portal](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Klicka någonstans i diagrammet för att redigera diagrammet.

1. Välj sedan **tidsintervallet** för de mått som ska visas i diagrammet och **tjänsten** (BLOB, kö, tabell, fil) vars mått du vill visa. Här väljer du de senaste vecko måtten som ska visas för Blob-tjänsten:

   ![Tidsintervall och tjänst val på bladet redigera diagram](./media/storage-monitor-storage-account/storage-edit-metric-time-range.png)

1. Välj de enskilda **mått** som du vill ska visas i diagrammet och klicka sedan på **OK**.

   ![Enskilda mått val i bladet redigera diagram](./media/storage-monitor-storage-account/storage-edit-metric-selections.png)

Dina diagram inställningar påverkar inte insamling, sammanställning eller lagring av övervaknings data i lagrings kontot.

### <a name="metrics-availability-in-charts"></a>Mått tillgänglighet i diagram

Listan över tillgängliga mått ändras baserat på vilken tjänst du har valt i list rutan och enhets typen för det diagram som du redigerar. Du kan till exempel välja procent värden som *PercentNetworkError* och *PercentThrottlingError* endast om du redigerar ett diagram som visar enheter i procent:

![Diagram över begär ande fel i procent i Azure Portal](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Mått matchning

Måtten som du har valt i **diagnostik** bestämmer upplösningen för de mått som är tillgängliga för ditt konto:

* **Aggregerad** övervakning innehåller mått som ingångs-/utgångs-, tillgänglighets-, fördröjnings-och procent andels procent. Dessa mått sammanställs från BLOB-, tabell-, fil-och Queue-tjänsterna.
* **Per API** ger bättre upplösning, med mått som är tillgängliga för enskilda lagrings åtgärder, förutom agg regeringar på tjänst nivå.

## <a name="configure-metrics-alerts"></a>Konfigurera mått aviseringar

Du kan skapa aviseringar som meddelar dig när tröskelvärden har nåtts för lagrings resurs mått.

1. Öppna **bladet aviserings regler** genom att rulla ned till avsnittet **övervakning** i **meny bladet** och välja **aviseringar (klassisk)**.
2. Välj **Lägg till mått varning (klassisk)** för att öppna bladet **Lägg till en varnings regel**
3. Ange ett **namn** och en **Beskrivning** för den nya varnings regeln.
4. Välj det **mått** som du vill lägga till en avisering för, ett varnings **villkor** och ett **tröskelvärde**. Tröskeln för enhets typ ändras beroende på mått som du har valt. Exempel: "count" är enhets typen för *ContainerCount*, medan enheten för *PercentNetworkError* -måttet är en procent andel.
5. Välj en **period**. Mått som når eller överskrider tröskelvärdet inom perioden utlöser en avisering.
6. Valfritt Konfigurera **e-post** och **webhook** -meddelanden. Mer information om Webhooks finns i [Konfigurera en webhook på en Azure Metric-avisering](../../azure-monitor/platform/alerts-webhooks.md). Om du inte konfigurerar e-post eller webhook-meddelanden visas endast aviseringar i Azure Portal.

![Bladet Lägg till en varnings regel i Azure Portal](./media/storage-monitor-storage-account/add-alert-rule.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Lägg till mått diagram på portalens instrument panel

Du kan lägga till Azure Storage Mät diagram för alla dina lagrings konton till portalens instrument panel.

1. Välj Klicka på **Redigera instrument panel** när du visar instrument panelen i [Azure Portal](https://portal.azure.com).
1. I **panel galleriet** väljer du **hitta paneler efter**  >  **typ**.
1. Välj **Skriv**  >  **lagrings konton**.
1. I **resurser** väljer du det lagrings konto vars mått du vill lägga till på instrument panelen.
1. Välj **Kategorier**  >  **övervakning**.
1. Dra och släpp diagram panelen på instrument panelen för måttet som du vill visa. Upprepa för alla mått som du vill ska visas på instrument panelen. I följande bild markeras diagrammet "blobbar-totalt antal förfrågningar" som ett exempel, men alla diagram är tillgängliga för placering på instrument panelen.

   ![Panel galleri i Azure Portal](./media/storage-monitor-storage-account/storage-customize-dashboard.png)
1. Välj **anpassning som gjorts** nära överkanten på instrument panelen när du är klar med att lägga till diagram.

När du har lagt till diagram på instrument panelen kan du ytterligare anpassa dem enligt beskrivningen i anpassa mått diagram.

## <a name="configure-logging"></a>Konfigurera loggning

Du kan instruera Azure Storage att spara diagnostikloggar för läsnings-, skriv-och borttagnings begär Anden för BLOB-, tabell-och Queue-tjänsterna. Den data bevarande princip som du anger gäller även för dessa loggar.

> [!NOTE]
> Azure Files stöder för närvarande Lagringsanalyss mått, men har ännu inte stöd för loggning.
>

1. I [Azure Portal](https://portal.azure.com)väljer du **lagrings konton** och sedan namnet på lagrings kontot för att öppna bladet lagrings konto.
1. Välj **diagnostikinställningar (klassisk)** i avsnittet **övervakning (klassisk)** på Meny bladet.

    ![Meny alternativet diagnostik under övervakning i Azure Portal.](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Se till att **status** är inställt **på på** och välj de **tjänster** som du vill aktivera loggning för.

    ![Konfigurera loggning i Azure Portal.](./media/storage-monitor-storage-account/enable-diagnostics.png)
1. Klicka på **Spara**.

Diagnostikloggar sparas i en BLOB-behållare med namnet *$logs* i ditt lagrings konto. Du kan visa loggdata med hjälp av en lagrings Utforskare som [Microsoft Azure Storage Explorer](https://storageexplorer.com)eller program mässigt med hjälp av lagrings klient biblioteket eller PowerShell.

Information om hur du kommer åt $logs-behållaren finns i [Storage Analytics-loggning](storage-analytics-logging.md).

## <a name="next-steps"></a>Nästa steg

* Hitta mer information om [mått, loggning och fakturering](storage-analytics.md) för Lagringsanalys.