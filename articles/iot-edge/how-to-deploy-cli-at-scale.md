---
title: Distribuera moduler i stor skala med Azure CLI – Azure IoT Edge
description: Använd IoT-tillägget för Azure CLI för att skapa automatiska distributioner för grupper IoT Edge enheter
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: c502a9c02160c5a92d78ccdbb0532e6f173122da
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479516"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Distribuera och övervaka IoT Edge moduler i stor skala med hjälp av Azure CLI

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Skapa en **IoT Edge automatisk distribution med** hjälp av Azure-kommandoradsgränssnittet för att hantera pågående distributioner för många enheter samtidigt. Automatiska distributioner för IoT Edge ingår i funktionen [för automatisk enhetshantering](../iot-hub/iot-hub-automatic-device-management.md) i IoT Hub. Distributioner är dynamiska processer som gör att du kan distribuera flera moduler till flera enheter, spåra modulers status och hälsotillstånd och göra ändringar när det behövs.

Mer information finns i Förstå [IoT Edge distributioner för enskilda enheter eller i stor skala.](module-deployment-monitoring.md)

I den här artikeln konfigurerade du Azure CLI och IoT-tillägget. Sedan får du lära dig hur du distribuerar moduler till en IoT Edge enheter och övervakar förloppet med hjälp av tillgängliga CLI-kommandon.

## <a name="prerequisites"></a>Förutsättningar

* En [IoT-hubb](../iot-hub/iot-hub-create-using-cli.md) i din Azure-prenumeration.
* En eller flera IoT Edge enheter.

  Om du inte har en IoT Edge enhet kan du skapa en på en virtuell Azure-dator. Följ stegen i någon av snabbstartsartiklarna för att [skapa en virtuell Linux-enhet](quickstart-linux.md) eller [Skapa en virtuell Windows-enhet.](quickstart.md)

* [Azure CLI](/cli/azure/install-azure-cli) i din miljö. Din Azure CLI-version måste minst vara 2.0.70 eller senare. Validera med `az --version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack.
* [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurera ett distributionsmanifest

Ett distributionsmanifest är ett JSON-dokument som beskriver vilka moduler som ska distribueras, hur data flödar mellan modulerna och önskade egenskaper för modultvillingarna. Mer information finns i Lär [dig hur du distribuerar moduler och etablerar vägar i IoT Edge](module-composition.md).

Om du vill distribuera moduler med Azure CLI sparar du distributionsmanifestet lokalt som en .txt-fil. Du använder filsökvägen i nästa avsnitt när du kör kommandot för att tillämpa konfigurationen på enheten.

Här är ett grundläggande distributionsmanifest med en modul som exempel:

>[!NOTE]
>Det här exempeldistributionsmanifestet använder schemaversion 1.1 IoT Edge agenten och hubben. Schemaversion 1.1 släpptes tillsammans med IoT Edge version 1.0.10 och aktiverar funktioner som startordning för moduler och vägprioritet.

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

## <a name="layered-deployment"></a>Skiktad distribution

Nivådistributioner är en typ av automatisk distribution som kan staplas ovanpå varandra. Mer information om flernivådistributioner finns i [Förstå IoT Edge distributioner för enskilda enheter eller i stor skala.](module-deployment-monitoring.md)

Du kan skapa och hantera flerlagersdistributioner med Azure CLI på samma sätt som med andra automatiska distributioner, med några få skillnader. När en flerlagersdistribution har skapats fungerar samma Azure CLI för flera distributioner på samma sätt som för alla distributioner. Om du vill skapa en distribution i flera lager lägger du `--layered` till flaggan i kommandot create.

Den andra skillnaden är att distributionsmanifestet byggs. Standard automatisk distribution måste innehålla moduler för systemkörning utöver användarmoduler, men flerlagersdistributioner kan bara innehålla användarmoduler. I stället behöver flera distributioner även en standardautomatisk distribution på en enhet för att tillhandahålla de komponenter som krävs för varje IoT Edge enhet, till exempel modulerna för systemkörning.

Här är ett grundläggande distributionsmanifest med flera lager med en modul som exempel:

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired.modules.SimulatedTemperatureSensor": {
          "settings": {
            "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": ""
          },
          "type": "docker",
          "status": "running",
          "restartPolicy": "always",
          "version": "1.0"
        }
      },
      "$edgeHub": {
        "properties.desired.routes.upstream": "FROM /messages/* INTO $upstream"
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

I föregående exempel visades en distributionsinställning i flera lager `properties.desired` för en modul. Om den här nivådistributionen riktar in sig på en enhet där samma modul redan har tillämpats, skulle den skriva över alla befintliga önskade egenskaper. För att uppdatera önskade egenskaper kan du definiera ett nytt underavsnitt i stället för att skriva över. Exempel:

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Mer information om hur du konfigurerar modultvillingarna i lagerdistributioner finns i [Skiktad distribution](module-deployment-monitoring.md#layered-deployment)

## <a name="identify-devices-using-tags"></a>Identifiera enheter med hjälp av taggar

Innan du kan skapa en distribution måste du kunna ange vilka enheter som du vill påverka. Azure IoT Edge identifierar enheter med hjälp av **taggar i** enhetstvillingen. Varje enhet kan ha flera taggar som du definierar på ett sätt som är logiskt för din lösning. Om du till exempel hanterar ett campus med smarta byggnader kan du lägga till följande taggar till en enhet:

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

Mer information om enhetstvillingarna och taggarna finns [i Förstå och använda enhetstvillingarna i IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Skapa en distribution

Du distribuerar moduler till målenheterna genom att skapa en distribution som består av distributionsmanifestet samt andra parametrar.

Använd kommandot [az iot edge deployment create](/cli/azure/iot/edge/deployment) för att skapa en distribution:

```azurecli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Använd samma kommando med flaggan `--layered` för att skapa en flerlagersdistribution.

