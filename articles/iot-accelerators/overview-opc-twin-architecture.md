---
title: OPC, dubbel arkitektur – Azure | Microsoft Docs
description: Den här artikeln innehåller en översikt över OPCs dubbla arkitektur. Det beskriver om identifiering, aktivering, bläddring och övervakning av servern.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 602d2f77564709294d28b797ff3f03f12b3b32d8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646985"
---
# <a name="opc-twin-architecture"></a>OPC, dubbel arkitektur

> [!IMPORTANT]
> Medan vi uppdaterar den här artikeln kan du läsa mer i [Azures industriella IoT](https://azure.github.io/Industrial-IoT/) .

Följande diagram illustrerar OPCs dubbla arkitektur.

## <a name="discover-and-activate"></a>Identifiera och aktivera

1. Operatorn aktiverar nätverks genomsökning i modulen eller utför en engångs identifiering med en identifierings-URL. Identifierade slut punkter och programinformation skickas via telemetri till onboarding-agenten för bearbetning.  Onboarding-agenten för OPC UA Device bearbetar OPC UA Server Discovery-händelser som skickas av den dubbla IoT Edge-modulen i identifierings-eller genomsöknings läge. Identifierings händelser resulterar i program registrering och uppdateringar i OPC UA Device-registret.

   ![Diagram som visar den OPC dubbla arkitekturen med OPC-dubbla IoT Edge-modulen i identifierings-eller skannings läge.](media/overview-opc-twin-architecture/opc-twin1.png)

1. Operatorn kontrollerar certifikatet för den identifierade slut punkten och aktiverar den registrerade slut punkten för åtkomst. 

   ![Diagram som visar den OPC dubbla arkitekturen med IoT Edge "dubbla identiteter".](media/overview-opc-twin-architecture/opc-twin2.png)

## <a name="browse-and-monitor"></a>Bläddra och övervaka

1. När den är aktive rad kan operatören använda den dubbla tjänst REST API för att bläddra i eller granska Server informations modellen, läsa/skriva-objektvariabler och anropa metoder.  Användaren använder ett förenklat OPC UA API som uttrycks fullständigt i HTTP och JSON.

   ![Diagram som visar den OPC dubbla arkitektur installationen för att bläddra och inspektera Server informations modellen.](media/overview-opc-twin-architecture/opc-twin3.png)

1. Det dubbla tjänst REST-gränssnittet kan också användas för att skapa övervakade objekt och prenumerationer i OPC-utgivaren. OPC-utgivaren tillåter att telemetri skickas från OPC UA server-system till IoT Hub. Mer information om OPC-utgivare finns i [Vad är OPC Publisher](overview-opc-publisher.md).

   ![Hur OPCs dubbla fungerar](media/overview-opc-twin-architecture/opc-twin4.png)
