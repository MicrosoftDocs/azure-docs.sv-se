---
title: Data modellering i Gen2-miljöer – Azure Time Series Insights | Microsoft Docs
description: Lär dig mer om data modellering i Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 89efc1d4f34b250d211f9fd7492588bd2896eb6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95016861"
---
# <a name="data-modeling-in-azure-time-series-insights-gen2"></a>Data modellering i Azure Time Series Insights Gen2

Den här artikeln beskriver hur du arbetar med tids serie modellen i Azure Time Series Insights Gen2. Det beskriver flera vanliga data scenarier.

> [!TIP]
>
> * Läs mer om [tids serie modellen](concepts-model-overview.md).
> * Lär dig mer om att navigera i [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md).

## <a name="instances"></a>Instanser

Azure Time Series Insights Explorer stöder instans åtgärderna **skapa**, **läsa**, **Uppdatera** och **ta bort** i webbläsaren.

Börja genom att välja vyn **modell** i vyn **analysera** i Azure Time Series Insights Explorer.

### <a name="create-a-single-instance"></a>Skapa en enskild instans

1. Gå till panelen tids serie modell väljare och välj **instanser** på menyn. Alla instanser som är kopplade till den valda Azure Time Series Insightss miljön visas.

    [![Skapa en enda instans genom att först välja instanser.](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png#lightbox)

1. Välj **+ Lägg till**.

    [![Lägg till en instans genom att klicka på knappen + Lägg till.](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png#lightbox)

1. Ange instans information, Välj associationen typ och hierarki och välj **skapa**.

### <a name="bulk-upload-one-or-more-instances"></a>Mass uppladdning av en eller flera instanser

> [!TIP]
> Du kan spara dina instanser på Skriv bordet i JSON. Den nedladdade JSON-filen kan sedan överföras genom följande steg.

1. Välj **Ladda upp JSON**.
1. Välj den fil som innehåller nytto lasten för instanser.

    [![Mass överförings instanser via JSON.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png#lightbox)

1. Välj **Överför**.

### <a name="edit-a-single-instance"></a>Redigera en enskild instans

1. Markera instansen och välj **redigerings** -eller **Penn ikonen**.
1. Gör de ändringar som krävs och välj **Spara**.

    [![Redigera en enskild instans.](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png#lightbox)

### <a name="delete-an-instance"></a>Ta bort en instans

1. Välj instansen och välj ikonen **ta bort** eller **pappers korgen**.

   [![Ta bort en instans genom att välja ta bort.](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png#lightbox)

1. Bekräfta borttagningen genom att välja **ta bort**.

> [!NOTE]
> En instans måste ha godkänt en fält verifierings kontroll för att kunna tas bort.

## <a name="hierarchies"></a>Hierarkier

Azure Time Series Insights Explorer stöder hierarkier **skapa**, **läsa**, **Uppdatera** och **ta bort** åtgärder i webbläsaren.

Börja genom att välja vyn **modell** i vyn **analysera** i Azure Time Series Insights Explorer.

### <a name="create-a-single-hierarchy"></a>Skapa en enskild hierarki

1. Gå till panelen Välj tids serie modell väljare och välj **hierarkier** på menyn. Alla hierarkier som är associerade med den valda Azure Time Series Insightss miljön visas.

    [![Skapa en hierarki i fönstret.](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png#lightbox)

1. Välj **+ Lägg till**.

    [![Hierarki + Lägg till-knapp.](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png#lightbox)

1. Välj **+ Lägg till nivå** i den högra rutan.

    [![Lägg till en nivå i hierarkin.](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png)](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png#lightbox)

1. Ange information om hierarkin och välj **Spara**.

    [![Ange information om hierarkin.](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png)](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Mass uppladdning av en eller flera hierarkier

> [!TIP]
> Du kan spara dina hierarkier på Skriv bordet i JSON. Den nedladdade JSON-filen kan sedan överföras genom följande steg.

1. Välj **Ladda upp JSON**.
1. Välj den fil som innehåller hierarkins nytto Last.
1. Välj **Överför**.

    [![Val för Mass uppladdning av hierarkier.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Redigera en enskild hierarki

1. Välj hierarkin och välj **redigerings** -eller **Penn ikonen**.
1. Gör de ändringar som krävs och välj **Spara**.

    [![Alternativ för att redigera en enskild hierarki.](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png#lightbox)

### <a name="delete-a-hierarchy"></a>Ta bort en hierarki

1. Välj hierarkin och välj ikonen **ta bort** eller **pappers korgen**.

    [![Ta bort en hierarki genom att klicka på knappen Ta bort.](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png#lightbox)

1. Bekräfta borttagningen genom att välja **ta bort**.

## <a name="types"></a>Typer

Azure Time Series Insights Explorer stöder åtgärder för att **skapa**, **läsa**, **Uppdatera** och **ta bort** i webbläsaren.

Börja genom att välja vyn **modell** i vyn **analysera** i Azure Time Series Insights Explorer.

### <a name="create-a-single-type"></a>Skapa en enskild typ

1. Gå till panelen tids serie modell väljare och välj **typer** på menyn. Alla typer som är associerade med den valda Azure Time Series Insightss miljön visas.

    [![Fönstret tids serie modell typer.](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png#lightbox)

1. Välj **+ Lägg** till för att visa **Lägg till en ny typ** av popup-fönster.
1. Ange egenskaper och variabler för din typ. När du har angett väljer du **Spara**.

    [![Konfigurations inställningar för att lägga till en typ.](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Mass uppladdning av en eller flera typer

> [!TIP]
> Du kan spara dina typer på Skriv bordet i JSON. Den nedladdade JSON-filen kan sedan överföras genom följande steg.

1. Välj **Ladda upp JSON**.
1. Välj den fil som innehåller typen nytto Last.
1. Välj **Överför**.

    [![Överförings alternativ för Mass typer.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png#lightbox)

### <a name="edit-a-single-type"></a>Redigera en enskild typ

1. Välj typ och välj sedan ikonen **Redigera** eller **penna**.
1. Gör de ändringar som krävs och välj **Spara**.

    [![Redigera en typ i fönstret.](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png#lightbox)

### <a name="delete-a-type"></a>Ta bort en typ

1. Välj typ och välj ikonen **ta bort** eller **pappers korgen**.

   [![Ta bort en typ genom att välja ta bort.](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png#lightbox)

1. Bekräfta borttagningen genom att välja **ta bort**.

## <a name="next-steps"></a>Nästa steg

* Mer information om tids serie modellen finns i [data modellering](./concepts-model-overview.md).

* Om du vill veta mer om Gen2 kan du läsa [visualisera data i Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md).

* Läs de [JSON-former som stöds](./time-series-insights-send-events.md#supported-json-shapes)för att lära dig mer om JSON-former som stöds.