Kommandot för att skapa distributionen använder följande parametrar:

* **--layered** – En valfri flagga för att identifiera distributionen som en skiktad distribution.
* **--deployment-id** – Namnet på distributionen som ska skapas i IoT-hubben. Ge distributionen ett unikt namn som är upp till 128 gemener. Undvik blanksteg och följande ogiltiga tecken: `& ^ [ ] { } \ | " < > /` . Obligatorisk parameter.
* **--content** – Filökväg till JSON-distributionsmanifestet. Obligatorisk parameter.
* **--hub-name** – Namnet på den IoT-hubb där distributionen ska skapas. Hubben måste finnas i den aktuella prenumerationen. Ändra din aktuella prenumeration med `az account set -s [subscription name]` kommandot .
* **--labels** – Lägg till etiketter för att spåra dina distributioner. Etiketter är namn, värdepar som beskriver distributionen. Etiketterna använder JSON-formatering för namn och värden. Till exempel `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--target-condition** – Ange ett målvillkor för att avgöra vilka enheter som ska vara mål för den här distributionen. Villkoret baseras på enhetstvillingens taggar eller enhetstvillingens rapporterade egenskaper och ska matcha uttrycksformatet. Till exempel `tags.environment='test' and properties.reported.devicemodel='4000x'`.
* **--priority** – Ett positivt heltal. Om två eller flera distributioner riktas mot samma enhet gäller distributionen med det högsta numeriska värdet för Prioritet.
* **--metrics** – Skapa mått som frågar edgeHub-rapporterade egenskaper för att spåra statusen för en distribution. Mått tar JSON-indata eller en filökväg. Till exempel `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`.

Information om hur du övervakar en distribution med Hjälp av Azure CLI [finns i övervaka IoT Edge distributioner.](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli)

## <a name="modify-a-deployment"></a>Ändra en distribution

När du ändrar en distribution replikeras ändringarna omedelbart till alla målenheter.

Om du uppdaterar målvillkoret sker följande uppdateringar:

* Om en enhet inte uppfyller det gamla målvillkoret, men uppfyller det nya målvillkoret och distributionen är den högsta prioriteten för enheten, tillämpas den här distributionen på enheten.
* Om en enhet som för närvarande kör den här distributionen inte längre uppfyller målvillkoret avinstallerar den här distributionen och använder den näst högsta prioritetsdistributionen.
* Om en enhet som för närvarande kör den här distributionen inte längre uppfyller målvillkoret och inte uppfyller målvillkoret för andra distributioner sker ingen ändring på enheten. Enheten fortsätter att köra sina aktuella moduler i det aktuella tillståndet, men hanteras inte längre som en del av den här distributionen. När den uppfyller målvillkoret för en annan distribution avinstalleras den här distributionen och den nya.

Du kan inte uppdatera innehållet i en distribution, som innehåller de moduler och vägar som definierats i distributionsmanifestet. Om du vill uppdatera innehållet i en distribution gör du det genom att skapa en ny distribution som riktar sig till samma enheter med högre prioritet. Du kan ändra vissa egenskaper för en befintlig modul, inklusive målvillkor, etiketter, mått och prioritet.

Använd kommandot [az iot edge deployment update](/cli/azure/iot/edge/deployment) för att uppdatera en distribution:

```azurecli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

Distributionsuppdateringskommandot använder följande parametrar:

* **--deployment-id** – Namnet på distributionen som finns i IoT-hubben.
* **--hub-name** – Namnet på den IoT-hubb där distributionen finns. Hubben måste finnas i den aktuella prenumerationen. Växla till önskad prenumeration med kommandot `az account set -s [subscription name]`
* **--set** – Uppdatera en egenskap i distributionen. Du kan uppdatera följande egenskaper:
  * targetCondition – till exempel `targetCondition=tags.location.state='Oregon'`
  * Etiketter
  * prioritet
* **--add** – Lägg till en ny egenskap i distributionen, inklusive målvillkor eller etiketter.
* **--remove** – Ta bort en befintlig egenskap, inklusive målvillkor eller etiketter.

## <a name="delete-a-deployment"></a>Ta bort en distribution

När du tar bort en distribution får alla enheter sin näst högsta prioritet. Om dina enheter inte uppfyller målvillkoret för någon annan distribution tas modulerna inte bort när distributionen tas bort.

Använd kommandot [az iot edge deployment delete för](/cli/azure/iot/edge/deployment) att ta bort en distribution:

```azurecli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

Kommandot för att ta bort distributionen använder följande parametrar:

* **--deployment-id** – Namnet på distributionen som finns i IoT-hubben.
* **--hub-name** – Namnet på den IoT-hubb där distributionen finns. Hubben måste finnas i den aktuella prenumerationen. Växla till önskad prenumeration med kommandot `az account set -s [subscription name]`

## <a name="next-steps"></a>Nästa steg

Läs mer om [att distribuera moduler till IoT Edge enheter](module-deployment-monitoring.md).