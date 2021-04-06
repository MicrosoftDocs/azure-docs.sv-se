---
title: Distribuera modul & vägar med distributions manifest – Azure IoT Edge
description: Lär dig hur ett distributions manifest deklarerar vilka moduler som ska distribueras, hur du distribuerar dem och hur du skapar meddelande vägar mellan dem.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2e3f1891a786751365a0bea58097e03bd41f85bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103489937"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Lär dig hur du distribuerar moduler och etablerar vägar i IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Varje IoT Edge enhet kör minst två moduler: $edgeAgent och $edgeHub, som ingår i IoT Edge Runtime. IoT Edge enhet kan köra flera ytterligare moduler för valfritt antal processer. Använd ett distributions manifest för att berätta för enheten vilka moduler som ska installeras och hur de ska konfigureras för att fungera tillsammans.

*Distributions manifestet* är ett JSON-dokument som beskriver:

* **IoT Edge agent** -modul, som innehåller tre komponenter:
  * Behållar avbildningen för varje modul som körs på enheten.
  * Autentiseringsuppgifterna för åtkomst till privata behållar register som innehåller modul bilder.
  * Instruktioner för hur varje modul ska skapas och hanteras.
* **IoT Edge Hub** -modul, som innehåller hur meddelanden flödar mellan moduler och slutligen för att IoT Hub.
* De önskade egenskaperna för någon ytterligare modul är dubbla (valfritt).

Alla IoT Edge enheter måste konfigureras med ett distributions manifest. En nyligen installerad IoT Edge körning rapporterar en felkod tills den har kon figurer ATS med ett giltigt manifest.

I Azure IoT Edge självstudier skapar du ett distributions manifest genom att gå igenom en guide i Azure IoT Edge portalen. Du kan också använda ett distributions manifest via programmering med REST eller IoT Hub service SDK. Mer information finns i [förstå IoT Edge distributioner](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Skapa ett distributionsmanifest

På en hög nivå är ett distributions manifest en lista över moduler som kon figurer ATS med deras önskade egenskaper. Ett distributions manifest talar om för en IoT Edge enhet (eller en grupp av enheter) vilka moduler som ska installeras och hur de ska konfigureras. Distributions manifest innehåller de *önskade egenskaperna* för varje modul med dubbla. IoT Edge enheterna rapporterar de *rapporterade egenskaperna* för varje modul.

Två moduler krävs i varje distributions manifest: `$edgeAgent` , och `$edgeHub` . Dessa moduler är en del av IoT Edge runtime som hanterar IoT Edges enheten och modulerna som körs på den. Mer information om dessa moduler finns i [förstå IoT Edge Runtime och dess arkitektur](iot-edge-runtime.md).

Förutom de två körnings modulerna kan du lägga till upp till 50 moduler som ska köras på en IoT Edge enhet.

Ett distributions manifest som bara innehåller IoT Edge Runtime (edgeAgent och edgeHub) är giltigt.

Distributions manifest följer den här strukturen:

```json
{
  "modulesContent": {
    "$edgeAgent": { // required
      "properties.desired": {
        // desired properties of the IoT Edge agent
        // includes the image URIs of all deployed modules
        // includes container registry credentials
      }
    },
    "$edgeHub": { //required
      "properties.desired": {
        // desired properties of the IoT Edge hub
        // includes the routing information between modules, and to IoT Hub
      }
    },
    "module1": {  // optional
      "properties.desired": {
        // desired properties of module1
      }
    },
    "module2": {  // optional
      "properties.desired": {
        // desired properties of module2
      }
    }
  }
}
```

## <a name="configure-modules"></a>Konfigurera moduler

Definiera hur IoT Edge runtime installerar modulerna i distributionen. IoT Edge agent är körnings komponenten som hanterar installation, uppdateringar och status rapportering för en IoT Edge enhet. Därför innehåller $edgeAgent modul dubbla konfigurations-och hanterings information för alla moduler. Den här informationen inkluderar konfigurations parametrar för själva IoT Edge agenten.

$EdgeAgent egenskaperna följer den här strukturen:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": {
          "settings":{
            "registryCredentials":{
              // give the IoT Edge agent access to container images that aren't public
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            // configuration and management details
          },
          "edgeHub": {
            // configuration and management details
          }
        },
        "modules": {
          "module1": {
            // configuration and management details
          },
          "module2": {
            // configuration and management details
          }
        }
      }
    },
    "$edgeHub": { ... },
    "module1": { ... },
    "module2": { ... }
  }
}
```

IoT Edge agent schema version 1,1 släpptes tillsammans med IoT Edge version 1.0.10 och aktiverar startordning för modulen. Schema version 1,1 rekommenderas för alla IoT Edge-distributioner som kör version 1.0.10 eller senare.

### <a name="module-configuration-and-management"></a>Konfiguration och hantering av moduler

I listan med önskade egenskaper för IoT Edge agent kan du definiera vilka moduler som distribueras till en IoT Edge-enhet och hur de ska konfigureras och hanteras.

En fullständig lista över önskade egenskaper som kan eller måste inkluderas finns i [Egenskaper för IoT Edge agent och IoT Edge Hub](module-edgeagent-edgehub.md).

Exempel:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": { ... },
        "systemModules": {
          "edgeAgent": { ... },
          "edgeHub": { ... }
        },
        "modules": {
          "module1": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 2,
            "settings": {
              "image": "myacr.azurecr.io/module1:latest",
              "createOptions": "{}"
            }
          },
          "module2": { ... }
        }
      }
    },
    "$edgeHub": { ... },
    "module1": { ... },
    "module2": { ... }
  }
}
```

