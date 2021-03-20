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
ms.openlocfilehash: 632490498b8dd13414657edb9518cd543ac07af6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "81678837"
---
1. På sidan **skapa en peering** på fliken **konfiguration** fyller du i rutorna som visas här.

    > [!div class="mx-imgBorder"]
    > ![Fliken Konfiguration av sidan Skapa en peering-sida](../media/setup-direct-conf-tab.png)

    * För **peering-typ** väljer du **Direct**.
    * För **Microsoft Network** väljer du **AS8075**. Välj inte ASN 8069. Den är reserverad för särskilda program och används endast av [Microsoft-peering](mailto:peering@microsoft.com).
    * Välj **SKU** som **standard kostnads fritt**. Välj inte Premium kostnads fritt eftersom det är reserverat för särskilda program.
    * Välj den **tunnelbane** plats där du vill konvertera peering till en Azure-resurs. Om du har peering-anslutningar med Microsoft på den valda **Metro** -platsen som inte har konverterats till en Azure-resurs visas dessa anslutningar i avsnittet **peering Connections** (se). Nu kan du konvertera peering-anslutningarna till en Azure-resurs.

        > [!div class="mx-imgBorder"]
        > ![Lista över peering-anslutningar](../media/setup-directlegacy-conf-tab.png)

1. Om du behöver uppdatera bandbredden väljer du knappen Redigera för en rad för att ändra anslutnings inställningarna.

    > [!div class="mx-imgBorder"]
    > ![Knappen Redigera](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Om du vill lägga till ytterligare peering-anslutningar med Microsoft på vald **tunnelbane** plats väljer du **Skapa ny**. Mer information finns i [skapa eller ändra en direkt peering med hjälp av portalen](../howto-direct-portal.md).
    >

1. Välj **Granska + skapa**. Observera att portalen kör grundläggande verifiering av den information som du har angett. Ett menyfliksområde överst visar meddelandet som kör den *slutliga verifieringen...*

    > [!div class="mx-imgBorder"]
    > ![Fliken peering-verifiering](../media/setup-direct-review-tab-validation.png)

1. När meddelandet har ändrats till *verifieringen* verifierar du din information. Skicka begäran genom att välja **skapa**. Om du vill ändra din begäran väljer du **föregående** och upprepar stegen.

    > [!div class="mx-imgBorder"]
    > ![Överföring av peering](../media/setup-direct-review-tab-submit.png)

1. När du har skickat begäran väntar du tills distributionen är klar. Om distributionen Miss lyckas, kontakta [Microsoft-peering](mailto:peering@microsoft.com). En lyckad distribution visas på det sätt som visas här.

    > [!div class="mx-imgBorder"]
    > ![Peer-kopplingen lyckades](../media/setup-direct-success.png)
