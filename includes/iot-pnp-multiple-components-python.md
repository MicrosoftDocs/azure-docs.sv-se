---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 5e0fa474412815a1ce830267ac2f09830a6353d3
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834198"
---
I den här självstudien får du lära dig hur du skapar ett exempel på IoT Plug and Play enhets program med komponenter, ansluter det till din IoT-hubb och använder Azure IoT Explorer-verktyget för att visa den information som skickas till hubben. Exempel programmet skrivs i python och ingår i Azure IoT-enhetens SDK för python. Ett Solution Builder kan använda Azure IoT Explorer-verktyget för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhets kod.

I den här kursen får du:

> [!div class="checklist"]
> * Hämta exempel koden.
> * Kör enhets exempel programmet och kontrol lera att det ansluter till din IoT-hubb.
> * Granska käll koden.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

För att slutföra den här självstudien behöver du python 3,7 på din utvecklings dator. Du kan ladda ned den senaste rekommenderade versionen för flera plattformar från [python.org](https://www.python.org/). Du kan kontrol lera din python-version med följande kommando:  

```cmd/sh
python --version
```

Du kan ladda ned den senaste rekommenderade versionen för flera plattformar från [python.org](https://www.python.org/).

## <a name="download-the-code"></a>Ladda ned koden

Paketet **Azure-IoT-Device** publiceras som en pip.

I din lokala python-miljö installerar du paketet på följande sätt:

```cmd/sh
pip install azure-iot-device
```

Om du har slutfört [snabb starten: ansluta ett exempel på IoT plug and Play Device-program som körs på Windows till IoT Hub (python)](../articles/iot-pnp/quickstart-connect-device.md)har du redan klonat lagrings platsen.

Klona python SDK IoT-lagringsplatsen:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="review-the-code"></a>Granska koden

Det här exemplet implementerar en IoT Plug and Play temperatur styrenhets enhet. Modellen som det här exemplet implementerar använder [flera komponenter](../articles/iot-pnp/concepts-components.md). [DTDL-modell filen (Digital Definition Language) för temperatur enheten](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definierar telemetri, egenskaper och kommandon som enheten implementerar.

Mappen *Azure-IoT-SDK-python\azure-IoT-device\samples\pnp* innehåller exempel koden för IoT plug and Play-enheten. Filerna för temperatur styrenhets exemplet är:

- temp_controller_with_thermostats. py
- pnp_helper. py

Temperatur styrenheten har flera komponenter och en standard komponent, baserat på temperatur styrenhetens DTDL-modell.

Öppna filen *temp_controller_with_thermostats. py* i valfritt redigerings program. Koden i den här filen:

1. Importer `pnp_helper.py` för att få åtkomst till hjälp metoder.

1. Definierar två digitala dubbla modell identifierare (DTMIs) som unikt representerar två olika gränssnitt, definierade i DTDL-modellen. Komponenterna i en riktig temperatur kontroll bör implementera dessa två gränssnitt. Dessa två gränssnitt har redan publicerats på en central lagrings plats. Dessa DTMIs måste vara kända för användaren och varierar beroende på användnings scenariot för enheten. I det aktuella exemplet representerar dessa två gränssnitt:

    - En termostat
    - Enhets information som har utvecklats av Azure.

1. Definierar DTMI `model_id` för den enhet som implementeras. DTMI är användardefinierad och måste matcha DTMI i DTDL modell fil.

1. Definierar de namn som har angetts för komponenterna i DTDL-filen. Det finns två termostater i DTDL och en komponent för enhets information. En konstant `serial_number` som kallas är också definierad i standard komponenten. En `serial_number` enhet kan inte ändras.

1. Definierar implementeringar av kommando hanterare. Dessa definierar vad enheten gör när den tar emot kommando begär Anden.

1. Definierar funktioner för att skapa ett kommando svar. Dessa definierar hur enheten svarar med till-kommando begär Anden. Du skapar funktioner för kommando svar om ett kommando behöver skicka ett anpassat svar tillbaka till IoT Hub. Om en Response-funktion för ett kommando inte anges skickas ett allmänt svar. I det här exemplet har endast kommandot **getMaxMinReport** ett anpassat svar.

1. Definierar en funktion för att skicka telemetri från den här enheten. Både termostater och standard komponenten skicka telemetri. Den här funktionen tar i en valfri komponent namn parameter för att kunna identifiera vilken komponent som skickade telemetri.

1. Definierar en lyssnare för kommando begär Anden.

1. Definierar en lyssnare för önskade egenskaps uppdateringar.

1. Har en `main` funktion som:

    - Använder enhets-SDK för att skapa en enhets klient och ansluta till IoT Hub. Enheten skickar `model_id` så att IoT Hub kan identifiera enheten som en IoT plug and Play-enhet.

    - Använder `create_reported_properties` funktionen i Hjälp filen för att skapa egenskaperna. Överför komponent namnet och egenskaperna som nyckel värdes par till den här funktionen.

    - Uppdaterar läsbara egenskaper för dess komponenter genom att anropa `patch_twin_reported_properties` .

    - Börjar lyssna efter kommando begär Anden med hjälp av `execute_command_listener` funktionen. Funktionen konfigurerar en lyssnare för kommando begär Anden från tjänsten. När du konfigurerar lyssnaren anger du en `method_name` , `user_command_handler` och en valfri `create_user_response_handler` som-parameter.
        - `method_name`Definierar kommando förfrågan. I det här exemplet definierar modellen kommandona **reboot** och **getMaxMinReport**.
        - `user_command_handler`Funktionen definierar vad enheten ska göra när den får ett kommando.
        - `create_user_response_handler`Funktionen skapar ett svar som skickas till din IoT-hubb när ett kommando körs utan problem. Du kan visa det här svaret i portalen. Om den här funktionen inte anges skickas ett allmänt svar till tjänsten.

    - Använder `execute_property_listener` för att lyssna efter egenskaps uppdateringar.

    - Börjar skicka telemetri med `send_telemetry` . Exempel koden använder en slinga för att anropa tre telemetri som skickar funktioner. Var och en kallas var åttonde sekund

    - Inaktiverar alla lyssnare och uppgifter och avslutar slingan när du trycker på **q** eller **q**.

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Läs mer om exempel konfigurationen i [README-exemplet](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md).

Använd följande kommando för att köra exemplet:

```cmd/sh
python temp_controller_with_thermostats.py
```

Exempel enheten skickar telemetridata med några sekunder till din IoT-hubb.

Följande utdata visas, som anger att enheten skickar telemetridata till hubben och är nu redo att ta emot kommandon och egenskaps uppdateringar.

![Enhets bekräftelse meddelanden](media/iot-pnp-multiple-components-python/multiple-component.png)

Se till att exemplet körs när du slutför nästa steg.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Använd Azure IoT Explorer för att verifiera koden

När enhets klient exemplet startar använder du verktyget Azure IoT Explorer för att kontrol lera att det fungerar.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]
