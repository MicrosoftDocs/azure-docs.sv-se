---
title: Azure IoT-enhetshantering med Visual Studio Cloud Explorer
description: Använd Cloud Explorer för Visual Studio för Azure IoT Hub för enhetshantering, med Direct-metoder och de önskade egenskapshanteringsalternativen för tvillingen.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 96f93325e0f17daaaf2bad91123fea81531ca152
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566970"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Använda Cloud Explorer för Visual Studio för Azure IoT Hub enhetshantering

![Diagram från slutet till slut](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

I den här artikeln får du lära dig hur du använder Cloud Explorer för Visual Studio med olika hanteringsalternativ på utvecklingsdatorn. [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) är ett användbart Visual Studio-tillägg som gör att du kan visa dina Azure-resurser, inspektera deras egenskaper och utföra viktiga utvecklingsåtgärder Visual Studio. Den levereras med hanteringsalternativ som du kan använda för att utföra olika uppgifter.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Hanteringsalternativ          | Uppgift                    |
|----------------------------|--------------------------------|
| Direkta metoder             | Få en enhet att fungera, till exempel starta eller stoppa sändning av meddelanden eller starta om enheten.                                        |
| Läsa enhetstvilling           | Hämta det rapporterade tillståndet för en enhet. Enheten rapporterar till exempel att lysdioden blinkar nu.                                    |
| Uppdatera enhetstvilling         | Placera en enhet i vissa tillstånd, till exempel att ange en lysdiod till grön eller ställa in telemetrisändintervallet till 30 minuter.         |
| Meddelanden från moln till enhet   | Skicka meddelanden till en enhet. Till exempel " Det är mycket troligt regn idag. Glöm inte att ta med ett umbrella."              |

Mer detaljerad förklaring av skillnaderna och vägledningen [](iot-hub-devguide-d2c-guidance.md) om hur du använder dessa alternativ finns i Vägledning för kommunikation från enhet till moln och Vägledning för kommunikation från moln [till enhet.](iot-hub-devguide-c2d-guidance.md)

Enhetstvillingar är JSON-dokument som lagrar information om enhetstillstånd, som metadata, konfigurationer och villkor. IoT Hub en enhetstvilling för varje enhet som ansluter till den. Mer information om enhetstvillingarna finns i [Kom igång med enhetstvillingarna.](iot-hub-node-node-twin-getstarted.md)

## <a name="prerequisites"></a>Förutsättningar

- En aktiv Azure-prenumeration.

- En Azure IoT Hub under din prenumeration.

- Microsoft Visual Studio 2017 Uppdatering 9 eller senare. Den här artikeln [använder Visual Studio 2017 eller Visual Studio 2019](https://www.visualstudio.com/vs/).

- Cloud Explorer-komponent från Visual Studio Installer, som väljs som standard med Azure Workload.

## <a name="update-cloud-explorer-to-latest-version"></a>Uppdatera Cloud Explorer till den senaste versionen

Cloud Explorer-komponenten från Visual Studio Installer för Visual Studio 2017 stöder endast övervakning av meddelanden från enhet till moln och från moln till enhet. Om du Visual Studio 2017 laddar du ned och installerar den senaste [Versionen av Cloud Explorer.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)

## <a name="sign-in-to-access-your-hub"></a>Logga in för att få åtkomst till hubben

1. I Visual Studio du **Visa**  >  **Cloud Explorer för** att öppna Cloud Explorer.

1. Välj ikonen Kontohantering för att visa dina prenumerationer.

    ![Ikon för kontohantering](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Om du är inloggad på Azure visas dina konton. Om du vill logga in på Azure för första gången väljer du **Lägg till ett konto**.

1. Välj de Azure-prenumerationer som du vill använda och välj **Tillämpa.**

1. Expandera din prenumeration och expandera sedan **IoT Hubs**.  Under varje hubb kan du se dina enheter för hubben. Högerklicka på en enhet för att få åtkomst till hanteringsalternativen.

    ![Hanteringsalternativ](media/iot-hub-device-management-visual-studio/management-options-vs2019.png)

## <a name="direct-methods"></a>Direkta metoder

Om du vill använda direktmetoder gör du följande:

1. Högerklicka på enheten och välj **Invoke Device Direct Method (Anropa enhet direktmetod).**

1. Ange metodnamnet och nyttolasten **i Anropa direktmetod** och välj sedan **OK.**

    Resultaten visas i **Utdata**.

## <a name="update-device-twin"></a>Uppdatera enhetstvilling

Gör följande för att redigera en enhetstvilling:

1. Högerklicka på enheten och välj Redigera **enhetstvilling.**

   En **azure-iot-device-twin.jsfil** öppnas med innehållet i enhetstvillingen.

1. Gör några ändringar av **taggar eller** **properties.desired-fält** till **azure-iot-device-twin.jspå** filen.

1. Tryck **på Ctrl + S** för att uppdatera enhetstvillingen.

   Resultaten visas i **Utdata**.

## <a name="send-cloud-to-device-messages"></a>Skicka meddelanden från moln till enhet

Följ dessa steg om du IoT Hub skicka ett meddelande från din enhet:

1. Högerklicka på enheten och välj Skicka **C2D-meddelande.**

1. Ange meddelandet i Skicka **C2D-meddelande** och välj **OK.**

   Resultaten visas i **Utdata**.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du använder Cloud Explorer för Visual Studio med olika hanteringsalternativ.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
