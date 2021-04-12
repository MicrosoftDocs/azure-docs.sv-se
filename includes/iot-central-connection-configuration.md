---
title: ta med fil
description: ta med fil
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 11/03/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: e4f9fd537d7743a5bbb9d129b21c4bf0a529d32d
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491154"
---
När du kör exempel enhets programmet senare i den här självstudien behöver du följande konfigurations värden:

* ID-omfång: gå till **Administration > enhets anslutning** i IoT Central-programmet. Anteckna värdet för **ID-omfång** .
* Primär nyckel grupp: i IoT Central-programmet går du till **Administration > enhets anslutning > SAS-IoT-enheter**. Anteckna **värdet för** signaturen för delad åtkomst.

Använd Cloud Shell för att generera en enhets nyckel från den grupp primära nyckel som du hämtade:

```azurecli-interactive
az extension add --name azure-iot
az iot central device compute-device-key --device-id sample-device-01 --pk <the group primary key value>
```

Anteckna den genererade enhets nyckeln, du använder den senare i den här självstudien.
