---
title: Självstudie – Använd Azure Time Series Insights för att lagra och analysera Azure IoT Plug and Play Device-telemetri
description: Självstudie – konfigurera en Time Series Insights miljö och Anslut IoT Hub för att visa och analysera telemetri från IoT Plug and Play-enheter.
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 55fa10cce038c83f0758a9537a916e2dca7e13f9
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181694"
---
# <a name="tutorial-create-and-configure-a-time-series-insights-gen2-environment"></a>Självstudie: skapa och konfigurera en Time Series Insights Gen2-miljö

I den här självstudien får du lära dig hur du skapar och konfigurerar en [Azure Time Series Insights Gen2](../time-series-insights/overview-what-is-tsi.md) -miljö som kan integreras med din IoT plug and Play-lösning. Använd Time Series Insights för att samla in, bearbeta, lagra, fråga och visualisera tids serie data i Sakernas Internets skala (IoT).

I den här kursen har du:

> [!div class="checklist"]
> * Etablera en Time Series Insights-miljö och Anslut IoT Hub som en händelse källa för strömning.
> * Arbeta med synkronisering av modeller för att redigera din [tids serie modell](../time-series-insights/concepts-model-overview.md).
> * Använd [DTDL-exempelfilerna (Digital-definitions språket)](https://github.com/Azure/opendigitaltwins-dtdl) som du använde för temperatur styrenheten och termostat-enheter.

> [!NOTE]
> Den här integrationen mellan Time Series Insights och IoT Plug and Play är i för hands version. Det sätt som DTDL enhets modeller mappar till Time Series Insights Time Series-modellen kan ändras. 

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

I det här läget har du:

* En Azure IoT-hubb.
* En tjänst för enhets etablerings tjänsten (DPS) som är länkad till din IoT-hubb. DPS-instansen bör ha en individuell enhets registrering för din IoT Plug and Play-enhet.
* En anslutning till din IoT-hubb från antingen en enskild komponent enhet eller en enhet med flera komponenter som strömmar simulerade data.

Du kan undvika kravet på att installera Azure CLI lokalt genom att använda Azure Cloud Shell för att konfigurera moln tjänsterna.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-your-event-source"></a>Förbered din händelse källa

IoT-hubben som du skapade tidigare är din Time Series Insights Miljös [händelse källa](../time-series-insights/concepts-streaming-ingestion-event-sources.md).

> [!IMPORTANT]
> Inaktivera alla befintliga IoT Hub vägar. Det finns ett känt problem med att använda en IoT-hubb med konfigurerad [routning](../iot-hub/iot-hub-devguide-messages-d2c.md#routing-endpoints) . Inaktivera alla Dirigerings slut punkter tillfälligt. När din IoT-hubb är ansluten till Time Series Insights, kan du aktivera Dirigerings slut punkter igen.

I IoT-hubben skapar du en unik konsument grupp för Time Series Insights att använda. I följande exempel ersätter `my-pnp-hub` du med namnet på den IoT-hubb som du använde tidigare.

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group
```

## <a name="choose-a-time-series-id"></a>Välj ett Time Series-ID

När du etablerar din Time Series Insights-miljö måste du välja ett *Time Series-ID*. Det är viktigt att välja rätt tids serie-ID. Den här egenskapen är oföränderlig och kan inte ändras efter att den har angetts. Ett Time Series-ID är som en databas partitions nyckel. Time Series-ID fungerar som primär nyckel för din tids serie modell. Mer information finns i [metod tips för att välja ett Time Series-ID](../time-series-insights/how-to-select-tsid.md).

Som IoT Plug and Play användare för tids seriens ID anger du en _sammansatt nyckel_ som består av `iothub-connection-device-id` och `dt-subject` . IoT Hub lägger till dessa system egenskaper som innehåller IoT Plug and Play enhets-ID och enhets komponent namnen.

Även om din IoT Plug and Play enhets modeller för närvarande inte använder komponenter, bör du inkludera `dt-subject` som en del av en sammansatt nyckel så att du kan använda komponenterna i framtiden. Eftersom din Time Series-ID är oföränderlig rekommenderar Microsoft att du aktiverar det här alternativet om du behöver det i framtiden.

> [!NOTE]
> Exemplen i den här artikeln gäller för enheten med flera komponenter `TemperatureController` . Men begreppen är desamma för enheten nr-komponent `Thermostat` .

## <a name="provision-your-time-series-insights-environment"></a>Etablera din Time Series Insightss miljö

I det här avsnittet beskrivs hur du etablerar din Azure Time Series Insights Gen2-miljö.

Kör följande kommando för att:

* Skapa ett Azure Storage-konto för din miljös [kall Arkiv](../time-series-insights/concepts-storage.md#cold-store). Det här kontot är utformat för långsiktig kvarhållning och analys för historiska data.
  * Ersätt `mytsicoldstore` med ett unikt namn för ditt kalla lagrings konto i din kod.
* Skapa en Azure Time Series Insights Gen2-miljö. Miljön kommer att skapas med varm lagring som har en kvarhållningsperiod på sju dagar. Det kalla lagrings kontot kommer att bifogas för oändlig kvarhållning.
  * Ersätt `my-tsi-env` med ett unikt namn för din Time Series Insights miljö i din kod.
  * I din kod ersätter `my-pnp-resourcegroup` du med namnet på den resurs grupp som du använde under installationen.
  * Din Time Series ID-egenskap är `iothub-connection-device-id, dt-subject` .

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az tsi environment gen2 create --name "my-tsi-env" --location eastus2 --resource-group $rg --sku name="L1" capacity=1 --time-series-id-properties name=iothub-connection-device-id type=String --time-series-id-properties name=dt-subject type=String --warm-store-configuration data-retention=P7D --storage-configuration account-name=$storage management-key=$key
```

Anslut din IoT Hub händelse källa. Ersätt `my-pnp-resourcegroup` , `my-pnp-hub` och `my-tsi-env` med de värden som du har valt. Följande kommando refererar till konsument gruppen för Time Series Insights som du skapade tidigare:

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az tsi event-source iothub create --event-source-name iot-hub-event-source --environment-name $env --resource-group $rg --location eastus2 --consumer-group-name tsi-consumer-group --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id --iot-hub-name $iothub
```

I [Azure Portal](https://portal.azure.com)går du till din resurs grupp och väljer sedan den nya Time Series Insightss miljön. Gå till **URL: en för Time Series Insights Explorer** som visas i instans översikten:

![Skärm bild av sidan med Portal översikt.](./media/tutorial-configure-tsi/view-environment.png)

I Utforskaren ser du tre instanser:

* &lt;ditt PnP-enhets-ID &gt; , thermostat1
* &lt;ditt PnP-enhets-ID &gt; , thermostat2
* &lt;ditt PnP-enhets-ID &gt; , `null`

> [!NOTE]
> Den tredje taggen representerar telemetri från `TemperatureController` sig själv, till exempel arbets minnet för enhets minnet. Eftersom det här är en egenskap på den översta nivån är värdet för komponent namnet null. I ett senare steg kan du göra det här namnet mer användarvänligt.

![Skärm bild som visar tre instanser i Utforskaren.](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="configure-model-translation"></a>Konfigurera modell Översättning

Sedan översätter du din DTDL-enhets modell till till gångs modellen i Azure Time Series Insights. I Time Series Insights är Time Series-modellen ett semantiskt modellerings verktyg för data contextualization. Modellen har tre huvud komponenter:

* [Tids serie modell instanser](../time-series-insights/concepts-model-overview.md#time-series-model-instances) är virtuella representationer av själva tids serien. Instanser identifieras unikt av ditt Time Series ID.
* [Tids serie modell hierarkier](../time-series-insights/concepts-model-overview.md#time-series-model-hierarchies) organiserar instanser genom att ange egenskaps namn och deras relationer.
* Med [modell typer för tids serier](../time-series-insights/concepts-model-overview.md#time-series-model-types) kan du definiera [variabler](../time-series-insights/concepts-variables.md) eller formler för beräkningar. Typer är associerade med en angiven instans.

### <a name="define-your-types"></a>Definiera dina typer

Du kan börja mata in data i Azure Time Series Insights Gen2 utan att ha en fördefinierad modell. När telemetri kommer Time Series Insights försöker automatiskt lösa Time Series-instanser baserat på dina tids serie-ID-egenskaps värden. Alla instanser tilldelas *standard typen*. Du måste skapa en ny typ manuellt för att kunna kategorisera dina instanser. 

Följande information beskriver den enklaste metoden för att synkronisera enhets DTDL modeller med dina tids serie modell typer:

* Den digitala, dubbla modell identifieraren blir ditt typ-ID.
* Typ namnet kan vara antingen modell namnet eller visnings namnet.
* Modell beskrivningen blir typens beskrivning.
* Minst en typ variabel skapas för varje telemetri som har ett numeriskt schema.
  * Endast numeriska data typer kan användas för variabler, men om ett värde skickas som en annan typ som kan konverteras, `"0"` kan du använda en [konverterings](/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) funktion som t `toDouble` . ex..
* Variabel namnet kan antingen vara telemetri-namnet eller visnings namnet.
* När du definierar uttrycks variabeln för tids serier, se telemetrins namn på kabeln och till telemetrins datatyp.

| DTDL-JSON | JSON-typ för tids serie modell | Exempelvärde |
|-----------|------------------|-------------|
| `@id` | `id` | `dtmi:com:example:TemperatureController;1` |
| `displayName`    | `name`   |   `Temperature Controller`  |
| `description`  |  `description`  |  `Device with two thermostats and remote reboot.` |  
|`contents` lagringsmatriser| `variables` jobbobjektet  | Se följande exempel.

![Skärm bild som visar D T D L till Time Series-modell typ.](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

> [!NOTE]
> Det här exemplet visar tre variabler, men varje typ kan ha upp till 100 variabler. Olika variabler kan referera till samma telemetri-värde för att göra olika beräkningar efter behov. En fullständig lista över filter, agg regeringar och skalära funktioner finns [Time Series Insights Gen2 Time Series Expression syntax](/rest/api/time-series-insights/reference-time-series-expression-syntax).

Öppna en text redigerare och spara följande JSON-fil på din lokala enhet.

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:TemperatureController;1",
      "name": "Temperature Controller",
      "description": "Device with two thermostats and remote reboot.",
      "variables": {
        "workingSet": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.workingSet.Long, toLong($event.workingSet.Double))"
          }, 
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.temperature.Long, toLong($event.temperature.Double))"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "eventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        }
      }
    }
  ]
}
```

I Time Series Insights Explorer väljer du modell ikonen till vänster för att öppna fliken **modell** . Välj **typer** och välj sedan **Ladda upp JSON**:

![Skärm bild som visar hur du laddar upp JSON.](./media/tutorial-configure-tsi/upload-type.png)

Välj **Välj fil**, Välj den JSON som du sparade tidigare och välj sedan **Ladda upp**.

Du ser den nyligen definierade typen av **temperatur kontrollant** .

### <a name="create-a-hierarchy"></a>Skapa en hierarki

Skapa en hierarki för att organisera taggarna under deras `TemperatureController` överordnade objekt. Följande enkla exempel har en nivå, men IoT-lösningar har ofta många nivåer av kapsling till sätta-Taggar i deras fysiska och semantiska position inom en organisation.

Välj **hierarkier** och välj sedan **Lägg till hierarki**. Ange *enhets flottan* som namn. Skapa en nivå med namnet *enhets namn*. Välj sedan **Spara**.

![Skärm bild som visar hur du lägger till en hierarki.](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>Tilldela dina instanser till rätt typ

Sedan ändrar du typ av instanser och placerar dem i hierarkin.

Välj fliken **instanser** . Hitta den instans som representerar enhetens arbets minne och välj sedan ikonen **Redigera** längst till höger.

![Skärm bild som visar hur du redigerar en instans.](./media/tutorial-configure-tsi/edit-instance.png)

Öppna den nedrullningsbara menyn **typ** och välj sedan **temperatur styrenhet**. Ange *defaultComponent <your device name>* för att uppdatera namnet på instansen som representerar alla översta taggar som är associerade med din enhet.

![Skärm bild som visar hur du ändrar en instans typ.](./media/tutorial-configure-tsi/change-type.png)

Innan du väljer **Spara** väljer du först fliken **instans fält** och väljer sedan **enhets flottan**. Ange *\<your device name> -Temp-styrenheten* om du vill gruppera Telemetrin tillsammans. Välj sedan **Spara**.

![Skärm bild som visar hur du tilldelar en instans till en hierarki](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Upprepa föregående steg för att tilldela termostat-taggarna rätt typ och hierarki.

## <a name="view-your-data"></a>Visa dina data

Gå tillbaka till diagram fönstret och expandera **enhets flottan** > enheten. Välj **thermostat1**, Välj **temperatur** variabeln och välj sedan **Lägg till** i diagrammet värdet. Gör samma sak för **thermostat2** och **defaultComponent** för det **aktiva** värdet.

![Skärm bild som visar hur du ändrar instans typen för thermostat2.](./media/tutorial-configure-tsi/charting-values.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> Mer information om olika diagram alternativ, inklusive intervall storlek och kontroll av y-axeln, finns i [Azure Time Series Insights Explorer](../time-series-insights/concepts-ux-panels.md).
