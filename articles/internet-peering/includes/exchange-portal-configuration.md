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
ms.openlocfilehash: 268703081a2a40e8bcc665889eaeaf8edd673bfd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "81680960"
---
1. På sidan **skapa en peering** på fliken **konfiguration** fyller du i rutorna som visas.

    > [!div class="mx-imgBorder"]
    > ![Skapa en peering-sida Exchange peering-typ](../media/setup-exchange-conf-tab.png)

    * För **peering-typ** väljer du **Exchange**.
    * Välj **SKU** som **standard kostnads fritt**.
    * Välj den **tunnelbane** plats där du vill konfigurera peering.

        > [!NOTE]
        > Om du redan har peering-anslutningar med Microsoft på den valda platsen för **tunnelbane linje** och du använder portalen för första gången för att konfigurera peering på den platsen, visas dina befintliga peering-anslutningar i avsnittet **peering Connections** (se). Microsoft kommer automatiskt att konvertera peering-anslutningarna till en Azure-resurs så att du kan hantera dem tillsammans med de nya anslutningarna på ett och samma ställe. Mer information finns i [konvertera en äldre Exchange-peering till en Azure-resurs med hjälp av portalen](../howto-legacy-exchange-portal.md).
        >

1. Under **peering-anslutningar** väljer du **Skapa ny** för att lägga till en rad för varje ny anslutning som du vill konfigurera.

    * Om du vill konfigurera eller ändra anslutnings inställningarna väljer du knappen Redigera för en rad.

        > [!div class="mx-imgBorder"]
        > ![Knappen Redigera](../media/setup-exchange-conf-tab-edit.png)

    * Om du vill ta bort en rad väljer du **...**  >  **Ta bort**.

        > [!div class="mx-imgBorder"]
        > ![Knappen Ta bort](../media/setup-exchange-conf-tab-delete.png)

    * Du måste ange alla inställningar för en anslutning, som du ser här.

         > [!div class="mx-imgBorder"]
         > ![Sidan Exchange peering-anslutning](../media/setup-exchange-conf-tab-connection.png)

        1. Välj **peering-funktionen** där anslutningen måste konfigureras.
        1. I rutorna **IPv4-adress** och **IPv6-adress** anger du de IPv4-och IPv6-adresser som ska konfigureras i Microsoft-routrar med hjälp av Neighbor-kommandot.
        1. Ange antalet IPv4-och IPv6-prefix som du kommer att annonsera i rutorna **maximalt annonserade IPv4-adresser** och **maximalt annonserade IPv6-adresser** .
        1. Välj **OK** för att spara dina anslutnings inställningar.

1. Upprepa steget för att lägga till fler anslutningar på alla anläggningar där Microsoft samverkar med ditt nätverk, inom den **tunnelbane** som valdes tidigare.

1. När du har lagt till alla nödvändiga anslutningar väljer du **Granska + skapa**.

    > [!div class="mx-imgBorder"]
    > ![Fliken Konfiguration av peering](../media/setup-exchange-conf-tab-final.png)

1. Observera att portalen kör grundläggande verifiering av den information som du har angett. Ett menyfliksområde överst visar meddelandet som kör den *slutliga verifieringen...*

    > [!div class="mx-imgBorder"]
    > ![Fliken peering-verifiering](../media/setup-direct-review-tab-validation.png)

1. När meddelandet har ändrats till *verifieringen* verifierar du din information. Skicka begäran genom att välja **skapa**. Om du vill ändra din begäran väljer du **föregående** och upprepar stegen.

    > [!div class="mx-imgBorder"]
    > ![Överföring av peering](../media/setup-exchange-review-tab-submit.png)

1. När du har skickat begäran väntar du tills distributionen är klar. Om distributionen Miss lyckas, kontakta [Microsoft-peering](mailto:peering@microsoft.com). En lyckad distribution visas på det sätt som visas här.

    > [!div class="mx-imgBorder"]
    > ![Peer-kopplingen lyckades](../media/setup-direct-success.png)
