---
title: Fel sökning av Azure IoT Hub-fel 409001 DeviceAlreadyExists
description: Förstå hur du åtgärdar fel 409001 DeviceAlreadyExists
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: b075519fff2c7c328778d770ef68e9751922807b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061136"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

I den här artikeln beskrivs orsaker och lösningar för **409001 DeviceAlreadyExists** -fel.

## <a name="symptoms"></a>Symtom

När du försöker registrera en enhet i IoT Hub Miss lyckas begäran med felet **409001 DeviceAlreadyExists**.

## <a name="cause"></a>Orsak

Det finns redan en enhet med samma enhets-ID i IoT Hub. 

## <a name="solution"></a>Lösning

Använd ett annat enhets-ID och försök igen.