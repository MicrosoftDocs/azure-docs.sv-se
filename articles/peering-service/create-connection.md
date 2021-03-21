---
title: 'Azure-peering-tjänst: skapa '
description: I den här självstudien får du lära dig hur du registrerar Azure peering-tjänsten och ett prefix.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/2/2020
ms.author: derekol
Customer intent: With Azure Peering service enhancing the customer connectivity to Microsoft cloud services .
ms.openlocfilehash: f8f0ee71a8dd00498d868bb6dc11b3fa083df766
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021103"
---
# <a name="tutorial-create-a-peering-service-connection"></a>Självstudie: skapa en peering service-anslutning

Den här självstudien visar hur du skapar en peering service-resurs och hur du konfigurerar en peering service-anslutning. 

1. Om du vill registrera en peering service-anslutning väljer du **skapa en resurs**-  >  **peering-tjänst**.

 
    ![Registrera Peering Service](./media/peering-service-portal/peering-servicecreate.png)

2. Ange följande information på fliken **grundläggande** på **anslutnings sidan Skapa en peering-tjänst** .
 
3. Välj prenumerationen och resurs gruppen som är kopplad till prenumerationen.

    ![Fliken grundläggande för registrering av peering-tjänsten](./media/peering-service-portal/peering-servicebasics.png)

4. Ange ett **namn** som peering-tjänstens instans ska registreras på.

5. Välj nu **Nästa: konfigurations** knappen längst ned på sidan. Sidan **konfiguration** visas.
## <a name="configure-the-peering-service-connection"></a>Konfigurera peering-tjänstens anslutning

1. På sidan **konfiguration** väljer du den plats som peering-tjänsten måste aktive ras på genom att välja samma från List rutan **peering service location** .

1. Välj den tjänst leverantör som peering-tjänsten måste hämtas från genom att välja ett providernamn i list rutan **peering Service Provider** .
 
1. Välj **Skapa nytt prefix** längst ned i avsnittet **prefix** och text rutor visas. Ange nu namnet på den prefix resurs och de prefix som är associerade med tjänst leverantören.

1. Välj **prefixlängd** och Lägg till den prefixlängd som du har fått av din Provider (ISP eller IXP). Med den här nyckeln kan MS verifiera prefixet och providern som har tilldelat ditt IP-prefix.

    ![Skärm bild som visar fliken Konfiguration på anslutnings sidan Skapa en peering-tjänst där du kan ange prefixlängden.](./media/peering-service-portal/peering-serviceconfiguration.png)

1. Välj knappen **Granska + skapa** längst ned till vänster på sidan. Sidan **Granska + skapa** visas och Azure verifierar konfigurationen.

 1. När du ser det **överförda** meddelandet som visas väljer du **skapa**.

> ![Skärm bild som visar fliken Granska + skapa på anslutnings sidan Skapa en peering-tjänst.](./media/peering-service-portal/peering-service-prefix.png)

1. När du har registrerat en peering service-anslutning utförs ytterligare verifiering på de inkluderade prefixen. Du kan granska validerings statusen under avsnittet **prefix** i resurs namnet. Om verifieringen Miss lyckas visas ett av följande fel meddelanden:

   - Ogiltigt prefix för peering service, prefixet måste vara ett giltigt format, endast IPv4-prefix stöds.
   - Inget prefix togs emot från peering service-providern.
   - Prefixet har ingen giltig BGP-community. kontakta peering service-providern.
   - Säkerhets kopierings vägen hittades inte, kontakta peering service-leverantören.
   - Prefixet togs emot med längre som sökväg. kontakta peering service-providern.
   - Prefix mottaget med privat som i sökvägen, kontakta peering service-providern.

### <a name="add-or-remove-a-prefix"></a>Lägga till eller ta bort ett prefix

Välj **Lägg till prefix** på sidan **prefix** för att lägga till prefix.

Välj ellipsen (...) bredvid prefixet som visas och välj alternativet **ta bort** .

### <a name="delete-a-peering-service-connection"></a>Ta bort en peering service-anslutning

På sidan **alla resurser** markerar du kryss rutan på peering-tjänsten och väljer alternativet **ta bort** högst upp på sidan.
## <a name="next-steps"></a>Nästa steg

- Mer information om peering service-anslutning finns i [peering service-anslutning](connection.md).
- Om du vill veta mer om telemetri för peering service-anslutning läser du [telemetri för peering service-anslutning](connection-telemetry.md).
- För att mäta telemetri, se [mått på telemetri för anslutning](measure-connection-telemetry.md).
- Information om hur du registrerar anslutningen med hjälp av Azure PowerShell finns i [Registrera en peering-tjänst anslutning-Azure PowerShell](powershell.md).
- Information om hur du registrerar anslutningen med hjälp av Azure CLI finns i [Registrera en peering service-anslutning – Azure CLI](cli.md).