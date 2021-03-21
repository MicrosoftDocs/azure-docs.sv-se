---
title: Introduktion till IoT Plug and Play | Microsoft Docs
description: Lär dig mer om IoT Plug and Play. IoT Plug and Play baseras på ett öppet modellerings språk som gör det möjligt för smarta IoT-enheter att deklarera sina funktioner. IoT-enheter presenterar deklarationen, som kallas en enhets modell, när de ansluter till moln lösningar. Moln lösningen kan sedan automatiskt förstå enheten och börja interagera med den, utan att behöva skriva någon kod.
author: rido-min
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.custom: references_regions
ms.openlocfilehash: dcdd19faec5e428ac26917178aa8114245c205b3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594577"
---
# <a name="what-is-iot-plug-and-play"></a>Vad är IoT Plug and Play?

Med IoT Plug and Play kan lösningsutvecklare integrera smarta enheter med sina lösningar utan manuell konfiguration. I IoT Plug and Play är en enhets _modell_ som en enhet använder för att annonsera sina funktioner till ett IoT plug and Play-aktiverat program. Den här modellen är strukturerad som en uppsättning element som definierar:

- _Egenskaper_ som representerar ett skrivskyddat eller skrivbart tillstånd för en enhet eller annan enhet. Ett enhets serie nummer kan till exempel vara en skrivskyddad egenskap och en mål temperatur på en termostat kan vara en skrivbar egenskap.
- _Telemetri_ som är data som skickas av en enhet, oavsett om datan är en vanlig ström av sensor avläsningar, ett tillfälligt fel eller ett informations meddelande.
- _Kommandon_ som beskriver en funktion eller åtgärd som kan utföras på en enhet. Ett kommando kan till exempel starta om en gateway eller ta en bild med en fjärran sluten kamera.

Du kan gruppera dessa element i gränssnitt för att kunna använda olika modeller för att förenkla samarbetet och påskynda utvecklingen.

Om du vill att IoT Plug and Play fungerar med [Azure Digitals](../digital-twins/overview.md), definierar du modeller och gränssnitt med hjälp av [DTDL (Digitals Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl). IoT Plug and Play och DTDL är öppna för communityn och Microsoft välkomnar samarbetet med kunder, partner och branschen. Båda baseras på öppna W3C-standarder som JSON-LD och RDF, vilket gör det enklare att införa tjänster och verktyg.

Det kostar inget extra att använda IoT Plug and Play och DTDL. Standardpriserna för [azure IoT Hub](../iot-hub/about-iot-hub.md) och andra Azure-tjänster förblir desamma.

Den här artikeln beskriver:

- Vanliga roller som är kopplade till ett projekt som använder IoT Plug and Play.
- Använda IoT Plug and Play-enheter i ditt program.
- Utveckla ett IoT-enhets program som stöder IoT Plug and Play.

## <a name="user-roles"></a>Användarroller

IoT Plug and Play är användbart för två typer av utvecklare:

- En _lösnings byggare_ ansvarar för att utveckla en IoT-lösning med Azure IoT Hub och andra Azure-resurser och för att identifiera IoT-enheter som ska integreras.
- En _Device Builder_ skapar den kod som körs på en enhet som är ansluten till din lösning.

## <a name="use-iot-plug-and-play-devices"></a>Använda IoT Plug and Play-enheter

Som Solution Builder kan du använda [IoT Central](../iot-central/core/overview-iot-central.md) eller [IoT Hub](../iot-hub/about-iot-hub.md) för att utveckla en lösning för en molnbaserad IoT-lösning som använder IoT plug and Play-enheter.

Med webb gränssnittet i IoT Central kan du övervaka enhets villkor, skapa regler och hantera miljon tals enheter och deras data under hela livs cykeln. IoT Plug and Play-enheter ansluter direkt till ett IoT Central program där du kan använda anpassningsbara instrument paneler för att övervaka och kontrol lera dina enheter. Du kan också använda enhetsspecifika i IoT Central-webbgränssnittet för att skapa och redigera DTDL-modeller.

IoT Hub – en hanterad moln tjänst – fungerar som en meddelande hubb för säker, dubbelriktad kommunikation mellan IoT-programmet och dina enheter. När du ansluter en IoT Plug and Play-enhet till en IoT-hubb kan du använda [Azure IoT Explorer](./howto-use-iot-explorer.md) -verktyget för att Visa telemetri, egenskaper och kommandon som definierats i DTDL-modellen.

Om du har befintliga sensorer som är kopplade till en Windows-eller Linux-Gateway kan du använda [IoT plug and Play Bridge](./concepts-iot-pnp-bridge.md)för att ansluta sensorer och skapa IoT plug and Play-enheter utan att behöva skriva enhets program/inbyggd program vara (för [protokoll som stöds](./concepts-iot-pnp-bridge.md#supported-protocols-and-sensors)).

## <a name="develop-an-iot-device-application"></a>Utveckla ett IoT-enhets program

Som enhets byggare kan du utveckla en IoT-maskinvara som stöder IoT Plug and Play. Processen innehåller tre viktiga steg:

1. Definiera enhets modellen. Du skapar en uppsättning JSON-filer som definierar enhetens funktioner med hjälp av [DTDL](https://github.com/Azure/opendigitaltwins-dtdl). En modell beskriver en fullständig entitet, till exempel en fysisk produkt, och definierar den uppsättning gränssnitt som implementeras av entiteten. Gränssnitt är delade kontrakt som unikt identifierar telemetri, egenskaper och kommandon som stöds av en enhet. Gränssnitt kan återanvändas mellan olika modeller.

1. Redigera enhets program vara eller inbyggd program vara på ett sätt som deras telemetri, egenskaper och kommandon följer IoT Plug and Play konventioner. Om du ansluter befintliga sensorer som är anslutna till en Windows-eller Linux-Gateway kan [IoT plug and Play-bryggan](./concepts-iot-pnp-bridge.md) förenkla det här steget.

1. Enheten meddelar modell-ID som en del av MQTT-anslutningen. Azure IoT SDK innehåller nya konstruktioner som ger modell-ID vid anslutnings tiden.

> [!Important]
> IoT Plug and Play-enheter måste använda MQTT eller MQTT över WebSockets. Andra protokoll som AMQP eller HTTP är inte giltiga för att implementera IoT Plug and Play-enheter.

## <a name="device-certification"></a>Enhetscertifiering

[Iot plug and Play enhets certifierings programmet](howto-certify-device.md) verifierar att en enhet uppfyller IoT-plug and Play certifierings krav. Du kan lägga till en certifierad enhet i [katalogen för offentlig certifierad för Azure IoT-enheter](https://aka.ms/devicecatalog).

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över IoT Plug and Play är det föreslagna nästa steg att prova en av snabb starterna:

- [Ansluta en enhet till IoT Hub](./quickstart-connect-device.md)
- [Interagera med en enhet från din lösning](./quickstart-service.md)
