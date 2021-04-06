---
title: Utveckla moduler för Azure IoT Edge | Microsoft Docs
description: Utveckla anpassade moduler för Azure IoT Edge som kan kommunicera med körningen och IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a30b4b056d56e096f80b9494ab80a585fff76e66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103489943"
---
# <a name="develop-your-own-iot-edge-modules"></a>Utveckla dina egna IoT Edge-moduler

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Azure IoT Edge moduler kan ansluta till andra Azure-tjänster och bidra till din större data pipeline i molnet. I den här artikeln beskrivs hur du kan utveckla moduler för att kommunicera med IoT Edge Runtime och IoT Hub, och därmed resten av Azure-molnet.

## <a name="iot-edge-runtime-environment"></a>IoT Edge körnings miljö

IoT Edge runtime tillhandahåller infrastrukturen för att integrera funktionerna i flera IoT Edge moduler och distribuera dem till IoT Edge enheter. Alla program kan paketeras som en IoT Edge modul. För att dra full nytta av IoT Edge kommunikations-och hanterings funktioner kan ett program som körs i en modul använda Azure IoT-enhetens SDK för att ansluta till den lokala IoT Edge Hub.
::: moniker range=">=iotedge-2020-11"
Moduler kan också använda en MQTT-klient för att ansluta till den lokala IoT Edge Hub MQTT-Broker.
::: moniker-end

### <a name="packaging-your-program-as-an-iot-edge-module"></a>Paketera ditt program som en IoT Edge modul

