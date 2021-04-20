---
title: Lösningsbygge för Azure IoT Central | Microsoft Docs
description: Azure IoT Central är en IoT-programplattform som gör skapandet av IoT-lösningar enklare. Den här artikeln innehåller en översikt över hur du skapar integrerade lösningar IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: e762b8c2e2d7f72b89629c520560b205cedcd036
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728566"
---
# <a name="iot-central-solution-builder-guide"></a>IoT Central för lösningsverktyget

*Den här artikeln gäller för lösningsbyggare.*

Med IoT Central program kan du övervaka och hantera miljontals enheter under hela livscykeln. Den här guiden är för lösningsbyggare som använder IoT Central för att skapa integrerade lösningar. Med IoT Central program kan du hantera enheter, analysera enhettelemetri och integrera med andra backend-tjänster.

En lösningsbyggare:

- Konfigurerar instrumentpaneler och vyer IoT Central webbgränssnittet.
- Använder de inbyggda reglerna och analysverktygen för att härleda affärsinsikter från de anslutna enheterna.
- Använder funktionerna för dataexport och regler för att IoT Central med andra backend-tjänster.

## <a name="configure-dashboards-and-views"></a>Konfigurera instrumentpaneler och vyer

Ett IoT Central program kan ha en eller flera instrumentpaneler som operatörer använder för att visa och interagera med programmet. Som lösningsbyggare kan du anpassa standardinstrumentpanelen och skapa specialiserade instrumentpaneler:

- Några exempel på anpassade instrumentpaneler finns i [Branschfokuserade mallar.](concepts-app-templates.md#industry-focused-templates)
- Mer information om instrumentpaneler finns i [Skapa och hantera flera instrumentpaneler](howto-create-personal-dashboards.md) och Konfigurera [instrumentpanelen för programmet.](howto-add-tiles-to-your-dashboard.md)

När en enhet ansluter IoT Central en enhet associeras enheten med en enhetsmall för enhetstypen. En enhetsmall har anpassningsbara vyer som en operatör använder för att hantera enskilda enheter. Som lösningsutvecklare kan du skapa och anpassa tillgängliga vyer för en enhetstyp. Mer information finns i Lägga [till vyer.](howto-set-up-template.md#add-views)

## <a name="use-built-in-rules-and-analytics"></a>Använda inbyggda regler och analyser

En lösningsutvecklare kan lägga till regler i IoT Central program som kör anpassningsbara åtgärder. Regler utvärderar villkor baserat på data som kommer från en enhet för att avgöra när en åtgärd ska köras. Mer information om regler finns i:

- [Självstudier: Skapa en regel och konfigurera aviseringar i ditt Azure IoT Central-program](tutorial-create-telemetry-rules.md)
- [Skapa webhook-åtgärder för regler i Azure IoT Central](howto-create-webhooks.md)
- [Gruppera flera åtgärder som ska köras från en eller flera regler](howto-use-action-groups.md)

IoT Central har inbyggda analysfunktioner som en operatör kan använda för att analysera data som flödar från anslutna enheter. Mer information finns i Så [här använder du analyser för att analysera enhetsdata.](howto-create-analytics.md)

## <a name="integrate-with-other-services"></a>Integrera med andra tjänster

Som lösningsbyggare kan du använda funktionerna för dataexport och regler i IoT Central integrera med andra tjänster. Mer information finns i:

- [Exportera IoT-data till molnmål med hjälp av dataexport](howto-export-data.md)
- [Transformera data för IoT Central](howto-transform-data.md)
- [Använda arbetsflöden för att integrera Azure IoT Central med andra molntjänster](howto-configure-rules-advanced.md)
- [Utöka Azure IoT Central med anpassade regler med hjälp av Stream Analytics, Azure Functions och SendGrid](howto-create-custom-rules.md)
- [Utöka Azure IoT Central med anpassad analys med hjälp av Azure Databricks](howto-create-custom-analytics.md)
- [Visualisera och analysera dina Azure IoT Central data i en Power BI instrumentpanel](howto-connect-powerbi.md)

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om hur du använder IoT Central föreslår vi att du provar snabbstarterna, från och med Skapa ett [Azure IoT Central-program](./quick-deploy-iot-central.md).
