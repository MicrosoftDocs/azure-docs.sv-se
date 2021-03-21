---
title: Planera din Gen2-miljö – Azure Time Series Insights | Microsoft Docs
description: Metod tips för att konfigurera, hantera, planera och distribuera din Azure Time Series Insights Gen2-miljö.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: c8d96d7b5952c020493af278ee1ea8ad5ff46716
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95016793"
---
# <a name="plan-your-azure-time-series-insights-gen2-environment"></a>Planera din Azure Time Series Insights Gen2-miljö

Den här artikeln beskriver metod tips för att planera och komma igång snabbt med hjälp av Azure Time Series Insights Gen2.

## <a name="best-practices-for-planning-and-preparation"></a>Metod tips för planering och förberedelser

Metod tips som omger planeringen av och förbereda din miljö beskrivs ytterligare i följande artiklar:

* Vad du får när du [etablerar en Azure Time Series Insights Gen2-miljö](#the-gen2-environment).
* Vad dina [Time Series-ID och tidsstämpel-egenskaper är](#configure-time-series-ids-and-timestamp-properties).
* Vad den nya [tids serie modellen är](#understand-the-time-series-model)och hur du skapar en egen.
* Hur du [skickar händelser effektivt i JSON](#shape-your-events).
* Azure Time Series Insights [affärs haveri återställnings alternativ](#business-disaster-recovery).

Azure Time Series Insights använder en affärs modell enligt principen betala per användning. Läs [Azure Time Series Insights priser](https://azure.microsoft.com/pricing/details/time-series-insights/)om du vill ha mer information om avgifter och kapacitet.

## <a name="the-gen2-environment"></a>Gen2-miljön

När du etablerar en Azure Time Series Insights Gen2-miljö skapar du två Azure-resurser:

* En Azure Time Series Insights Gen2-miljö
* Ett Azure Storage konto

Som en del av etablerings processen anger du om du vill aktivera ett varmt arkiv. Med den varmt butiken får du en nivå av frågans upplevelse. När aktive rad måste du ange en kvarhållningsperiod mellan 7 och 30 dagar. Frågor som utförs inom lagrings perioden för varm lagring ger normalt kortare svars tider. När en fråga sträcker sig över lagrings perioden för varma lager kommer den att hanteras från kall lagring.

Frågor i varmt lager är kostnads fria, medan frågor om kall lagring ådrar sig kostnader. Det är viktigt att förstå dina fråge mönster och planera din varmt Store-konfiguration. Vi rekommenderar att interaktiva analyser på den senaste informationen finns i din varma lagrings-och mönster analys och långsiktiga trender finns i kall.

> [!NOTE]
> Läs [API-referensen](/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters)om du vill veta mer om hur du frågar dina varma data.

För att starta måste du ha tre ytterligare objekt:

* En [tids serie modell](./concepts-model-overview.md)
* En [händelse källa som är ansluten till Time Series Insights](./concepts-streaming-ingestion-event-sources.md)
* [Händelser som flödar till händelse källan](./time-series-insights-send-events.md) som båda mappas till modellen och är i giltigt JSON-format

## <a name="review-azure-time-series-insights-gen2-limits"></a>Granska Azure Time Series Insights Gen2-gränser

[!INCLUDE [Review Azure Time Series Insights Gen2 limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Konfigurera Time Series-ID: n och egenskaper för tidsstämpel

Om du vill skapa en ny Azure Time Series Insights miljö väljer du ett Time Series-ID. Detta fungerar som logisk partition för dina data. Se till att dina Time Series ID är klara.

> [!IMPORTANT]
> Time Series-ID: n *kan inte ändras senare*. Verifiera var och en innan du väljer den sista och Använd den första.

Du kan välja upp till tre nycklar för att unikt särskilja dina resurser. Mer information finns [i metod tips för att välja ett tids serie-ID](./how-to-select-tsid.md) och inmatnings [regler](concepts-json-flattening-escaping-rules.md).

Egenskapen **timestamp** är också viktig. Du kan ange den här egenskapen när du lägger till händelse källor. Varje händelse källa har en valfri tidstämpel egenskap som används för att spåra händelse källor över tid. Tidsstämplar-värden är Skift läges känsliga och måste formateras till den enskilda specifikationen för varje händelse källa.

När den lämnas tom används den tidpunkt då händelsen köade i IoT Hub eller Händelsehubben används som händelsens tidsstämpel. I allmänhet bör användare välja egenskapen timestamp och använda den tid då sensorn eller taggen genererar läsningen, i stället för hubben i kö. För mer information och Läs om tids zons förskjutningar Läs tids [stämpling för händelse källan](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp).

## <a name="understand-the-time-series-model"></a>Förstå tids serie modellen

Nu kan du konfigurera din Azure Time Series Insights Miljös tids serie modell. Den nya modellen gör det enkelt att hitta och analysera IoT-data. Det möjliggör hantering, underhåll och anrikning av tids serie data och hjälper till att förbereda konsument klara data uppsättningar. Modellen använder Time Series-ID: n, som mappar till en instans som associerar den unika resursen med variabler, som kallas typer och hierarkier. Läs mer om [tids serie modell](./concepts-model-overview.md) översikten.

Modellen är dynamisk, så den kan skapas när som helst. Kom igång snabbt genom att skapa och ladda upp den innan du överför data till Azure Time Series Insights. Om du vill bygga din modell läser du [använda tids serie modellen](./concepts-model-overview.md).

För många kunder mappas Time Series-modellen till en befintlig till gångs modell eller ERP-system redan på plats. Om du inte har en befintlig modell [tillhandahålls](https://github.com/Microsoft/tsiclient) en fördefinierad användar upplevelse för att snabbt komma igång. Om du vill Envision hur en modell kan hjälpa dig kan du Visa [exempel demonstrations miljön](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Forma dina händelser

Du kan kontrol lera hur du skickar händelser till Azure Time Series Insights. Vi rekommenderar att händelserna är denormaliserade och effektivt.

En lämplig tumregel:

* Lagra metadata i din tids serie modell.
* Se till att tids serie läge, instans fält och händelser bara innehåller nödvändig information, till exempel ett Time Series-ID eller en tidstämpel egenskap.

Mer information och för att förstå hur händelser kommer att förenklas och lagras finns i reglerna för att [förenkla och stoppa JSON](./concepts-json-flattening-escaping-rules.md).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Nästa steg

* Granska [Azure Advisor](../advisor/advisor-overview.md) för att planera dina konfigurations alternativ för företags återställning.
* Granska [Azure Advisor](../advisor/advisor-overview.md) för att planera dina konfigurations alternativ för företags återställning.
* Läs mer om [data inmatning](./concepts-ingestion-overview.md) i Azure Time Series Insights Gen2.
* Läs artikeln om [data lagring](./concepts-storage.md) i Azure Time Series Insights Gen2.
* Lär dig mer om [data modellering](./concepts-model-overview.md) i Azure Time Series Insights Gen2.