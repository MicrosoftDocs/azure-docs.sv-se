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
ms.openlocfilehash: e06b5261ca6923e158c818d236a30cf6ebff189b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "81680944"
---
I det här avsnittet beskrivs hur du utför följande ändrings åtgärder för direkt peering.

### <a name="add-exchange-peering-connections"></a>Lägg till Exchange peering-anslutningar

1. Välj knappen **+ Lägg till anslutningar** och konfigurera en ny peering-anslutning.
    > [!div class="mx-imgBorder"]
    > ![Vyn peering-resurs](../media/setup-exchange-modify-addconnection.png)
1. Fyll i formuläret **Exchange peering-anslutning** och välj **Spara**. Om du behöver hjälp med att konfigurera en peering-anslutning går du igenom stegen i avsnittet "skapa och etablera en direkt peering".
    > [!div class="mx-imgBorder"]
    > ![Anslutnings formulär för Exchange-peering](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Ta bort Exchange peering-anslutningar

1. Välj en peering-anslutning som du vill ta bort och välj sedan **...**  >  **Ta bort anslutning**.
    > [!div class="mx-imgBorder"]
    > ![Knappen Ta bort anslutning](../media/setup-exchange-modify-deleteconnection.png)
1. Ange resurs-ID i rutan **Bekräfta borttagning** och välj **ta bort**.
    > [!div class="mx-imgBorder"]
    > ![Bekräftelse på borttagning](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>Lägg till en IPv4-eller IPv6-session på aktiva anslutningar

1. Välj en peering-anslutning som du vill ändra och välj sedan **...**  >  **Redigera anslutning**.
    > [!div class="mx-imgBorder"]
    > ![Knappen Redigera anslutning](../media/setup-exchange-modify-editconnection.png)
1. Lägg till information om **IPv4-adress** eller **IPv6-adress** och välj **Spara**.
    > [!div class="mx-imgBorder"]
    > ![Ändringar av peering-anslutning](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>Ta bort en IPv4-eller IPv6-session på aktiva anslutningar

Det finns för närvarande inte stöd för att ta bort en IPv4-eller IPv6-session från en befintlig anslutning på portalen. Kontakta [Microsoft-peering](mailto:peeringexperience@microsoft.com)om du vill ha mer information.