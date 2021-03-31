---
title: ta med fil
description: ta med fil
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/22/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: ebc23ce4238c736442fbc4507e858876f9192fd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "76020928"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Koppla ett Azure Storage-konto till IoT Hub

Eftersom den simulerade enhets appen laddar upp en fil till en BLOB måste du ha ett [Azure Storage](../articles/storage/common/storage-account-create.md) -konto kopplat till din IoT-hubb. När du kopplar ett Azure Storage-konto till en IoT-hubb genererar IoT Hub en SAS-URI. En enhet kan använda SAS-URI: n för att på ett säkert sätt ladda upp en fil till en BLOB-behållare. IoT Hub-tjänsten och enhets-SDK: n koordinerar processen som genererar SAS-URI: n och gör den tillgänglig för en enhet som ska användas för att ladda upp en fil.

Följ anvisningarna i [Konfigurera fil överföringar med hjälp av Azure Portal](../articles/iot-hub/iot-hub-configure-file-upload.md). Se till att en BLOB-behållare är kopplad till din IoT-hubb och att fil meddelanden har Aktiver ATS.

![Aktivera fil meddelanden i portalen](./media/iot-hub-associate-storage/file-notifications-vs2019.png)
