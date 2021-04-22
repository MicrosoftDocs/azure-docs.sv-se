---
title: Utöka Azure IoT Central med anpassade | Microsoft Docs
description: Som lösningsutvecklare konfigurerar du ett IoT Central för att göra anpassade analyser och visualiseringar. Den här lösningen använder Azure Databricks.
author: philmea
ms.author: philmea
ms.date: 03/15/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 9ada9947b217944d9aec9f785f4716bfe43315b1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872775"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Utöka Azure IoT Central med anpassad analys med hjälp av Azure Databricks

Den här guiden visar hur du som lösningsutvecklare kan utöka ditt IoT Central med anpassade analyser och visualiseringar. I exemplet används en [Azure Databricks för](/azure/azure-databricks/) att analysera IoT Central telemetriström och för att generera visualiseringar som [låddiagram](https://wikipedia.org/wiki/Box_plot).  

Den här guiden visar hur du utökar IoT Central utöver vad den redan kan göra med de [inbyggda analysverktygen](./howto-create-custom-analytics.md).

I den här guiden får du lära dig att:

* Strömma telemetri från ett IoT Central program med hjälp av *kontinuerlig dataexport*.
* Skapa en Azure Databricks miljö för att analysera och rita enhettelemetri.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här instruktionerna behöver du en aktiv Azure-prenumeration.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

### <a name="iot-central-application"></a>IoT Central program

Skapa ett IoT Central-program på [Azure IoT Central programhanterarens](https://aka.ms/iotcentral) webbplats med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Prisplan | Standard |
| Programmall | Analys i butik – villkorsövervakning |
| Programnamn | Acceptera standardinställningen eller välj ditt eget namn |
| URL | Acceptera standardinställningen eller välj ett eget unikt URL-prefix |
| Katalog | Din Azure Active Directory klient |
| Azure-prenumeration | Din Azure-prenumeration |
| Region | Din närmaste region |

Exemplen och skärmbilderna i den här artikeln använder **USA** region. Välj en plats nära dig och se till att du skapar alla dina resurser i samma region.

Den här programmallen innehåller två simulerade termostatenheter som skickar telemetri.

### <a name="resource-group"></a>Resursgrupp

Använd den [Azure Portal för att skapa en resursgrupp med](https://portal.azure.com/#create/Microsoft.ResourceGroup) namnet **IoTCentralAnalysis** som ska innehålla de andra resurser som du skapar. Skapa dina Azure-resurser på samma plats som ditt IoT Central program.

### <a name="event-hubs-namespace"></a>Event Hubs-namnområde

Använd Azure Portal [för att skapa Event Hubs ett namnområde](https://portal.azure.com/#create/Microsoft.EventHub) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Namn    | Välj namnområdesnamn |
| Prisnivå | Grundläggande |
| Prenumeration | Din prenumeration |
| Resursgrupp | IoTCentralAnalysis |
| Plats | USA, östra |
| Genomflödesenheter | 1 |

### <a name="azure-databricks-workspace"></a>Azure Databricks arbetsyta

Använd Azure Portal [för att skapa Azure Databricks tjänst](https://portal.azure.com/#create/Microsoft.Databricks) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Namn på arbetsyta    | Välj namnet på din arbetsyta |
| Prenumeration | Din prenumeration |
| Resursgrupp | IoTCentralAnalysis |
| Plats | USA, östra |
| Prisnivå | Standard |

När du har skapat de nödvändiga resurserna ser resursgruppen **IoTCentralAnalysis** ut som på följande skärmbild:

:::image type="content" source="media/howto-create-custom-analytics/resource-group.png" alt-text="bild IoT Central resursgrupp för analys.":::

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

Du kan konfigurera ett IoT Central att kontinuerligt exportera telemetri till en händelsehubb. I det här avsnittet skapar du en händelsehubb för att ta emot telemetri från ditt IoT Central program. Händelsehubben levererar telemetrin till ditt Stream Analytics för bearbetning.

1. I Azure Portal navigerar du till ditt Event Hubs och väljer **+ Händelsehubb**.
1. Namnge händelsehubben **centralexportera**.
1. I listan över händelsehubbbar i namnområdet väljer du **centralexportera**. Välj sedan **Policyer för delad åtkomst.**
1. Välj **+ Lägg till**. Skapa en princip med **namnet SendListen** med **anspråken Skicka** **och** lyssna.
1. När principen är klar markerar du den i listan och kopierar sedan värdet **För anslutningssträngens primära** nyckel.
1. Anteckna den här anslutningssträngen. Du använder den senare när du konfigurerar databricks-notebook-datorn så att den läser från händelsehubben.

Ditt Event Hubs ser ut som på följande skärmbild:

:::image type="content" source="media/howto-create-custom-analytics/event-hubs-namespace.png" alt-text="bild av Event Hubs namnområdet.":::

## <a name="configure-export-in-iot-central"></a>Konfigurera export i IoT Central

I det här avsnittet konfigurerar du programmet för att strömma telemetri från dess simulerade enheter till din händelsehubb.

På den [Azure IoT Central programhanterarens](https://aka.ms/iotcentral) webbplats går du till IoT Central som du skapade tidigare. Skapa först ett mål för att konfigurera exporten:

1. Gå till **sidan Dataexport** och välj sedan **Mål.**
1. Välj **+ Nytt mål.**
1. Använd värdena i följande tabell för att skapa ett mål:

    | Inställning | Värde |
    | ----- | ----- |
    | Målnamn | Händelsehubb för telemetri |
    | Måltyp | Azure Event Hubs |
    | Anslutningssträng | Händelsehubbens anslutningssträng som du antecknade tidigare |

    **Händelsehubben** visas som **centralexportera**.

    :::image type="content" source="media/howto-create-custom-analytics/data-export-1.png" alt-text="Skärmbild som visar dataexportmålet.":::

1. Välj **Spara**.

Så här skapar du exportdefinitionen:

1. Gå till **sidan Dataexport** och välj **+ Ny export.**

1. Använd värdena i följande tabell för att konfigurera exporten:

    | Inställning | Värde |
    | ------- | ----- |
    | Exportera namn | Exportera händelsehubb |
    | Enabled | På |
    | Typ av data som ska exporteras | Telemetri |
    | Mål | Välj **+ Mål** och sedan **Telemetrihändelsehubb** |

1. Välj **Spara**.

    :::image type="content" source="media/howto-create-custom-analytics/data-export-2.png" alt-text="Skärmbild som visar dataexportdefinitionen.":::

Vänta tills exportstatusen är **Felfri** på **sidan Dataexport** innan du fortsätter.

## <a name="configure-databricks-workspace"></a>Konfigurera Databricks-arbetsytan

I dialogrutan Azure Portal du till din Azure Databricks och väljer **Starta arbetsyta.** En ny flik öppnas i webbläsaren och loggar in på din arbetsyta.

### <a name="create-a-cluster"></a>Skapa ett kluster

På sidan **Azure Databricks** väljer du Nytt kluster under listan med **vanliga uppgifter.**

Använd informationen i följande tabell för att skapa klustret:

| Inställning | Värde |
| ------- | ----- |
| Klusternamn | centralanalysis |
| Klusterläge | Standard |
| Databricks Runtime Version | 5.5 LTS (Scala 2.11, Spark 2.4.5) |
| Python-version | 3 |
| Aktivera autoskalning | No |
| Avsluta efter minuters inaktivitet | 30 |
| Arbetstyp | Standard_DS3_v2 |
| Arbetstagare | 1 |
| Drivrutinstyp | Samma som arbetare |

Det kan ta flera minuter att skapa ett kluster. Vänta tills klustret har skapats innan du fortsätter.

### <a name="install-libraries"></a>Installera bibliotek

På sidan **Kluster väntar** du tills klustertillståndet **körs.**

Följande steg visar hur du importerar biblioteket som ditt exempel behöver till klustret:

1. På sidan **Kluster väntar** du tills tillståndet för det interaktiva klustret **för centralanalys är** **Körs.**

1. Välj klustret och välj sedan **fliken** Bibliotek.

1. På fliken **Bibliotek** väljer du **Installera ny.**

1. På sidan **Installera** bibliotek väljer du **Maven** som bibliotekskälla.

1. I **textrutan Koordinater** anger du följande värde: `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Välj **Installera** för att installera biblioteket i klustret.

1. Biblioteksstatusen är nu **Installerad:**

:::image type="content" source="media/howto-create-custom-analytics/cluster-libraries.png" alt-text="Skärmbild av bibliotek installerat.":::

### <a name="import-a-databricks-notebook"></a>Importera en Databricks-notebook-dator

Använd följande steg för att importera en Databricks-notebook-fil som innehåller Python-koden för att analysera och visualisera IoT Central telemetri:

1. Gå till sidan **Arbetsyta** i Databricks-miljön. Välj listrutan bredvid namnet på ditt konto och välj sedan **Importera**.

1. Välj att importera från en URL och ange följande adress: [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Om du vill importera anteckningsboken väljer du **Importera**.

1. Välj arbetsytan **för att** visa den importerade notebook-filen:

:::image type="content" source="media/howto-create-custom-analytics/import-notebook.png" alt-text="Skärmbild av importerad notebook-dator.":::

5. Redigera koden i den första Python-cellen för att lägga till Event Hubs anslutningssträng som du sparade tidigare:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Köra analys

Om du vill köra analysen måste du koppla notebook-datorn till klustret:

1. Välj **Frånkopplad** och välj sedan **klustret centralanalysis.**
1. Om klustret inte körs startar du det.
1. Starta anteckningsboken genom att välja körningsknappen.

Du kan se ett fel i den sista cellen. I så fall kontrollerar du att de föregående cellerna körs, väntar en minut tills vissa data skrivs till lagringen och kör sedan den sista cellen igen.

### <a name="view-smoothed-data"></a>Visa utjämnade data

I anteckningsboken rullar du ned till cell 14 för att se ett diagram över den rullande genomsnittliga luftfuktigheten efter enhetstyp. Det här ritytan uppdateras kontinuerligt när strömmande telemetri tas emot:

:::image type="content" source="media/howto-create-custom-analytics/telemetry-plot.png" alt-text="Skärmbild av Smoothed telemetry plot (Utjämnad telemetriritning).":::

Du kan ändra storlek på diagrammet i anteckningsboken.

### <a name="view-box-plots"></a>Visa box-diagram

I anteckningsboken rullar du ned till cell 20 för att se [boxritningar](https://en.wikipedia.org/wiki/Box_plot). Box-ritytan baseras på statiska data, så för att uppdatera dem måste du köra cellen igen:

:::image type="content" source="media/howto-create-custom-analytics/box-plots.png" alt-text="Skärmbild av låddiagram.":::

Du kan ändra storlek på diagram i anteckningsboken.

## <a name="tidy-up"></a>Tidy up (Tidy Up)

För att få ordning på och undvika onödiga kostnader tar du bort resursgruppen **IoTCentralAnalysis** i Azure Portal.

Du kan ta bort IoT Central från **sidan Hantering** i programmet.

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig att:

* Strömma telemetri från ett IoT Central program med hjälp av *kontinuerlig dataexport*.
* Skapa en Azure Databricks miljö för att analysera och rita telemetridata.

Nu när du vet hur du skapar anpassad analys föreslår vi att du går vidare med att lära dig hur du visualiserar och analyserar Azure IoT Central data i en [Power BI instrumentpanel](howto-connect-powerbi.md).
