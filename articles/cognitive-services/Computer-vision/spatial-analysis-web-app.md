---
title: Distribuera en webb program för spatial analys
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder rums analys i ett webb program.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 6d3be90cc81b1bcd9a55fc8e53cb9f2238e8c6de
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285985"
---
# <a name="how-to-deploy-a-people-counting-web-application"></a>Så här distribuerar du en användare som räknar webb program

I den här artikeln får du lära dig hur du integrerar rums analys i en webbapp som förstår rörligheten för människor och övervakar antalet personer som har ett fysiskt utrymme. 

I den här självstudien får du lära dig hur man

* Distribuera behållaren för rums analys
* Konfigurera åtgärden och kameran
* Konfigurera IoT Hub anslutning i webb programmet
* Distribuera och testa webb programmet

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* Grundläggande förståelse för Azure IoT Edge distributions konfiguration och en [Azure-IoT Hub](../../iot-hub/index.yml)
* En konfigurerad [värddator](spatial-analysis-container.md).

## <a name="deploy-the-spatial-analysis-container"></a>Distribuera behållaren för rums analys

Fyll i [programmet för begäran](https://aka.ms/csgate) för att få åtkomst till att köra behållaren. 

Följ [installations programmet för värddatorn](./spatial-analysis-container.md) för att konfigurera värddatorn och ansluta en IoT Edge-enhet till Azure IoT Hub. 

### <a name="deploy-an-azure-iot-hub-service-in-your-subscription"></a>Distribuera en Azure IoT Hub-tjänst i din prenumeration

Börja med att skapa en instans av en Azure IoT Hub-tjänst med antingen standard pris nivån (S1) eller den kostnads fria nivån (S0). Följ dessa instruktioner för att skapa den här instansen med hjälp av Azure CLI.

Fyll i de obligatoriska parametrarna:
* Prenumeration: namnet eller ID: t för din Azure-prenumeration
* Resurs grupp: skapa ett namn för din resurs grupp
* IoT Hub-namn: skapa ett namn för din IoT Hub
* IoTHub namn: namnet på IoT Hub du skapade 
* Gräns enhets namn: skapa ett namn för din Edge-enhet

```azurecli
az login
az account set --subscription <name or ID of Azure Subscription>
az group create --name "<Resource Group Name>" --location "WestUS"

az iot hub create --name "<IoT Hub Name>" --sku S1 --resource-group "test-resource-group"

az iot hub device-identity create --hub-name "<IoT Hub Name>" --device-id "<Edge Device Name>" --edge-enabled
```

### <a name="deploy-the-container-on-azure-iot-edge-on-the-host-computer"></a>Distribuera behållaren på Azure IoT Edge på värddatorn

Distribuera behållaren för rums analys som en IoT-modul på värddatorn med hjälp av Azure CLI. Distributions processen kräver en distributions manifest fil som beskriver de obligatoriska behållarna, variablerna och konfigurationerna för distributionen. Du hittar ett Azure Stack exempel på ett särskilt distributions [manifest](https://go.microsoft.com/fwlink/?linkid=2142179), ett [icke Azure Stack-](https://go.microsoft.com/fwlink/?linkid=2152189)särskilt distributions manifest och en [virtuell Azure-dator med GPU](https://go.microsoft.com/fwlink/?linkid=2152189) -GitHub, som innehåller en grundläggande distributions konfiguration för behållaren för *spatial analys* . 

Du kan också använda Azure IoT-tillägg för Visual Studio Code för att utföra åtgärder med IoT Hub. Gå till [distribuera Azure IoT Edge moduler från Visual Studio Code](../../iot-edge/how-to-deploy-modules-vscode.md) för mer information.

> [!NOTE] 
> Behållarna *spatial-analys-teleympkvistar* och *spatial-analys – diagnostik* är valfria. Du kan välja att ta bort dem från *DeploymentManifest.jsi* filen. Mer information finns i artikeln om [telemetri och fel sökning](./spatial-analysis-logging.md) . Du kan hitta tre exempel *DeploymentManifest.jspå* filer på GitHub, för [Azure Stack gräns enheter](https://go.microsoft.com/fwlink/?linkid=2142179), en [stationär dator](https://go.microsoft.com/fwlink/?linkid=2152189)eller en [virtuell Azure-dator med GPU](https://go.microsoft.com/fwlink/?linkid=2152189)

### <a name="set-environment-variables"></a>Ange miljövariabler

De flesta **miljövariabler** för IoT Edge-modulen har redan ställts in i exemplet *DeploymentManifest.jspå* filer som är länkade ovan. I filen söker du efter `BILLING_ENDPOINT` `API_KEY` variablerna och för miljön, som visas nedan. Ersätt värdena med slut punkts-URI: n och den API-nyckel som du skapade tidigare. Se till att EULA-värdet är inställt på acceptera. 

```json
"EULA": { 
    "value": "accept"
},

"BILLING_ENDPOINT":{ 
    "value": "<Use a key from your Computer Vision resource>"
},
"API_KEY":{
    "value": "<Use the endpoint from your Computer Vision resource>"
}
```

### <a name="configure-the-operation-parameters"></a>Konfigurera åtgärds parametrar

Nu när den inledande konfigurationen av behållaren för *spatial analys* är klar, är nästa steg att konfigurera parametrarna för drift och lägga till dem i distributionen. 

Det första steget är att uppdatera det exempel distributions manifest som är länkat ovan och konfigurera operationId `cognitiveservices.vision.spatialanalysis-personcount` så som visas nedan:


```json
"personcount": {
    "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "<Replace RTSP URL here>",
        "VIDEO_SOURCE_ID": "<Replace with friendly name>",
        "VIDEO_IS_LIVE":true,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0 }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[<Replace with your values>], \"events\": [{\"type\":\"count\"}], \"threshold\":<use 0 for no threshold.}]}"
    }
},
```

När distributions manifestet har uppdaterats följer du kamera tillverkarens instruktioner för att installera kameran, konfigurerar kamerans URL och konfigurerar användar namn och lösen ord. 

Ange sedan `VIDEO_URL` till RTSP-URL: en för kameran och autentiseringsuppgifterna för att ansluta till kameran.

Om gräns enheten har fler än en GPU väljer du den GPU som åtgärden ska köras på. Se till att belastningsutjämna de åtgärder där det inte finns fler än 8 åtgärder som körs på en enda GPU i taget.  

Konfigurera sedan den zon där du vill räkna antalet personer. Om du vill konfigurera zonens polygon måste du först följa tillverkarens instruktioner för att hämta en ram från kameran. Om du vill fastställa varje hörn av polygonen markerar du en punkt i ramen, tar koordinaterna x, y-pixlar för punkten i förhållande till det vänstra, övre hörnet i ramen och dividerar med motsvarande ram dimensioner. Ange resultatet som x, y-koordinaterna för hörnen. Du kan ange zon polygonens konfiguration i `SPACEANALYTICS_CONFIG` fältet.

Det här är ett exempel på en video RAM som visar hur hörn koordinaterna beräknas för en ram med storleken 1920/1080.
![Exempel på video RAM](./media/spatial-analysis/sample-video-frame.jpg)

Du kan också välja ett konfidens tröskelvärde för när identifierade personer räknas och händelser genereras. Ange tröskelvärdet till 0 om du vill att alla händelser ska skrivas ut.

### <a name="execute-the-deployment"></a>Kör distributionen

Nu när distributions manifestet är klart använder du det här kommandot i Azure CLI för att distribuera behållaren på värddatorn som en IoT Edge modul.

```azurecli
az login
az extension add --name azure-iot
az iot edge set-modules --hub-name "<IoT Hub name>" --device-id "<IoT Edge device name>" --content DeploymentManifest.json -–subscription "<subscriptionId>"
```

Fyll i de obligatoriska parametrarna:

* IoT Hub namn: namnet på din Azure-IoT Hub
* DeploymentManifest.jspå: namnet på din distributions fil
* IoT Edge enhets namn: IoT Edge enhets namnet på värddatorn
* Prenumeration: ditt prenumerations-ID eller namn

Det här kommandot startar distributionen och du kan visa distributions statusen i Azure IoT Hub-instansen i Azure Portal. Statusen kan visas som *417 – enhetens distributions konfiguration har inte angetts* förrän enheten har hämtat behållar avbildningarna och börjar köras.

### <a name="validate-that-the-deployment-was-successful"></a>Verifiera att distributionen lyckades

Leta upp *körnings statusen* i inställningarna för IoT Edge modulen för modulen för spatial analys i IoT Hub-instansen på Azure Portal. Det **önskade värdet** och det **rapporterade värdet** för *körnings statusen* bör stå `Running` . Se nedan för att det ska se ut på Azure Portal.

![Exempel på distributions verifiering](./media/spatial-analysis/deployment-verification.png)

I det här läget Kör behållaren för spatial analys åtgärden. Den genererar AI-insikter för `cognitiveservices.vision.spatialanalysis-personcount` åtgärden och dirigerar dessa insikter som telemetri till Azure IoT Hub-instansen. Om du vill konfigurera ytterligare kameror kan du uppdatera distributions manifest filen och köra distributionen igen.

## <a name="person-counting-web-application"></a>Webb program för person inventering

Med den här webb program inventeringen kan du snabbt konfigurera ett exempel på en webbapp och vara värd för den i din Azure-miljö.

### <a name="get-the-person-counting-app-container"></a>Hämta app-containern för person inventering

Ett behållar formulär för den här appen finns på Azure Container Registry. Använd följande Docker pull-kommando för att ladda ned det. Kontakta Microsoft på projectarchon@microsoft.com för åtkomst-token.

```bash
docker login rtvsofficial.azurecr.io -u <token name> -p <password>
docker pull rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0
```

Skicka behållaren till din Azure Container Registry (ACR).

```bash
az acr login --name <your ACR name>

docker tag rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0 [desired local image name]

docker push [desired local image name]
```

Om du vill installera behållaren skapar du en ny Azure App Service och fyller i de obligatoriska parametrarna. Gå sedan till fliken **Docker** och välj **en behållare** och **Azure Container Registry**. Använd din instans av Azure Container Registry där du skickade avbildningen ovan.

![Ange bild information](./media/spatial-analysis/solution-app-create-screen.png)

När du har angett ovanstående parametrar klickar du på **Granska + skapa** och skapar appen.

### <a name="configure-the-app"></a>Konfigurera appen 

Vänta tills installationen är klar och navigera till din resurs i Azure Portal. Gå till **konfigurations** avsnittet och Lägg till följande två **program inställningar**.

* `EventHubConsumerGroup` – Namnet på konsument gruppen från Azure IoT Hub kan du skapa en ny konsument grupp i IoT Hub eller använda standard gruppen. 
* `IotHubConnectionString` – Anslutnings strängen till Azure-IoT Hub kan hämtas från avsnittet nycklar i Azure IoT Hub resurs ![ Konfigurera parametrar](./media/spatial-analysis/solution-app-config-page.png)

När de här två inställningarna har lagts till klickar du på **Spara**. Klicka sedan på **autentisering/auktorisering** i den vänstra navigerings menyn och uppdatera den med önskad autentiseringsnivå. Vi rekommenderar Azure Active Directory (Azure AD) Express. 

### <a name="test-the-app"></a>Testa appen

Gå till Azure-tjänsten och kontrol lera att distributionen lyckades och att webb programmet körs. Navigera till den konfigurerade URL: en `<yourapp>.azurewebsites.net` för att Visa appen som körs.

![Testa distributionen](./media/spatial-analysis/solution-app-output.png)

## <a name="get-the-personcount-source-code"></a>Hämta käll koden för PersonCount
Om du vill visa eller ändra käll koden för det här programmet kan du hitta den [på GitHub](https://github.com/Azure-Samples/cognitive-services-spatial-analysis).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera åtgärder för rums analys](./spatial-analysis-operations.md)
* [Loggning och felsökning](spatial-analysis-logging.md)
* [Guide för kamera placering](spatial-analysis-camera-placement.md)
* [Guide för zon-och linje placering](spatial-analysis-zone-line-placement.md)
