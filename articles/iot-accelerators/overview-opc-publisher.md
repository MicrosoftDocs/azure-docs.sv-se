---
title: Vad är OPC Publisher – Azure | Microsoft Docs
description: Den här artikeln innehåller en översikt över funktionerna i OPC Publisher. Det gör att du kan publicera kodade JSON-telemetridata med en JSON-nyttolast till Azure IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 49ca12ed4f408e2a3fce1c8e59f541778f35311e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91281787"
---
# <a name="what-is-opc-publisher"></a>Vad är OPC Publisher?

> [!IMPORTANT]
> Medan vi uppdaterar den här artikeln kan du läsa mer i [Azures industriella IoT](https://azure.github.io/Industrial-IoT/) .

OPC Publisher är en referens implementering som visar hur du:

- Anslut till befintliga OPC UA-servrar.
- Publicera JSON-kodade telemetridata från OPC UA-servrar i OPC UA pub/sub-format med en JSON-nyttolast till Azure IoT Hub.

Du kan använda något av de transport protokoll som Azure IoT Hub-klientens SDK stöder: HTTPS, AMQP och MQTT.

Referens implementeringen innehåller:

- En OPC UA- *klient* för att ansluta till befintliga OPC UA-servrar som du har i nätverket.
- En OPC UA- *Server* på port 62222 som du kan använda för att hantera vad som publiceras och erbjuder IoT Hub direkta metoder för att göra samma sak.

Du kan ladda ned [referens implementeringen OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) från GitHub.

Programmet implementeras med hjälp av .NET Core Technology och kan köras på alla plattformar som stöds av .NET Core.

OPC Publisher implementerar omprövnings logik för att upprätta anslutningar till slut punkter som inte svarar på ett visst antal Keep Alive-begäranden. Om en OPC UA-server till exempel slutar svara på grund av strömavbrott.

För varje distinkt publicerings intervall till en OPC UA-Server skapar programmet en separat prenumeration där alla noder med publicerings intervallet uppdateras.

OPC Publisher stöder batchbearbetning av data som skickas till IoT Hub för att minska belastningen på nätverket. Den här batchen skickar ett paket till IoT Hub endast om den konfigurerade paket storleken har nåtts.

I det här programmet används OPC Foundation OPC UA-referens stack som NuGet-paket. Se [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) för licens villkoren.

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig vad OPC Publisher är, och det föreslagna nästa steg är att lära dig att:

[Konfigurera OPC Publisher](howto-opc-publisher-configure.md)