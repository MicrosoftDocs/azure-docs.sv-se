---
title: Distribuera moduler i skala med Azure CLI – Azure IoT Edge
description: Använd IoT-tillägget för Azure CLI för att skapa automatiska distributioner för grupper med IoT Edge enheter
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 4ecb1c3dc0e72523b19e3183e17306774b3ce164
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370384"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Distribuera och övervaka IoT Edge moduler i skala med hjälp av Azure CLI

Skapa en **IoT Edge automatisk distribution** med hjälp av Azures kommando rads gränssnitt för att hantera pågående distributioner för många enheter samtidigt. Automatiska distributioner för IoT Edge ingår i funktionen [Automatisk enhets hantering](../iot-hub/iot-hub-automatic-device-management.md) i IoT Hub. Distributioner är dynamiska processer som gör att du kan distribuera flera moduler till flera enheter, spåra status och hälsa för modulerna och göra ändringar när det behövs.

Mer information finns i [förstå IoT Edge automatiska distributioner för enskilda enheter eller i skala](module-deployment-monitoring.md).

I den här artikeln ställer du in Azure CLI och IoT-tillägget. Du lär dig sedan att distribuera moduler till en uppsättning IoT Edge enheter och övervaka förloppet med hjälp av tillgängliga CLI-kommandon.

## <a name="prerequisites"></a>Förutsättningar

* En [IoT-hubb](../iot-hub/iot-hub-create-using-cli.md) i din Azure-prenumeration.
* En eller flera IoT Edge enheter.

  Om du inte har konfigurerat en IoT Edge enhet kan du skapa en på en virtuell Azure-dator. Följ stegen i en av snabb starts artiklarna för att [skapa en virtuell Linux-enhet](quickstart-linux.md) eller [skapa en virtuell Windows-enhet](quickstart.md).

* [Azure CLI](/cli/azure/install-azure-cli) i din miljö. Som minst måste din Azure CLI-version vara 2.0.70 eller högre. Validera med `az --version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack.
* [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurera ett distributions manifest

Ett distributions manifest är ett JSON-dokument som beskriver vilka moduler som ska distribueras, hur data flödar mellan moduler och önskade egenskaper för modulen. Mer information finns i [Lär dig hur du distribuerar moduler och etablerar vägar i IoT Edge](module-composition.md).

Om du vill distribuera moduler med Azure CLI sparar du distributions manifestet lokalt som en txt-fil. Du använder fil Sök vägen i nästa avsnitt när du kör kommandot för att tillämpa konfigurationen på enheten.

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

## <a name="layered-deployment"></a>Lager distribution

Lager distributioner är en typ av automatisk distribution som kan staplas ovanpå varandra. Mer information om lager distributioner finns i [förstå IoT Edge automatiska distributioner för enskilda enheter eller i skala](module-deployment-monitoring.md).

Lager distributioner kan skapas och hanteras med Azure CLI som en automatisk distribution, med bara några få skillnader. När en lager distribution har skapats fungerar samma Azure CLI för lager distributioner på samma sätt som alla distributioner. Om du vill skapa en lager distribution lägger du till `--layered` flaggan i kommandot CREATE.

Den andra skillnaden är att distributions manifestet byggs. Även om standard automatisk distribution måste innehålla systemets körnings moduler, förutom alla användare, kan lager distributioner bara innehålla användarattribut. I stället behöver lager distributioner en standard automatisk distribution på en enhet, för att tillhandahålla nödvändiga komponenter för varje IoT Edge enhet, t. ex. system runtime-moduler.

Här är ett grundläggande distributions manifest med lager med en modul som exempel:

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

I föregående exempel visades en lager distributions inställning `properties.desired` för en modul. Om den här skiktade distributionen är riktad mot en enhet där samma modul redan tillämpades, skulle den skriva över alla befintliga önskade egenskaper. För att kunna uppdatera, i stället för att skriva över, önskade egenskaper, kan du definiera ett nytt underavsnitt. Exempel:

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Mer information om hur du konfigurerar modul dubbla i lager distributioner finns i [lager distribution](module-deployment-monitoring.md#layered-deployment)

## <a name="identify-devices-using-tags"></a>Identifiera enheter som använder Taggar

Innan du kan skapa en distribution måste du kunna ange vilka enheter som du vill påverka. Azure IoT Edge identifierar enheter med hjälp av **taggar** i enheten med dubbla. Varje enhet kan ha flera taggar som du definierar på ett sätt som passar din lösning. Om du till exempel hanterar ett campus-område med smarta byggnader kan du lägga till följande taggar till en enhet:

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

Mer information om enhets sammanflätade och taggar finns i [förstå och använda enhets uppIoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Skapa en distribution

Du distribuerar moduler till mål enheterna genom att skapa en distribution som består av distributions manifestet och andra parametrar.

Använd kommandot [AZ IoT Edge Deployment Create](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-create) för att skapa en distribution:

```azurecli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Använd samma kommando med `--layered` flaggan för att skapa en lager distribution.

