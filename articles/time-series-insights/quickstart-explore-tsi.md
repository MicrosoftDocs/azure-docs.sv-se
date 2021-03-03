---
title: 'Snabb start: utforska Gen2 demo Environment – Azure Time Series Insights Gen2 | Microsoft Docs'
description: Utforska viktiga funktioner i Azure Time Series Insights Gen2 demo Environment.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 03/01/2021
ms.openlocfilehash: 25340af243217313ce6d3f7205c102d4bcd350b4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698118"
---
# <a name="quickstart-explore-the-azure-time-series-insights-gen2-demo-environment"></a>Snabbstart: Utforska demomiljön för Azure Time Series Insights Gen2

Den här snabb starten hjälper dig att komma igång med en Azure Time Series Insights Gen2-miljö. I den kostnads fria demonstrationen guidar vi dig att guida viktiga funktioner som har lagts till Azure Time Series Insights Gen2.

Azure Time Series Insights Gen2 demo Environment innehåller ett scenario företag, contoso, som fungerar med två lindnings turbin-servergrupper. Varje server grupp har 10 turbiner. Varje turbin har 20 sensorer som rapporterar data varje minut till Azure IoT Hub. Sensorer samlar in information om väder förhållanden, bladets färgdjup och Yaw position. Information om Generator prestanda, växel lådans beteende och säkerhets övervakare registreras också.

I den här snabb starten får du lära dig hur du använder Azure Time Series Insights Gen2 för att hitta åtgärds bara insikter i Contoso-data. Du utför även en kort rotor Saks analys för att bättre förutse kritiska fel och utföra underhåll.

> [!IMPORTANT]
> Skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)   om du inte har något.

## <a name="explore-the-azure-time-series-insights-gen2-explorer-in-a-demo-environment"></a>Utforska Azure Time Series Insights Gen2 Explorer i en demo miljö

Azure Time Series Insights Gen2 Explorer visar historiska data och rotor Saks analys. Så här kommer du igång:

