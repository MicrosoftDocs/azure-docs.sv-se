---
title: ta med fil
description: ta med fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/16/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 55cdd864d73ce084d994c64233e79d5a58b17def
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "69558506"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

Följ dessa steg om du vill hämta IoT Hub anslutnings sträng för **tjänst** principen:

1. I [Azure Portal](https://portal.azure.com)väljer du **resurs grupper**. Välj den resurs grupp där navet finns och välj sedan hubben i listan över resurser.

1. I den vänstra rutan i IoT Hub väljer du **principer för delad åtkomst**.

1. Välj **tjänst** princip i listan över principer.

1. Under **delade åtkomst nycklar** väljer du kopierings ikonen för **anslutnings strängen – primär nyckel** och spara värdet.

    ![Visa hur anslutningssträngen hämtas](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

Mer information om IoT Hub principer för delad åtkomst och behörigheter finns i [åtkomst kontroll och behörigheter](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
