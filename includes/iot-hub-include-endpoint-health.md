---
title: ta med fil
description: ta med fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 24a07109fc8f4d6ebd283dee7ee00107f0eb49b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95553514"
---
Du kan använda REST API [Hämta slut punkts hälsa](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) för att få hälso status för slut punkterna. Vi rekommenderar att du använder [IoT Hub routnings mått](../articles/iot-hub/monitor-iot-hub-reference.md#routing-metrics) som är relaterade till routnings meddelande svars tid för att identifiera och felsöka fel när slut punktens hälso tillstånd är avstängt eller dåligt Mer information om hur du använder IoT Hub mått finns i [övervaka IoT Hub](../articles/iot-hub/monitor-iot-hub.md).

|Hälsostatus|Beskrivning|
|---|---|
|felfri|Slut punkten accepterar meddelanden som förväntat.|
|fel tillstånd|Slut punkten tar inte emot meddelanden och IoT Hub försöker skicka meddelanden till den här slut punkten igen.|
|okänd|IoT Hub har inte försökt leverera meddelanden till den här slut punkten.|
|försämrad|Slut punkten tar emot meddelanden långsammare än förväntat eller återställer från ett ohälsosamt tillstånd.|
|meddelanden|IoT Hub inte längre leverera meddelanden till den här slut punkten. Försök att skicka meddelanden till den här slut punkten misslyckades.|