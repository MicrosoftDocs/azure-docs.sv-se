---
title: Utöka Azure IoT Central med anpassad analys | Microsoft Docs
description: Som en lösnings utvecklare konfigurerar du ett IoT Central program för att göra anpassade analyser och visualiseringar. Den här lösningen använder Azure Databricks.
author: TheRealJasonAndrew
ms.author: v-anjaso
ms.date: 03/15/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 3132ec8fb3cb123653887d92a2f33788f40564c0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105033831"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Utöka Azure IoT Central med anpassad analys med Azure Databricks

Den här instruktions guiden visar dig som lösnings utvecklare och hur du kan utöka ditt IoT Central program med anpassade analyser och visualiseringar. Exemplet använder en [Azure Databricks](/azure/azure-databricks/) arbets yta för att analysera IoT Central telemetri-dataströmmen och för att generera visualiseringar som [låddiagram](https://wikipedia.org/wiki/Box_plot).  

Den här instruktions guiden visar hur du utökar IoT Central bortom det som redan kan utföras med de [inbyggda analys verktygen](./howto-create-custom-analytics.md).

I den här instruktions guiden får du lära dig att:

* Strömma telemetri från ett IoT Central program med *kontinuerlig data export*.
* Skapa en Azure Databricks-miljö för att analysera och rita telemetri för enheter.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här instruktions guiden behöver du en aktiv Azure-prenumeration.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

### <a name="iot-central-application"></a>IoT Central program

Skapa ett IoT Central-program på webbplatsen för [Azure IoT Central Application Manager](https://aka.ms/iotcentral) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Pris plan | Standard |
| Programmall | In-Store Analytics – villkors övervakning |
| Programnamn | Acceptera standardvärdet eller Välj ditt eget namn |
| URL | Acceptera standardvärdet eller Välj ditt eget unika URL-prefix |
| Katalog | Din Azure Active Directory klient |
| Azure-prenumeration | Din Azure-prenumeration |
| Region | Din närmaste region |

I exemplen och skärm bilderna i den här artikeln används den **USA** regionen. Välj en plats nära dig och se till att du skapar alla resurser i samma region.

Den här program mal len innehåller två simulerade termostat-enheter som skickar telemetri.

### <a name="resource-group"></a>Resursgrupp

Använd [Azure Portal för att skapa en resurs grupp](https://portal.azure.com/#create/Microsoft.ResourceGroup) med namnet **IoTCentralAnalysis** som innehåller de andra resurser som du skapar. Skapa dina Azure-resurser på samma plats som ditt IoT Central-program.

### <a name="event-hubs-namespace"></a>Event Hubs-namnområde

Använd [Azure Portal för att skapa ett Event Hubs-namnområde](https://portal.azure.com/#create/Microsoft.EventHub) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Namn    | Välj namn på namn område |
| Prisnivå | Grundläggande |
| Prenumeration | Din prenumeration |
| Resursgrupp | IoTCentralAnalysis |
| Plats | USA, östra |
| Genomflödesenheter | 1 |

### <a name="azure-databricks-workspace"></a>Azure Databricks arbets yta

Använd [Azure Portal för att skapa en Azure Databricks tjänst](https://portal.azure.com/#create/Microsoft.Databricks) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Namn på arbetsyta    | Välj namn på arbets yta |
| Prenumeration | Din prenumeration |
| Resursgrupp | IoTCentralAnalysis |
| Plats | USA, östra |
| Prisnivå | Standard |

När du har skapat de resurser som krävs ser **IoTCentralAnalysis** -resurs gruppen ut som följande skärm bild:

:::image type="content" source="media/howto-create-custom-analytics/resource-group.png" alt-text="bild av IoT Central analys resurs grupp.":::

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

Du kan konfigurera ett IoT Central program för att kontinuerligt exportera telemetri till en Event Hub. I det här avsnittet skapar du en händelsehubben som tar emot telemetri från ditt IoT Central-program. Händelsehubben ger telemetri till din Stream Analytics jobb för bearbetning.

1. I Azure Portal navigerar du till Event Hubs namn området och väljer **+ Event Hub**.
1. Namnge Event Hub- **centralexport**.
1. I listan över händelse nav i namn området väljer du **centralexport**. Välj sedan **principer för delad åtkomst**.
1. Välj **+ Lägg till**. Skapa en princip med namnet **SendListen** med **Skicka** och **Lyssna** anspråk.
1. När principen är klar markerar du den i listan och kopierar sedan **anslutnings strängen – primär nyckel** värde.
1. Anteckna den här anslutnings strängen. du använder den senare när du konfigurerar din Databricks-anteckningsbok för att läsa från händelsehubben.

Event Hubs namn området ser ut som på följande skärm bild:

:::image type="content" source="media/howto-create-custom-analytics/event-hubs-namespace.png" alt-text="bild av Event Hubs namn område.":::

## <a name="configure-export-in-iot-central"></a>Konfigurera export i IoT Central

I det här avsnittet konfigurerar du programmet till att strömma telemetri från dess simulerade enheter till händelsehubben.

På webbplatsen [Azure IoT Central Application Manager](https://aka.ms/iotcentral) navigerar du till det IoT Central program som du skapade tidigare. Konfigurera exporten genom att först skapa ett mål:

1. Gå till sidan **data export** och välj sedan **destinationer**.
1. Välj **+ nytt mål**.
1. Använd värdena i följande tabell för att skapa ett mål:

    | Inställning | Värde |
    | ----- | ----- |
    | Målnamn | Händelsehubben för telemetri |
    | Måltyp | Azure Event Hubs |
    | Anslutningssträng | Den Event Hub-anslutningssträng du antecknade tidigare |

    **Händelsehubben** visas som **centralexport**.

    :::image type="content" source="media/howto-create-custom-analytics/data-export-1.png" alt-text="Skärm bild som visar data export destination.":::

1. Välj **Spara**.

Skapa export definitionen:

1. Gå till sidan **data export** och välj **+ ny export**.

1. Använd värdena i följande tabell för att konfigurera exporten:

    | Inställning | Värde |
    | ------- | ----- |
    | Export namn | Export av Event Hub |
    | Enabled | På |
    | Typ av data som ska exporteras | Telemetri |
    | Mål | Välj **+ mål** och välj sedan **telemetri Event Hub** |

1. Välj **Spara**.

    :::image type="content" source="media/howto-create-custom-analytics/data-export-2.png" alt-text="Skärm bild som visar data export definition.":::

Vänta tills export statusen är **felfri** på **data export** sidan innan du fortsätter.

## <a name="configure-databricks-workspace"></a>Konfigurera Databricks-arbetsyta

I Azure Portal navigerar du till Azure Databricks-tjänsten och väljer **Starta arbets yta**. En ny flik öppnas i webbläsaren och du loggas in på din arbets yta.

### <a name="create-a-cluster"></a>Skapa ett kluster

På sidan **Azure Databricks** går du till listan med vanliga uppgifter och väljer **nytt kluster**.

Använd informationen i följande tabell för att skapa klustret:

| Inställning | Värde |
| ------- | ----- |
| Klusternamn | centralanalysis |
| Kluster läge | Standard |
| Databricks Runtime version | 5,5 LTS (Scala 2,11, Spark 2.4.5) |
| Python-version | 3 |
| Aktivera automatisk skalning | Inga |
| Avsluta efter minuter av inaktivitet | 30 |
| Typ av arbetare | Standard_DS3_v2 |
| Arbetare | 1 |
| Driv rutins typ | Samma som arbetare |

Det kan ta flera minuter att skapa ett kluster, vänta tills klustret har skapats innan du fortsätter.

### <a name="install-libraries"></a>Installera bibliotek

På sidan **kluster** väntar du tills kluster status är **igång**.

Följande steg visar hur du importerar biblioteket som exempel behov i klustret:

1. På sidan **kluster** väntar du tills status för det interaktiva **Centralanalysis** -klustret **körs**.

1. Välj klustret och välj sedan fliken **bibliotek** .

1. Välj **installera ny** på fliken **bibliotek** .

1. På sidan **Installera bibliotek** väljer du **maven** som biblioteks källa.

1. I text rutan **koordinater** anger du följande värde: `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Välj **Installera** för att installera biblioteket på klustret.

1. Bibliotekets status är nu **installerad**:

:::image type="content" source="media/howto-create-custom-analytics/cluster-libraries.png" alt-text="Skärm bild av installerat bibliotek.":::

### <a name="import-a-databricks-notebook"></a>Importera en Databricks Notebook

Använd följande steg för att importera en Databricks-anteckningsbok som innehåller python-koden för att analysera och visualisera IoT Central telemetri:

1. Navigera till sidan för **arbets ytan** i din Databricks-miljö. Välj List rutan bredvid ditt konto namn och välj sedan **Importera**.

1. Välj att importera från en URL och ange följande adress: [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Importera antecknings boken genom att välja **Importera**.

1. Välj **arbets ytan** för att visa den importerade antecknings boken:

:::image type="content" source="media/howto-create-custom-analytics/import-notebook.png" alt-text="Skärm bild av importerad Notebook.":::

5. Redigera koden i den första python-cellen för att lägga till Event Hubs anslutnings strängen som du sparade tidigare:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Kör analys

Om du vill köra analysen måste du ansluta antecknings boken till klustret:

1. Välj **frånkopplad** och välj sedan **centralanalysis** -klustret.
1. Om klustret inte körs startar du det.
1. Starta antecknings boken genom att klicka på knappen Kör.

Du kan se ett fel i den sista cellen. I så fall, kontrol lera att de föregående cellerna körs, vänta en minut tills vissa data skrivs till lagring och kör sedan den sista cellen igen.

### <a name="view-smoothed-data"></a>Visa jämna data

Rulla ned till cell 14 i antecknings boken för att se ett diagram över den rullande genomsnittliga fuktighets typen per enhets typ. Den här ritningen uppdateras kontinuerligt när en strömmande telemetri tas emot:

:::image type="content" source="media/howto-create-custom-analytics/telemetry-plot.png" alt-text="Skärm bild av utjämnat telemetri.":::

Du kan ändra storlek på diagrammet i antecknings boken.

### <a name="view-box-plots"></a>Visa Box-kurvor

Rulla ned till cell 20 i antecknings boken för att se [rutorna](https://en.wikipedia.org/wiki/Box_plot). Rutorna baseras på statiska data så att du kan uppdatera dem du måste köra om cellen:

:::image type="content" source="media/howto-create-custom-analytics/box-plots.png" alt-text="Skärm bild av låddiagram.":::

Du kan ändra storlek på ritytan i antecknings boken.

## <a name="tidy-up"></a>Städa upp

Ta bort resurs gruppen **IoTCentralAnalysis** i Azure Portal för att städa upp efter den här instruktionen och undvika onödiga kostnader.

Du kan ta bort IoT Central-programmet från **hanterings** sidan i programmet.

## <a name="next-steps"></a>Nästa steg

I den här instruktions guiden har du lärt dig att:

* Strömma telemetri från ett IoT Central program med *kontinuerlig data export*.
* Skapa en Azure Databricks-miljö för att analysera och rita telemetridata.

Nu när du vet hur du skapar anpassade analyser är det föreslagna nästa steg att lära dig att [visualisera och analysera dina Azure IoT Central-data på en Power BI instrument panel](howto-connect-powerbi.md).
