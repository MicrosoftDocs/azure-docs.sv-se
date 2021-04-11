---
title: Fel sökning av Azure IoT Hub-fel 412002 DeviceMessageLockLost
description: Förstå hur du åtgärdar fel 412002 DeviceMessageLockLost
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: eb3d7f15109d3b217f651a7d927601ef7fae66c2
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061034"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

I den här artikeln beskrivs orsaker och lösningar för **412002 DeviceMessageLockLost** -fel.

## <a name="symptoms"></a>Symtom

När du försöker skicka ett meddelande från molnet till enheten Miss lyckas begäran med felet **412002 DeviceMessageLockLost**.

## <a name="cause"></a>Orsak

När en enhet tar emot ett meddelande från molnet till enheten från kön (t. ex. med [`ReceiveAsync()`](/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync) ) låses meddelandet av IoT Hub för en tids gräns för låsning av en minut. Om enheten försöker att slutföra meddelandet när tids gränsen för låsning går ut, utlöses detta undantag av IoT Hub.

## <a name="solution"></a>Lösning

Om IoT Hub inte får meddelandet inom en tids gräns på en minut, ställer det tillbaka meddelandet i *kö* . Enheten kan försöka ta emot meddelandet igen. Om du vill förhindra att felet uppstår i framtiden implementerar du enhets sidans logik för att slutföra meddelandet inom en minut med att ta emot meddelandet. Timeout för en minut kan inte ändras.