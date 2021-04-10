---
title: Check lista för certifiering för IoT Edge modul erbjudanden i Azure Marketplace
description: Lär dig mer om de särskilda certifierings kraven för publicering IoT Edge modul erbjudanden i Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/01/2021
ms.openlocfilehash: 31c19f62f0328fca05562eaa2f19b7a79c0f3e15
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562706"
---
# <a name="pre-certification-checklist-for-iot-edge-modules"></a>Check lista för för certifiering för IoT Edge moduler

> [!NOTE]
> Vi rekommenderar att du läser igenom den här check listan och validerar modulens funktioner innan du skickar in för certifieringar. Detta påskyndar certifierings processen genom att minska behovet av ändringar och återsändningar.

## <a name="validation-of-image"></a>Validering av bild

När Edge-modulen är klar för att skickas, utför du dessa steg för att se till att avbildningen fungerar på det sätt som Microsoft förväntar sig.

### <a name="steps-to-perform-in-the-azure-portal"></a>Steg som ska utföras i Azure Portal

1. Öppna [Azure-portalen](https://partner.microsoft.com/).
1. Skapa en resurs grupp.
1. Skapa en IoT Hub.
1. Skapa en IoT Edge enhet.
1. Kopiera anslutnings strängen och spara den i anteckningar.
1. Välj Ange **moduler på gräns enheten som skapats**.
1. Lägg till ACR-information där den senaste versionen av avbildningen finns.
1. Välj **Lägg till IoT Edge modul** och ange:
    - Bild-URI i modulen inställning
    - Miljö variabeln (samma som den som läggs till i Partner Center)
    - Alternativen för att skapa behållare (samma som det som läggs till i Partner Center)
    - Modulens dubbla inställning (samma som det som läggs till i Partner Center)
1. Lägg till vägar (samma som det som läggs till i Partner Center).
1. Välj **Granska + skapa**.

Edge-moduler distribueras på gräns enheten som skapats på Azure.

### <a name="steps-to-perform-on-the-device"></a>Steg som ska utföras på enheten

#### <a name="device-details"></a>Information om enhet

Certifierings teamet använder följande maskin vara för att verifiera avbildningar i olika arkitekturer:

- För x64-avbildningar är en virtuell Azure-dator med konfigurations storlek som standard D2s v3 som kör Ubuntu Server 18.04/Ubuntu Server 16,04.
- För Azure Resource Manager 32-bilder, en Raspberry Pi 3 modell B.
- För Azure Resource Manager 64-avbildningar är en NVIDIA Jetson nano 4 GB.

#### <a name="steps"></a>Steg

1. Se till att enheter/VM som skapats kan nås via SparaTillFil.
1. Ladda ned [IoT Edge runtime](../iot-edge/how-to-install-iot-edge.md) på enheten.
1. Uppdatera anslutnings strängen som kopierades i steg 5 till filen config. yaml.
1. Starta om Edge-modulen med `sudo systemctl restart iotedge` .
1. Kontrol lera om modulen har distribuerats på enheten med `sudo iotedge list` ; den ska vara i körnings läge.
1. Se till att loggarna för modulen som distribueras med inte `sudo iotedge logs “Module Name“ -f` innehåller några fel. Om det finns kända fel beskriver du detta i Partner Center- **anteckningarna till granskare** innan du skickar in erbjudandet.

## <a name="metadata-validation"></a>Verifiering av metadata

Kontrollera följande:

- Den senaste taggen visas i både Partner Center och Azure Container Registry.
- Lägsta maskin varu krav läggs till i beskrivningen av erbjudandet.
- Användar namn och lösen ord för Azure Container Registry uppdateras och läggs till i Partner Center.
- Noggrannhet för önskad, **dubbel egenskap** *om tillämpligt*.
- Noggrannhet för önskade **miljövariabler** *om tillämpligt*.
- Noggrannhet för önskade **skapande alternativ** *om tillämpligt*.
- Anslutnings strängen för lead management finns.
- Sekretess policy finns
- Användningsvillkor finns
- Lägg till IoT Edge enhets länk som stöds från [Azure IoT-katalogen](https://devicecatalog.azure.com/devices?certificationBadgeTypes=IoTEdgeCompatible) 

## <a name="next-steps"></a>Nästa steg

- [Distribuera moduler från den kommersiella Marketplace](../iot-edge/how-to-deploy-modules-portal.md#deploy-from-azure-marketplace)
- [Publicera Edge-modulen i Partner Center](./partner-center-portal/azure-iot-edge-module-creation.md)
- [Distribuera IoT Edge modul](../iot-edge/quickstart-linux.md)