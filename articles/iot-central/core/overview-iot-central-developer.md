---
title: Enhetsutveckling för Azure IoT Central | Microsoft Docs
description: Azure IoT Central är en IoT-programplattform som gör skapandet av IoT-lösningar enklare. Den här artikeln innehåller en översikt över hur du utvecklar enheter för att ansluta IoT Central program. Enheter använder telemetri för att skicka strömmande data och egenskaper för att rapportera enhetens tillstånd. IoT Central kan ange enhetstillstånd med skrivbara egenskaper och anropa kommandon på en enhet.
author: dominicbetts
ms.author: dobett
ms.date: 05/05/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
ms.openlocfilehash: ebd2759d4dfb8ee79130f9b4876eba8d45226d04
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718799"
---
# <a name="iot-central-device-development-guide"></a>IoT Central för enhetsutveckling

*Den här artikeln gäller enhetsutvecklare.*

Med IoT Central program kan du övervaka och hantera miljontals enheter under hela livscykeln. Den här guiden är avsedd för enhetsutvecklare som implementerar kod som ska köras på enheter som ansluter till IoT Central.

Enheter interagerar med IoT Central program med hjälp av följande primitiver:

- _Telemetri är_ data som en enhet skickar till IoT Central. Till exempel en ström med temperaturvärden från en inbyggd sensor.
- _Egenskaper_ är tillståndsvärden som en enhet rapporterar till IoT Central. Till exempel enhetens aktuella version av den inbyggda programvaran. Du kan också ha skrivbara egenskaper som IoT Central kan uppdatera på enheten, till exempel en måltemperatur.
- _Kommandon_ anropas från IoT Central för att styra beteendet för en enhet. Till exempel kan ditt IoT Central anropa ett kommando för att starta om en enhet.

En lösningsbyggare ansvarar för att konfigurera instrumentpaneler och vyer i IoT Central webbgränssnitt för att visualisera telemetri, hantera egenskaper och anropa kommandon.

## <a name="types-of-device"></a>Typer av enheter

I följande avsnitt beskrivs de viktigaste typerna av enheter som du kan ansluta till IoT Central program:

### <a name="standalone-device"></a>Fristående enhet

En fristående enhet ansluter direkt till IoT Central. En fristående enhet skickar vanligtvis telemetri från sin registrering eller anslutna sensorer till ditt IoT Central program. Fristående enheter kan också rapportera egenskapsvärden, ta emot skrivbara egenskapsvärden och svara på kommandon.

### <a name="gateway-device"></a>Gatewayenhet

En gatewayenhet hanterar en eller flera nedströmsenheter som ansluter till ditt IoT Central program. Du använder IoT Central för att konfigurera relationerna mellan nedströmsenheterna och gatewayenheten. Mer information finns i Define a new IoT gateway device type in your Azure IoT Central application (Definiera en ny [IoT-gatewayenhetstyp i ditt Azure IoT Central program).](./tutorial-define-gateway-device-type.md)

### <a name="edge-device"></a>Edge-enhet

En gränsenhet ansluter direkt till IoT Central, men fungerar som en mellanhand för andra enheter som kallas _lövenheter._ En gränsenhet finns vanligtvis nära lövenheterna som den fungerar som mellanhand för. Scenarier som använder gränsenheter är:

- Aktivera enheter som inte kan ansluta direkt till IoT Central ansluta via gränsenhet. En lövenhet kan till exempel använda Bluetooth för att ansluta till gränsenhet, som sedan ansluter via Internet till IoT Central.
- Aggregera telemetri innan den skickas till IoT Central. Den här metoden kan minska kostnaderna för att skicka data till IoT Central.
- Kontrollera lövenheter lokalt för att undvika den svarstid som är associerad med att IoT Central till nätverk via Internet.

En gränsenhet kan också skicka sin egen telemetri, rapportera dess egenskaper och svara på skrivbara egenskapsuppdateringar och kommandon.

IoT Central bara kantenheten, inte lövenheterna som är anslutna till gränsenheterna.

Mer information finns i Lägga [till en Azure IoT Edge enhet i ditt Azure IoT Central program.](./tutorial-add-edge-as-leaf-device.md)

## <a name="connect-a-device"></a>Ansluta en enhet

Azure IoT Central använder Azure IoT Hub [Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) för att hantera all enhetsregistrering och anslutning.

Genom att använda DPS aktiveras:

- IoT Central stöd för registrering och anslutning av enheter i stor skala.
- Du kan generera enhetsautentiseringsuppgifter och konfigurera enheterna offline utan att registrera enheterna via IoT Central användargränssnitt.
- Du kan använda dina egna enhets-ID:er för att registrera enheter i IoT Central. Med egna enhets-ID:er förenklas integreringen med befintliga back office-system.
- Ett enda, konsekvent sätt att ansluta enheter till IoT Central.

Mer information finns i [Anslut till Azure IoT Central](./concepts-get-connected.md) [metodtips.](concepts-best-practices.md)

### <a name="security"></a>Säkerhet

