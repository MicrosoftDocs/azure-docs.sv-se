---
title: Programmet Azure Certified Device – självstudie – välja ditt certifierings program
description: Steg-för-steg-guide för att välja rätt certifierings program för din enhet
author: Chuckb1300
ms.author: cbroad
ms.service: certification
ms.topic: tutorial
ms.date: 03/19/2021
ms.custom: template-tutorial
ms.openlocfilehash: b6ab8a4f971a065764731abddf72e26c628ba6cb
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969803"
---
# <a name="tutorial-select-your-certification-program"></a>Självstudie: Välj ditt certifierings program

Grattis på valet av Azure Certified Device-program! Vi är glada över att du har gått med i vårt eko system för certifierade enheter. Innan du börjar måste du först avgöra vilka certifierings program som passar dina enhets funktioner bäst.

I de här självstudierna får du lära dig att:

> [!div class="checklist"]
> * Välj ett eller flera bästa certifierings program för din enhet

## <a name="selecting-a-certification-program-for-your-device"></a>Välja ett certifierings program för din enhet

Alla enheter måste uppfylla bas linje kraven som beskrivs i certifieringen av [**Azure-certifierade enheter**](./program-requirements-azure-certified-device.md) . De andra tre certifierings märkena bygger på det här programmet och levererar ett annat kund värde. Du kan välja en eller flera av de tre stegvisa aktivitets ikonerna (IoT Plug and Play, Edge Managed och Edge Secure Core * Preview).

1. Granska alla krav för certifierings program i tabellen nedan. Detaljerade krav för varje program kan markerade från huvudena.

    |Krav|[IoT Plug and Play](./program-requirements-edge-secured-core.md)|[Edge-hanterad](./program-requirements-edge-managed.md)|[Edge-säkrad-kärna](./program-requirements-edge-secured-core.md)|
    ---|---|---|---
    | Processor | Valfri|MPU/CPU|MPU/CPU|
    | Operativsystem | Valfri|[Nivå 1-OS](../iot-edge/support.md?view=iotedge-2018-06&preserve-view=true))|[Nivå 1-OS](../iot-edge/support.md?view=iotedge-2018-06&preserve-view=true)|
    | IoT Edge-körning | Stöds inte |Obligatorisk|Obligatorisk|
    | Defender för IoT | Stöds inte|Obligatorisk|Obligatorisk|
    | ADU/Windows Update | Stöds inte|Obligatorisk|Obligatorisk|

1. När du känner till att du förstår vad som krävs för din enhet bör du gå igenom de tekniska kraven för programmet. Detta kan vara Azure-certifierad enhets certifiering, eller en kombination av bas linje certifieringen med någon av de tre stegvisa aktivitets ikonerna. 

## <a name="next-steps"></a>Nästa steg

Nu är du redo att börja certifiera din enhet! Fortsätt till nästa artikel för att påbörja projektet.
> [!div class="nextstepaction"]
>[Självstudie: skapa ditt projekt](tutorial-01-creating-your-project.md)
