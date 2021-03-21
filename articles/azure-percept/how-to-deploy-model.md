---
title: Distribuera en vision AI-modell till din Azure percept DK
description: Lär dig hur du distribuerar en vision AI-modell till din Azure percept DK från Azure percept Studio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 01bd3709050d8a2b57c1bf51920308188546fb31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035491"
---
# <a name="deploy-a-vision-ai-model-to-your-azure-percept-dk"></a>Distribuera en vision AI-modell till din Azure percept DK

Följ den här guiden för att distribuera en vision AI-modell till din Azure percept DK inifrån Azure percept Studio.

## <a name="prerequisites"></a>Förutsättningar

- Azure percept DK (devkit)
- [Azure-prenumeration](https://azure.microsoft.com/free/)
- [Installations upplevelse för Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): du anslöt din devkit till ett Wi-Fi nätverk, skapat ett IoT Hub och anslöt din devkit till IoT Hub

## <a name="model-deployment"></a>Modelldistribution

1. Slå på din devkit.

1. Gå till [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Klicka på **enheter** på vänster sida av översikts sidan.

    :::image type="content" source="./media/how-to-deploy-model/overview-devices-inline.png" alt-text="Översikts skärm för Azure percept Studio." lightbox="./media/how-to-deploy-model/overview-devices.png":::

1. Välj din devkit i listan.

    :::image type="content" source="./media/how-to-deploy-model/select-device.png" alt-text="Lista med percept-enheter.":::

1. På nästa sida klickar du på **distribuera en exempel modell** om du vill distribuera en av de förtränade exempel Visions modellerna. Om du vill distribuera en befintlig [anpassad lösning utan kod](./tutorial-nocode-vision.md)klickar du på **distribuera ett Custom vision projekt**.

    :::image type="content" source="./media/how-to-deploy-model/deploy-model.png" alt-text="Modell val för distribution.":::

1. Om du valde att distribuera en lösning utan kod, väljer du projektet och önskad modell iteration och klickar på **distribuera**.

1. Om du har valt att distribuera en exempel modell väljer du modellen och klickar på **distribuera till enhet**.

1. När modell distributionen lyckas får du ett status meddelande i det övre högra hörnet på skärmen. Om du vill se hur din modell inferencing fungerar klickar du på länken **Visa ström** i status meddelandet för att Visa RTSP-videoströmmen från den vision som du har devkit.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du visar din [Azure PERCEPT DK-telemetri](how-to-view-telemetry.md).