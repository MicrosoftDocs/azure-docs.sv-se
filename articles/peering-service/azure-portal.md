---
title: Registrera Azure peering-tjänsten – Azure Portal
description: Lär dig hur du registrerar Azure peering-tjänsten med hjälp av Azure Portal
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 62210105ba4ba84cff4b246c5b7cf8cc794fb5e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91534955"
---
# <a name="register-peering-service-by-using-the-azure-portal"></a>Registrera peering-tjänsten med hjälp av Azure Portal

Azure peering-tjänsten är en nätverks tjänst som förbättrar kund anslutningen till Microsofts moln tjänster, till exempel Microsoft 365, Dynamics 365, SaaS-tjänster (Software as a Service), Azure eller Microsoft-tjänster som är tillgängliga via det offentliga Internet.

I den här artikeln får du lära dig hur du registrerar en peering service-anslutning med hjälp av Azure Portal.

Om du inte har en Azure-prenumeration kan du skapa ett [konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nu.

> 

## <a name="prerequisites"></a>Förutsättningar

Du måste ha följande:

### <a name="azure-account"></a>Azure-konto

Du måste ha ett giltigt och aktivt Microsoft Azure-konto. Detta konto krävs för att konfigurera peering-tjänstens anslutning. Peering-tjänsten är en resurs i Azure-prenumerationer. 

### <a name="connectivity-provider"></a>Anslutningsleverantör

Du kan arbeta med en Internet-leverantör eller en Internet Exchange-partner för att få peering-tjänsten att ansluta ditt nätverk till Microsoft-nätverket.

Se till att [anslutnings leverantörerna](location-partners.md) är partner med Microsoft.



## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Gå till Azure Portal i en webbläsare och logga in med ditt Azure-konto.

## <a name="register-a-peering-service-connection"></a>Registrera en Peering Service-anslutning

1. Om du vill registrera en peering service-anslutning väljer du **skapa en resurs**-  >  **peering-tjänst**.

    ![Registrera Peering Service](./media/peering-service-portal/peering-servicecreate.png)
1. Ange följande information på fliken **grundläggande** på **anslutnings sidan Skapa en peering-tjänst** .

 
1. Välj prenumerationen och resurs gruppen som är kopplad till prenumerationen.

   ![Fliken registrera peering Basic](./media/peering-service-portal/peering-servicebasics.png)

1. Ange ett **namn** som peering-tjänstens instans ska registreras på.
 
1. Välj nu **Nästa: konfigurations** knappen längst ned på sidan. Sidan **konfiguration** visas.

## <a name="configure-the-peering-service-connection"></a>Konfigurera peering-tjänstens anslutning

1. På sidan **konfiguration** väljer du den plats som peering-tjänsten måste aktive ras på genom att välja samma från List rutan **peering service location** .

1. Välj den tjänst leverantör som peering-tjänsten måste hämtas från genom att välja ett providernamn i list rutan **peering Service Provider** .
 
1. Välj **Skapa nytt prefix** längst ned i avsnittet **prefix** och text rutor visas. Ange nu namnet på den prefix resurs och de prefix som är associerade med tjänst leverantören.

1. Välj **prefixlängd** och Lägg till den prefixlängd som du har fått av din Provider (ISP eller IXP). Med den här nyckeln kan MS verifiera prefixet och providern som har tilldelat ditt IP-prefix.
   > ![Skärm bild som visar fliken Konfiguration på anslutnings sidan Skapa en peering-tjänst där du kan ange prefixlängden.](./media/peering-service-portal/peering-serviceconfiguration.png)

1. Välj knappen **Granska + skapa** längst ned till vänster på sidan. Sidan **Granska + skapa** visas och Azure verifierar konfigurationen.
    

1. När du ser det **överförda** meddelandet som visas väljer du **skapa**.

   > ![Skärm bild som visar fliken Granska + skapa på anslutnings sidan Skapa en peering-tjänst.](./media/peering-service-portal/peering-service-prefix.png)


1. När du har registrerat en peering service-anslutning utförs ytterligare verifiering på de inkluderade prefixen. Du kan granska validerings statusen under avsnittet **prefix** i resurs namnet. Om verifieringen Miss lyckas visas ett av följande fel meddelanden:

   - Ogiltigt prefix för peering service, prefixet måste vara ett giltigt format, endast IPv4-prefix stöds.
   - Inget prefix togs emot från peering service-providern.
   - Prefixet har inte någon giltig BGP-community, kontakta peering Service Provider.
   - Säkerhets kopierings vägen hittades inte, kontakta peering service-providern.
   - Prefixet togs emot med längre som sökväg, kontakta peering Service Provider.
   - Prefix mottaget med privat som i sökvägen, kontakta peering service-providern.

### <a name="add-or-remove-a-prefix"></a>Lägga till eller ta bort ett prefix

Välj **Lägg till prefix** på sidan **prefix** för att lägga till prefix.

Välj ellipsen (...) bredvid prefixet som visas och välj alternativet **ta bort** .

### <a name="delete-a-peering-service-connection"></a>Ta bort en peering service-anslutning

På sidan **alla resurser** markerar du kryss rutan på peering-tjänsten och väljer alternativet **ta bort** högst upp på sidan.

> [!NOTE]
> Du kan inte ändra ett befintligt prefix.
>

## <a name="next-steps"></a>Nästa steg

- Mer information om peering service-anslutning finns i [peering service-anslutning](connection.md).
- Om du vill veta mer om telemetri för peering service-anslutning läser du [telemetri för peering service-anslutning](connection-telemetry.md).
- För att mäta telemetri, se [mått på telemetri för anslutning](measure-connection-telemetry.md).
- Information om hur du registrerar anslutningen med hjälp av Azure PowerShell finns i [Registrera en peering-tjänst anslutning-Azure PowerShell](powershell.md).
- Information om hur du registrerar anslutningen med hjälp av Azure CLI finns i [Registrera en peering service-anslutning – Azure CLI](cli.md).
