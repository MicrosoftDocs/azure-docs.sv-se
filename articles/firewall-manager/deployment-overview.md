---
title: Översikt över Azure Firewall Manager-distribution
description: Lär dig mer om distributions steg på hög nivå som krävs för Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: ceb6e84b31067f7289b9e003a4fb273ce717de33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079106"
---
# <a name="azure-firewall-manager-deployment-overview"></a>Översikt över Azure Firewall Manager-distribution

Det finns mer än ett sätt att distribuera Azure Firewall Manager, men följande allmänna process rekommenderas.

## <a name="general-deployment-process"></a>Allmän distributionsprocess

### <a name="hub-virtual-networks"></a>Hubb virtuella nätverk

1.  Skapa en brand Väggs princip

    - Skapa en ny policy
<br>*eller*<br>
    - Härled en grundläggande princip och anpassa en lokal princip
<br>*eller*<br>
    - Importera regler från en befintlig Azure-brandvägg. Se till att ta bort NAT-regler från principer som ska tillämpas på flera brand väggar
1. Skapa din hubb och eker-arkitektur
   - Skapa en hubb Virtual Network med hjälp av virtuella nätverk i Azure Firewall Manager och virtuella peer-ekrar till den med hjälp av peering för virtuella nätverk
<br>*eller*<br>
    - Skapa ett virtuellt nätverk och Lägg till virtuella nätverks anslutningar och peer-ekrar virtuella nätverk med hjälp av peering för virtuella nätverk

3. Välj säkerhets leverantörer och koppla brand Väggs principen. För närvarande är endast Azure-brandväggen en leverantör som stöds.

   - Detta görs när du skapar en hubb Virtual Network
<br>*eller*<br>
    - Konvertera ett befintligt virtuellt nätverk till en hubb Virtual Network. Det är också möjligt att konvertera flera virtuella nätverk.

4. Konfigurera användare definiera vägar för att dirigera trafik till hubben Virtual Network brand väggen.


### <a name="secured-virtual-hubs"></a>Skyddade virtuella hubbar

1. Skapa din hubb och eker-arkitektur

   - Skapa en säker virtuell hubb med Azure Firewall Manager och Lägg till virtuella nätverks anslutningar.<br>*eller*<br>
   - Skapa en virtuell WAN-hubb och Lägg till virtuella nätverks anslutningar.
2. Välj säkerhets leverantörer

   - Klar när du skapade en säker virtuell hubb.<br>*eller*<br>
   - Konvertera en befintlig virtuell WAN-hubb till en säker virtuell hubb.
3. Skapa en brand Väggs princip och koppla den till din hubb

   - Gäller endast om du använder Azure-brandväggen.
   - SECaaS-principer (från tredje part) konfigureras med hjälp av partner hanterings upplevelser.
4. Konfigurera väg inställningar för att dirigera trafik till din skyddade hubb

   - Dirigera snabbt trafik till din säkra hubb för filtrering och loggning utan användardefinierade vägar (UDR) i ekrar virtuella nätverk med hjälp av den säkra väg inställnings sidan för virtuella hubbar.

> [!NOTE]
> - Du kan inte ha mer än en hubb per virtuell WAN per region. Men du kan lägga till flera virtuella WAN-näti regionen för att uppnå detta.
> - Du kan inte ha överlappande IP-utrymmen för hubbar i en vWAN.
> - Hubbens VNet-anslutningar måste finnas i samma region som hubben.
>
> Mer kända problem finns i [Vad är Azure Firewall Manager?](overview.md#known-issues)

## <a name="convert-virtual-networks"></a>Konvertera virtuella nätverk

Följande information gäller om du konverterar ett befintligt virtuellt nätverk till ett virtuellt hubb nätverk:

- Om det virtuella nätverket har en befintlig Azure-brandvägg väljer du en brand Väggs princip som ska associeras med den befintliga brand väggen. Brand Väggs etablerings status uppdateras medan brand Väggs principen ersätter brand Väggs regler. Under etablerings statusen fortsätter brand väggen att bearbeta trafiken och har ingen stillestånds tid. Du kan importera befintliga regler till en brand Väggs princip med hjälp av brand Väggs hanteraren eller Azure PowerShell.
- Om det virtuella nätverket inte har någon tillhör ande Azure-brandvägg distribueras en brand vägg och brand Väggs principen är associerad med den nya brand väggen.

## <a name="next-steps"></a>Nästa steg

- [Självstudie: skydda ditt moln nätverk med Azure Firewall Manager med hjälp av Azure Portal](secure-cloud-network.md)