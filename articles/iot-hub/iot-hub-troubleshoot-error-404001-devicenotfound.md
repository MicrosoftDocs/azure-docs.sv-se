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
ms.openlocfilehash: 15aa21c2ec2c11bb251f7208fd22c92ceb859d6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "76960833"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

I den här artikeln beskrivs orsaker och lösningar för **404001 DeviceNotFound** -fel.

## <a name="symptoms"></a>Symtom

Under en kommunikation från moln till enhet (C2D), t. ex. C2D-meddelande, dubbel uppdatering eller direkt metod, Miss lyckas åtgärden med fel **404001 DeviceNotFound**.

## <a name="cause"></a>Orsak

Åtgärden misslyckades eftersom det inte går att hitta enheten i IoT Hub. Enheten är antingen inte registrerad eller inaktive rad.

## <a name="solution"></a>Lösning

Registrera det enhets-ID som du använde och försök sedan igen.