För att distribuera ditt program på en IoT Edge-enhet måste den först vara containerd och köras med en Docker-kompatibel motor. IoT Edge använder [Moby](https://github.com/moby/moby), projektet med öppen källkod bakom Docker, som Docker-kompatibel motor. Samma parametrar som du använder med Docker kan skickas till dina IoT Edge-moduler. Mer information finns i [så här konfigurerar du behållare skapa alternativ för IoT Edge moduler](how-to-use-create-options.md).

## <a name="using-the-iot-edge-hub"></a>Använda IoT Edge Hub

IoT Edge Hub tillhandahåller två huvudsakliga funktioner: proxy till IoT Hub och lokal kommunikation.

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Ansluta till IoT Edge hubb från en modul

Anslutning till den lokala IoT Edge hubben från en modul innebär samma anslutnings steg som för alla klienter. Mer information finns i [ansluta till IoT Edge Hub](iot-edge-runtime.md#connecting-to-the-iot-edge-hub).

Om du vill använda IoT Edge routning över AMQP eller MQTT kan du använda ModuleClient från Azure IoT SDK. Skapa en ModuleClient-instans för att ansluta modulen till IoT Edge Hub som körs på enheten, på liknande sätt som DeviceClient-instanser ansluter IoT-enheter till IoT Hub. Mer information om klassen ModuleClient och dess kommunikations metoder finns i API-referensen för ditt prioriterade SDK-språk: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient), [Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)eller [Node.js](/javascript/api/azure-iot-device/moduleclient).

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Om du vill använda IoT Edge MQTT Broker måste du ta med din egen MQTT-klient och upprätta anslutningen med information som du hämtar från IoT Edge daemon-arbetsbelastnings-API: et. <!--Need to add details here-->

Mer information om hur du väljer mellan routning eller publicering/prenumeration med MQTT-Broker finns i [lokal kommunikation](iot-edge-runtime.md#local-communication).

### <a name="mqtt-broker-primitives"></a>Primitiver för MQTT-Broker

#### <a name="send-a-message-on-a-user-defined-topic"></a>Skicka ett meddelande i ett användardefinierat ämne

Med IoT Edge MQTT Broker kan du publicera meddelanden i alla användardefinierade ämnen. Det gör du genom att auktorisera modulen att publicera på specifika ämnen och sedan hämta en token från arbets belastnings-API: et för att använda som lösen ord när du ansluter till MQTT-koordinatorn och slutligen publicera meddelanden på de behöriga ämnena med MQTT-klienten som du väljer.

#### <a name="receive-messages-on-a-user-defined-topic"></a>Ta emot meddelanden i ett användardefinierat ämne

Med IoT Edge MQTT-Broker är det liknande att ta emot meddelanden. Se först till att modulen har behörighet att prenumerera på specifika ämnen och hämta sedan en token från arbets belastnings-API: et som ska användas som lösen ord när du ansluter till MQTT-Broker och slutligen prenumererar på meddelanden på de behöriga ämnena med MQTT-klienten som du väljer.

::: moniker-end

### <a name="iot-hub-primitives"></a>IoT Hub primitiver

IoT Hub ser en analogously till en enhet, i den mening som:

* den kan skicka [meddelanden från enheten till molnet](../iot-hub/iot-hub-devguide-messaging.md).
* den kan ta emot [direkta metoder](../iot-hub/iot-hub-devguide-direct-methods.md) som riktar sig specifikt till identiteten.
* den har en modul med dubbla som är distinkt och isolerad från [enheten](../iot-hub/iot-hub-devguide-device-twins.md) , och den andra modulen är sammanflätad med enheten.

För närvarande kan inte moduler ta emot meddelanden från molnet till enheten eller använda fil uppladdnings funktionen.

När du skriver en modul kan du ansluta till IoT Edge Hub och använda IoT Hub primitiver på samma sätt som när du använder IoT Hub med ett enhets program. Den enda skillnaden mellan IoT Edge moduler och IoT-enhets program är att du måste referera till modulens identitet i stället för enhetens identitet.

#### <a name="device-to-cloud-messages"></a>Meddelanden från enheten till molnet

En IoT Edge-modul kan skicka meddelanden till molnet via IoT Edge Hub som fungerar som lokal Broker och sprider meddelanden till molnet. För att möjliggöra komplex bearbetning av meddelanden från enheten till molnet kan en IoT Edge-modul också fånga och bearbeta meddelanden som skickas av andra moduler eller enheter till den lokala IoT Edge Hub och skicka nya meddelanden med bearbetade data. Kedjor av IoT Edge moduler kan därför skapas för att bygga pipeliner för lokal bearbetning.

Om du vill skicka telemetri från enhet till moln med hjälp av routning använder du ModuleClient i Azure IoT SDK. Med Azure IoT SDK har varje modul begreppet *indata* och *utdata* -slutpunkter för moduler, som mappar till särskilda MQTT-ämnen. Använd- `ModuleClient.sendMessageAsync` metoden så skickas meddelanden på slut punkten för utdata i modulen. Konfigurera sedan en väg i edgeHub för att skicka slut punkten för utdata till IoT Hub.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Att skicka telemetri från enhet till moln med MQTT-Broker liknar att publicera meddelanden i användardefinierade ämnen, men med hjälp av följande IoT Hub Special ämne för modulen: `devices/<device_name>/<module_name>/messages/events` . Auktoriseringar måste konfigureras på rätt sätt. MQTT-bryggan måste också konfigureras för att vidarebefordra meddelandena i det här avsnittet till molnet.

::: moniker-end

Om du vill bearbeta meddelanden med hjälp av routning måste du först konfigurera en väg för att skicka meddelanden från en annan slut punkt (modul eller enhet) till ingångs slut punkten för din modul. Lyssna sedan efter meddelanden på ingångs slut punkten för modulen. Varje gången ett nytt meddelande kommer tillbaka utlöses en callback-funktion av Azure IoT SDK. Bearbeta ditt meddelande med den här motringningsfunktionen och skicka eventuellt nya meddelanden i din moduls slut punkts kö.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Bearbetning av meddelanden med hjälp av MQTT-koordinatorn liknar att prenumerera på meddelanden om användardefinierade ämnen, men med hjälp av IoT Edge Special ämnen i modulens utgående kö: `devices/<device_name>/<module_name>/messages/events` . Auktoriseringar måste konfigureras på rätt sätt. Du kan också skicka nya meddelanden i de avsnitt som du väljer.

::: moniker-end

#### <a name="twins"></a>Tvillingar

Dubbla är en av de primitiver som tillhandahålls av IoT Hub. Det finns JSON-dokument som lagrar statusinformation, inklusive metadata, konfigurationer och villkor. Varje modul eller enhet har sin egen enhet.

Anropa metoden för att hämta en modul med Azure IoT SDK `ModuleClient.getTwin` .

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

För att få en modul sammanflätad med valfri MQTT-klient, är det lite mer arbete som är förväntat eftersom hämtningen av en mitt inte är ett typiskt MQTT-mönster. Modulen måste först prenumerera på IoT Hub Special ämne `$iothub/twin/res/#` . Det här ämnes namnet ärvs från IoT Hub och alla enheter/moduler måste prenumerera på samma ämne. Det innebär inte att enheterna får den dubbla av varandra. IoT Hub-och edgeHub vet vilka dubbla ska levereras där, även om alla enheter lyssnar på samma ämnes namn. När prenumerationen har gjorts måste modulen be om den dubbla genom att publicera ett meddelande till följande IoT Hub Special ämne med ett fråge-ID `$iothub/twin/GET/?$rid=1234` . Detta ID för begäran är ett godtyckligt ID (det vill säga ett GUID), som skickas tillbaka med IoT Hub tillsammans med de begärda data. Så här kan en klient koppla sina begär anden till svaren. Resultat koden är en HTTP-liknande status kod där lyckad är kodad som 200.

::: moniker-end

Om du vill ta emot en modul med dubbla korrigeringar med Azure IoT SDK, implementera en callback-funktion och registrera den med `ModuleClient.moduleTwinCallback` metoden från Azure IoT SDK så att motringningsfunktionen aktive ras varje tillfälle som en dubbel korrigering kommer in.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

För att kunna ta emot en modul med en MQTT-klient, är processen mycket lik att ta emot fullständiga sändningar: en klient måste prenumerera på särskilda IoT Hubs ämnen `$iothub/twin/PATCH/properties/desired/#` . När IoT Hub skickar en ändring av det önskade avsnittet av den dubbla efter att prenumerationen har gjorts, tar klienten emot den.

::: moniker-end

#### <a name="receive-direct-methods"></a>Ta emot direkta metoder

För att få en direkt metod med Azure IoT SDK, implementera en callback-funktion och registrera den med `ModuleClient.methodCallback` metoden från Azure IoT SDK så att motringningsfunktionen aktive ras varje tillfälle som en direkt metod kommer in.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

För att få en direkt metod med en MQTT-klient, är processen mycket lik att ta emot dubbla korrigeringar. Klienten måste bekräfta att den har tagit emot anropet och kan skicka tillbaka viss information på samma tidpunkt. Special IoT Hub-avsnittet för att prenumerera på `$iothub/methods/POST/#` .

::: moniker-end

## <a name="language-and-architecture-support"></a>Stöd för språk och arkitektur

IoT Edge stöder flera operativ system, enhets arkitekturer och utvecklings språk så att du kan bygga upp det scenario som passar dina behov. Använd det här avsnittet för att förstå alternativen för att utveckla anpassade IoT Edge-moduler. Du kan lära dig mer om verktygs support och krav för varje språk för [att förbereda utvecklings-och test miljön för IoT Edge](development-environment.md).

### <a name="linux"></a>Linux

För alla språk i följande tabell IoT Edge stöder utveckling för AMD64-och ARM32 Linux-enheter.

| Utvecklingsspråk | Utvecklingsverktyg |
| -------------------- | ----------------- |
| C | Visuell Studio-kod<br>Visual Studio 2017/2019 |
| C# | Visuell Studio-kod<br>Visual Studio 2017/2019 |
| Java | Visuell Studio-kod |
| Node.js | Visual Studio-koden |
| Python | Visuell Studio-kod |

>[!NOTE]
>Att utveckla och felsöka stöd för ARM64 Linux-enheter finns i [offentlig för hands version](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Mer information finns i [utveckla och FELSÖKA ARM64 IoT Edge moduler i Visual Studio Code (för hands version)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

### <a name="windows"></a>Windows

För alla språk i följande tabell IoT Edge stöder utveckling för AMD64 Windows-enheter.

| Utvecklingsspråk | Utvecklingsverktyg |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code (inga fel söknings funktioner)<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>Nästa steg

[Förbered din utvecklings-och test miljö för IoT Edge](development-environment.md)

[Använd Visual Studio för att utveckla C#-moduler för IoT Edge](how-to-visual-studio-develop-module.md)

[Använd Visual Studio Code för att utveckla moduler för IoT Edge](how-to-vs-code-develop-module.md)

[Förstå och använda SDK:er för Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md)
