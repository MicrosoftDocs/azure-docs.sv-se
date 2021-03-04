---
title: Visa din Azure percept DK-video ström
description: Lär dig att Visa RTSP-videoströmn från Azure percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 5f77e99dc5c34867fef2b0ac47c709824fa4477d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096011"
---
# <a name="view-your-azure-percept-dks-rtsp-video-stream"></a>Visa din Azure percept DK-video ström

Följ den här guiden för att Visa RTSP-videoströmn från Azure percept DK i Azure percept Studio. Inferencing från vision AI-modeller som distribueras till enheten visas i webb strömmen.

## <a name="prerequisites"></a>Förutsättningar

- Azure percept DK (devkit)
- [Azure-prenumeration](https://azure.microsoft.com/free/)
- [Installations upplevelse för Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): du anslöt din devkit till ett Wi-Fi nätverk, skapat ett IoT Hub och anslöt din devkit till IoT Hub

## <a name="view-the-rtsp-video-stream"></a>Visa RTSP-video strömmen

1. Slå på din devkit.

1. Gå till [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Klicka på **enheter** på vänster sida av översikts sidan.

    :::image type="content" source="./media/how-to-view-video-stream/overview-devices-inline.png" alt-text="Översikts skärm för Azure percept Studio." lightbox="./media/how-to-view-video-stream/overview-devices.png":::

1. Välj din devkit i listan.

    :::image type="content" source="./media/how-to-view-video-stream/select-device.png" alt-text="Skärm bild av tillgängliga enheter i Azure percept Studio.":::

1. Klicka på **Visa enhets strömmen**.

    :::image type="content" source="./media/how-to-view-video-stream/view-device-stream.png" alt-text="Skärm bild av sidan enhet med tillgängliga visioner-åtgärder.":::

    Då öppnas en separat flik som visar live-webbströmmen från din Azure percept DK.

    :::image type="content" source="./media/how-to-view-video-stream/webstream.png" alt-text="Skärm bild av enhetens webb ström.":::

## <a name="next-steps"></a>Nästa steg

Lär dig hur du visar din [Azure PERCEPT DK-telemetri](./how-to-view-telemetry.md).