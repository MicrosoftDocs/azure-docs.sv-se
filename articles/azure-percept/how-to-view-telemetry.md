---
title: Visa din Azure Percepts DK-telemetri för modells härledning
description: Lär dig hur du visar din Azure percept DK-mall för att ta del av den här konfigurationen i Azure IoT Explorer
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: b03cd8bc71f87e3ce7984a55d330bc116614f928
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663932"
---
# <a name="view-your-azure-percept-dks-model-inference-telemetry"></a>Visa din Azure Percepts DK-telemetri för modells härledning

Följ den här guiden om du vill visa din Azure percept DK: s vision modells härlednings telemetri i [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases).

## <a name="prerequisites"></a>Förutsättningar

- Azure percept DK (devkit)
- [Azure-prenumeration](https://azure.microsoft.com/free/)
- [Installations upplevelse för Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): du anslöt din devkit till ett Wi-Fi nätverk, skapat ett IoT Hub och anslöt din devkit till IoT Hub
- [Vision AI-modellen har distribuerats till din Azure percept DK](./how-to-deploy-model.md)

## <a name="view-telemetry"></a>Visa telemetrin

1. Slå på din devkit.

1. Ladda ned och installera [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases). Om du är en Windows-användare väljer du MSI-filen.

    :::image type="content" source="./media/how-to-view-telemetry/azure-iot-explorer-download.png" alt-text="Hämtnings skärm för Azure IoT Explorer.":::

1. Anslut din IoT Hub till Azure IoT Explorer:

    1. Gå till [Azure-portalen](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

    1. Välj **Alla resurser**.

        :::image type="content" source="./media/how-to-view-telemetry/azure-portal.png" alt-text="Azure Portal start sida.":::

    1. Välj IoT Hub som din Azure percept DK är ansluten till.

        :::image type="content" source="./media/how-to-view-telemetry/iot-hub.png" alt-text="IoT Hub listan i Azure Portal.":::

    1. På vänster sida av IoT Hub sidan väljer du principer för **delad åtkomst**.

        :::image type="content" source="./media/how-to-view-telemetry/shared-access-policies.png" alt-text="IoT Hub sida som visar principer för delad åtkomst.":::

    1. Klicka på **iothubowner**.

        :::image type="content" source="./media/how-to-view-telemetry/iothubowner.png" alt-text="Fönstret principer för delad åtkomst med iothubowner markerat.":::

    1. Klicka på ikonen blå kopia bredvid **anslutnings strängen – primär nyckel**.

        :::image type="content" source="./media/how-to-view-telemetry/connection-string.png" alt-text="iothubowner-fönstret med kopierings knappen för anslutnings strängen markerat.":::

    1. Öppna Azure IoT Explorer och klicka på **+ Lägg till anslutning**.

    1. Klistra in anslutnings strängen i rutan **anslutnings sträng** i fönstret **Lägg till anslutnings sträng** och klicka på **Spara**.

        :::image type="content" source="./media/how-to-view-telemetry/add-connection-string.png" alt-text="Fönstret Azure IoT Explorer med box för att klistra in anslutnings strängen i.":::

    1. Peka insikten som ett objekt för modell inferencing.

    1. Välj **telemetri**.

    1. Klicka på **Starta** för att Visa telemetri-händelser från enheten.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du visar din [Azure PERCEPT DK-videoström](how-to-view-video-stream.md).