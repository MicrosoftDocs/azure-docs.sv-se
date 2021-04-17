---
title: Använda VS Cloud Explorer för att hantera Azure IoT Hub enhetsmeddelanden
description: Lär dig hur du använder Cloud Explorer för Visual Studio för att övervaka meddelanden från enheten till molnet och skicka meddelanden från molnet till Azure IoT Hub.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 8461a77d06a63c2ac319323a91b5577ca4dce1cf
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567038"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Använd Cloud Explorer för Visual Studio att skicka och ta emot meddelanden mellan enheten och IoT Hub

![Diagram från slutet till slut](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

I den här artikeln får du lära dig hur du använder Cloud Explorer för Visual Studio för att övervaka "enhet till molnet"-meddelanden och för att skicka meddelanden från molnet till enheten. Meddelanden från enhet till moln kan vara sensordata som enheten samlar in och sedan skickar till din IoT Hub. Meddelanden från moln till enhet kan vara kommandon som IoT Hub skickar till enheten. Du kan till exempel blinka en lysdiod som är ansluten till enheten.

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) är ett användbart Visual Studio-tillägg som gör att du kan visa dina Azure-resurser, inspektera deras egenskaper och utföra viktiga utvecklingsåtgärder Visual Studio. Den här artikeln fokuserar på hur du använder Cloud Explorer för att skicka och ta emot meddelanden mellan din enhet och din hubb.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="prerequisites"></a>Förutsättningar

- En aktiv Azure-prenumeration.

- En Azure IoT Hub under din prenumeration.

- Microsoft Visual Studio 2017 Update 9 eller senare. Den här artikeln [använder Visual Studio 2019](https://www.visualstudio.com/vs/).

- Cloud Explorer-komponenten från Visual Studio Installer, som väljs som standard med Azure Workload.

## <a name="update-cloud-explorer-to-latest-version"></a>Uppdatera Cloud Explorer till den senaste versionen

Cloud Explorer-komponenten från Visual Studio Installer för Visual Studio 2017 stöder endast övervakning av meddelanden från enhet till moln och från moln till enhet. Om du Visual Studio 2017 laddar du ned och installerar den senaste [Versionen av Cloud Explorer.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)

## <a name="sign-in-to-access-your-hub"></a>Logga in för att få åtkomst till hubben

Följ dessa steg för att få åtkomst till hubben:

1. I Visual Studio du Visa  >  **Cloud Explorer för** att öppna Cloud Explorer.

1. Välj ikonen Kontohantering för att visa dina prenumerationer.

    ![Ikon för kontohantering](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Om du är inloggad på Azure visas dina konton. Om du vill logga in på Azure för första gången väljer du **Lägg till ett konto**.

1. Välj de Azure-prenumerationer som du vill använda och välj **Tillämpa**.

1. Expandera din prenumeration och expandera sedan **IoT Hubs**.  Under varje hubb kan du se dina enheter för hubben.

    ![Enhetslista](media/iot-hub-visual-studio-cloud-device-messaging/hub-device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Övervaka meddelanden från enhet till moln

Följ dessa steg om du vill övervaka meddelanden som skickas IoT Hub enheten till din enhet:

1. Högerklicka på din IoT Hub enhet och välj **Starta övervakning av D2C-meddelande.**

    ![Börja övervaka D2C-meddelande](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message-vs2019.png)

1. De övervakade meddelandena visas under **Utdata**.

    ![Övervaka D2C-meddelanderesultat](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result-vs2019.png)

1. Om du vill stoppa övervakningen högerklickar du på IoT Hub enhet och väljer **Stoppa övervakning av D2C-meddelande.**

## <a name="send-cloud-to-device-messages"></a>Skicka meddelanden från moln till enhet

Följ dessa steg om du IoT Hub skicka ett meddelande från din enhet:

1. Högerklicka på enheten och välj Skicka **C2D-meddelande.**

1. Ange meddelandet i inmatningsrutan.

    ![Skicka C2D-meddelande](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-test.png)

    Resultaten visas under **Utdata**.

    ![Skicka C2D-meddelanderesultat](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result-vs2019.png)

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du övervakar "enhet till molnet"-meddelanden och skickar meddelanden från moln till enhet mellan din IoT-enhet och Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]