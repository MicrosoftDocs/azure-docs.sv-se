---
title: Distribuera moduler i skala med Visual Studio Code-Azure IoT Edge
description: Använd IoT-tillägget för Visual Studio Code för att skapa automatiska distributioner för grupper med IoT Edge enheter.
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a20992ef26b74bcc37a7403e4ee77cacc0f8f66e
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200295"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>Distribuera IoT Edge moduler i skala med Visual Studio Code

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Du kan skapa en **IoT Edge automatisk distribution** med hjälp av Visual Studio Code för att hantera pågående distributioner för många enheter samtidigt. Automatiska distributioner för IoT Edge ingår i funktionen [Automatisk enhets hantering](../iot-hub/iot-hub-automatic-device-management.md) i IoT Hub. Distributioner är dynamiska processer som gör att du kan distribuera flera moduler till flera enheter. Du kan också spåra status och hälsa för modulerna och göra ändringar när det behövs.

Mer information finns i [förstå IoT Edge automatiska distributioner för enskilda enheter eller i skala](module-deployment-monitoring.md).

I den här artikeln ställer du in Visual Studio Code och IoT-tillägget. Du lär dig sedan hur du distribuerar moduler till en uppsättning IoT Edge enheter.

## <a name="prerequisites"></a>Förutsättningar

