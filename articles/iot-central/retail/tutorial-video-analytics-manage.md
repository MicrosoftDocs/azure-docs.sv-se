---
title: Självstudie – övervaka video med hjälp av program mal len Azure IoT Central Video Analytics – objekt och rörelse identifiering
description: Den här självstudien visar hur du använder instrument panelerna i program mal len video analys – objekt och rörelse identifiering för att hantera dina kameror och övervaka videon.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: fbfef094cd062e437f2a28369162de96631ef41b
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832614"
---
# <a name="tutorial-monitor-and-manage-a-video-analytics---object-and-motion-detection-application"></a>Självstudie: övervaka och hantera ett video analys program för identifiering av objekt och motion

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Lägg till kameror för objekt-och motion-identifiering till ditt IoT Central-program.
> * Hantera dina video strömmar och spela upp dem när intressanta händelser identifieras.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du slutföra:

* Ett tidigare program för att [skapa ett video analys program i azure IoT Central](./tutorial-video-analytics-create-app-yolo-v3.md) eller [skapa en video analys i Azure IoT Central- &trade; ](tutorial-video-analytics-create-app-openvino.md) självstudier.
* En av de tidigare [Create a IoT Edge-instansen för Live Video Analytics (Linux VM)](tutorial-video-analytics-iot-edge-vm.md) eller [skapa en IoT Edge-instans för självstudierna för live video analys (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md) .

Du bör ha [Docker](https://www.docker.com/products/docker-desktop) installerat på den lokala datorn för att köra video visnings programmet.

## <a name="add-an-object-detection-camera"></a>Lägg till en kamera för objekt identifiering

I ditt IoT Central program navigerar du till den **lva Gateway 001** -enhet som du skapade tidigare. Välj sedan fliken **kommandon** .

Använd värdena i följande tabell som parameter värden för kommandot **Lägg till Camera-begäran** . Värdena som visas i tabellen förutsätter att du använder den simulerade kameran i den virtuella Azure-datorn, justera värdena efter behov om du använder en riktig kamera:

| Fält| Beskrivning| Exempelvärde|
|---------|---------|---------|
| Kamera-ID      | Enhets-ID för etablering | kamera-003 |
| Kamera namn    | Eget namn           | Objekt identifierings kamera |
| RTSP-URL       | Adress till data strömmen   | RTSP://10.0.0.4:554/media/camera-300s.mkv|
| RTSP-användarnamn  |                         | användare    |
| RTSP-lösenord  |                         | password    |
| Identifierings typ | Listruta                | Objektidentifiering       |

Välj **Kör** för att lägga till kamera enheten:

:::image type="content" source="media/tutorial-video-analytics-manage/add-camera.png" alt-text="Lägg till kamera":::

> [!NOTE]
> Enhets mal len för **lva Edge objekt detektor** finns redan i programmet.

## <a name="add-a-motion-detection-camera-optional"></a>Lägg till en rörelse identifierings kamera (valfritt)

Om du har två kameror anslutna till din IoT Edge gateway-enhet upprepar du de föregående stegen för att lägga till en kamera för rörelse identifiering i programmet. Använd olika värden för **kamera-ID**, **kamera namn** och parametrar för **RTSP-URL** .

## <a name="view-the-downstream-devices"></a>Visa de efterföljande enheterna

Välj fliken **underordnade enheter** för **lva Gateway 001** -enheten för att se de kamera enheter som du nyss lagt till:

:::image type="content" source="media/tutorial-video-analytics-manage/inspect-downstream.png" alt-text="Allmänt":::

Kamera enheterna visas också i listan på sidan **enheter** i programmet.

## <a name="configure-and-manage-the-camera"></a>Konfigurera och hantera kameran

Navigera till **kameran-003-** enheten och välj fliken **Hantera** .

Använd standardvärdena eller ändra om du behöver anpassa enhets egenskaperna:

**Kamera inställningar**

| Egenskap | Beskrivning | Föreslaget värde |
|-|-|-|
| Video uppspelnings värd | Värd för Azure Media Player Viewer | http://localhost:8094 |

**LVA-åtgärder och diagnostik**

| Egenskap | Beskrivning | Föreslaget värde |
|-|-|-|
| Automatisk start | Starta objekt identifieringen när LVA-gatewayen startar om | Markerad |
| Felsöka telemetri | Händelse spår | Valfritt |
|Timeout för tids gräns (SEK)| Hur lång tid som används för att avgöra om inferences har stoppats | 20 |

**Identifiering av AI-objekt**

| Egenskap | Beskrivning | Föreslaget värde |
|-|-|-|
| Maximal hämtnings tid för härledning (SEK) | Maximal hämtnings tid | 15 |
| Identifierings klasser | Strängar, avgränsade med kommatecken, med identifierings taggarna. Mer information finns i [listan över taggar som stöds](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/yolov3-onnx/tags.txt) | Last bil, buss |
| Konfidens tröskelvärde | Kvalificerings procent för att avgöra om objekt identifieringen är giltig | 70 |
| Samplings frekvens för härlednings ram (FPS) | [Beskrivning här] | 2 |

Välj **Spara**.

Efter några sekunder visas bekräftelse meddelandet som **accepterats** för varje inställning:

:::image type="content" source="media/tutorial-video-analytics-manage/object-detection.png" alt-text="Identifiera objekt":::

## <a name="start-lva-processing"></a>Starta LVA-bearbetning

Navigera till **kameran-003-** enheten och välj fliken **kommandon** .

Kör kommandot **Starta lva bearbetning** .

När kommandot har slutförts, Visa kommando historiken för att se till att det inte finns några fel:

:::image type="content" source="media/tutorial-video-analytics-manage/start-processing.png" alt-text="Starta LVA bearbetar kommando":::

## <a name="monitor-the-cameras"></a>Övervaka kamerorna

Navigera till **kameran-003-** enheten och välj fliken **instrument panel** :

:::image type="content" source="media/tutorial-video-analytics-manage/camera-dashboard.png" alt-text="Instrument panel för kamera":::

Panelen **antal identifieringar** visar det genomsnittliga identifierings antalet för varje markerat objekt för identifierings klasser under ett identifierings intervall på en sekund.

Cirkel diagrammet för **identifierings klasser** visar procent andelen identifieringar för varje klass typ.

**Videon om händelsen** för att visa en video är en lista över länkar till till gångar i Azure Media Services som innehåller identifieringar. Länken använder värd spelaren som beskrivs i följande avsnitt.

## <a name="start-the-streaming-endpoint"></a>Starta slutpunkten för direktuppspelning

Starta Media Services-slutpunkten för att göra det möjligt för ditt klient Media Player program att strömma den inspelade videon:

* I Azure Portal navigerar du till resurs gruppen **lva-RG** .
* Öppna den **strömmande slut punkts** resursen.
* På sidan **information om strömnings slut punkt** väljer du **Start**. Du ser en varning om att faktureringen startar när slut punkten startas.

## <a name="view-stored-video"></a>Visa lagrad video

Dagar för att titta på kameror och reagera på misstänkta bilder är över. Med automatisk händelse märkning och direkta länkar till den lagrade videon med den uppskjutna identifieringen kan säkerhets operatörer hitta händelser av intresse i en lista och sedan följa länken för att visa videon.

Du kan använda [amp Videos Player](https://github.com/Azure/live-video-analytics/tree/master/utilities/amp-viewer) för att visa videon som lagras i ditt Azure Media Services-konto.

IoT Central programmet lagrar videon i Azure Media Services varifrån du kan strömma den. Du behöver en Videos pelare för att spela upp videon som lagras i Azure Media Services.

Se till att **Docker** körs på den lokala datorn.

Öppna en kommando tolk och Använd följande kommando för att köra Videos pelaren i en Docker-behållare på den lokala datorn. Ersätt plats hållarna i kommandot med de värden som du antecknade tidigare i *scratchpad.txt* -filen. Du har gjort en anteckning om `amsAadClientId` ,,, `amsAadSecret` `amsAadTenantId` `amsSubscriptionId` och `amsAccountName` när du skapade tjänstens huvud namn för ditt Media Services-konto:

```bash
docker run -it --rm -e amsAadClientId="<FROM_AZURE_PORTAL>" -e amsAadSecret="<FROM_AZURE_PORTAL>" -e amsAadTenantId="<FROM_AZURE_PORTAL>" -e amsArmAadAudience="https://management.core.windows.net" -e amsArmEndpoint="https://management.azure.com" -e amsAadEndpoint="https://login.microsoftonline.com" -e amsSubscriptionId="<FROM_AZURE_PORTAL>" -e amsResourceGroup="<FROM_AZURE_PORTAL>" -e amsAccountName="<FROM_AZURE_PORTAL>" -p 8094:8094 mcr.microsoft.com/lva-utilities/amp-viewer:1.0-amd64
```

|Docker-parameter | AMS API-åtkomst (JSON)|
|-|-|
|amsAadClientId| AadClientId|
|amsAadSecret| AadSecret |
|amsAadTenantId| AadTenantId |
|amsSubscriptionId| SubscriptionId|
|amsResourceGroup| ResourceGroup |
|amsAccountName| AccountName|

Gå till **kameran-003-** enheten och välj fliken **instrument panel** . Klicka sedan på någon av hyperlänkarna för hämtade objekt identifiering på video panelen för **video händelsen** . Videon visas på en sida som visas av den lokala Videos pelaren:

:::image type="content" source="media/tutorial-video-analytics-manage/video-snippet.png" alt-text="Video kodfragment":::

## <a name="change-the-simulated-devices-in-application-dashboards"></a>Ändra de simulerade enheterna i program instrument paneler

Program instrument paneler fylls i ursprungligen med telemetri och egenskaper som genereras från IoT Central simulerade enheter. Följ dessa steg om du vill konfigurera panelerna till telemetri från riktiga kameror eller Live555-simulatorn:

1. Gå till instrument panelen **(exempel) i det riktiga kamera övervaknings** programmet.
1. Välj **Redigera**.
1. Välj **antecknings** panelen och ta bort den.
1. Ändra instrument panelens rubrik till den *riktiga kamera övervakaren*.
1. Välj ikonen konfigurera på panelen **antal härledningar** .
1. I avsnittet **Konfigurera diagram** väljer du en eller flera riktiga kameror i enhets gruppen **lva Edge objekt detektor** .
1. Välj `AI Inference Interface/Inference Count` fältet telemetri.
1. Välj **Uppdatera**.

1. Upprepa stegen för följande paneler:
    1. Diagram över **identifieringen** använder `AI Inference Interface/Inference/entity/tag/value` typen telemetri.
    1. En **härledning** använder det `AI Inference Interface/Inference/entity/tag/value` senaste kända värdet.
    1. **Konfidens%** använder det `AI Inference Interface/Inference/entity/tag/confidence` senaste kända värdet.
    1. **Ögonblicks bilden** används `AI Inference Interface/Inference Image` som en bild.
    1. **Video om händelsen härledning** används `AI Inference Interface/Inference Event Video` som en länk.
1. Välj **Spara**.

På instrument panelen för den **riktiga kamera övervakaren** visas nu värden från den riktiga kamera enheten:

:::image type="content" source="media/tutorial-video-analytics-manage/update-real-cameras.png" alt-text="Real Cameras program instrument panel":::

## <a name="pause-processing"></a>Pausa bearbetning

Du kan pausa live video analys bearbetningen i programmet:

1. I ditt program går du till **kommando** sidan för objekt identifierings kamera. Kör sedan kommandot **Stop lva bearbetning** .

1. Stoppa slut punkten för direkt uppspelning för ditt Media Services-konto:
    * I Azure Portal navigerar du till resurs gruppen **lva-RG** .
    * Klicka på resursen för **strömnings slut punkt** .
    * På sidan **information om strömnings slut punkt** väljer du **stoppa**.

## <a name="clean-up-resources"></a>Rensa resurser

Om du är klar med programmet kan du ta bort alla resurser som du har skapat på följande sätt:

1. I IoT Central programmet navigerar du till sidan **program** i avsnittet **Administration** . Välj sedan **Ta bort**.
1. Ta bort resurs gruppen **lva-RG** i Azure Portal.
1. På den lokala datorn stoppar du Docker-behållaren för **amp-visningsprogrammet** .

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig hur du lägger till kameror i IoT Central programmet och konfigurerar dem för identifiering av objekt och rörelser.

Lär dig hur du anpassar käll koden för IoT Edge moduler:

> [!div class="nextstepaction"]
> [Ändra och bygg moduler för live video analys-Gateway](./tutorial-video-analytics-build-module.md)