Varje modul har en **inställnings** egenskap som innehåller modulens **avbildning**, en adress för behållar avbildningen i ett behållar register och eventuella **createOptions** för att konfigurera avbildningen vid start. Mer information finns i [så här konfigurerar du behållare skapa alternativ för IoT Edge moduler](how-to-use-create-options.md).

EdgeHub-modulen och de anpassade modulerna har också tre egenskaper som talar om för IoT Edge-agenten hur de ska hanteras:

* **Status**: anger om modulen ska köras eller stoppas när den först distribueras. Krävs.
* **RestartPolicy**: när och om den IoT Edge agenten ska starta om modulen om den stoppas. Krävs.
* **StartupOrder**: *introducerades i IoT Edge version 1.0.10.* Vilken ordning som IoT Edge agenten ska starta modulerna vid första distributionen. Ordningen deklareras med heltal, där en modul med ett start värde på 0 startas först och sedan följer högre tal. EdgeAgent-modulen har inget start värde eftersom den alltid startar först. Valfritt.

  Den IoT Edge agenten initierar modulerna i början av startvärdet, men väntar inte på att varje modul ska avslutas innan den skickas till nästa.

  Start ordningen är användbar om vissa moduler är beroende av andra. Du kanske till exempel vill att edgeHub-modulen ska starta först så att den är redo att skicka meddelanden när de andra modulerna startar. Eller så kanske du vill starta en lagrings modul innan modulerna som skickar data till den. Du bör dock alltid utforma dina moduler för att hantera problem med andra moduler. Det är den typ av behållare som de kan stoppa och starta om när som helst, och hur många gånger som helst.

## <a name="declare-routes"></a>Deklarera vägar

IoT Edge Hub hanterar kommunikation mellan moduler, IoT Hub och eventuella löv enheter. Därför innehåller $edgeHub modul dubbla en önskad egenskap som kallas *vägar* som deklarerar hur meddelanden skickas i en distribution. Du kan ha flera vägar i samma distribution.

Vägar deklareras i **$edgeHub** önskade egenskaper med följande syntax:

```json
{
  "modulesContent": {
    "$edgeAgent": { ... },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "routes": {
          "route1": "FROM <source> WHERE <condition> INTO <sink>",
          "route2": {
            "route": "FROM <source> WHERE <condition> INTO <sink>",
            "priority": 0,
            "timeToLiveSecs": 86400
          }
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    },
    "module1": { ... },
    "module2": { ... }
  }
}
```

