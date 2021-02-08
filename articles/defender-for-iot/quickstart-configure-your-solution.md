---
title: Lägg till Azure-resurser i din IoT-lösning
description: I den här snabb starten får du lära dig hur du konfigurerar din IoT-lösning från slut punkt till slut punkt med hjälp av Azure Defender för IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: Shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2021
ms.author: shhazam
ms.openlocfilehash: 8912e8d66ae0cc1b5dba80ee9aabb0fbd288e3c6
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809047"
---
# <a name="quickstart-configure-your-azure-defender-for-iot-solution"></a>Snabb start: Konfigurera din Azure Defender för IoT-lösning

Den här artikeln innehåller en förklaring av hur du utför inledande konfiguration av din IoT-säkerhetslösning med hjälp av Defender för IoT.

## <a name="what-is-defender-for-iot"></a>Vad är Defender för IoT?

Defender för IoT ger omfattande säkerhet från slut punkt till slut punkt för Azure-baserade IoT-lösningar.

Med Defender för IoT kan du övervaka hela IoT-lösningen på en instrument panel, Visa alla IoT-enheter, IoT-plattformar och Server dels resurser i Azure.

När den är aktive rad på IoT Hub, identifierar Defender för IoT automatiskt andra Azure-tjänster, även anslutna till din IoT Hub och relaterat till din IoT-lösning.

Förutom automatisk Relations identifiering kan du också välja vilka andra Azure-resurs grupper som ska tagga som en del av din IoT-lösning.

Med dina val kan du lägga till hela prenumerationer, resurs grupper eller enskilda resurser.

När du har definierat alla resurs relationer använder Defender för IoT Defender för att tillhandahålla säkerhets rekommendationer och aviseringar för dessa resurser.

## <a name="add-azure-resources-to-your-iot-solution"></a>Lägg till Azure-resurser i din IoT-lösning

Så här lägger du till en ny resurs i IoT-lösningen:

1. Öppna din **IoT Hub** i Azure Portal.

1. Under **säkerhet** Välj **Översikt** följt av **Inställningar** och välj sedan **övervakade resurser**.

1. Välj **Redigera** och välj de övervakade resurser som hör till din IoT-lösning.

1. Välj **Lägg till**.

Grattis! Du har lagt till en ny resurs grupp i IoT-lösningen.

Defender för IoT övervakar nu att du nyligen har lagt till resurs grupper och viktiga säkerhets rekommendationer och aviseringar som en del av din IoT-lösning.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du skapar säkerhetsmoduler...

> [!div class="nextstepaction"]
> [Skapa säkerhetsmoduler](quickstart-create-security-twin.md)
