---
title: Systemkrav
description: Hämta de system krav som krävs för att köra Azure Defender för IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/30/2020
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 4b5db049e6d1cfe76bdd0d5cd6d7360e0b98bad0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489819"
---
# <a name="system-prerequisites"></a>Systemkrav
Den här artikeln innehåller en lista över system krav för att köra Azure Defender för IoT.

## <a name="minimum-requirements"></a>Minimikrav

- Nätverks växlar som stöder trafik övervakning via SPAN-port.
- Maskin varu anordningar för NTA-sensorer.
- Rollen Azure-prenumerations deltagare. Det krävs endast under onboarding för att definiera allokerade enheter och anslutning till Azure Sentinel.
- Azure IoT Hub- **deltagar** rollen (kostnads fri eller standard nivå) för moln ansluten hantering. Kontrol lera att **Azure Defender för IoT** -funktionen är aktive rad.
- För stöd på enhets nivå Defender-IoT-Micro-agent har Defender för IoT-agenter stöd för en växande lista över enheter och plattformar. Se [listan över plattformar som stöds](how-to-deploy-agent.md).

## <a name="supported-service-regions"></a>Service regioner som stöds

Defender för IoT dirigerar all trafik från alla europeiska regioner till regionala Data Center för västra Europa. Den dirigerar trafik från alla återstående regioner till det centrala amerikanska regionala data centret.

Mer information finns i [IoT Hub regioner som stöds](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).

## <a name="see-also"></a>Se även

- [Identifiera apparater som krävs](how-to-identify-required-appliances.md)
- [Om installation av Azure Defender för IoT-nätverk](how-to-set-up-your-network.md)
