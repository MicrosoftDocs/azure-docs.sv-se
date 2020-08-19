---
title: Ladda upp filer från enheter till Azure Storage | Microsoft Docs
description: Så här konfigurerar du fil överföringar från dina enheter till molnet. När du har konfigurerat fil överföringar ska du implementera fil överföringar på dina enheter.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: 6b717fd15b25ae4abd2af3520dba2e72f8f9f3a4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556375"
---
# <a name="upload-files-from-your-devices-to-the-cloud"></a>Ladda upp filer från dina enheter till molnet

*Det här avsnittet gäller för administratörer och enhets utvecklare.*

Med IoT Central kan du ladda upp media och andra filer från anslutna enheter till moln lagring. Du konfigurerar fil uppladdnings funktionen i IoT Central programmet och implementerar fil överföringar i enhets koden.

## <a name="prerequisites"></a>Förutsättningar

Du måste vara administratör i IoT Central program för att konfigurera fil överföringar.

Du behöver ett Azure Storage-konto och en behållare för att lagra de överförda filerna. Om du inte har ett befintligt lagrings konto och en behållare att använda skapar du ett [nytt lagrings konto i Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="configure-device-file-uploads"></a>Konfigurera enhets fil överföringar

Konfigurera enhets fil överföringar:

1. Navigera till avsnittet **Administration** i ditt program.

1. Välj **enhets fil uppladdning**.

1. Välj det lagrings konto och den behållare som ska användas. Om lagrings kontot finns i en annan Azure-prenumeration än ditt program anger du en anslutnings sträng för lagrings kontot.

1. Vid behov kan du justera överförings tids gränsen som anger hur länge en uppladdnings förfrågan är giltig för. Giltiga värden är mellan 1 och 24 timmar.

1. Välj **Spara**. När statusen **har kon figurer ATS**är du redo att ladda upp filer från enheter.

:::image type="content" source="media/howto-configure-file-uploads/file-upload-configuration.png" alt-text="Konfigurera fil uppladdning i program":::

## <a name="disable-device-file-uploads"></a>Inaktivera överföring av enhets fil

Om du vill inaktivera överföring av enhets filen till ditt IoT Central-program:

1. Navigera till avsnittet **Administration** i ditt program.

1. Välj **enhets fil uppladdning**.

1. Välj **Ta bort**.

## <a name="upload-a-file-from-a-device"></a>Ladda upp en fil från en enhet

IoT Central använder IoT Hubs fil överförings funktion för att göra det möjligt för enheter att ladda upp filer. Exempel kod som visar hur du laddar upp filer från en enhet finns i exemplet på [IoT Central fil överförings enhet](https://docs.microsoft.com/samples/iot-for-all/iotc-file-upload-device/iotc-file-upload-device/).

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du konfigurerar och implementerar enhets fil överföringar i IoT Central, är ett föreslaget nästa steg att lära sig mer om enhets fil överföringar:

- [Ladda upp filer från enheten till molnet med IoT Hub (.NET)](../../iot-hub/iot-hub-csharp-csharp-file-upload.md)
- [Ladda upp filer från enheten till molnet med IoT Hub (Java)](../../iot-hub/iot-hub-java-java-file-upload.md)
- [Ladda upp filer från enheten till molnet med IoT Hub (Node.js)](../../iot-hub/iot-hub-node-node-file-upload.md)
- [Ladda upp filer från enheten till molnet med IoT Hub (python)](../../iot-hub/iot-hub-python-python-file-upload.md)
