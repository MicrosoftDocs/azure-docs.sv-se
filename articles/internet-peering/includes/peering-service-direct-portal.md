---
title: ta med fil
titleSuffix: Azure
description: ta med fil
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: e5804aa1b005e670d8b430b1c0a3bd62efd0bb06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "81687061"
---
1. Välj en peering-anslutning som du vill aktivera för Azure peering-tjänsten. Välj sedan **...**  >  **Redigera anslutning**.
    > [!div class="mx-imgBorder"]
    > ![Redigera anslutning för peering-anslutning](../media/setup-direct-modify-editconnection.png)
1. Under **Använd för peering-tjänsten** väljer du **aktive rad** och väljer sedan **Spara**.
    > [!div class="mx-imgBorder"]
    > ![Peering-anslutning aktivera peering-tjänsten](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. På **översikts** skärmen ser du distributions informationen. När distributionen är färdig väljer **du gå till resurs**.
    > [!div class="mx-imgBorder"]
    > ![Distributionen är klar](../media/setup-direct-modify-overview-deployment-complete.png)

1. I fönstret **registrerade** prefix väljer du **Lägg till registrerat prefix**.
    > [!div class="mx-imgBorder"]
    > ![Lägg till registrerat prefix](../media/setup-direct-modify-add-registered-prefix.png)
1. Registrera ett prefix genom att välja ett **namn** och ett **prefix** och välja **Spara**.
    > [!div class="mx-imgBorder"]
    >  ![Registrera ett prefix](../media/setup-direct-modify-register-a-prefix.png) 

1. När ett prefix har skapats visas det i listan över **registrerade** prefix. Välj prefixets **namn** om du vill visa mer information.
    > [!div class="mx-imgBorder"]
    > ![Registrerade prefix och anslutningar](../media/setup-direct-modify-registered-prefixes.png)
1. På sidan registrerat prefix ser du all information, som innehåller **prefixet** för varje prefix. Den här nyckeln måste tillhandahållas kunden som tilldelats det här prefixet från sin leverantörs Internet leverantör. Kunden kan sedan registrera sitt prefix i prenumerationen med hjälp av den här nyckeln.
    > [!div class="mx-imgBorder"]
    > ![Prefix med prefixlängd](../media/setup-direct-modify-registered-prefix-detail.png)