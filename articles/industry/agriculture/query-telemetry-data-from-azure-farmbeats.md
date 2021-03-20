---
title: Fråga inmatade telemetridata
description: I den här artikeln beskrivs hur du frågar efter insamlade telemetridata.
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: b9067e2f78c8098d4a21263ac89caf03da631274
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92677491"
---
# <a name="query-ingested-telemetry-data"></a>Fråga inmatade telemetridata

I den här artikeln beskrivs hur du frågar efter inmatade sensor data från Azure FarmBeats.

Att mata in data från Sakernas Internet-resurser (IoT) som enheter och sensorer är ett vanligt scenario i FarmBeats. Du skapar metadata för enheter och sensorer och matar sedan in historiska data till FarmBeats i kanoniskt format. När sensor data är tillgängliga på FarmBeats Datahub kan vi fråga samma för att generera användbara insikter eller skapa modeller.

## <a name="before-you-begin"></a>Innan du börjar

Innan du fortsätter med den här artikeln måste du kontrol lera att du har installerat FarmBeats och matat in sensorer för telemetri från IoT-enheter till FarmBeats.

Om du vill mata in information om sensor telemetri kan du besöka Hämta [historiska telemetridata](ingest-historical-telemetry-data-in-azure-farmbeats.md)

Innan du fortsätter måste du också se till att du är bekant med FarmBeats REST API: er eftersom du kommer att fråga inmatad telemetri med hjälp av API: erna. Mer information om FarmBeats-API: er finns i [FARMBEATS REST-API: er](rest-api-in-azure-farmbeats.md). **Se till att du kan göra API-begäranden till din FarmBeats Datahub-slutpunkt**.

## <a name="query-ingested-sensor-telemetry-data"></a>Fråga inmatade sensorer för telemetri

Det finns två sätt att komma åt och fråga telemetridata från FarmBeats:

- API och
- Time Series Insights (TSD).

### <a name="query-using-rest-api"></a>Fråga med REST API

Följ stegen för att fråga efter inmatade sensorer för telemetri med hjälp av FarmBeats REST API: er:

1. Identifiera den sensor som du är intresse rad av. Du kan göra detta genom att göra en GET-begäran på/sensor-API: et.

> [!NOTE]
> **ID** och **sensorModelId** för det intresserade sensor objekt.

2. Gör ett GET/{ID} på/SensorModel-API: et för **sensorModelId** som anges i steg 1. Sensor modellen har alla metadata och information om den inmatade Telemetrin från sensorn. **Sensor mått** i objektet **sensor modell** har till exempel information om vilka åtgärder som sensorn skickar och i vilka typer och enheter. Exempel:

  ```json
  {
      "name": "moist_soil_last <name of the sensor measure - this is what we will receive as part of the queried telemetry data>",
      "dataType": "Double <Data Type - eg. Double>",
      "type": "SoilMoisture <Type of measure eg. temperature, soil moisture etc.>",
      "unit": "Percentage <Unit of measure eg. Celsius, Percentage etc.>",
      "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation>",
      "description": "<Description of the measure>"
  }
  ```
Anteckna svaret från GET/{ID}-anropet för sensor modellen.

3. Gör ett anrop i/Telemetry-API: et med följande indata-nyttolast

  ```json
  {
    "sensorId": "<id of the sensor as noted in step 1>",
    "searchSpan": {
      "from": "<desired start timestamp in ISO 8601 format; default is UTC>",
      "to": "<desired end timestamp in ISO 8601 format; default is UTC>"
    },
    "filter": {
      "tsx": "string"
    },
    "projectedProperties": [
      {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    ]
  }
  ```
4. Svaret från/Telemetry-API: et ser ut ungefär så här:

  ```json
  {
    "timestamps": [
      "2020-XX-XXT07:30:00Z",
      "2020-XX-XXT07:45:00Z"
    ],
    "properties": [
      {
        "values": [
          "<id of the sensor>",
          "<id of the sensor>"
        ],
        "name": "Id",
        "type": "String"
      },
      {
        "values": [
          2.1,
          2.2
        ],
        "name": "moist_soil_last <name of the SensorMeasure as defined in the SensorModel object>",
        "type": "Double <Data Type of the value - eg. Double>"
      }
    ]
  }
  ```
I ovanstående exempel svar ger den eftersökta sensorn för data för två tidsstämplar tillsammans med mått namnet ("moist_soil_last") och värden för den rapporterade Telemetrin i de två tidsstämplarna. Du måste referera till den associerade sensor modellen (enligt beskrivningen i steg 2) för att tolka typen och enheten för de rapporterade värdena.

### <a name="query-using-azure-time-series-insights-tsi"></a>Fråga med Azure Time Series Insights (TSD)

FarmBeats utnyttjar [Azure Time Series Insights (TSD)](https://azure.microsoft.com/services/time-series-insights/) för att mata in, lagra, fråga och visualisera data i IoT-skala – data som är mycket sammanhangsbaserade och optimerade för tids serier.

Telemetridata tas emot på en EventHub och sedan bearbetas och skickas till en TSD-miljö i FarmBeats-resurs gruppen. Data kan sedan direkt frågas från TSD. Mer information finns i [TSD-dokumentation](../../time-series-insights/time-series-insights-explorer.md)

Följ stegen för att visualisera data i TSD:

1. Gå till **Azure Portal**  >  **FarmBeats DataHub-resurs grupp** > Välj **Time Series Insights** miljö (TSD-xxxx) > **principer för data åtkomst**. Lägg till användare med läsare eller deltagar åtkomst.
2. Gå till sidan **Översikt** i **Time Series Insights** Environment (TSD-xxxx) och välj **URL: en för Time Series Insights Explorer**. Nu kan du visualisera den inmatade Telemetrin.

Förutom att lagra, fråga och visualisering av telemetri kan TSD också integreras med en Power BI instrument panel. Mer information finns [här]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)

## <a name="next-steps"></a>Nästa steg

Du har nu frågat sensor data från din Azure FarmBeats-instans. Nu kan du lära dig hur du [genererar kartor](generate-maps-in-azure-farmbeats.md#generate-maps) för dina grupper.