---
title: Skicka händelser till en miljö – Azure Time Series Insights | Microsoft Docs
description: Lär dig hur du konfigurerar en Event Hub, kör ett exempel program och skickar händelser till din Azure Time Series Insights-miljö.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: 8ee2cc5473d83eafc2443f3aea7cab6a2e77727c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463569"
---
# <a name="send-events-to-an-azure-time-series-insights-gen1-environment-by-using-an-event-hub"></a>Skicka händelser till en Azure Time Series Insights gen1-miljö med hjälp av en Event Hub

> [!CAUTION]
> Det här är en gen1-artikel.

Den här artikeln beskriver hur du skapar och konfigurerar en Event Hub i Azure Event Hubs. Det beskriver också hur du kör ett exempel program för att push-överföra händelser till Azure Time Series Insights från Event Hubs. Om du har en befintlig händelsehubben med händelser i JSON-format, hoppar du över den här självstudien och visar din miljö i [Azure Time Series Insights](./tutorial-set-up-environment.md).

## <a name="configure-an-event-hub"></a>Skapa en Event Hub

1. Information om hur du skapar en Event Hub finns i [Event Hubs-dokumentationen](../event-hubs/index.yml).
1. Sök efter **Event Hubs** i sökrutan. I listan som returneras väljer du **Event Hubs**.
1. Välj händelsehubben.
1. När du skapar en Event Hub skapar du ett namn område för Event Hub. Om du ännu inte har skapat en händelsehubben i namn området går du till menyn och skapar en händelsehubben under **entiteter**.

    [![Lista över händelse nav](media/send-events/tsi-connect-event-hub-namespace.png)](media/send-events/tsi-connect-event-hub-namespace.png#lightbox)

1. När du har skapat en Event Hub väljer du den i listan över händelse nav.
1. På menyn under **entiteter** väljer du **Event Hubs**.
1. Välj namnet på händelsehubben för att konfigurera det.
1. Under **Översikt** väljer du **konsument grupper** och sedan **konsument grupp**.

    [![Skapa en konsument grupp](media/send-events/add-event-hub-consumer-group.png)](media/send-events/add-event-hub-consumer-group.png#lightbox)

1. Se till att du skapar en konsument grupp som uteslutande används av din Azure Time Series Insights händelse källa.

    > [!IMPORTANT]
    > Se till att konsument gruppen inte används av någon annan tjänst, till exempel ett Azure Stream Analytics jobb eller någon annan Azure Time Series Insights miljö. Om konsument gruppen används av andra tjänster påverkas Läs åtgärder negativt både för den här miljön och för andra tjänster. Om du använder **$default** som konsument grupp kan andra läsare eventuellt återanvända din konsument grupp.

1. Välj **principer för delad åtkomst** på menyn under **Inställningar** och välj sedan **Lägg till**.

    [![Välj principer för delad åtkomst och välj sedan knappen Lägg till](media/send-events/add-shared-access-policy.png)](media/send-events/add-shared-access-policy.png#lightbox)

1. I fönstret **Lägg till ny princip för delad åtkomst** skapar du en delad åtkomst med namnet **MySendPolicy**. Du använder den här delade åtkomst principen för att skicka händelser i C#-exemplen senare i den här artikeln.

    [![I rutan princip namn anger du MySendPolicy](media/send-events/configure-shared-access-policy-confirm.png)](media/send-events/configure-shared-access-policy-confirm.png#lightbox)

1. Under **anspråk** markerar du kryss rutan **Skicka** .

## <a name="add-an-azure-time-series-insights-instance"></a>Lägg till en Azure Time Series Insights-instans

I Azure Time Series Insights Gen2 kan du lägga till sammanhangsbaserade data till inkommande telemetri med Time Series-modellen (TSM). I TSM kallas taggar eller signaler som *instanser,* och du kan lagra sammanhangsbaserade data i *instans fält.* Data kopplas vid en tidpunkt med hjälp av ett **Time Series-ID**. **Time Series-ID: t** för det exempel Windmills-projekt som vi använder senare i den här artikeln är `id` . Läs mer om hur du lagrar data i instans fält i Översikt över [Time Series-modellen](./concepts-model-overview.md) .

### <a name="create-an-azure-time-series-insights-event-source"></a>Skapa en händelse källa för Azure Time Series Insights

1. Om du inte har skapat en händelse källa slutför du stegen för att [skapa en händelse källa](./how-to-ingest-data-event-hub.md).

1. Ange ett värde för `timeSeriesId` . Läs [tids serie modeller](./concepts-model-overview.md)för att lära dig mer om **Time Series ID**.

### <a name="push-events-to-windmills-sample"></a>Push-händelser till Windmills-exempel

1. Sök efter **Event Hubs** i Sök fältet. I listan som returneras väljer du **Event Hubs**.

1. Välj Event Hub-instansen.

1. Gå till **principer för delad åtkomst**  >  **MySendPolicy**. Kopiera värdet för **anslutnings strängen – primär nyckel**.

    [![Kopiera värdet för primär nyckelns anslutnings sträng](media/send-events/configure-sample-code-connection-string.png)](media/send-events/configure-sample-code-connection-string.png#lightbox)

1. Gå till <https://tsiclientsample.azurewebsites.net/windFarmGen.html>. URL: en skapar och kör simulerade Windmill-enheter.
1. I rutan **anslutnings sträng för händelsehubben** på webb sidan klistrar du in anslutnings strängen som du kopierade i fältet [Windmill](#push-events-to-windmills-sample).

    [![Klistra in anslutnings strängen primär nyckel i rutan anslutnings sträng för händelse hubb](media/send-events/configure-wind-mill-sim.png)](media/send-events/configure-wind-mill-sim.png#lightbox)

1. Välj **Klicka för att starta**.

    > [!TIP]
    > Windmill-simulatorn skapar också JSON som du kan använda som en nytto last med [Azure Time Series Insights ga-fråge-API: er](/rest/api/time-series-insights/gen1-query).

    > [!NOTE]
    > Simulatorn fortsätter att skicka data tills fliken webbläsare stängs.

1. Gå tillbaka till händelsehubben i Azure Portal. På sidan **Översikt** visas nya händelser som tas emot av händelsehubben.

    [![En översikts sida för händelsehubben som visar mått för händelsehubben](media/send-events/review-windmill-telemetry.png)](media/send-events/review-windmill-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>JSON-former som stöds

### <a name="example-one"></a>Exempel en

* **Inmatade**: ett enkelt JSON-objekt.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Utdata**: en händelse.

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Exempel två

* **Inmatade**: en JSON-matris med två JSON-objekt. Varje JSON-objekt konverteras till en händelse.

    ```JSON
    [
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
    ```

* **Utdata**: två händelser.

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>Exempel tre

* **Inmatade**: ett JSON-objekt med en KAPSLAd JSON-matris som innehåller två JSON-objekt.

    ```JSON
    {
        "location":"WestUs",
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z"
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z"
            }
        ]
    }
    ```

* **Utdata**: två händelser. Egenskaps **platsen** kopieras till varje händelse.

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Exempel fyra

* **Inmatade**: ett JSON-objekt med en KAPSLAd JSON-matris som innehåller två JSON-objekt. Den här indatamängden visar att globala egenskaper kan representeras av det komplexa JSON-objektet.

    ```JSON
    {
        "location":"WestUs",
        "manufacturer":{
            "name":"manufacturer1",
            "location":"EastUs"
        },
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z",
                "data":{
                    "type":"pressure",
                    "units":"psi",
                    "value":108.09
                }
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z",
                "data":{
                    "type":"vibration",
                    "units":"abs G",
                    "value":217.09
                }
            }
        ]
    }
    ```

* **Utdata**: två händelser.

    |location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|tryck|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Nästa steg

* [Visa din miljö](https://insights.timeseries.azure.com) i Azure Time Series Insights Explorer.

* Läs mer om [IoT Hub enhets meddelanden](../iot-hub/iot-hub-devguide-messages-construct.md)