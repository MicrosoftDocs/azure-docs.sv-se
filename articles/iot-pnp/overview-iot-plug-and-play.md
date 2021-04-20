---
title: Introduktion till IoT-Plug and Play | Microsoft Docs
description: Läs mer om IoT-Plug and Play. IoT Plug and Play baseras på ett öppet modelleringsspråk som gör det möjligt för smarta IoT-enheter att deklarera sina funktioner. IoT-enheter presenterar deklarationen, som kallas enhetsmodell, när de ansluter till molnlösningar. Molnlösningen kan sedan automatiskt förstå enheten och börja interagera med den, utan att behöva skriva någon kod.
author: rido-min
ms.author: rmpablos
ms.date: 03/21/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.custom: references_regions
ms.openlocfilehash: 91a09db16524ebc7e4c04069b69b1c42c67538c6
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739718"
---
# <a name="what-is-iot-plug-and-play"></a>Vad är IoT Plug and Play?

Med IoT Plug and Play kan lösningsutvecklare integrera smarta enheter med sina lösningar utan manuell konfiguration. Kärnan i IoT Plug and Play är en enhetsmodell som en enhet använder för att annonsera sina funktioner till ett IoT-Plug and Play-aktiverat program.  Den här modellen är strukturerad som en uppsättning element som definierar:

- _Egenskaper_ som representerar det skrivskyddade eller skrivbara tillståndet för en enhet eller en annan entitet. Till exempel kan ett enhetsserienummer vara en skrivskyddat egenskap och en måltemperatur på en termostat kan vara en skrivbar egenskap.
- _Telemetri som_ är de data som genereras av en enhet, oavsett om data är en vanlig ström av sensoravläsningar, ett enstaka fel eller ett informationsmeddelande.
- _Kommandon_ som beskriver en funktion eller åtgärd som kan utföras på en enhet. Ett kommando kan till exempel starta om en gateway eller ta en bild med hjälp av en fjärrkamera.

Du kan gruppera de här elementen i gränssnitt för att återanvända dem i olika modeller för att underlätta samarbetet och påskynda utvecklingen.

Om du vill Plug and Play IoT-Azure Digital Twins du modeller och gränssnitt med hjälp [av DTDL (Digital Twins Definition Language).](https://github.com/Azure/opendigitaltwins-dtdl) [](../digital-twins/overview.md) IoT Plug and Play och DTDL är öppna för communityn och Microsoft samarbetar gärna med kunder, partner och branschen. Båda baseras på öppna W3C-standarder som JSON-LD och RDF, vilket gör det enklare att införa tjänster och verktyg.

Det kostar inget extra att använda IoT-Plug and Play och DTDL. Standardpriserna för [Azure IoT Hub](../iot-hub/about-iot-hub.md) och andra Azure-tjänster förblir desamma.

Den här artikeln beskriver:

- De typiska roller som är associerade med ett projekt som använder IoT-Plug and Play.
- Så här använder du IoT Plug and Play enheter i ditt program.
- Utveckla ett IoT-enhetsprogram som stöder IoT-Plug and Play.

## <a name="user-roles"></a>Användarroller

IoT Plug and Play är användbart för två typer av utvecklare:

- En _lösningsbyggare_ ansvarar för att utveckla en IoT-lösning med Azure IoT Hub och andra Azure-resurser och för att identifiera IoT-enheter som ska integreras.
- En _enhetsbyggare_ skapar den kod som körs på en enhet som är ansluten till din lösning.

## <a name="use-iot-plug-and-play-devices"></a>Använda IoT Plug and Play enheter

Som lösningsbyggare kan du använda [IoT Central](../iot-central/core/overview-iot-central.md) eller [IoT Hub](../iot-hub/about-iot-hub.md) för att utveckla en molnbaserad IoT-lösning som använder IoT-Plug and Play enheter.

Med webbgränssnittet i IoT Central du övervaka enhetsförhållanden, skapa regler och hantera miljontals enheter och deras data under hela livscykeln. IoT Plug and Play enheter ansluter direkt till ett IoT Central program där du kan använda anpassningsbara instrumentpaneler för att övervaka och kontrollera dina enheter. Du kan också använda enhetsmallar i IoT Central för att skapa och redigera DTDL-modeller.

IoT Hub – en hanterad molntjänst – fungerar som en meddelandehubb för säker dubbelriktad kommunikation mellan ditt IoT-program och dina enheter. När du ansluter en IoT Plug and Play-enhet till en IoT-hubb kan du använda [Verktyget Azure IoT Explorer](./howto-use-iot-explorer.md) för att visa telemetri, egenskaper och kommandon som definierats i DTDL-modellen.

Om du har befintliga sensorer som är anslutna till en Windows- eller Linux-gateway kan du använda [IoT Plug and Play Bridge](./concepts-iot-pnp-bridge.md)för att ansluta dessa sensorer och skapa IoT Plug and Play-enheter utan att behöva skriva programvara/inbyggd programvara för enheter (för protokoll som [stöds).](./concepts-iot-pnp-bridge.md#supported-protocols-and-sensors)

## <a name="develop-an-iot-device-application"></a>Utveckla ett IoT-enhetsprogram

Som enhetsbyggare kan du utveckla en IoT-maskinvaruprodukt som stöder IoT-Plug and Play. Processen omfattar tre viktiga steg:

1. Definiera enhetsmodellen. Du skapar en uppsättning JSON-filer som definierar enhetens funktioner med hjälp av [DTDL](https://github.com/Azure/opendigitaltwins-dtdl). En modell beskriver en fullständig entitet, till exempel en fysisk produkt, och definierar den uppsättning gränssnitt som implementeras av den entiteten. Gränssnitt är delade kontrakt som unikt identifierar telemetri, egenskaper och kommandon som stöds av en enhet. Gränssnitt kan återanvändas i olika modeller.

1. Skapa programvara eller inbyggd programvara för enheter på ett sätt som gör att deras telemetri, egenskaper och kommandon följer IoT Plug and Play konventionerna. Om du ansluter befintliga sensorer som är anslutna till en Windows- eller Linux-gateway kan [IoT Plug and Play bryggan](./concepts-iot-pnp-bridge.md) förenkla det här steget.

1. Enheten meddelar modell-ID:t som en del av MQTT-anslutningen. Azure IoT SDK innehåller nya konstruktioner för att tillhandahålla modell-ID vid anslutning.

> [!Important]
> IoT Plug and Play enheter måste använda MQTT eller MQTT över WebSockets. Andra protokoll som AMQP eller HTTP är inte giltiga för att implementera IoT-Plug and Play enheter.

## <a name="device-certification"></a>Enhetscertifiering

Programmet [för IoT Plug and Play enhetscertifiering](../certification/program-requirements-pnp.md) verifierar att en enhet uppfyller IoT Plug and Play certifieringskraven. Du kan lägga till en certifierad enhet i den offentliga [enhetskatalogen Certifierad för Azure IoT.](https://aka.ms/devicecatalog)

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över IoT Plug and Play är nästa steg att testa en av snabbstarterna:

- [Ansluta en enhet till IoT Hub](./quickstart-connect-device.md)
- [Interagera med en enhet från din lösning](./quickstart-service.md)
