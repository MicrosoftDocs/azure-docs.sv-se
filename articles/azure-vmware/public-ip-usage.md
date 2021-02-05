---
title: Använda funktionen för offentliga IP-funktioner i Azure VMware-lösningen
description: Den här artikeln förklarar hur du använder funktionen för offentliga IP-funktioner i Azure Virtual WAN.
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: 640f359ed8934048bffd7f58b36e8a3eef71c2f1
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581388"
---
# <a name="how-to-use-the-public-ip-functionality-in-azure-vmware-solution"></a>Använda funktionen för offentliga IP-funktioner i Azure VMware-lösningen

Offentlig IP är en ny funktion i anslutning till Azure VMware-lösningar. Det gör resurser, till exempel webb servrar, virtuella datorer och värdar som är tillgängliga via ett offentligt nätverk. 

Du aktiverar offentlig Internet åtkomst på två sätt. 

- Program kan hanteras och publiceras under Application Gateway belastningsutjämnare för HTTP/HTTPS-trafik.
- Publicerad via offentliga IP-funktioner i Azure Virtual WAN.

Som en del av distributionen av privata moln i Azure VMware-lösningen, vid aktivering av offentliga IP-funktioner, skapas nödvändiga komponenter med Automation get och Enabled:

-  Virtuellt WAN

-  Virtuell WAN-hubb med ExpressRoute-anslutning

-  Azure Firewall-tjänster med offentlig IP

Den här artikeln beskriver hur du kan använda funktionen för offentliga IP-funktioner i virtuella WAN-nätverk.

## <a name="prerequisites"></a>Förutsättningar

- Azure VMware-lösnings miljö
- En-server som körs i Azure VMware-lösnings miljö.
- Ett nytt icke överlappande IP-intervall för den virtuella WAN Hub-distributionen, vanligt vis a `/24` .

## <a name="reference-architecture"></a>Referensarkitektur

:::image type="content" source="media/public-ip-usage/public-ip-architecture-diagram.png" alt-text="Diagram över arkitektur för offentliga IP-adresser" border="false" lightbox="media/public-ip-usage/public-ip-architecture-diagram.png":::

Arkitektur diagrammet visar en webb server som finns i Azure VMwares lösnings miljö och som kon figurer ATS med RFC1918 privata IP-adresser.  Webb tjänsten görs tillgänglig för Internet via offentliga IP-funktioner i virtuella WAN.  Offentlig IP är vanligt vis en översatt NAT-destination i Azure-brandväggen. Med DNAT-regler översätter brand Väggs principen offentliga IP-adresser till en privat adress (webserver) med en port.

Användar förfrågningar träffar brand väggen på en offentlig IP-adress som i sin tur översätts till privat IP med DNAT-regler i Azure-brandväggen. Brand väggen kontrollerar NAT-tabellen och om begäran matchar en post vidarebefordras trafiken till den översatta adressen och porten i Azure VMware-lösnings miljön.

Webb servern tar emot begäran och svarar med den begärda informationen eller sidan till brand väggen, och sedan vidarebefordrar brand väggen informationen till användaren på den offentliga IP-adressen.

## <a name="test-case"></a>Testfall
I det här scenariot publicerar du IIS-webbservern på Internet. Använd funktionen offentlig IP i Azure VMware-lösningen för att publicera webbplatsen på en offentlig IP-adress.  Du kan också konfigurera NAT-regler i brand väggen och komma åt Azure VMware-lösningen resurs (VM: ar med en webb server) med offentlig IP-adress.

## <a name="deploy-virtual-wan"></a>Distribuera Virtual WAN

1. Logga in på Azure Portal och Sök sedan efter och välj **Azure VMware-lösning**.

1. Välj det privata molnet Azure VMware-lösning.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-resource.png" alt-text="Skärm bild av det privata molnet för Azure VMware-lösningen." border="true" lightbox="media/public-ip-usage/avs-private-cloud-resource.png":::

1. Under **Hantera** väljer du **anslutning**.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-manage-menu.png" alt-text="Skärm bild av anslutnings avsnittet." border="true" lightbox="media/public-ip-usage/avs-private-cloud-manage-menu.png":::

1. Välj fliken **offentlig IP-adress** och välj sedan **Konfigurera**.

   :::image type="content" source="media/public-ip-usage/connectivity-public-ip-tab.png" alt-text="Skärm bild som visar var du ska börja konfigurera den offentliga IP-adressen" border="true" lightbox="media/public-ip-usage/connectivity-public-ip-tab.png":::

1. Acceptera standardvärdena eller ändra dem och välj sedan **skapa**.

   - Virtuell WAN-resurs grupp

   - Virtuellt WAN-namn

   - Adress block för virtuellt nav (med nytt IP-intervall som inte överlappar)

   - Antal offentliga IP-adresser (1-100)