IoT Edge Hub-schemat version 1,1 släpptes tillsammans med IoT Edge version 1.0.10 och gör det möjligt att använda väg prioritering och Time to Live. Schema version 1,1 rekommenderas för alla IoT Edge-distributioner som kör version 1.0.10 eller senare.

Varje väg behöver en *källa* där meddelandena kommer från och en *mottagare* där meddelandena går. *Villkoret* är en valfri del som du kan använda för att filtrera meddelanden.

Du kan tilldela *prioritet* till vägar som du vill se till att bearbeta sina meddelanden först. Den här funktionen är användbar i scenarier där den överordnade anslutningen är svag eller begränsad och du har viktiga data som ska prioriteras över standardtelemetri-meddelanden.

### <a name="source"></a>Källa

Källan anger var meddelandena kommer från. IoT Edge kan dirigera meddelanden från moduler eller löv enheter.

Med IoT SDK: er kan moduler deklarera vissa utdataportar för sina meddelanden med hjälp av klassen ModuleClient. Utgående köer är inte nödvändiga, men är användbara för att hantera flera vägar. Löv enheter kan använda DeviceClient-klassen i IoT SDK: er för att skicka meddelanden till IoT Edge gateway-enheter på samma sätt som de skulle skicka meddelanden till IoT Hub. Mer information finns i [förstå och använda Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md): er.

Egenskapen source kan vara något av följande värden:

| Källa | Beskrivning |
| ------ | ----------- |
| `/*` | Alla meddelanden från enhet till moln eller dubbla ändrings meddelanden från alla moduler eller löv enheter |
| `/twinChangeNotifications` | En dubbel förändring (rapporterade egenskaper) som kommer från alla moduler eller löv enheter |
| `/messages/*` | Alla enhets-till-moln-meddelanden som skickas av en modul genom några eller inga utdata eller av en löv enhet |
| `/messages/modules/*` | Alla enhets-till-moln-meddelanden som skickas av en modul genom några eller inga utdata |
| `/messages/modules/<moduleId>/*` | Alla enhets-till-moln-meddelanden som skickas av en viss modul genom några eller inga utdata |
| `/messages/modules/<moduleId>/outputs/*` | Alla enhets-till-moln-meddelanden som skickas av en viss modul via vissa utdata |
| `/messages/modules/<moduleId>/outputs/<output>` | Alla enhets-till-moln-meddelanden som skickas av en modul via en speciell utdata |

### <a name="condition"></a>Villkor

Villkoret är valfritt i en flödes deklaration. Om du vill skicka alla meddelanden från källan till mottagaren ska du bara lämna **WHERE** -satsen helt. Eller så kan du använda det [IoT Hub frågespråket](../iot-hub/iot-hub-devguide-routing-query-syntax.md) för att filtrera efter vissa meddelanden eller meddelande typer som uppfyller villkoret. IoT Edge vägar stöder inte filtrering av meddelanden baserat på dubbla taggar eller egenskaper.

Meddelanden som skickas mellan moduler i IoT Edge formateras på samma sätt som de meddelanden som skickas mellan enheterna och Azure IoT Hub. Alla meddelanden formateras som JSON och har parametrarna **systemProperties**, **appProperties** och **Body** .

Du kan bygga frågor runt någon av de tre parametrarna med följande syntax:

* System egenskaper: `$<propertyName>` eller `{$<propertyName>}`
* Program egenskaper: `<propertyName>`
* Egenskaper för brödtext: `$body.<propertyName>`

