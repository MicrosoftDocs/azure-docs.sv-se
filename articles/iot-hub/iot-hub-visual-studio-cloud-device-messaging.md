---
title: Använda VS Cloud Explorer för att hantera Azure IoT Hub enhets meddelanden
description: Lär dig hur du använder Cloud Explorer för Visual Studio för att övervaka enhet till moln meddelanden och skicka molnet till enhets meddelanden i Azure IoT Hub.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: c56bb7030b2ebc12e3afc24e2d8cb29ce2dda0bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "74079482"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Använd Cloud Explorer för Visual Studio för att skicka och ta emot meddelanden mellan enheten och IoT Hub

![Diagram från slut punkt till slut punkt](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) är ett användbart Visual Studio-tillägg som gör att du kan visa dina Azure-resurser, inspektera deras egenskaper och utföra åtgärder för att utveckla och utveckla dem i Visual Studio. Den här artikeln fokuserar på hur du använder Cloud Explorer för att skicka och ta emot meddelanden mellan din enhet och hubben.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-learn"></a>Detta får du får lära dig

I den här artikeln får du lära dig hur du använder Cloud Explorer för Visual Studio för att övervaka meddelanden från enheten till molnet och för att skicka meddelanden från moln till enhet. Meddelanden från enheten till molnet kan vara sensor data som samlas in av enheten och sedan skickas till din IoT Hub. Meddelanden från moln till enhet kan vara kommandon som IoT Hub skickar till din enhet. Du kan till exempel blinka med en indikator som är ansluten till din enhet.

## <a name="what-you-do"></a>Vad du gör

I den här artikeln utför du följande uppgifter:

- Använd Cloud Explorer för Visual Studio för att övervaka meddelanden från enheten till molnet.

- Använd Cloud Explorer för Visual Studio för att skicka meddelanden från molnet till enheten.

## <a name="what-you-need"></a>Detta behöver du

Du behöver följande krav:

- En aktiv Azure-prenumeration.

- En Azure-IoT Hub under din prenumeration.

- Microsoft Visual Studio 2017 Update 9 eller senare. I den här artikeln används [Visual Studio 2019](https://www.visualstudio.com/vs/).

- Cloud Explorer-komponenten från Visual Studio Installer, som är markerad som standard med Azure-arbetsbelastningar.

## <a name="update-cloud-explorer-to-latest-version"></a>Uppdatera Cloud Explorer till den senaste versionen

Cloud Explorer-komponenten från Visual Studio Installer för Visual Studio 2017 stöder bara övervakning av enhet-till-moln-och moln-till-enhet-meddelanden. Hämta och installera den senaste [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)om du vill använda Visual Studio 2017.

## <a name="sign-in-to-access-your-hub"></a>Logga in för att komma åt hubben

Följ dessa steg för att få åtkomst till hubben:

1. I Visual Studio väljer du **Visa**  >  **Cloud Explorer** för att öppna Cloud Explorer.

1. Välj ikonen för konto hantering för att visa dina prenumerationer.

    ![Ikon för konto hantering](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Om du är inloggad på Azure visas dina konton. Om du vill logga in på Azure för första gången väljer du **Lägg till ett konto**.

1. Välj de Azure-prenumerationer som du vill använda och välj **Använd**.

1. Expandera din prenumeration och expandera **IoT-hubbar**.  Under varje hubb kan du se dina enheter för den hubben.

    ![Enhets lista](media/iot-hub-visual-studio-cloud-device-messaging/hub-device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Övervaka meddelanden från enheten till molnet

Följ dessa steg om du vill övervaka meddelanden som skickas från enheten till IoT Hub:

1. Högerklicka på din IoT Hub eller enhet och välj **starta övervakning D2C-meddelande**.

    ![Starta övervakning av D2C-meddelande](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message-vs2019.png)

1. De övervakade meddelandena visas under **utdata**.

    ![Övervaka D2C meddelande resultat](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result-vs2019.png)

1. Om du vill stoppa övervakningen högerklickar du på någon IoT Hub eller enhet och väljer **stoppa övervakning D2C-meddelande**.

## <a name="send-cloud-to-device-messages"></a>Skicka meddelanden från moln till enhet

Följ dessa steg om du vill skicka ett meddelande från IoT Hub till din enhet:

1. Högerklicka på enheten och välj **Skicka C2D-meddelande**.

1. Ange meddelandet i rutan indatamängd.

    ![Skicka C2D-meddelande](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-test.png)

    Resultaten visas under **utdata**.

    ![Skicka C2D meddelande resultat](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result-vs2019.png)

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du övervakar meddelanden från enheten till molnet och skickar meddelanden från molnet till enheten mellan din IoT-enhet och Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]