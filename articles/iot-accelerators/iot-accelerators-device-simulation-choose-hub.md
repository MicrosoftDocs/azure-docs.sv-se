---
title: Använd befintlig IoT Hub med enhets simulerings lösning – Azure | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar lösnings Accelerator för enhets simulering till att använda en befintlig IoT Hub.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 1f89e23d7bb279e7cce5c104060cc7898517f8b7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009372"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Använda en befintlig IoT Hub med lösningsaccelerator för enhetssimulering

När du distribuerar enhets simuleringen kan du också välja att distribuera en IoT-hubb som ska användas i simuleringen. Det här alternativet distribuerar en [IoT-hubb på S2-nivå med en enda skalnings enhet](../iot-hub/iot-hub-scaling.md). Om du distribuerar den här valfria IoT-hubben kan du fortfarande välja att rikta en annan IoT Hub för en simulerings körning.

Om du väljer att inte distribuera de valfria IoT Hub måste du använda ditt eget nav för alla simuleringar som du kör.

Om du inte har en IoT-hubb kan du alltid skapa en ny från [Azure Portal](https://portal.azure.com).

Om du vill använda en befintlig IoT-hubb behöver du anslutnings strängen för **iothubowner** -principen för delad åtkomst. Du kan hämta den här anslutnings strängen från [Azure Portal](https://portal.azure.com):

1. På hubbens konfigurations sida i portalen klickar du på **principer för delad åtkomst**.

1. Klicka på **iothubowner**.

1. Kopiera antingen den primära eller sekundära anslutnings strängen.

[![Hämta anslutningssträng](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

Använd anslutnings strängen som du kopierade när du konfigurerade simuleringen:

![Konfigurera simulering](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation.png)

### <a name="next-steps"></a>Nästa steg

I den här instruktions guiden har du lärt dig hur du använder en befintlig IoT-hubb i en simulering. Härnäst kanske du vill lära dig hur du [skapar en avancerad enhets modell](iot-accelerators-device-simulation-advanced-device.md) för en simulering.
