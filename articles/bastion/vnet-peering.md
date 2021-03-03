---
title: VNet-peering och Azure skydds-arkitektur
description: I den här artikeln får du lära dig hur VNet-peering och Azure-skydds kan användas tillsammans för att ansluta till virtuella datorer.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 12/09/2020
ms.author: cherylmc
ms.openlocfilehash: f72a3739fac1e7d6afdafd2676ea6fcefe847b2a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710591"
---
# <a name="vnet-peering-and-azure-bastion-preview"></a>VNet-peering och Azure-skydds (för hands version)

Azure-skydds och VNet-peering kan användas tillsammans. När VNet-peering har kon figurer ATS behöver du inte Distribuera Azure-skydds i varje peer-peer-nätverk. Det innebär att om du har en Azure skydds-värd som kon figurer ATS i ett virtuellt nätverk (VNet), kan den användas för att ansluta till virtuella datorer som distribueras i ett peer-kopplat VNet utan att distribuera ytterligare en skydds-värd. Mer information om VNet-peering finns i [om virtuell nätverks-peering](../virtual-network/virtual-network-peering-overview.md).

Azure-skydds fungerar med följande typer av peering:

* **Peering för virtuellt nätverk:** Anslut virtuella nätverk inom samma Azure-region.
* **Global peering för virtuellt nätverk:** Ansluta virtuella nätverk i Azure-regioner.

## <a name="architecture"></a>Arkitektur

När VNet-peering har kon figurer ATS kan Azure skydds distribueras i topologier för nav-och-eker eller fullständig mask. Azure skydds-distributionen är per virtuellt nätverk, inte per prenumeration/konto eller virtuell dator.

När du etablerar Azure skydds-tjänsten i det virtuella nätverket är RDP/SSH-upplevelsen tillgänglig för alla virtuella datorer i samma VNet, samt peer-virtuella nätverk. Det innebär att du kan konsolidera skydds-distribution till ett enda VNet och fortfarande komma åt virtuella datorer som distribueras i ett peer-använt VNet och centralisera den övergripande distributionen.

:::image type="content" source="./media/vnet-peering/design.png" alt-text="Diagram över design och arkitektur":::

Den här bilden visar arkitekturen för en Azure skydds-distribution i en nav-och-eker-modell. I det här diagrammet kan du se följande konfiguration:

* Skydds-värden har distribuerats i det centraliserade virtuella Hubbs nätverket.
* Den centraliserade nätverks säkerhets gruppen (NSG) har distribuerats.
* Det krävs ingen offentlig IP-adress på den virtuella Azure-datorn.

**Sätt**

1. Anslut till Azure Portal med valfri HTML5-webbläsare.
2. Se till att du har **Läs** behörighet till både den virtuella mål datorn och det peer-virtuella VNet. Kontrol lera dessutom under IAM att du har Läs behörighet till följande resurser:
   * Rollen läsare på den virtuella datorn.
   * Rollen läsare på NÄTVERKSKORTet med den virtuella datorns privata IP-adress.
   * Rollen läsare på Azure skydds-resursen.
   * Rollen läsare i Virtual Network (behövs inte om det inte finns något peer-kopplat virtuellt nätverk).
3. Om du vill se skydds i den nedrullningsbara menyn **Anslut** måste du välja den åtkomst som du har åtkomst till i **prenumerationen > global prenumeration**.
4. Välj den virtuella dator som du vill ansluta till.
5. Azure skydds har sömlöst identifierats i det peer-distribuerade virtuella nätverket.
6. Med ett enda klick öppnas RDP/SSH-sessionen i webbläsaren. För gränser för RDP och SSH samtidigt, se [RDP-och SSH-sessioner](bastion-faq.md#limits).

  :::image type="content" source="../../includes/media/bastion-vm-rdp/connect-vm.png" alt-text="Anslut":::

   Mer information om hur du ansluter till en virtuell dator via Azure skydds finns i:

   * [Anslut till en VM-RDP](bastion-connect-vm-rdp.md).
   * [Anslut till en VM – SSH](bastion-connect-vm-ssh.md).

## <a name="faq"></a>Vanliga frågor

[!INCLUDE [FAQ for VNet peering](../../includes/bastion-faq-peering-include.md)]

## <a name="next-steps"></a>Nästa steg

Läs [vanliga frågor och svar om skydds](bastion-faq.md).
