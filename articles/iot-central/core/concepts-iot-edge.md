---
title: Azure IoT Edge och Azure IoT Central | Microsoft Docs
description: Förstå hur du använder Azure IoT Edge med ett IoT Central-program.
author: dominicbetts
ms.author: dobett
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
- iot-edge
ms.openlocfilehash: e0f3464420c5cb429f780999bf5983b2ab142567
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102608639"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Ansluta Azure IoT Edge-enheter till ett Azure IoT Central-program

*Den här artikeln gäller lösnings byggare och enhets utvecklare.*

Azure IoT Edge flyttar Cloud Analytics och anpassad affärs logik till enheter så att din organisation kan fokusera på affärs insikter i stället för data hantering. Skala ut din IoT-lösning genom att paketera affärs logiken i standard behållare, distribuera dessa behållare till dina enheter och övervaka dem från molnet.

I den här artikeln beskrivs:

* Hur IoT Edge enheter ansluter till ett IoT Central-program.
* Använda IoT Central för att hantera dina IoT Edge-enheter.

Mer information om IoT Edge finns i [Vad är Azure IoT Edge?](../../iot-edge/about-iot-edge.md)

## <a name="iot-edge"></a>IoT Edge

IoT Edge består av tre komponenter:

* *IoT Edge moduler* är behållare som kör Azure-tjänster, partner tjänster eller din egen kod. Moduler distribueras till IoT Edge enheter och körs lokalt på dessa enheter. Mer information finns i [förstå Azure IoT Edge moduler](../../iot-edge/iot-edge-modules.md).
* *IoT Edge runtime* körs på varje IoT Edge enhet och hanterar de moduler som distribueras till varje enhet. Körningen består av två IoT Edge moduler: *IoT Edge agent* och *IoT Edge hubb*. Mer information finns i [förstå Azure IoT Edge Runtime och dess arkitektur](../../iot-edge/iot-edge-runtime.md).
* Med ett *moln baserat gränssnitt* kan du fjärrövervaka och hantera IoT Edge enheter. IoT Central är ett exempel på ett moln gränssnitt.

En IoT Edge enhet kan vara:

* En fristående enhet som består av moduler.
* En *gateway-enhet*, med underordnade enheter som ansluter till den.

## <a name="iot-edge-as-a-gateway"></a>IoT Edge som en gateway

En IoT Edge enhet kan användas som en gateway som ger en anslutning mellan andra efterföljande enheter i nätverket och ditt IoT Central-program.

Det finns två Gateway-mönster:

* I det *transparenta Gateway* -mönstret fungerar IoT Edge Hub-modulen som IoT Central och hanterar anslutningar från enheter som är registrerade i IoT Central. Meddelanden skickas från efterföljande enheter till IoT Central som om det inte finns någon gateway mellan dem.

* I mönstret för *översättnings-Gateway* , enheter som inte kan ansluta till IoT Central på egen hand, ansluter du till en anpassad IoT Edge modul i stället. Modulen i IoT Edge-enheten bearbetar inkommande meddelanden om underordnade enheter och vidarebefordrar dem sedan till IoT Central.

Mönstren för transparent och översättning av gatewayen är inte ömsesidigt uteslutande. En enda IoT Edge enhet kan fungera både som en transparent gateway och en översättnings-Gateway.

Mer information om mönstren för IoT Edge Gateway finns i [så här kan en IoT Edge enhet användas som en gateway](../../iot-edge/iot-edge-as-gateway.md).

### <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Underordnade enhets relationer med en gateway och moduler

Underordnade enheter kan ansluta till en IoT Edge gateway-enhet via modulen *IoT Edge Hub*  . I det här scenariot är IoT Edge enheten en transparent Gateway:

:::image type="content" source="media/concepts-iot-edge/gateway-transparent.png" alt-text="Diagram över transparent Gateway" border="false":::

