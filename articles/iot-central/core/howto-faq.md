---
title: Vanliga frågor och svar om Azure IoT Central | Microsoft Docs
description: Vanliga frågor och svar om Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 12/20/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: acabaf843f8acfe7bc0b5e9456dee09bde74bef7
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2020
ms.locfileid: "97796048"
---
# <a name="frequently-asked-questions-for-iot-central"></a>Vanliga frågor och svar om IoT Central

**Hur gör jag för att kontrol lera om det finns problem med autentiseringsuppgifter om en enhet inte ansluter till mitt IoT Central program?**

[Fel sökning av varför data från dina enheter inte visas i Azure IoT Central](troubleshoot-connection.md) innehåller steg för att diagnostisera anslutnings problem för enheter.

**Hur gör jag för att filen en biljett med kund support?**

Om du behöver hjälp kan du skriva ett [support ärende för Azure](https://portal.azure.com/#create/Microsoft.Support).

Mer information, inklusive andra support alternativ finns i [Azure IoT-support och hjälp alternativ](../../iot-fundamentals/iot-support-help.md).

**Hur gör jag för att avblockera en enhet?**

När en enhet blockeras kan den inte skicka data till ditt IoT Central-program. Blockerade enheter har statusen **blockerad** på sidan **enheter** i ditt program. En operatör måste avblockera enheten innan den kan återuppta sändnings data:

:::image type="content" source="media/howto-faq/blocked.png" alt-text="Skärm bild som visar blockerad enhet":::

När en operatör avblockerar en enhet återgår statusen till sitt tidigare värde, **registrerad** eller **etablerad**.

**Hur gör jag för att godkänna en enhet?**

Om enhetens status **väntar på godkännande** på sidan **enheter** , innebär det att alternativet **Godkänn automatiskt** är inaktiverat:

:::image type="content" source="media/howto-faq/auto-approve.png" alt-text="Skärm bild som visar alternativet program med automatiskt godkännande inaktiverat.":::

En operatör måste explicit godkänna en enhet innan den börjar skicka data. Enheter som inte är registrerade manuellt på sidan **enheter** , men som är anslutna med giltiga autentiseringsuppgifter, har enhets statusen **väntar på godkännande**. Operatörer kan godkänna dessa enheter från sidan **enheter** med knappen **Godkänn** :

:::image type="content" source="media/howto-faq/approve-device.png" alt-text="Skärm bild som visar hur du godkänner en enhet":::

**Hur gör jag för att associera en enhet med en enhets mall?**

Om enhetens status är **associerad** innebär det att enheten som ansluter till IoT Central saknar en associerad enhets mall. Den här situationen inträffar vanligt vis i följande scenarier:

- En uppsättning enheter läggs till med hjälp av **Importera** på sidan **enheter** utan att ange enhets mal len.
- En enhet registrerades manuellt på sidan **enheter** utan att ange enhets mal len. Enheten anslöt sedan med giltiga autentiseringsuppgifter.  

Operatören kan koppla en enhet till en enhets mall från sidan **enheter** med knappen **migrera** . Mer information finns i [Hantera enheter i ditt Azure IoT Central-program > migrera enheter till en mall](howto-manage-devices.md).

**Var kan jag läsa mer om IoT Hub?**

Azure IoT Central använder Azure IoT Hub som en molnbaserad gateway som aktiverar enhets anslutningen. IoT Hub aktiverar:

- Data inmatning i skala i molnet.
- Enhets hantering.
- Skydda enhets anslutningen.

Mer information om IoT Hub finns i [Azure IoT Hub](../../iot-hub/index.yml).

**Var kan jag lära mig mer om enhets etablerings tjänsten (DPS)?**

Azure IoT Central använder DPS för att göra det möjligt för enheter att ansluta till ditt program. Om du vill veta mer om hur rollen DPS fungerar när du ansluter enheter till IoT Central, se [Anslut till Azure IoT Central](concepts-get-connected.md). Läs mer om DPS i [Konfigurera enheter med Azure IoT Hub Device Provisioning service](../../iot-dps/about-iot-dps.md).