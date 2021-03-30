---
title: ta med fil
description: ta med fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 04/03/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 1a0502568c1673bcd7f57d3e9bc9c95ed90bbefa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "80757018"
---
<!-- This tells how to create a custom shared access policy that has service connect and registry RW permissions for your IoT hub and get the connection string for it-->

Följ dessa steg om du vill skapa en princip för delad åtkomst som beviljar **tjänsten Connect** och **Registry Skriv** behörigheter och för att få en anslutnings sträng för den här principen:

1. I [Azure Portal](https://portal.azure.com)väljer du **resurs grupper**. Välj den resurs grupp där navet finns och välj sedan hubben i listan över resurser.

1. Välj **principer för delad åtkomst** i det vänstra fönstret i hubben.

1. Välj **Lägg till** på den översta menyn ovanför listan över principer.

1. Under **Lägg till en princip för delad åtkomst** anger du ett beskrivande namn för principen, till exempel *serviceAndRegistryReadWrite*. Under **behörigheter** väljer du **Registry Write** och **service Connect** och väljer sedan **skapa**. (Läs behörighet för **registret** inkluderas automatiskt när du väljer **register skrivning**.)

    ![Visa hur du lägger till en ny princip för delad åtkomst](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-add-svc-regrw-policy.png)

1. Välj den nya principen i listan över principer.

1. Under **delade åtkomst nycklar** väljer du kopierings ikonen för **anslutnings strängen – primär nyckel** och spara värdet.

    ![Visa hur anslutningssträngen hämtas](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-get-connection-string.png)

Mer information om IoT Hub principer för delad åtkomst och behörigheter finns i [åtkomst kontroll och behörigheter](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