Det tar ungefär en timme att slutföra distributionen av alla komponenter. Den här distributionen måste bara ske en gång för att stödja alla framtida offentliga IP-adresser för den här Azure VMware-lösningen.  

>[!TIP]
>Du kan övervaka statusen från **meddelande** fältet. 

## <a name="view-and-add-public-ip-addresses"></a>Visa och lägga till offentliga IP-adresser

Vi kan kontrol lera och lägga till fler offentliga IP-adresser genom att följa stegen nedan.

1. Sök efter och välj **brand vägg** i Azure Portal.

1. Välj en distribuerad brand vägg och välj sedan **besök Azure Firewall Manager för att konfigurera och hantera den här brand väggen**.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="Skärm bild som visar alternativet för att konfigurera och hantera brand väggen" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. Välj **skyddade virtuella hubbar** och välj en virtuell hubb i listan.

   :::image type="content" source="media/public-ip-usage/select-virtual-hub.png" alt-text="Skärm bild av brand Väggs hanteraren" lightbox="media/public-ip-usage/select-virtual-hub.png":::

1. På sidan virtuellt nav väljer du **offentlig IP-konfiguration** och lägger till mer offentlig IP-adress och väljer sedan **Lägg till**. 

   :::image type="content" source="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png" alt-text="Skärm bild av hur du lägger till en offentlig IP-konfiguration i brand Väggs hanteraren" border="true" lightbox="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png":::

1. Ange antalet IP-adresser som krävs och välj **Lägg till**.

   :::image type="content" source="media/public-ip-usage/add-number-of-ip-addresses-required.png" alt-text="Skärm bild för att lägga till ett angivet antal offentliga IP-konfigurationer" border="true":::


## <a name="create-firewall-policies"></a>Skapa brand Väggs principer

När alla komponenter har distribuerats kan du se dem i den tillagda resurs gruppen. Nästa steg är att lägga till en brand Väggs princip.

1. Sök efter och välj **brand vägg** i Azure Portal.

1. Välj en distribuerad brand vägg och välj sedan **besök Azure Firewall Manager för att konfigurera och hantera den här brand väggen**.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="Skärm bild som visar alternativet för att konfigurera och hantera brand väggen" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. Välj **Azure Firewall-principer** och välj sedan **Skapa Azure Firewall-princip**.

   :::image type="content" source="media/public-ip-usage/create-firewall-policy.png" alt-text="Skärm bild av hur du skapar en brand Väggs princip i brand Väggs hanteraren" border="true" lightbox="media/public-ip-usage/create-firewall-policy.png":::

1. Ange nödvändig information på fliken **grundläggande** och välj **sedan Nästa: DNS-inställningar**. 

1. Välj **inaktivera** på fliken **DNS** och välj sedan **Nästa: regler**.

1. Välj **Lägg till en regel samling**, ange informationen nedan och välj **Lägg till** och välj sedan **Nästa: Hot information**.

   -  Name
   -  Typ av regel samling – DNAT
   -  Prioritet
   -  Regel samlings åtgärd – Tillåt
   -  Namn på regel
   -  Källtyp- **IPaddress**
   -  Källicensservern **\***
   -  Protokoll – **TCP**
   -  Målport – **80**
   -  Mål typ – **IP-adress**
   -  Mål – **offentlig IP-adress**
   -  Översatt adress – **Azure VMware-Webb serverns privata IP-adress**
   -  Översatt port – **webb server port för Azure VMware-lösning**

1. Låt standardvärdet vara kvar och välj sedan **Nästa: hubbar**.

1. Välj **associera virtuell hubb**.

1. Välj en hubb i listan och välj **Lägg till**.

   :::image type="content" source="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png" alt-text="Skärm bild som visar de valda hubbarna som kommer att konverteras till skyddade virtuella nav." border="true" lightbox="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png":::

1. Välj **Nästa: Taggar**. 

1. Valfritt Skapa namn-och värdepar för att kategorisera dina resurser. 

1. Välj **Nästa: granska + skapa** och välj sedan **skapa**.

## <a name="limitations"></a>Begränsningar

Du kan ha 100 offentliga IP-adresser per SDDCs.

## <a name="next-steps"></a>Nästa steg

Nu när du har använt funktionen för offentliga IP-funktioner i Azure VMware-lösningen kanske du vill lära dig mer om:

- Använda offentliga IP-adresser med [Azure Virtual WAN](../virtual-wan/virtual-wan-about.md).
- [Skapa en IPSec-tunnel i Azure VMware-lösningen](create-ipsec-tunnel.md).