1. Gå till [Contosos grupp demonstrations](https://insights.timeseries.azure.com/preview/samples) miljö.  

1. Om du uppmanas till det loggar du in på Azure Time Series Insights Gen2 Explorer genom att använda dina autentiseringsuppgifter för Azure-kontot.

## <a name="work-with-historical-data"></a>Arbeta med historiska data

1. Välj **contoso WindFarm-hierarki**.

   [![Contoso WindFarm-hierarki](media/quickstart-explore/quick-start-contoso-1.png)](media/quickstart-explore/quick-start-contoso-1.png#lightbox)

1. I **contoso-anläggningar 1** tittar du på lindning turbin **W7**.

   [![W7 i Contoso-anläggning 1](media/quickstart-explore/quick-start-contoso-2.png)](media/quickstart-explore/quick-start-contoso-2.png#lightbox)

   1. Ändra visnings intervallet till **1/1/17 20:00:00.00 till 3/10/17 20:00:00.00 (UTC)**.

      [![Visnings område](media/quickstart-explore/range-setting-1.png)](media/quickstart-explore/range-setting-1.png#lightbox)

      [![Visnings inställning för intervall](media/quickstart-explore/range-setting-2.png)](media/quickstart-explore/range-setting-2.png#lightbox)

   1. Justera visnings området med hjälp av zooma in **(+)** och ut **(-)** och flytta bild fältet.

      [![Justera visnings området](media/quickstart-explore/view-range-setting.png)](media/quickstart-explore/view-range-setting.png#lightbox)

   1. Välj en sensor genom att välja **contoso planta 1**  >  **W7**  >  **Generator system**  >  **GeneratorSpeed**. Granska sedan värdena som visas.

      [![Generator hastighet](media/quickstart-explore/quick-start-generator-speed-1.png)](media/quickstart-explore/quick-start-generator-speed-1.png#lightbox)

1. Contoso upptäckte nyligen en brand i vindturbin **W7**. Åsikterna varierar om vad som orsakade branden. I Azure Time Series Insights Gen2 visas den brand varnande sensor som aktiverades under branden.

   1. Ändra visnings intervallet till **3/9/17 20:00:00.00 till 3/10/17 20:00:00.00 (UTC)**.
   1. Välj **säkerhets system**  >  **FireAlert**.

      [![Contoso hittade en brand i lindning turbin W7](media/v2-update-quickstart/quick-start-fire-alert.png)](media/v2-update-quickstart/quick-start-fire-alert.png#lightbox)

1. Granska andra händelser kring tiden för brand för att förstå vad som hände. Olje tryck och aktiva varningar som tillsätts precis före branden.

   1. Välj **system**  >  **HydraulicOilPressure**.
   1. Välj **system**  >  **ActiveWarning**.

      [![Granska andra händelser ungefär samma gång](media/v2-update-quickstart/quick-start-active-warning.png)](media/v2-update-quickstart/quick-start-active-warning.png#lightbox)

1. Olje trycket och aktiva varnings sensorer förtäta sig direkt före branden. Expandera den visade tids serien för att granska andra tecken som skulle leda till en tydligare introduktion. Båda sensorer varierar konsekvent över tid. Fluktuationerna indikerar ett beständigt och Worrisome mönster.

    * Ändra visnings intervallet till **2/24/17 20:00:00.00 till 3/10/17 20:00:00.00 (UTC)**.

      [![Olje tryck och aktiva varnings sensorer är också förtätade](media/v2-update-quickstart/quick-start-view-range.png)](media/v2-update-quickstart/quick-start-view-range.png#lightbox)

1. Om du undersöker två års historik data upptäcks en annan Fire-händelse som hade samma sensor variationer.

    * Ändra visnings intervallet till **1/1/16 till 12/31/17** (alla data).

      [![Sök efter historiska mönster](media/v2-update-quickstart/quick-start-expand-view-range.png)](media/v2-update-quickstart/quick-start-expand-view-range.png#lightbox)

Genom att använda Azure Time Series Insights Gen2 och sensor telemetri har vi upptäckt en långsiktig trend som är dold i historiska data. Med dessa nya insikter kan vi:

* Förklara vad som faktiskt hände.
* Rätta till problemet.
* Placera bättre system för varnings meddelanden på plats.

## <a name="root-cause-analysis"></a>Rotorsaksanalys

1. Vissa scenarier kräver avancerad analys för att få fram LED trådar i data. Välj Windmill- **W6** på datumet **6/25**.

    1. Ändra visnings intervallet till **6/1/17 20:00:00.00 till 7/1/17 20:00:00.00 (UTC)**.
    1. Välj **contoso plantera 1**  >  **W6**  >  **säkerhets system**  >  **VoltageActuatorSwitchWarning**.

       [![Ändra visnings intervallet och välj W6](media/v2-update-quickstart/quick-start-voltage-switch-warning.png)](media/v2-update-quickstart/quick-start-voltage-switch-warning.png#lightbox)

1. Varningen indikerar ett problem med spänningen från generatorn. Den totala effekten av generatorn är inom normala parametrar i det aktuella intervallet. Ett annat mönster uppstår genom att öka intervallet. En kombination är tydlig.

    1. Ta bort **VoltageActuatorSwitchWarning** -sensorn.
    1. Välj **Generator system**  >  **ActivePower**.
    1. Ändra intervallet till **3D**.

       [![Ändra intervallet till 3D](media/v2-update-quickstart/quick-start-interval-change.png)](media/v2-update-quickstart/quick-start-interval-change.png#lightbox)

1. Genom att expandera tidsintervallet kan vi avgöra om problemet har stoppats eller om det fortsätter.

    * Förläng tids perioden till 60 dagar.

      [![Förläng tids perioden till 60 dagar](media/v2-update-quickstart/quick-start-expand-interval-range.png)](media/v2-update-quickstart/quick-start-expand-interval-range.png#lightbox)

1. Andra sensor data punkter kan läggas till för att ge större kontext. De fler sensorer som vi visar, desto en fullständig förståelse för problemet är. Nu ska vi släppa en markör för att visa de faktiska värdena.

    1. Välj **Generator system** och välj sedan tre sensorer: **GridVoltagePhase1**, **GridVoltagePhase2** och **GridVoltagePhase3**.
    1. Släpp en markör på den sista datapunkten i det synliga området.

       [![Släpp en markör](media/v2-update-quickstart/quick-start-drop-marker.png)](media/v2-update-quickstart/quick-start-drop-marker.png#lightbox)

    Två av spännings sensorerna fungerar på ett jämförbart och inom normala parametrar. Det verkar som om **GridVoltagePhase3** -sensorn är orsaken.

1. Med mycket kontextuella data som lagts till, är fas 3-programmenyen ännu mer som problemet. Nu har vi en bättre introduktion till orsaken till varningen. Vi är redo att referera till vårt underhålls team.  

    * Ändra visningen så att alla **Generator system** sensorer på samma diagram skala läggs till.

      [![Ändra visningen så att den innehåller allt](media/v2-update-quickstart/quick-start-generator-system.png)](media/v2-update-quickstart/quick-start-generator-system.png#lightbox)

## <a name="next-steps"></a>Nästa steg

Du är redo att skapa din egen Azure Time Series Insights Gen2-miljö. Starta:

> [!div class="nextstepaction"]
> [Planera din Azure Time Series Insights Gen2-miljö](./how-to-plan-your-environment.md)

Lär dig att använda demonstrationen och dess funktioner:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md)
