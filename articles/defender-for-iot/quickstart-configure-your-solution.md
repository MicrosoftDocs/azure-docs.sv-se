---
title: 'Snabb start: lägga till Azure-resurser i din IoT-lösning'
description: I den här snabb starten får du lära dig hur du konfigurerar din IoT-lösning från slut punkt till slut punkt med hjälp av Azure Defender för IoT.
ms.topic: quickstart
ms.date: 01/25/2021
ms.openlocfilehash: 1bde15919f9fa69bb9f9de7459895a70e9b74f71
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781032"
---
# <a name="quickstart-configure-your-azure-defender-for-iot-solution"></a>Snabb start: Konfigurera din Azure Defender för IoT-lösning

Den här artikeln innehåller en förklaring av hur du utför inledande konfiguration av din IoT-säkerhetslösning med hjälp av Defender för IoT.

## <a name="prerequisites"></a>Förutsättningar

Inget

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

Gå vidare till nästa artikel om du vill lära dig hur du skapar Defender-IoT-mikroagenter...

> [!div class="nextstepaction"]
> [Skapa Defender-IoT-Micro-agenter](quickstart-create-security-twin.md)
