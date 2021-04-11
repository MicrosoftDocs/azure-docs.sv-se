---
title: Fel sökning av Azure IoT Hub-fel 404001 DeviceNotFound
description: Förstå hur du åtgärdar fel 404001 DeviceNotFound
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 09f3c00dadc6e95aae01fb8082fb746e155d4688
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061289"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

I den här artikeln beskrivs orsaker och lösningar för **404001 DeviceNotFound** -fel.

## <a name="symptoms"></a>Symtom

Under en kommunikation från moln till enhet (C2D), t. ex. C2D-meddelande, dubbel uppdatering eller direkt metod, Miss lyckas åtgärden med fel **404001 DeviceNotFound**.

## <a name="cause"></a>Orsak

Åtgärden misslyckades eftersom det inte går att hitta enheten i IoT Hub. Enheten är antingen inte registrerad eller inaktive rad.

## <a name="solution"></a>Lösning

Registrera det enhets-ID som du använde och försök sedan igen.