---
title: Funktioner för ansluten fabriks lösningen – Azure | Microsoft Docs
description: I den här artikeln beskrivs en översikt över funktionerna i den förkonfigurerade lösningen ansluten fabrik, till exempel moln instrument panel, regler och aviseringar.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dobett
ms.openlocfilehash: e492ebf70f69c985691852a1c1f2351d1e09578e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92425016"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Vad är anslutet IoT Solution Accelerator för fabriks anslutning?

> [!IMPORTANT]
> Medan vi uppdaterar den här artikeln kan du läsa mer i [Azures industriella IoT](https://azure.github.io/Industrial-IoT/) .

Connected Factory är en implementering av Microsoft Azures referens arkitektur för industriella IoT som är paketerad som en lösning med öppen källkod. Du kan använda den som utgångs punkt för en kommersiell produkt. Du kan distribuera en fördefinierad version av den anslutna fabriks lösningen till din Azure-prenumeration från [Azure IoT Solution-acceleratorer](https://www.azureiotsolutions.com/#solutions/types/CF).

![Instrument panel för ansluten fabriks lösning](./media/iot-accelerators-connected-factory-features/dashboard.png)

Den anslutna fabriks lösnings acceleratorns [kod finns på GitHub](https://github.com/Azure/azure-iot-connected-factory).

Ansluten fabrik innehåller följande funktioner:

## <a name="industrial-device-interoperability"></a>Interoperabilitet för industriella enheter

- Anslut till industri till gångar med ett OPC UA-gränssnitt.
- Använd de simulerade produktions linjerna (som kör OPC UA-servrar i Docker-behållare) om du vill visa Live-telemetri från dem.
- Bläddra i OPC UA-informations modell för OPC UA-servrar från en moln instrument panel.

## <a name="remote-management"></a>Fjärrhantering

- Konfigurera dina OPC UA-tillgångar från moln instrument panelen (anrops metoder, läsa och skriva data).
- Publicera och avpublicera telemetridata från OPC UA-tillgångar från en instrument panel i molnet.

## <a name="cloud-dashboard"></a>Moln instrument panel

- Visa för hands versionerna av telemetri direkt i en instrument panel i molnet.
- Visa trender i telemetridata och skapa korrelationer med hjälp av instrument panelen i Time Series Insights Explorer.
- Se Beräknad total utrustnings effektivitet (OEE) och nyckeltal (KPI: er) från en instrument panel i molnet.
- Visa hierarkier för industriella till gångar i en träd topologi och på en interaktiv karta.
- Visa, bekräfta och Stäng aviseringar från en instrument panel i molnet.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) har skapats för att lagra, visualisera och skicka frågor till stora mängder tids serie data. Ansluten fabrik utnyttjar den här tjänsten.
- Ansluten fabrik integreras med den här tjänsten och gör att du kan utföra djup analys av enhets data i real tid.

## <a name="rules-and-alerts"></a>Regler och aviseringar

[Konfigurera tröskelbaserade regler för aviseringar](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Säkerhet från slut punkt till slut punkt

- Konfigurera säkerhets behörigheter för användare med rollbaserad åtkomst kontroll (RBAC).
- Kryptering från slut punkt till slut punkt implementeras med hjälp av OPC UA-autentisering (med X. 509-certifikat) samt säkerhetstoken.

## <a name="customizability"></a>Anpassningsbarhet

- Anpassa lösningen efter specifika affärs behov.
- Fullständig lösnings källa – kod finns på GitHub. Se lagrings platsen för den [förkonfigurerade lösningen ansluten fabrik](https://github.com/Azure/azure-iot-connected-factory) .

## <a name="next-steps"></a>Nästa steg

Mer information om den anslutna fabriks lösnings acceleratorn finns i snabb start [prova en molnbaserad lösning för att hantera mina industriella IoT-enheter](quickstart-connected-factory-deploy.md).
