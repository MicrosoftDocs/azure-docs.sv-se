---
title: ta med fil
titleSuffix: Azure
description: ta med fil
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: b8869e93a7156b24d61ac555c95b9ca7f850ae34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "81678590"
---
1. På sidan **skapa en peering** på fliken **konfiguration** fyller du i rutorna som visas här.

    > [!div class="mx-imgBorder"]
    > ![Skapa en peering-sida Exchange peering-typ](../media/setup-exchange-conf-tab.png)

    * För **peering-typ** väljer du **Exchange**.
    * Välj **SKU** som **standard kostnads fritt**.
    * Välj den **tunnelbane** plats där du vill konvertera peering till en Azure-resurs. Om du har peering-anslutningar med Microsoft på den valda **Metro** -platsen som inte har konverterats till en Azure-resurs visas dessa anslutningar i avsnittet **peering Connections** (se). Nu kan du konvertera peering-anslutningarna till en Azure-resurs.

        > [!div class="mx-imgBorder"]
        > ![Lista över peering-anslutningar](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > Du kan inte ändra inställningar för äldre peering-anslutningar. Om du vill lägga till ytterligare peering-anslutningar med Microsoft på vald **tunnelbane** plats väljer du **Skapa ny**. Mer information finns i [skapa eller ändra en Exchange-peering med hjälp av portalen](../howto-exchange-portal.md).
        >

1. Välj **Granska + skapa**. Observera att portalen kör grundläggande verifiering av den information som du har angett. Ett menyfliksområde överst visar meddelandet som kör den *slutliga verifieringen...*

    > [!div class="mx-imgBorder"]
    > ![Fliken peering-verifiering](../media/setup-direct-review-tab-validation.png)

1. När meddelandet har ändrats till *verifieringen* verifierar du din information. Skicka begäran genom att välja **skapa**. Om du behöver ändra din begäran väljer du **föregående** och upprepar stegen.

    > [!div class="mx-imgBorder"]
    > ![Överföring av peering](../media/setup-exchange-review-tab-submit.png)

1. När du har skickat begäran väntar du tills distributionen är klar. Om distributionen Miss lyckas, kontakta [Microsoft-peering](mailto:peering@microsoft.com). En lyckad distribution visas som det visas.

    > [!div class="mx-imgBorder"]
    > ![Peer-kopplingen lyckades](../media/setup-direct-success.png)