* En [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i din Azure-prenumeration.
* En eller flera IoT Edge enheter.

  Om du inte har konfigurerat en IoT Edge enhet kan du skapa en på en virtuell Azure-dator. Följ stegen i en av snabb starts artiklarna för att [skapa en virtuell Linux-enhet](quickstart-linux.md) eller [skapa en virtuell Windows-enhet](quickstart.md).

* [Visual Studio Code](https://code.visualstudio.com/).
* [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) för Visual Studio Code.

## <a name="sign-in-to-access-your-iot-hub"></a>Logga in för att få åtkomst till din IoT Hub

Du kan använda Azure IoT-tillägg för Visual Studio Code för att utföra åtgärder med hubben. För att dessa åtgärder ska fungera måste du logga in på ditt Azure-konto och välja den IoT-hubb som du arbetar med.

1. Öppna vyn **Utforskare** i Visual Studio Code.

1. Expandera avsnittet **Azure IoT Hub** längst ned i Utforskaren.

1. Klicka på **...** i avsnitts rubriken för **Azure IoT Hub** . Om du inte ser ellipsen hovrar du över sidhuvudet.

1. Välj **välj IoT Hub**.

1. Om du inte är inloggad på ditt Azure-konto följer du anvisningarna för att göra det.

1. Välj din Azure-prenumeration.

1. Välj din IoT-hubb.

## <a name="configure-a-deployment-manifest"></a>Konfigurera ett distributions manifest

Ett distributions manifest är ett JSON-dokument som beskriver vilka moduler som ska distribueras. Den beskriver också hur data flödar mellan moduler och önskade egenskaper för modulen. Mer information finns i [Lär dig hur du distribuerar moduler och etablerar vägar i IoT Edge](module-composition.md).

Om du vill distribuera moduler med Visual Studio Code sparar du distributions manifestet lokalt som en. JSON-fil. Du måste ange dess plats när du kör kommandot för att tillämpa konfigurationen på enheten.

Här är ett grundläggande distributions manifest med en modul som exempel:

>[!NOTE]
>Det här exempel distributions manifestet använder schema version 1,1 för IoT Edge agent och hubb. Schema version 1,1 släpptes tillsammans med IoT Edge version 1.0.10, och möjliggör funktioner som start ordning och väg prioritering.

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.1",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {}
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.1",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                "createOptions": "{}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

Om du behöver bestämma vilka IoT Edge-enheter som du för närvarande kan konfigurera kör du kommandot **IoT Edge: Hämta enhets information** .

## <a name="identify-devices-with-target-conditions"></a>Identifiera enheter med mål villkor

För att identifiera de IoT Edge enheter som ska ta emot distributionen måste du ange ett mål villkor. Ett mål villkor uppfylls när det angivna villkoret matchas av ett deviceId, ett tagg värde eller ett rapporterat egenskaps värde.

Du konfigurerar Taggar i enheten med dubbla. Här är ett exempel på en enhet som har Taggar:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Den här enheten får en distribution om mål villkoret för distributionen innehåller ett uttryck som matchar en av taggens värden, till exempel `tag.location.building = '20'` .

Om du vill rikta in en speciell enhet oavsett dess taggar eller andra värden anger du bara `deviceId` för mål villkoret.

Här följer några fler exempel:

* deviceId = ' linuxprod1 '
* deviceId = ' linuxprod1 ' eller deviceId = ' linuxprod2 ' eller deviceId = ' linuxprod3 '
* Taggar. miljö = ' Prod '
* Taggar. Environment = ' Prod ' och taggar. location = ' westus2 '
* Taggar. Environment = ' Prod ' eller taggar. location = ' westus2 '
* Taggar. operator = ' John ' och taggar. miljö = ' Prod ' och inte deviceId = ' linuxprod1 '

Se [mål villkor](module-deployment-monitoring.md#target-condition) för mer information. Mer information om enhets sammanflätade och taggar finns i [förstå och använda enhets uppIoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

### <a name="edit-the-device-twin"></a>Redigera enheten med dubbla

Du kan redigera enheten i Visual Studio Code för att konfigurera taggarna. I menyn **Visa** väljer du **kommando-paletten** och kör kommandot **IoT Edge: Edit enhets dubbla** . Välj din IoT Edge-enhet så visas enheten med dubbla.

I det här exemplet har inga Taggar definierats. Ersätt det aktuella tomma avsnittet `"tags": {}` med din egen tag-definition.

```json
{
    "deviceId": "myEdgeDevice",
    "etag": "AAAAAAAAAAE=",
    "deviceEtag": "NTgwMDg5MDAz",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        }
    },
    "capabilities": {
        "iotEdge": true
    },
    "deviceScope": "ms-azure-iot-edge://myEdgeDevice-637131779299315265",
    "tags": {}
}
```

När du har sparat den lokala filen kör du kommandot **IoT Edge: uppdatera enhet dubbla** .

## <a name="create-deployment-at-scale"></a>Skapa distribution i stor skala

När du har konfigurerat distributions manifestet och konfigurerat Taggar i enheten, är du redo att distribuera.

1. I menyn **Visa** väljer du **kommando-paletten** och väljer kommandot **Azure IoT Edge: skapa distribution i skala** .

1. Navigera till den distributions manifest-JSON-fil som du vill använda och klicka på **Välj gräns distributions manifest**.

1. Ange värden som uppmaning, från och med **distributions-ID**.

   ![Ange ett distributions-ID](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   Ange värden för dessa parametrar:

  | Parameter | Beskrivning |
  | --- | --- |
  | Distributions-ID | Namnet på den distribution som ska skapas i IoT Hub. Ge din distribution ett unikt namn som består av upp till 128 små bokstäver. Undvik blank steg och följande ogiltiga tecken: `& ^ [ ] { } \ | " < > /` . |
  | Mål villkor | Ange ett mål villkor för att avgöra vilka enheter som ska vara mål för distributionen. Villkoret baseras på enhetens dubbla taggar eller enhets egenskaper med dubbla rapporter och ska överensstämma med uttrycks formatet. Till exempel `tags.environment='test' and properties.reported.devicemodel='4000x'`. |
  | Prioritet |  Ett positivt heltal. Om två eller flera distributioner är riktade till samma enhet kommer distributionen med det högsta numeriska värdet för prioritet att gälla. |

  När du har angett prioriteten bör terminalen Visa utdata som liknar följande:

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>Övervakning och ändring av distributioner

Använd [Azure Portal](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-in-the-azure-portal) eller [Azure CLI](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli) för att övervaka, ändra och ta bort distributioner. Båda anger måtten för dina distributioner.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [att distribuera moduler till IoT Edge enheter](module-deployment-monitoring.md).