Exempel på hur du skapar frågor för meddelande egenskaper finns i [fråga om enhets-till-moln-meddelande vägar](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Ett exempel som är speciellt för IoT Edge är när du vill filtrera efter meddelanden som anlänt till en gateway-enhet från en löv enhet. Meddelanden som kommer från moduler innehåller en system egenskap som heter **connectionModuleId**. Så om du vill dirigera meddelanden från löv enheter direkt till IoT Hub, använder du följande väg för att utesluta module-meddelanden:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Kanalmottagare

Mottagaren definierar var meddelandena ska skickas. Endast moduler och IoT Hub kan ta emot meddelanden. Meddelanden kan inte dirigeras till andra enheter. Det finns inga jokertecken i egenskapen Sink.

Egenskapen Sink kan vara något av följande värden:

| Kanalmottagare | Beskrivning |
| ---- | ----------- |
| `$upstream` | Skicka meddelandet till IoT Hub |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Skicka meddelandet till en speciell inmatare för en speciell modul |

IoT Edge ger minst en garanti. IoT Edge Hub lagrar meddelanden lokalt om en väg inte kan leverera meddelandet till mottagaren. Om IoT Edge hubb till exempel inte kan ansluta till IoT Hub eller om mål modulen inte är ansluten.

IoT Edge Hub lagrar meddelandena upp till den tid som anges i `storeAndForwardConfiguration.timeToLiveSecs` egenskapen för [önskade egenskaper för IoT Edge Hub](module-edgeagent-edgehub.md).

### <a name="priority-and-time-to-live"></a>Prioritet och Time-to-Live

Vägar kan deklareras antingen med bara en sträng som definierar vägen eller som ett objekt som tar en väg sträng, ett prioritets-heltal och ett Time to Live-heltal.

Alternativ 1:

   ```json
   "route1": "FROM <source> WHERE <condition> INTO <sink>",
   ```

Alternativ 2, som introducerades i IoT Edge version 1.0.10 med IoT Edge Hub schema version 1,1:

   ```json
   "route2": {
     "route": "FROM <source> WHERE <condition> INTO <sink>",
     "priority": 0,
     "timeToLiveSecs": 86400
   }
   ```

**Prioritets** värden kan vara 0-9, inklusive, där 0 är den högsta prioriteten. Meddelandena köas baserat på deras slut punkter. Alla prioriteter på 0-meddelanden som är riktade mot en speciell slut punkt bearbetas innan prioritet 1-meddelanden riktade till samma slut punkt bearbetas och nedåt i raden. Om flera vägar för samma slut punkt har samma prioritet, bearbetas deras meddelanden i en första-först-hanterad bas. Om ingen prioritet har angetts tilldelas vägen lägst prioritet.

Egenskapen **timeToLiveSecs** ärver värdet från IoT Edge hubbens **storeAndForwardConfiguration** om det inte uttryckligen anges. Värdet kan vara valfritt positivt heltal.

För detaljerad information om hur prioritets köer hanteras, se referens sidan för [väg prioritet och Time-to-Live](https://github.com/Azure/iotedge/blob/master/doc/Route_priority_and_TTL.md).

## <a name="define-or-update-desired-properties"></a>Definiera eller uppdatera önskade egenskaper

Distributions manifestet anger önskade egenskaper för varje modul som distribueras till den IoT Edge enheten. Önskade egenskaper i distributions manifestet skriver över alla önskade egenskaper för närvarande i modulen.

Om du inte anger en moduls dubbla egenskaper i distributions manifestet kan IoT Hub inte ändra modulen på något sätt. I stället kan du ange önskade egenskaper program mässigt.

Samma mekanismer som gör att du kan ändra enhetens dubblare används för att ändra modulerna. Mer information finns i modulen för den [sammanflätade utvecklings guiden](../iot-hub/iot-hub-devguide-module-twins.md).

## <a name="deployment-manifest-example"></a>Exempel på distributions manifest

I följande exempel visas hur ett giltigt distributions manifest dokument kan se ut.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "<password>",
                "address": "myacr.azurecr.io"
              }
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 0,
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
              "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 2,
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 1,
            "env": {
              "tempLimit": {"value": "100"}
            },
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "routes": {
          "sensorToFilter": {
            "route": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
            "priority": 0,
            "timeToLiveSecs": 1800
          },
          "filterToIoTHub": {
            "route": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream",
            "priority": 1,
            "timeToLiveSecs": 1800
          }
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 100
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Nästa steg

* En fullständig lista över egenskaper som kan eller måste inkluderas i $edgeAgent och $edgeHub finns i [Egenskaper för IoT Edge agent och IoT Edge hubb](module-edgeagent-edgehub.md).

* Nu när du vet hur IoT Edge moduler används kan du [förstå kraven och verktygen för att utveckla IoT Edge moduler](module-development.md).
