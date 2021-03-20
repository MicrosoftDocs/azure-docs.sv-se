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
ms.openlocfilehash: e139954e6550e33edb75d01ab9dbec0bba543ea6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92548128"
---
När **peering** -resursen har distribuerats kan du Visa den genom att följa dessa steg.

1. Gå till **resurs grupper** och välj den resurs grupp som du valde när du skapade **peering** -resursen. Använd **filter** rutan om du har för många resurs grupper.

    > [!div class="mx-imgBorder"]
    > ![Resursgrupper](../media/setup-direct-get-resourcegroup.png)

1. Välj den **peering** -resurs som du har skapat.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar peering-resursen som du skapade.](../media/setup-direct-get-open.png)

1. På sidan **Översikt** visas information på hög nivå, som du ser här.

    > [!div class="mx-imgBorder"]
    > ![Översikts fönster över peering-resurs](../media/setup-exchange-get-overview.png)

1. Till vänster väljer du **ASN-information** för att visa informationen som skickas när du skapade PeerAsn.

    > [!div class="mx-imgBorder"]
    > ![ASN-information för peering-resurs](../media/setup-direct-get-asninfo.png)

1. Välj **anslutningar** till vänster. Längst upp på skärmen visas en sammanfattning av peering-anslutningar mellan ditt ASN och Microsoft, mellan olika anläggningar i Metro. Du kan också komma åt anslutnings sammanfattningen från **översikts** sidan genom att välja **anslutningar** i rutan i mitten, som du ser.

    > [!div class="mx-imgBorder"]
    > ![Peering resurs anslutningar](../media/setup-exchange-get-connectionssummary.png)

    * **Anslutnings status** motsvarar statusen för installationen av peering-anslutningen. De lägen som visas i det här fältet följer tillstånds diagrammet som visas i [genom gången av Exchange-peering](../walkthrough-exchange-all.md).
    * Status för **IPv4-sessionstillstånd** och **IPv6-sessionstillstånd** motsvarar tillstånden för IPv4-och IPv6 BGP-sessionstillstånd.  
    * När du väljer en rad högst upp på skärmen visas information om varje anslutning i avsnittet **anslutning** längst ned. Välj pilarna för att expandera **konfigurationen**, **IPv4-adressen** och **IPv6-adressen**.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar en pil som visar ett avsnitt.](../media/setup-exchange-get-connectionsipv4.png)