Underordnade enheter kan också ansluta till en IoT Edge gateway-enhet via en anpassad modul. I följande scenario ansluter underordnade enheter till en anpassad *Modbus* -modul. I det här scenariot är IoT Edge-enheten en översättnings-Gateway:

:::image type="content" source="media/concepts-iot-edge/gateway-module.png" alt-text="Diagram över anslutning till anpassad modul" border="false":::

Följande diagram visar anslutningar till en IoT Edge gateway-enhet via båda typerna av moduler. I det här scenariot är den IoT Edge enheten både transparent och översättnings-Gateway:

:::image type="content" source="media/concepts-iot-edge/gateway-module-transparent.png" alt-text="Diagram över anslutning med hjälp av båda nätmodulerna" border="false":::

Underordnade enheter kan ansluta till en IoT Edge gateway-enhet via flera anpassade moduler. Följande diagram visar underordnade enheter som ansluter via en anpassad modul för Modbus, en anpassad modul för en tabell och modulen *IoT Edge Hub*  :

:::image type="content" source="media/concepts-iot-edge/gateway-two-modules-transparent.png" alt-text="Diagram över anslutning med hjälp av flera anpassade moduler" border="false":::

## <a name="iot-edge-devices-and-iot-central"></a>IoT Edge enheter och IoT Central

IoT Edge enheter kan använda signatur-token för *delad åtkomst* eller X. 509-certifikat för att autentisera med IoT Central. Du kan registrera dina IoT Edge enheter manuellt i IoT Central innan de ansluter för första gången eller använda enhets etablerings tjänsten för att hantera registreringen. Mer information finns i Anslut [till Azure IoT Central](concepts-get-connected.md).

IoT Central använder [enhets mallar](concepts-device-templates.md) för att definiera hur IoT Central interagerar med en enhet. En enhets mal len anger till exempel:

* Typerna av telemetri och egenskaper som en enhet skickar så att IoT Central kan tolka dem och skapa visualiseringar.
* De kommandon som en enhet svarar på så att IoT Central kan visa ett användar gränssnitt för en operator som används för att anropa kommandona.

En IoT Edge enhet kan skicka telemetri, synkronisera egenskaps värden och svara på kommandon på samma sätt som en standard enhet. En IoT Edge enhet behöver därför en enhets mal len i IoT Central.

### <a name="iot-edge-device-templates"></a>IoT Edge mallar för enheter

IoT Central Device templates använder modeller för att beskriva enhetens funktioner. Följande diagram visar modell strukturen för en IoT Edge enhet:

:::image type="content" source="media/concepts-iot-edge/iot-edge-model.png" alt-text="Modell struktur för IoT Edge enhet som är ansluten till IoT Central" border="false":::

IoT Central modeller en IoT Edge-enhet på följande sätt:

* Varje IoT Edge enhets mal len har en kapacitets modell.
* För varje anpassad modul som anges i distributions manifestet genereras en moduls funktions modell.
* En relation upprättas mellan varje moduls funktions modell och en enhets modell.
* En modul kapacitets modell implementerar ett eller flera modul gränssnitt.
* Varje modul gränssnitt innehåller telemetri, egenskaper och kommandon.

### <a name="iot-edge-deployment-manifests-and-iot-central-device-templates"></a>IoT Edge distributions manifest och IoT Central mallar för enheter

I IoT Edge distribuerar och hanterar du affärs logik i form av moduler. IoT Edge moduler är den minsta beräknings enheten som hanteras av IoT Edge och kan innehålla Azure-tjänster som Azure Stream Analytics eller din egen lösnings kod.

I ett IoT Edge *distributions manifest* anges de IoT Edge moduler som ska distribueras på enheten och hur de ska konfigureras. Mer information finns i [Lär dig hur du distribuerar moduler och etablerar vägar i IoT Edge](../../iot-edge/module-composition.md).

I Azure IoT Central importerar du ett distributions manifest för att skapa en enhets mall för IoT Edge enheten.

