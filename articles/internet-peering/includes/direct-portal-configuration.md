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
ms.openlocfilehash: fda22346a44388248e37473bc7891b8a130569c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "81681032"
---
1. På sidan **skapa en peering** på fliken **konfiguration** fyller du i rutorna som visas här.

    * För **peering-typ** väljer du **Direct**.
    * För **Microsoft Network** väljer du **AS8075**. Skapa inte peering med ASN 8069. Den är reserverad för särskilda program och används endast av [Microsoft-peering](mailto:peering@microsoft.com).
    * Välj **SKU** som **standard kostnads fritt**. Välj inte Premium kostnads fritt eftersom det är reserverat för särskilda program.
    * Välj den **tunnelbane** plats där du vill konfigurera peering.

        > [!NOTE]
        > Om du redan har peering-anslutningar med Microsoft på den valda platsen för **tunnelbane linje** och du använder Azure Portal för första gången för att konfigurera peering på den platsen, visas dina befintliga peering-anslutningar i avsnittet **peering Connections** (se). Microsoft kommer automatiskt att konvertera peering-anslutningarna till en Azure-resurs så att du kan hantera dem alla, tillsammans med de nya anslutningarna på ett och samma ställe. Mer information finns i [konvertera en äldre direkt peering till en Azure-resurs med hjälp av portalen](../howto-legacy-direct-portal.md).
        >

1. Under **peering-anslutningar** väljer du **Skapa ny** för att lägga till en rad för varje ny anslutning som du vill konfigurera.

    * Om du vill konfigurera eller ändra anslutnings inställningarna väljer du knappen Redigera för en rad.

        > [!div class="mx-imgBorder"]
        > ![Knappen Redigera](../media/setup-direct-conf-tab-edit.png)
    
    * Om du vill ta bort en rad väljer du **...**  >  **Ta bort**.

        > [!div class="mx-imgBorder"]
        > ![Knappen Ta bort](../media/setup-direct-conf-tab-delete.png)

    * Du måste ange alla inställningar för en anslutning, som du ser här.

         > [!div class="mx-imgBorder"]
         > ![Anslutnings sida för direkt peering](../media/setup-direct-conf-tab-connection.png)

        1. Välj **peering-funktionen** där anslutningen måste konfigureras.
        1. **Session Address Provider** används för att fastställa vem som tillhandahåller det undernät som krävs för att konfigurera BGP-sessionen mellan ditt nätverk och Microsoft. Om du kan ange under nätet väljer du **peer**. Annars kontaktar du **Microsoft** och [Microsoft-peering](mailto:peering@microsoft.com) kommer att kontakta dig. Att välja det här alternativet tar längre tid för Microsoft att bearbeta peering-begäran. I vissa fall kanske Microsoft inte kan tillhandahålla undernät, vilket leder till att begäran nekas.
        1. Om du har valt alternativet för **sessions-providern** som **peer** anger du IPv4-och IPv6-adresserna tillsammans med rutorna för prefixet **IPv4 prefix** och **session IPv6** .
        1. Ange antalet IPv4-och IPv6-prefix som du kommer att annonsera i rutorna **maximalt annonserade IPv4-adresser** och **maximalt annonserade IPv6-adresser** .
        1. Justera skjutreglaget för **total bandbredd** för att avspegla bandbredden för anslutningen.
        1. Välj **Spara** för att spara dina anslutnings inställningar.

1. Upprepa föregående steg för att lägga till fler anslutningar på alla anläggningar där Microsoft samverkar med ditt nätverk, inom den **tunnelbane** som du valde tidigare.

1. När du har lagt till alla nödvändiga anslutningar väljer du **Granska + skapa**.

    > [!div class="mx-imgBorder"]
    > ![Fliken peering-konfiguration slutlig](../media/setup-direct-conf-tab-final.png)

1. Observera att portalen kör grundläggande verifiering av den information som du har angett. Ett menyfliksområde överst visar meddelandet som kör den *slutliga verifieringen...*

    > [!div class="mx-imgBorder"]
    > ![Fliken peering-verifiering](../media/setup-direct-review-tab-validation.png)

1. När meddelandet har ändrats till *verifieringen* verifierar du din information. Skicka begäran genom att välja **skapa**. Om du vill ändra din begäran väljer du **föregående** och upprepar stegen.

    > [!div class="mx-imgBorder"]
    > ![Överföring av peering](../media/setup-direct-review-tab-submit.png)

1. När du har skickat begäran väntar du tills distributionen är klar. Om distributionen Miss lyckas, kontakta [Microsoft-peering](mailto:peering@microsoft.com). En lyckad distribution visas på det sätt som visas här.

    > [!div class="mx-imgBorder"]
    > ![Peer-kopplingen lyckades](../media/setup-direct-success.png)
