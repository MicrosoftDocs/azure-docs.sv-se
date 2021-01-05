---
title: System krav
description: Hämta de system krav som krävs för att köra Azure Defender för IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/30/2020
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 8ee3afcae69ca6c082452e590eb8370bcc122af4
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97844514"
---
# <a name="system-prerequisites"></a>System krav
Den här artikeln innehåller en lista över system krav för att köra Azure Defender för IoT.

## <a name="minimum-requirements"></a>Minimikrav

- Nätverks växlar som stöder trafik övervakning via SPAN-port.
- Maskin varu anordningar för NTA-sensorer.
- Rollen Azure-prenumerations deltagare. Det krävs endast under onboarding för att definiera allokerade enheter och anslutning till Azure Sentinel.
- Azure IoT Hub- **deltagar** rollen (kostnads fri eller standard nivå) för moln ansluten hantering. Kontrol lera att **Azure Defender för IoT** -funktionen är aktive rad.
- För säkerhetsmodulen på enhets nivå stöder Defender för IoT-agenter en växande lista med enheter och plattformar. Se [listan över plattformar som stöds](how-to-deploy-agent.md).

## <a name="supported-service-regions"></a>Service regioner som stöds

Defender för IoT dirigerar all trafik från alla europeiska regioner till regionala Data Center för västra Europa. Den dirigerar trafik från alla återstående regioner till det centrala amerikanska regionala data centret.

Mer information finns i [IoT Hub regioner som stöds](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).

## <a name="see-also"></a>Se även

- [Identifiera nödvändiga enheter](how-to-identify-required-appliances.md)
- [Om installation av Azure Defender för IoT-nätverk](how-to-set-up-your-network.md)