Följande kodfragment visar ett exempel på IoT Edge distributions manifest:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
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
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
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
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10
      }
    }
  }
}
```

I föregående kodfragment kan du se:

* Det finns tre moduler. De *IoT Edge agent* -och *IoT Edge Hub* -systemmoduler som finns i varje distributions manifest. Den anpassade **SimulatedTemperatureSensor** -modulen.
* Bilderna för den offentliga modulen hämtas från en Azure Container Registry-lagringsplats som inte kräver några autentiseringsuppgifter för att ansluta. För bilder med privat modul anger du de autentiseringsuppgifter för behållar registret som ska användas i `registryCredentials` inställningen för modulen *IoT Edge agent* .
* Den anpassade **SimulatedTemperatureSensor** -modulen har två egenskaper `"SendData": true` och `"SendInterval": 10` .

När du importerar det här distributions manifestet till ett IoT Central-program genererar den följande enhets mal len:

:::image type="content" source="media/concepts-iot-edge/device-template.png" alt-text="Skärm bild som visar enhets mal len som skapades från distributions manifestet.":::

I föregående skärm bild kan du se:

* En modul med namnet **SimulatedTemperatureSensor**. System modulerna *IoT Edge agent* och *IoT Edge Hub* visas inte i mallen.
* Ett gränssnitt som kallas **hantering** som innehåller två skrivbara egenskaper som kallas **SendData** och **SendInterval**.

Distributions manifestet innehåller inte information om telemetri som **SimulatedTemperatureSensor** -modulen skickar eller de kommandon som den svarar på. Lägg till dessa definitioner i enhets mal len manuellt innan du publicerar den.

Mer information finns i [Självstudier: Lägg till en Azure IoT Edge-enhet till ditt Azure IoT Central-program](tutorial-add-edge-as-leaf-device.md).

### <a name="update-a-deployment-manifest"></a>Uppdatera ett distributions manifest

Om du skapar en ny [version](howto-version-device-template.md) av enhets mal len kan du ersätta distributions manifestet med en ny version:

När du ersätter distributions manifestet hämtar alla anslutna IoT Edge enheter det nya manifestet och uppdaterar deras moduler. IoT Central uppdaterar dock inte gränssnitten i enhets mal len med ändringar i modulens konfiguration. Om du till exempel ersätter manifestet som visas i föregående kodfragment med följande manifest, ser du inte automatiskt egenskapen **SendUnits** i **hanterings** gränssnittet i enhets mal len. Lägg till den nya egenskapen i **hanterings** gränssnittet för IoT Central manuellt för att identifiera den:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
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
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
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
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10,
           "SendUnits": "Celsius"
      }
    }
  }
}
```

## <a name="deploy-the-iot-edge-runtime"></a>Distribuera IoT Edge runtime

Information om var du kan köra IoT Edge runtime finns i [Azure IoT Edge system som stöds](../../iot-edge/support.md).

Du kan också installera IoT Edge runtime i följande miljöer:

* [Installera eller avinstallera Azure IoT Edge för Linux](../../iot-edge/how-to-install-iot-edge.md)
* [Installera och etablera Azure IoT Edge for Linux på en Windows-enhet (förhandsversion)](../../iot-edge/how-to-install-iot-edge-on-windows.md)
* [Köra Azure IoT Edge på Ubuntu Virtual Machines i Azure](../../iot-edge/how-to-install-iot-edge-ubuntuvm.md)

## <a name="iot-edge-gateway-devices"></a>IoT Edge gateway-enheter

Om du har valt en IoT Edge enhet som en gateway-enhet kan du lägga till underordnade relationer till enhets modeller för enheter som du vill ansluta till gateway-enheten.

Mer information finns i [så här ansluter du enheter via en IoT Edge transparent Gateway](how-to-connect-iot-edge-transparent-gateway.md).

## <a name="next-steps"></a>Nästa steg

Om du är en enhets utvecklare är ett förslag till nästa steg att lära dig hur du [utvecklar dina egna IoT Edge-moduler](../../iot-edge/module-development.md).
