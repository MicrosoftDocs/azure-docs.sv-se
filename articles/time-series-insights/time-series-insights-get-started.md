---
title: Skapa en miljö – Azure Time Series Insights | Microsoft Docs
description: Lär dig hur du använder Azure Portal för att skapa en ny Azure Time Series Insights-miljö.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 61af7922318514a7b86a349d1970c59d4d168d85
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95023334"
---
# <a name="create-a-new-azure-time-series-insights-gen1-environment-in-the-azure-portal"></a>Skapa en ny Azure Time Series Insights gen1-miljö i Azure Portal

> [!CAUTION]
> Det här är en gen1-artikel.

I den här artikeln beskrivs hur du skapar en ny Azure Time Series Insights miljö med hjälp av Azure Portal.

Med Azure Time Series Insights kan du komma igång med att visualisera och fråga data som flödar till Azure IoT-hubbar och Event Hubs på några minuter, så att du kan fråga stora volymer av tids serie data på några sekunder.  Den har utformats för att skala Internet-of-in-information (IoT) och kan hantera terabyte data.

## <a name="steps-to-create-the-environment"></a>Anvisningar för att skapa miljön

Följ de här stegen för att skapa en miljö:

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj knappen **+ skapa en resurs** .

1. Välj kategorin **Sakernas Internet** och välj **Time Series Insights**.

   [![Skapa Azure Time Series Insightss miljön](media/time-series-insights-get-started/tsi-create-new-environment.png)](media/time-series-insights-get-started/tsi-create-new-environment.png#lightbox)

1. På sidan **Time Series Insights** väljer du **skapa**.

1. Fyll i obligatoriska parametrar. I följande tabell beskrivs varje parameter:

   [![Skapa resurs gruppen Azure Time Series Insights](media/time-series-insights-get-started/tsi-configure-and-create.png)](media/time-series-insights-get-started/tsi-configure-and-create.png#lightbox)

   Inställning|Föreslaget värde|Beskrivning
   ---|---|---
   Miljönamn | Ett unikt namn | Det här namnet representerar miljön i [Time Series Explorer](https://insights.timeseries.azure.com)
   Prenumeration | Din prenumeration | Om du har flera prenumerationer väljer du den prenumeration som innehåller din händelse källa helst. Azure Time Series Insights kan automatiskt identifiera Azure IoT Hub-och Event Hub-resurser som är befintliga i samma prenumeration.
   Resursgrupp | Skapa en ny eller Använd befintlig | En resursgrupp är en samling med Azure-resurser som används tillsammans. Du kan välja en befintlig resurs grupp, till exempel en som innehåller händelsehubben eller IoT Hub. Eller så kan du skapa en ny om den här resursen inte är relaterad till andra resurser.
   Location | Närmaste händelse källa | Helst kan du välja samma data Center plats som innehåller dina händelse källdata, i arbetet för att undvika att extra bandbredds kostnader mellan regioner och flera zoner och extra fördröjning används när du flyttar data från regionen.
   Prisnivå | S1 | Välj det genomflöde som behövs. För lägsta kostnad och start kapacitet väljer du S1.
   Kapacitet | 1 | Kapaciteten är multiplikatorn som gäller för ingångs pris, lagrings kapacitet och kostnad som är associerad med den valda SKU: n.  Du kan ändra kapacitet för en miljö när den har skapats. För lägsta kostnad väljer du en kapacitet på 1.
  
1. Välj **skapa** för att påbörja etablerings processen. Det kan ta några minuter.

1. Välj **aviserings** symbolen (klock ikonen) för att övervaka distributions processen.

   [![Titta på meddelandena](media/time-series-insights-get-started/tsi-deploy-notifications.png)](media/time-series-insights-get-started/tsi-deploy-notifications.png#lightbox)

1. Kontrol lera distributions konfigurations inställningarna i resurs **översikten**.

   [![Skapa Azure Time Series Insights fäst vid instrument panelen](media/time-series-insights-get-started/tsi-verify-deployment.png)](media/time-series-insights-get-started/tsi-verify-deployment.png#lightbox)

1. **(Valfritt)** Välj **PIN-ikonen** i det övre högra hörnet för att enkelt få åtkomst till din Azure Time Series Insightss miljö i framtiden.

## <a name="next-steps"></a>Nästa steg

* [Definiera principer för data åtkomst](./concepts-access-policies.md) för att skydda din miljö.

* [Lägg till händelse källan för Event Hub](./how-to-ingest-data-event-hub.md) i Azure Time Series Insightss miljön.

* [Skicka händelser](time-series-insights-send-events.md) till händelse källan.

* Visa din miljö i [Azure Time Series Insights Explorer](https://insights.timeseries.azure.com).