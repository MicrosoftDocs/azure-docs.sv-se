---
title: 'Snabb start: system krav'
description: I den här snabb starten får du de system krav som krävs för att köra Azure Defender för IoT.
ms.date: 11/30/2020
ms.topic: quickstart
ms.openlocfilehash: 2aae849b6ee772c2aa29c680f3b107af3ed600b0
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382954"
---
# <a name="quickstart-system-prerequisites"></a>Snabb start: system krav

Den här artikeln innehåller en lista över system krav för att köra Azure Defender för IoT.

## <a name="prerequisites"></a>Krav

- Inget

## <a name="minimum-requirements"></a>Minimikrav

- Nätverks växlar som stöder trafik övervakning via SPAN-port.
- Maskin varu anordningar för NTA-sensorer.
- Rollen Azure-prenumerations deltagare. Det krävs endast under onboarding för att definiera allokerade enheter och anslutning till Azure Sentinel.
- Azure IoT Hub- **deltagar** rollen (kostnads fri eller standard nivå) för moln ansluten hantering. Kontrol lera att **Azure Defender för IoT** -funktionen är aktive rad.
- För stöd på enhets nivå Defender-IoT-Micro-agent har Defender för IoT-agenter stöd för en växande lista över enheter och plattformar. Se [listan över plattformar som stöds](how-to-deploy-agent.md).

## <a name="supported-service-regions"></a>Service regioner som stöds

Defender för IoT dirigerar all trafik från alla europeiska regioner till regionala Data Center för västra Europa. Den dirigerar trafik från alla återstående regioner till det centrala amerikanska regionala data centret.

Mer information finns i [IoT Hub regioner som stöds](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Identifiera apparater som krävs](how-to-identify-required-appliances.md)
