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
ms.openlocfilehash: 2b7881e7fa1ccbcec1325b2af0a570c86b0585a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017529"
---
När du kör exempel enhets programmet senare i den här självstudien behöver du följande konfigurations värden:

* ID-omfång: gå till **Administration > enhets anslutning** i IoT Central-programmet. Anteckna värdet för **ID-omfång** .
* Primär nyckel grupp: i IoT Central-programmet går du till **Administration > enhets anslutning > SAS-IoT-enheter**. Anteckna **värdet för** signaturen för delad åtkomst.

Använd Cloud Shell för att generera en enhets nyckel från den grupp SAS-nyckel som du nyss hämtade:

```azurecli-interactive
az extension add --name azure-iot
az iot central device compute-device-key  --device-id sample-device-01 --pk <the group SAS primary key value>
```

Anteckna den genererade enhets nyckeln, du använder den senare i den här självstudien.
