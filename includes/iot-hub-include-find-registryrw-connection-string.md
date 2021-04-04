---
title: ta med fil
description: ta med fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3386cb51a8a728576f6615002d6154d89ca662c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95993302"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

Följ dessa steg om du vill hämta IoT Hub anslutnings sträng för **registryReadWrite** -principen:

1. I [Azure Portal](https://portal.azure.com)väljer du **resurs grupper**. Välj den resurs grupp där navet finns och välj sedan hubben i listan över resurser.

2. Välj **principer för delad åtkomst** i det vänstra fönstret i hubben.

3. Välj **registryReadWrite** -principen i listan över principer.

4. Under **delade åtkomst nycklar** väljer du kopierings ikonen för **anslutnings strängen – primär nyckel** och spara värdet.

    ![Visa hur anslutningssträngen hämtas](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

Mer information om IoT Hub principer för delad åtkomst och behörigheter finns i [åtkomst kontroll och behörigheter](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
