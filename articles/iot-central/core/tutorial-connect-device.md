---
title: Självstudie – ansluta en allmän klient app till Azure IoT Central | Microsoft Docs
description: Den här självstudien visar hur du, som enhets utvecklare, ansluter en enhet som kör antingen en app med C, C#, Java, java script eller python till ditt Azure IoT Central-program. Du ändrar den automatiskt genererade enhets mal len genom att lägga till vyer som låter en operatör interagera med en ansluten enhet.
author: dominicbetts
ms.author: dobett
ms.date: 11/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mqtt
- device-developer
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: 2757d696f5922263abf87399d6491e46b5e5513c
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033904"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application"></a>Självstudie: skapa och ansluta ett klient program till ditt Azure IoT Central-program

*Den här artikeln gäller lösnings byggare och enhets utvecklare.*

Den här självstudien visar hur du, som enhets utvecklare, ansluter ett klient program till ditt Azure IoT Central-program. Programmet simulerar beteendet hos en termostat-enhet. När programmet ansluter till IoT Central, skickas modell-ID: t för enhets modellen termostat. IoT Central använder modell-ID: t för att hämta enhets modellen och skapa en enhets mall åt dig. Du kan lägga till anpassningar och vyer i enhets mal len så att en operatör kan interagera med en enhet.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa och kör enhets koden och se hur den ansluter till ditt IoT Central-program.
> * Visa den simulerade telemetri som skickas från enheten.
> * Lägg till anpassade vyer i en enhets mall.
> * Publicera enhets mal len.
> * Använd en vy för att hantera enhets egenskaper.
> * Anropa ett kommando för att kontrol lera enheten.

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-central-connect-device-c](../../../includes/iot-central-connect-device-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-central-connect-device-csharp](../../../includes/iot-central-connect-device-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-central-connect-device-java](../../../includes/iot-central-connect-device-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-central-connect-device-nodejs](../../../includes/iot-central-connect-device-nodejs.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-central-connect-device-python](../../../includes/iot-central-connect-device-python.md)]

:::zone-end

## <a name="view-raw-data"></a>Visa rå data

Som enhets utvecklare kan du använda vyn **rå data** för att undersöka rå data som enheten skickar till IoT Central:

:::image type="content" source="media/tutorial-connect-device/raw-data.png" alt-text="Vyn rå data":::

I den här vyn kan du välja vilka kolumner som ska visas och ange ett tidsintervall som ska visas. I kolumnen för staplade **data** visas data från enheten som inte matchar några definitioner för egenskaper eller telemetri i enhets mal len.

## <a name="next-steps"></a>Nästa steg

Om du föredrar att fortsätta med en uppsättning IoT Central själv studie kurser och lära dig mer om hur du skapar en IoT Central lösning, se:

> [!div class="nextstepaction"]
> [Mall för att skapa en gateway-enhet](./tutorial-define-gateway-device-type.md)

Som en enhets utvecklare nu har du lärt dig grunderna för hur du skapar en enhet med Java, men vissa föreslagna nästa steg är att:

* Läs [Vad är enhets mallar?](./concepts-device-templates.md) om du vill lära dig mer om rollen hets mallar när du implementerar din enhets kod.
* Läs [bli ansluten till Azure IoT Central](./concepts-get-connected.md) om du vill veta mer om hur du registrerar enheter med IoT Central och hur IoT Central skyddar enhets anslutningar.
* Läs [telemetri, egenskaper och kommando nytto laster](concepts-telemetry-properties-commands.md) för att lära dig mer om de data som enheten utbyter med IoT Central.
* Läs [IoT plug and Play enhets guide för utvecklare](../../iot-pnp/concepts-developer-guide-device.md).
