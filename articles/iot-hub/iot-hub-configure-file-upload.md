---
title: Använd Azure Portal för att konfigurera fil uppladdning | Microsoft Docs
description: Använda Azure Portal för att konfigurera IoT Hub för att aktivera fil överföringar från anslutna enheter. Innehåller information om hur du konfigurerar målets Azure Storage-konto.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: robinsh
ms.openlocfilehash: da28bfa31c74ff33a200967267500033dd6a9b1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92535883"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Konfigurera IoT Hub-filuppladdningar med Azure-portalen

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Fil uppladdning

Om du vill använda [fil uppladdnings funktionen i IoT Hub](iot-hub-devguide-file-upload.md)måste du först associera ett Azure Storage-konto med hubben. Välj **fil uppladdning** om du vill visa en lista över fil överförings egenskaper för IoT Hub som ändras.

![Visa IoT Hub fil överförings inställningar i portalen](./media/iot-hub-configure-file-upload/file-upload-settings.png)

* **Lagrings behållare**: Använd Azure Portal för att välja en BLOB-behållare i ett Azure Storage konto i din aktuella Azure-prenumeration som ska associeras med din IoT Hub. Om det behövs kan du skapa ett Azure Storage-konto på bladet **lagrings konton** och blob-behållaren på bladet **behållare** . IoT Hub skapar automatiskt SAS-URI: er med Skriv behörighet till den här BLOB-behållaren för enheter som ska användas när de laddar upp filer.

   ![Visa lagrings behållare för fil uppladdning i portalen](./media/iot-hub-configure-file-upload/file-upload-container-selection.png)

* **Ta emot meddelanden om överförda filer**: Aktivera eller inaktivera fil överförings aviseringar via växlingen.

* **SAS-TTL**: den här inställningen är TTL-värdet för de SAS-URI: er som returnerades till enheten med IoT Hub. Ställ in på en timme som standard, men kan anpassas till andra värden med hjälp av skjutreglaget.

* **Inställningar för fil meddelanden standard TTL: TTL-värde** för fil överföring innan det upphör att gälla. Ställ in på en dag som standard, men kan anpassas till andra värden med hjälp av skjutreglaget.

* **Maximalt antal leveranser för fil meddelanden**: antalet gånger som IoT Hub försöker leverera ett meddelande om fil överföring. Ange till 10 som standard, men kan anpassas till andra värden med skjutreglaget.

   ![Konfigurera IoT Hub fil uppladdning i portalen](./media/iot-hub-configure-file-upload/file-upload-selected-container.png)

## <a name="next-steps"></a>Nästa steg

Mer information om fil överförings funktionerna i IoT Hub finns i [överföra filer från en enhet](iot-hub-devguide-file-upload.md) i guiden för utvecklare av IoT Hub.

Följ dessa länkar om du vill veta mer om hur du hanterar Azure-IoT Hub:

* [Masshantera IoT-enheter](iot-hub-bulk-identity-mgmt.md)
* [Övervaka din IoT Hub](monitor-iot-hub.md)

För att ytterligare utforska funktionerna i IoT Hub, se:

* [Guide för IoT Hub utvecklare](iot-hub-devguide.md)
* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/quickstart-linux.md)
* [Skydda din IoT-lösning från grunden](../iot-fundamentals/iot-security-ground-up.md)