Kommandot för att skapa distribution tar följande parametrar:

* **--skiktad** – en valfri flagga för att identifiera distributionen som en lager distribution.
* **--Deployment-ID** – namnet på den distribution som ska skapas i IoT Hub. Ge din distribution ett unikt namn som består av upp till 128 små bokstäver. Undvik blank steg och följande ogiltiga tecken: `& ^ [ ] { } \ | " < > /` . Obligatorisk parameter.
* **--innehålls** -sökväg till distributions manifest-JSON. Obligatorisk parameter.
* **--hubb-Name** -namnet på den IoT-hubb som distributionen ska skapas i. Navet måste finnas i den aktuella prenumerationen. Ändra den aktuella prenumerationen med `az account set -s [subscription name]` kommandot.
* **--Etiketter** – Lägg till etiketter som hjälper dig att spåra dina distributioner. Etiketter är namn, värdepar som beskriver din distribution. Etiketter tar JSON-formatering för namn och värden. Till exempel `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--mål villkor** – ange ett mål villkor för att avgöra vilka enheter som ska vara mål för distributionen. Villkoret baseras på enhetens dubbla taggar eller enhets egenskaper med dubbla rapporter och ska överensstämma med uttrycks formatet. Till exempel `tags.environment='test' and properties.reported.devicemodel='4000x'`.
* **--prioritet** -ett positivt heltal. I händelse av att två eller flera distributioner är riktade mot samma enhet gäller distributionen med det högsta numeriska värdet för prioritet.
* **--mått** – skapa mått som frågar edgeHub-rapporterade egenskaper för att spåra statusen för en distribution. Måtten tar JSON-inmatade eller en fil Sök väg. Till exempel `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`.

Information om hur du övervakar en distribution med Azure CLI finns i [övervaka IoT Edge distributioner](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli).

## <a name="modify-a-deployment"></a>Ändra en distribution

När du ändrar en distribution replikeras ändringarna omedelbart till alla mål enheter.

Om du uppdaterar mål villkoret inträffar följande uppdateringar:

* Om en enhet inte uppfyller det gamla mål villkoret, men uppfyller det nya mål villkoret och den här distributionen är den högsta prioriteten för den enheten, tillämpas den här distributionen på enheten.
* Om en enhet som för närvarande kör distributionen inte längre uppfyller mål villkoret avinstalleras distributionen och börjar med den näst högsta prioriterade distributionen.
* Om en enhet som för närvarande kör distributionen inte längre uppfyller mål villkoret och inte uppfyller mål villkoren för andra distributioner, sker ingen ändring på enheten. Enheten fortsätter att köra sina aktuella moduler i det aktuella läget, men hanteras inte som en del av distributionen längre. När den uppfyller mål villkoren för en annan distribution avinstallerar den distributionen och tar den nya.

Det går inte att uppdatera innehållet i en distribution, som innehåller de moduler och vägar som definierats i distributions manifestet. Om du vill uppdatera innehållet i en distribution gör du det genom att skapa en ny distribution som riktar sig mot samma enheter med högre prioritet. Du kan ändra vissa egenskaper för en befintlig modul, inklusive mål villkor, etiketter, mått och prioritet.

Använd kommandot [AZ IoT Edge Deployment Update](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-update) för att uppdatera en-distribution:

```azurecli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

Kommandot för distributions uppdatering tar följande parametrar:

* **--Deployment-ID** – namnet på den distribution som finns i IoT Hub.
* **--hubb-Name** -namnet på den IoT-hubb som distributionen finns i. Navet måste finnas i den aktuella prenumerationen. Växla till önskad prenumeration med kommandot `az account set -s [subscription name]`
* **--Ange** – uppdatera en egenskap i distributionen. Du kan uppdatera följande egenskaper:
  * targetCondition – till exempel `targetCondition=tags.location.state='Oregon'`
  * Etiketter
  * prioritet
* **--Lägg** till en ny egenskap för distributionen, inklusive mål villkor eller etiketter.
* **--ta bort** en befintlig egenskap, inklusive mål villkor eller etiketter.

## <a name="delete-a-deployment"></a>Ta bort en distribution

När du tar bort en distribution tar alla enheter med den näst högsta prioritets distributionen. Om enheterna inte uppfyller mål villkoren för någon annan distribution, tas modulerna inte bort när distributionen tas bort.

Använd kommandot [AZ IoT Edge Deployment Delete](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-delete) för att ta bort en distribution:

```azurecli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

Kommandot för att ta bort distribution tar följande parametrar:

* **--Deployment-ID** – namnet på den distribution som finns i IoT Hub.
* **--hubb-Name** -namnet på den IoT-hubb som distributionen finns i. Navet måste finnas i den aktuella prenumerationen. Växla till önskad prenumeration med kommandot `az account set -s [subscription name]`

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [att distribuera moduler till IoT Edge enheter](module-deployment-monitoring.md).