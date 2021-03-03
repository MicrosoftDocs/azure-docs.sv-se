---
title: Distribuera en vision AI-modell till din Azure percept DK
description: Lär dig hur du distribuerar en vision AI-modell till din Azure percept DK från Azure percept Studio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 5ad263f5ae3b0b8e91de30b620002e33086608b4
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663792"
---
# <a name="capture-images-for-a-vision-project"></a>Avbilda avbildningar för ett syn projekt

Följ den här guiden för att avbilda bilder med hjälp av den vision som finns i Azure percept DK för ett befintligt syn projekt i Azure percept Studio. Om du inte har skapat ett vision-projekt ännu kan du läsa [själv studie kursen utan kod](./tutorial-nocode-vision.md).

## <a name="prerequisites"></a>Förutsättningar

- Azure percept DK (devkit)
- [Azure-prenumeration](https://azure.microsoft.com/free/)
- [Installations upplevelse för Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): du anslöt din devkit till ett Wi-Fi nätverk, skapat ett IoT Hub och anslöt din devkit till IoT Hub
- [Projekt utan kod vision](./tutorial-nocode-vision.md)

## <a name="capture-images"></a>Avbilda avbildningar

1. Slå på din devkit.

1. Gå till [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Klicka på **enheter** på vänster sida av översikts sidan.

    :::image type="content" source="./media/how-to-capture-images/overview-devices-inline.png" alt-text="Översikts skärm för Azure percept Studio." lightbox="./media/how-to-capture-images/overview-devices.png":::

1. Välj din devkit i listan.

    :::image type="content" source="./media/how-to-capture-images/select-device.png" alt-text="Lista med percept-enheter.":::

1. På din enhets sida klickar du på **avbilda avbildningar för ett projekt**.

    :::image type="content" source="./media/how-to-capture-images/capture-images.png" alt-text="Sidan percept-enheter med tillgängliga åtgärder visas.":::

1. Gör följande i fönstret **Skapa avbildning** :

    1. I list rutan **projekt** väljer du det vision-projekt som du vill samla in bilder för.

    1. Klicka på **Visa enhets ström** för att se till att kamerans vision är korrekt placerad.

    1. Klicka på **ta foto** för att avbilda en bild.

    1. Du kan också markera kryss rutan bredvid **Automatisk bild hämtning** för att ställa in en timer för avbildnings avbildning:

        1. Välj önskad bild hastighet under **avbildnings takten**.
        1. Välj det totala antalet avbildningar som du vill samla in under **mål**.

    :::image type="content" source="./media/how-to-capture-images/take-photo.png" alt-text="Sidan percept-enheter med tillgängliga åtgärder visas.":::

Alla avbildningar är tillgängliga i [Custom vision](https://www.customvision.ai/).

## <a name="next-steps"></a>Nästa steg

[Testa och träna om din modell utan kod](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/test-your-model).