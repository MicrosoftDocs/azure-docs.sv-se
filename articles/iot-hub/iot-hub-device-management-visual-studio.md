---
title: Azure IoT enhets hantering med Visual Studio Cloud Explorer
description: Använd Cloud Explorer för IoT Hub enhets hantering i Azure för Azure enhets hantering, med de direkta metoderna och de dubbla alternativen för önskade egenskaper för hantering.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 6fe5a45dda6632c56b3c6714827950e25e7d26af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "73953182"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Använd Cloud Explorer för Visual Studio för Azure IoT Hub enhets hantering

![Diagram från slut punkt till slut punkt](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) är ett användbart Visual Studio-tillägg som gör att du kan visa dina Azure-resurser, inspektera deras egenskaper och utföra åtgärder för att utveckla och utveckla dem i Visual Studio. Den innehåller hanterings alternativ som du kan använda för att utföra olika uppgifter.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Hanterings alternativ          | Uppgift                    |
|----------------------------|--------------------------------|
| Direkta metoder             | Se till att en enhet fungerar som att starta eller stoppa sändning av meddelanden eller starta om enheten.                                        |
| Läs enhetens dubbla           | Hämta rapporterat tillstånd för en enhet. Enheten rapporterar till exempel LAMPAn att blinka nu.                                    |
| Uppdatera enhet, dubbla         | Placera en enhet i vissa tillstånd, till exempel att ställa in en indikator på grönt eller ställa in ett intervall för telemetri-sändning till 30 minuter.         |
| Meddelanden från moln till enhet   | Skicka meddelanden till en enhet. Till exempel "det är mycket sannolikt att regna idag. Glöm inte att ta ett paraply. "              |

Mer detaljerad information om skillnaderna och vägledning om hur du använder dessa alternativ finns i [kommunikations vägledning för enhet till moln](iot-hub-devguide-d2c-guidance.md) och [kommunikation från moln till enhet](iot-hub-devguide-c2d-guidance.md).

Enhetstvillingar är JSON-dokument som lagrar information om enhetstillstånd, som metadata, konfigurationer och villkor. IoT Hub sparar en enhet för varje enhet som ansluter till den. Mer information om enhets uppflätade finns i [Kom igång med enhets dubbla](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Detta får du får lära dig

I den här artikeln får du lära dig hur du använder Cloud Explorer för Visual Studio med olika hanterings alternativ på din utvecklings dator.

## <a name="what-you-do"></a>Vad du gör

I den här artikeln kör du Cloud Explorer för Visual Studio med olika hanterings alternativ.

## <a name="what-you-need"></a>Detta behöver du

Du behöver följande krav:

- En aktiv Azure-prenumeration.

- En Azure-IoT Hub under din prenumeration.

- Microsoft Visual Studio 2017 Update 9 eller senare. I den här artikeln används [Visual studio 2017 eller Visual studio 2019](https://www.visualstudio.com/vs/).

- Cloud Explorer-komponenten från Visual Studio Installer, som väljs som standard med Azure-arbetsbelastningar.

## <a name="update-cloud-explorer-to-latest-version"></a>Uppdatera Cloud Explorer till den senaste versionen

Cloud Explorer-komponenten från Visual Studio Installer för Visual Studio 2017 stöder bara övervakning av enhet-till-moln-och moln-till-enhet-meddelanden. Hämta och installera den senaste [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)om du vill använda Visual Studio 2017.

## <a name="sign-in-to-access-your-hub"></a>Logga in för att komma åt hubben

1. I Visual Studio väljer du **Visa**  >  **Cloud Explorer** för att öppna Cloud Explorer.

1. Välj ikonen för konto hantering för att visa dina prenumerationer.

    ![Ikon för konto hantering](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Om du är inloggad på Azure visas dina konton. Om du vill logga in på Azure för första gången väljer du **Lägg till ett konto**.

1. Välj de Azure-prenumerationer som du vill använda och välj **Använd**.

1. Expandera din prenumeration och expandera **IoT-hubbar**.  Under varje hubb kan du se dina enheter för den hubben. Högerklicka på en enhet för att få åtkomst till hanterings alternativen.

    ![Hanterings alternativ](media/iot-hub-device-management-visual-studio/management-options-vs2019.png)

## <a name="direct-methods"></a>Direkta metoder

Gör så här om du vill använda direkta metoder:

1. Högerklicka på enheten och välj **anropa Device Direct-metod**.

1. Ange metod namnet och nytto lasten i **metoden Invoke Direct** och välj sedan **OK**.

    Resultaten visas i **utdata**.

## <a name="update-device-twin"></a>Uppdatera enhet, dubbla

Gör så här om du vill redigera en enhet:

1. Högerklicka på enheten och välj **Redigera enhets telefon**.

   En **azure-iot-device-twin.jspå** filen öppnas med enhetens innehåll.

1. Gör vissa redigeringar av **taggar** eller **Egenskaper. önskade** fält till **azure-iot-device-twin.jsi** filen.

1. Tryck på **CTRL + S** för att uppdatera enheten med dubbla.

   Resultaten visas i **utdata**.

## <a name="send-cloud-to-device-messages"></a>Skicka meddelanden från moln till enhet

Följ dessa steg om du vill skicka ett meddelande från IoT Hub till din enhet:

1. Högerklicka på enheten och välj **Skicka C2D-meddelande**.

1. Ange meddelandet i **Skicka C2D-meddelande** och välj **OK**.

   Resultaten visas i **utdata**.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du använder Cloud Explorer för Visual Studio med olika hanterings alternativ.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
