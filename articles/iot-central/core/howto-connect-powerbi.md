---
title: Visualisera dina Azure IoT Central-data på en Power BI instrument panel | Microsoft Docs
description: Använd Power BI lösning för Azure IoT Central för att visualisera och analysera dina IoT Central data.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/4/2019
ms.topic: conceptual
ms.openlocfilehash: dd67a4f1a005abc7319723efcc3f2944b18c4f5f
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109241"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Visualisera och analysera dina Azure IoT Central-data på en Power BI-instrumentpanel

*Det här avsnittet gäller för utvecklare av administratörer och lösningar.*

[!Note] Den här lösningen använder [äldre data export funktioner](./howto-export-data-legacy.md). Håll koll på Uppdaterad vägledning om hur du ansluter till Power BI med den senaste data exporten.

:::image type="content" source="media/howto-connect-powerbi/iot-continuous-data-export.png" alt-text="Pipeline för Power BI lösning":::

Använd Power BI lösning för Azure IoT Central v3 för att skapa en kraftfull Power BI-instrumentpanel för att övervaka prestanda för dina IoT-enheter. I Power BI instrument panelen kan du:

- Spåra hur mycket data dina enheter skickar med tiden
- Jämför data volymer mellan olika telemetri strömmar
- Filtrera ned till data som skickats av vissa enheter
- Visa de senaste telemetridata i en tabell

Den här lösningen konfigurerar en pipeline som läser data från ditt [kontinuerliga data export](./howto-export-data-legacy.md) Azure Blob Storage-konto. Pipelinen använder Azure Functions, Azure Data Factory och Azure SQL Database för att bearbeta och transformera data. Du kan visualisera och analysera data i en Power BI rapport som du hämtar som en PBIX-fil. Alla resurser skapas i din Azure-prenumeration så att du kan anpassa varje komponent så att den passar dina behov.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här instruktions guiden behöver du en aktiv Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Konfigurationen av lösningen kräver följande resurser:

- Ett IoT Central-program för version 3. Information om hur du kontrollerar program versionen finns i [om ditt program](./howto-get-app-info.md). Information om hur du skapar ett IoT Central program finns i [skapa ett Azure IoT Central-program](./quick-deploy-iot-central.md).
- Kontinuerlig data export har kon figurer ATS för att exportera telemetri, enheter och enhetsspecifika till Azure Blob Storage. Mer information finns i [så här exporterar du IoT-data till mål i Azure](howto-export-data.md).
  - Se till att endast ditt IoT Central-program exporterar data till BLOB-behållaren.
  - Dina [enheter måste skicka JSON-kodade meddelanden](../../iot-hub/iot-hub-devguide-messages-d2c.md). Enheter måste ange `contentType:application/JSON` och `contentEncoding:utf-8` eller `contentEncoding:utf-16` eller `contentEncoding:utf-32` i egenskaper för meddelande systemet.
- Power BI Desktop (senaste versionen). Se [Power BI hämtningar](https://powerbi.microsoft.com/downloads/).
- Power BI Pro (om du vill dela instrument panelen med andra).

> [!NOTE]
> Om du använder en version 2 IoT Central program, se [visualisera och analysera dina Azure IoT Central-data i en Power BI-instrumentpanel](/previous-versions/azure/iot-central/core/howto-connect-powerbi) på dokumentations webbplatsen för tidigare versioner.

## <a name="install"></a>Installera

Om du vill konfigurera pipelinen navigerar du till sidan [Power BI lösning för Azure IoT Central v3](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central) på **Microsoft AppSource** -platsen. Välj **Hämta nu** och följ instruktionerna.

När du öppnar PBIX-filen måste du se till att läsa och följa instruktionerna på försättsbladet. De här anvisningarna beskriver hur du ansluter din rapport till din SQL-databas.

## <a name="report"></a>Rapport

PBIX-filen innehåller rapporten **enheter och telemetri** som visar en historisk vy över telemetri som har skickats av enheter. Det ger en analys av de olika typerna av telemetri och visar även den senaste telemetri som skickas av enheter.

:::image type="content" source="media/howto-connect-powerbi/report.png" alt-text="Rapporten Power BI enheter och telemetri":::

## <a name="pipeline-resources"></a>Pipeline-resurser

Du kan komma åt alla Azure-resurser som utgör pipelinen i Azure Portal. Alla resurser finns i resurs gruppen som du skapade när du konfigurerade pipelinen.

:::image type="content" source="media/howto-connect-powerbi/azure-deployment.png" alt-text="Azure Portal visning av resurs grupp":::

I följande lista beskrivs rollen för varje resurs i pipelinen:

### <a name="azure-functions"></a>Azure Functions

Azure Function-appen utlöser varje tillfälle IoT Central skriver en ny fil till Blob Storage. Funktionerna extraherar data från blobarna telemetri, enheter och Device templates för att fylla i de mellanliggande SQL-tabeller som Azure Data Factory använder.

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory ansluter till SQL Database som en länkad tjänst. Den kör lagrade procedurer för att bearbeta data och lagra dem i analys tabellerna.

Azure Data Factory körs var 15: e minut för att omvandla den senaste batchen med data att läsas in i SQL-tabellerna (vilket är det aktuella minimala antalet för **utlösaren rullande Window**).

### <a name="azure-sql-database"></a>Azure SQL Database

Azure Data Factory genererar en uppsättning analys tabeller för Power BI. Du kan utforska dessa scheman i Power BI och använda dem för att bygga dina egna visualiseringar.

## <a name="estimated-costs"></a>Beräknade kostnader

Sidan [Power BI lösning för Azure IoT Central v3](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central) på Microsoft AppSource-platsen innehåller en länk till en kostnads uppskattning för de resurser som du distribuerar.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du visualiserar dina data i Power BI, är det föreslagna nästa steg att lära dig [hur du hanterar enheter](howto-manage-devices.md).
