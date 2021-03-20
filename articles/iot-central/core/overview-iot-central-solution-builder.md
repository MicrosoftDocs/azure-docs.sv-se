---
title: Lösnings skapande för Azure IoT Central | Microsoft Docs
description: Azure IoT Central är en IoT-programplattform som gör skapandet av IoT-lösningar enklare. Den här artikeln innehåller en översikt över hur du skapar integrerade lösningar med IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 72aa8e5e3284e0ee7fbe63e0fb617b9eba03292e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100418236"
---
# <a name="iot-central-solution-builder-guide"></a>Guide för IoT Central Solution Builder

*Den här artikeln gäller lösnings byggare.*

Med ett IoT Central program kan du övervaka och hantera miljon tals enheter under deras livs cykel. Den här guiden är till för lösnings byggare som använder IoT Central för att bygga integrerade lösningar. Med ett IoT Central-program kan du hantera enheter, analysera telemetri för enheter och integrera med andra server dels tjänster.

Ett Solution Builder:

- Konfigurerar instrument paneler och vyer i IoT Central-webbgränssnittet.
- Använder de inbyggda reglerna och analys verktygen för att härleda affärs insikter från anslutna enheter.
- Använder funktionerna för data export och regler för att integrera IoT Central med andra backend-tjänster.

## <a name="configure-dashboards-and-views"></a>Konfigurera instrument paneler och vyer

Ett IoT Central program kan ha en eller flera instrument paneler som operatörer använder för att visa och interagera med programmet. Som Solution Builder kan du anpassa standard instrument panelen och skapa specialiserade instrument paneler:

- För att visa några exempel på anpassade instrument paneler, se [bransch fokuserade mallar](concepts-app-templates.md#industry-focused-templates).
- Mer information om instrument paneler finns i [skapa och hantera flera instrument paneler](howto-create-personal-dashboards.md) och [Konfigurera program instrument panelen](howto-add-tiles-to-your-dashboard.md).

När en enhet ansluter till en IoT Central, är enheten kopplad till en enhets mall för enhets typen. En enhets mal len har anpassningsbara vyer som en operatör använder för att hantera enskilda enheter. Som en lösnings utvecklare kan du skapa och anpassa tillgängliga vyer för en enhets typ. Mer information finns i [lägga till vyer](howto-set-up-template.md#add-views).

## <a name="use-built-in-rules-and-analytics"></a>Använda inbyggda regler och analyser

En lösnings utvecklare kan lägga till regler i ett IoT Central program som kör anpassningsbara åtgärder. Regler utvärderar villkor, baserat på data som kommer från en enhet, för att fastställa när en åtgärd ska köras. Mer information om regler finns i:

- [Självstudier: Skapa en regel och konfigurera aviseringar i ditt Azure IoT Central-program](tutorial-create-telemetry-rules.md)
- [Skapa webhook-åtgärder för regler i Azure IoT Central](howto-create-webhooks.md)
- [Gruppera flera åtgärder för att köra från en eller flera regler](howto-use-action-groups.md)

IoT Central har inbyggda analys funktioner som en operatör kan använda för att analysera data som flödar från de anslutna enheterna. Mer information finns i [använda analyser för att analysera enhets data](howto-create-analytics.md).

## <a name="integrate-with-other-services"></a>Integrera med andra tjänster

Som Solution Builder kan du använda funktionerna för data export och regler i IoT Central för att integrera med andra tjänster. Mer information finns i:

- [Exportera IoT-data till moln mål med hjälp av data export](howto-export-data.md)
- [Använd arbets flöden för att integrera ditt Azure IoT Central-program med andra moln tjänster](howto-configure-rules-advanced.md)
- [Utöka Azure IoT Central med anpassade regler med hjälp av Stream Analytics, Azure Functions och SendGrid](howto-create-custom-rules.md)
- [Utöka Azure IoT Central med anpassad analys med Azure Databricks](howto-create-custom-analytics.md)
- [Visualisera och analysera dina Azure IoT Central-data på en Power BI-instrumentpanel](howto-connect-powerbi.md)

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om hur du använder IoT Central, är de föreslagna nästa steg som du behöver för att testa snabb starterna, från och med [skapa ett Azure IoT Central-program](./quick-deploy-iot-central.md).