Anslutningen mellan en enhet och ditt IoT Central-program skyddas med antingen [signaturer](./concepts-get-connected.md#sas-group-enrollment) för delad åtkomst eller [X.509-certifikat](./concepts-get-connected.md#x509-group-enrollment)som är branschstandard.

### <a name="communication-protocols"></a>Kommunikationsprotokoll

Kommunikationsprotokoll som en enhet kan använda för att ansluta till IoT Central omfattar MQTT, AMQP och HTTPS. Internt använder IoT Central IoT-hubb för att aktivera enhetsanslutning. Mer information om de kommunikationsprotokoll som IoT Hub stöder för enhetsanslutning finns i [Välj ett kommunikationsprotokoll.](../../iot-hub/iot-hub-devguide-protocols.md)

## <a name="implement-the-device"></a>Implementera enheten

En IoT Central-enhetsmall innehåller _en modell_ som anger de beteenden som en enhet av den typen ska implementera. Beteenden omfattar telemetri, egenskaper och kommandon.

> [!TIP]
> Du kan exportera modellen från IoT Central som [en Digital Twins DTDL (Definition Language) v2](https://github.com/Azure/opendigitaltwins-dtdl) JSON-fil.

Varje modell har en unik _enhetstvillingmodellidentifierare_ (DTMI), till exempel `dtmi:com:example:Thermostat;1` . När en enhet ansluter till IoT Central skickar den DTMI för den modell som den implementerar. IoT Central sedan associera rätt enhetsmall med enheten.

[IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) definierar en uppsättning konventioner som en enhet ska följa när den implementerar en DTDL-modell.

[Azure IoT-enhets-SDK:er](#languages-and-sdks) har stöd för IoT-Plug and Play konventionerna.

### <a name="device-model"></a>Enhetsmodell

En enhetsmodell definieras med hjälp av [DTDL](https://github.com/Azure/opendigitaltwins-dtdl). Med det här språket kan du definiera:

- Telemetrin som enheten skickar. Definitionen innehåller telemetrinamnet och datatypen. En enhet skickar till exempel temperaturtelemetri som en dubbel.
- Egenskaperna som enheten rapporterar till IoT Central. En egenskapsdefinition innehåller dess namn och datatyp. En enhet rapporterar till exempel tillståndet för en valve som ett booleskt tillstånd.
- Egenskaperna som enheten kan ta emot från IoT Central. Du kan också markera en egenskap som skrivbar. Till exempel IoT Central en måltemperatur som en dubbel till en enhet.
- De kommandon som en enhet svarar på. Definitionen innehåller namnet på kommandot och namn och datatyper för alla parametrar. En enhet svarar till exempel på ett omstartskommando som anger hur många sekunder som ska vänta innan omstarten.

En DTDL-modell kan vara _en modell utan_ komponent eller _flera_ komponenter:

- Ingen komponentmodell: En enkel modell använder inte inbäddade eller överlappande komponenter. All telemetri, alla egenskaper och kommandon definieras som en _standardkomponent._ Ett exempel finns i [termostatmodellen.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)
- Modell med flera komponenter. En mer komplex modell som innehåller två eller flera komponenter. Dessa komponenter innehåller en standardkomponent och en eller flera ytterligare kapslade komponenter. Ett exempel finns i [temperaturkontrollantmodellen.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)

Mer information finns i [IoT Plug and Play modeling guide (Guide för IoT-modellering)](../../iot-pnp/concepts-modeling-guide.md)

### <a name="conventions"></a>Konventioner

En enhet bör följa IoT-Plug and Play när den utbyter data med IoT Central. Konventionerna omfattar:

- Skicka DTMI när den ansluter till IoT Central.
- Skicka korrekt formaterade JSON-nyttolaster och metadata till IoT Central.
- Svara korrekt på skrivbara egenskaper och kommandon från IoT Central.
- Följ namngivningskonventionerna för komponentkommandon.

> [!NOTE]
> För närvarande IoT Central inte fullständigt stöd för **DTDL-matris-** och **geospatiala** datatyper.

Mer information om formatet för JSON-meddelanden som en enhet utbyter med IoT Central finns i Telemetri, egenskap och [kommandonyttolaster.](concepts-telemetry-properties-commands.md)

Mer information om IoT-Plug and Play finns i [IoT-Plug and Play konventioner.](../../iot-pnp/concepts-convention.md)

### <a name="device-sdks"></a>Enhets-SDK:er

Använd en av [Azure IoT-enhets-SDK:erna](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) för att implementera enhetens beteende. Koden bör:

- Registrera enheten med DPS och använd informationen från DPS för att ansluta till den interna IoT-hubben i ditt IoT Central program.
- Meddela DTMI för modellen som enheten implementerar.
- Skicka telemetri i det format som enhetsmodellen anger. IoT Central använder modellen i enhetsmallen för att avgöra hur telemetrin ska användas för visualiseringar och analys.
- Synkronisera egenskapsvärden mellan enheten och IoT Central. Modellen anger egenskapsnamnen och datatyperna så att IoT Central kan visa informationen.
- Implementera kommandohanterare för de kommandon som anges i modellen. Modellen anger de kommandonamn och parametrar som enheten ska använda.

Mer information om enhetsmallars roll finns i [Vad är enhetsmallar?](./concepts-device-templates.md).

Lite exempelkod finns i [Skapa och ansluta ett klientprogram](./tutorial-connect-device.md).

### <a name="languages-and-sdks"></a>Språk och SDK:er

Mer information om vilka språk och SDK:er som stöds finns i Understand and use Azure IoT Hub device SDKs (Förstå och använda [ENHETS-SDK:er).](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks)

## <a name="next-steps"></a>Nästa steg

Om du är enhetsutvecklare och vill fördjupa dig i kod föreslår vi att du går vidare med att skapa och ansluta ett klientprogram till [ditt Azure IoT Central program](./tutorial-connect-device.md).

Om du vill lära dig mer om att använda IoT Central är nästa steg att prova snabbstarterna, som börjar med Skapa ett [Azure IoT Central-program](./quick-deploy-iot-central.md).
