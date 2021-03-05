---
title: Översikt över Azure percept DK
description: Läs mer om Azure percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: c93ecdc138e757f84e995a69a6c3d7f1b5460abe
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179382"
---
# <a name="azure-percept-dk-overview"></a>Översikt över Azure percept DK

Azure percept DK är ett Edge AI och IoT Development Kit som utformats för att utveckla koncept för vision och Audio AI. När de kombineras med [Azure percept Studio](./overview-azure-percept-studio.md) -och [Azure percept-ljud](./overview-azure-percept-audio.md), blir det en kraftfull plattform som du kan använda för att bygga Edge AI-lösningar för en mängd olika vision-eller audio AI-program. Den kan köpas på [Microsoft Online Store](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

:::image type="content" source="./media/overview-azure-percept-dk/dk-image.png" alt-text="Azure percept DK-enhet.":::

## <a name="key-features"></a>Viktiga funktioner

- **Möjligheten att köra AI på gränsen**. Med inbyggd maskin varu acceleration kan den köra vision AI-modeller utan anslutning till molnet.
- **Inbyggd förtroende säkerhet i maskin varu roten**. Mer information finns i den här översikten över [Azure percept Security](./overview-percept-security.md) .
- **Sömlös integrering med [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)** och andra Azure-tjänster. Till exempel Azure IoT Hub, Azure Cognitive Services och [live video analys](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/overview)
- **Sömlös integrering med valfritt [Azure percept-ljud](./overview-azure-percept-audio.md)**
- **Stöd för de viktigaste AI-plattformarna**. Till exempel ONNX och TensorFlow.
- **Integrering med 80/20-järnvägs systemet**. Gör det enklare att skapa prototyper i produktions miljöer. Läs mer om [80/20-integrering](./overview-8020-integration.md).

## <a name="hardware-components"></a>Maskin varu komponenter

- Azure percept DK-tavlan
    - NXP iMX8m-processor
    - Trusted Platform Module (TPM) version 2,0
    - WiFi-och Bluetooth-anslutning
    - Se hela [data bladet](./azure-percept-dk-datasheet.md)
- Azure percept vision-systemet för modul (som)
    - Intel Movidius myriaden X (MA2085) vision Processing Unit (VPU)
    - RGB kamera sensor med möjlighet att lägga till en andra
    - Se hela [data bladet](./azure-percept-vision-datasheet.md)

## <a name="get-started-with-the-azure-percept-dk"></a>Kom igång med Azure percept DK

- Slutför de här snabb starterna
    - [Avpaketera och montera Azure percept DK](./quickstart-percept-dk-unboxing.md)
    - [Konfigurera Azure percept DK och kör din första vision AI-modell](./quickstart-percept-dk-set-up.md)
- Börja bygga bevis på begrepp med de här självstudierna
    - [Skapa en lösning utan kod i Azure percept Studio](./tutorial-nocode-vision.md)
    - [Skapa en röst assistent i Azure percept Studio](./tutorial-no-code-speech.md)

## <a name="next-steps"></a>Nästa steg

Beställ en Azure percept DK på [Microsoft Online Store](https://go.microsoft.com/fwlink/p/?LinkId=2155270).
