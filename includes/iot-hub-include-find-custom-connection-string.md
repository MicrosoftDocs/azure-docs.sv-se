---
title: ta med fil
description: ta med fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 6f43bbcd83861f7d39de2aa89bbe035c2ff5b809
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "70050422"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Följ dessa steg om du vill skapa en princip för delad åtkomst som beviljar Läs behörighet för **tjänsten Connect** och **Registry** och får en anslutnings sträng för den här principen:

1. I [Azure Portal](https://portal.azure.com)väljer du **resurs grupper**. Välj den resurs grupp där navet finns och välj sedan hubben i listan över resurser.

1. Välj **principer för delad åtkomst** i det vänstra fönstret i hubben.

1. Välj **Lägg till** på den översta menyn ovanför listan över principer.

1. Under **Lägg till en princip för delad åtkomst** anger du ett beskrivande namn för principen, till exempel *serviceAndRegistryRead*. Under **behörigheter** väljer du **register läsa** och **tjänst Anslut** och väljer sedan **skapa**.

    ![Visa hur du lägger till en ny princip för delad åtkomst](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

1. Välj den nya principen i listan över principer.

1. Under **delade åtkomst nycklar** väljer du kopierings ikonen för **anslutnings strängen – primär nyckel** och spara värdet.

    ![Visa hur anslutningssträngen hämtas](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

Mer information om IoT Hub principer för delad åtkomst och behörigheter finns i [åtkomst kontroll och behörigheter](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
