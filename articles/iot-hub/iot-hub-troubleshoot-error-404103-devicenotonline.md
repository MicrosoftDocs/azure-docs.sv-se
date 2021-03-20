---
title: Fel sökning av Azure IoT Hub-fel 404103 DeviceNotOnline
description: Förstå hur du åtgärdar fel 404103 DeviceNotOnline
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: e648428f924cfc33421c8591c41f7ac85b05a033
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "76960820"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

I den här artikeln beskrivs orsaker och lösningar för **404103 DeviceNotOnline** -fel.

## <a name="symptoms"></a>Symtom

En direkt metod till en enhet Miss lyckas med felet **404103 DeviceNotOnline** även om enheten är online. 

## <a name="cause"></a>Orsak

Om du vet att enheten är online och fortfarande får felet, är det troligt att anropet till direkt metod inte är registrerat på enheten.

## <a name="solution"></a>Lösning

Om du vill konfigurera enheten korrekt för återanrop i direkt metod läser du [hantera en direkt metod på en enhet](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device).