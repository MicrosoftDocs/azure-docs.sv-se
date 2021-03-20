---
title: Azure IoT Central Video Analytics-objekt och rörelse identifiering | Microsoft Docs
description: Lär dig att bygga ett IoT Central program med hjälp av program mal len video analys – objekt och rörelse identifiering i IoT Central. Den här mallen använder video analys och anslutna kameror.
author: KishorIoT
ms.author: nandab
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 48808f762536390287bae40e8af3849da20b81c2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91874296"
---
# <a name="video-analytics---object-and-motion-detection-application-architecture"></a>Video analys – program arkitektur för objekt och motion-identifiering

Med program mal len **video analys – objekt och rörelse identifiering** kan du bygga IoT-lösningar med funktioner för video analys i real tid.

:::image type="content" source="media/architecture-video-analytics/architecture.png" alt-text="Diagram över översikt över video analys objekt och rörelse identifierings komponenter.":::

Viktiga komponenter i video analys lösningen är:

## <a name="live-video-analytics-lva"></a>Video analys i real tid (LVA)

LVA tillhandahåller en plattform där du kan bygga intelligenta video program som spänner över gränsen och molnet. Plattformen gör att du kan bygga intelligenta video program som spänner över gränsen och molnet. Plattformen ger möjlighet att samla in, spela in, analysera direktsänd video och publicera resultaten, som kan vara video-eller video analyser till Azure-tjänster. Azure-tjänsterna kan köras i molnet eller på gränsen. Plattformen kan användas för att förbättra IoT-lösningar med video analys.

Mer information finns i [video analys i Real](https://github.com/Azure/live-video-analytics) tid på GitHub.

## <a name="iot-edge-lva-gateway-module"></a>IoT Edge LVA Gateway-modul

IoT Edge LVA Gateway-modulen instansierar kameror som nya enheter och ansluter dem direkt till IoT Central med hjälp av IoT-enhetens klient-SDK.

I den här referens implementeringen ansluter enheterna till lösningen med symmetriska nycklar från kanten. Mer information om enhets anslutning finns i Anslut [till Azure IoT Central](../core/concepts-get-connected.md)

## <a name="media-graph"></a>Mediegraf

Med Media Graph kan du definiera var du vill avbilda mediet från, hur det ska bearbetas och var resultatet ska skickas. Du konfigurerar medie diagram genom att ansluta komponenter eller noder på önskat sätt. Mer information finns i [medie diagram](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph) på GitHub.

## <a name="next-steps"></a>Nästa steg

Det föreslagna nästa steg är att lära dig hur du [distribuerar ett IoT Central program med hjälp av program mal len video analys – objekt och rörelse identifiering](tutorial-video-analytics-deploy.md).
