---
title: Azure DDoS Protection standard för | Microsoft Docs
description: Lär dig vad du kan göra om ett avbrott i Azure-tjänsten påverkar Azure DDoS Protection Standard.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2021
ms.author: yitoh
ms.topic: article
ms.openlocfilehash: 5085f1584a737e75adccf4ec23bf709bede28a4b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730498"
---
# <a name="azure-ddos-protection-standard--business-continuity"></a>Azure DDoS Protection Standard – affärskontinuhet

Affärskontinui och haveriberedskap i Azure DDoS Protection Standard gör att ditt företag kan fortsätta att fungera vid avbrott. Den här artikeln beskriver tillgänglighet (inom regionen) och haveriberedskap.

## <a name="overview"></a>Översikt
Azure DDoS Protection Standard skyddar offentliga IP-adresser i virtuella nätverk. Skyddet är enkelt att aktivera i alla nya eller befintliga virtuella nätverk och kräver inga program- eller resursändringar.

En Virtual Network (VNet) är en logisk representation av nätverket i molnet. Virtuella nätverk fungerar som en förtroendegräns för dina resurser, till exempel Azure Application Gateway, Azure Firewall och Azure Virtual Machines. Den skapas inom omfånget för en region. Du kan *skapa* virtuella nätverk med samma adressutrymme i två olika regioner (till exempel USA, östra och USA, västra), men eftersom de har samma adressutrymme kan du inte koppla ihop dem. 

## <a name="business-continuity"></a>Verksamhetskontinuitet

Det kan finnas flera olika sätt som ditt program kan störas. En region kan stängas av helt på grund av en naturhaveri, eller en partiell katastrof, på grund av ett fel på flera enheter eller tjänster. Påverkan på dina skyddade virtuella nätverk är olika i var och en av dessa situationer.

**F: Vad gör jag om ett avbrott inträffar för en hel region? Om en region till exempel är helt avskuren på grund av en naturkatastrof? Vad händer med de virtuella nätverk som finns i regionen?**

S: Det virtuella nätverket och resurserna i den berörda regionen är inte tillgängliga under tjänstavbrottet.

![Enkelt Virtual Network diagram.](../virtual-network/media/virtual-network-disaster-recovery-guidance/vnet.png)

**F: Vad kan jag göra för att skapa om samma virtuella nätverk i en annan region?**

S: Virtuella nätverk är ganska lätta resurser. Du kan anropa Azure-API:er för att skapa ett VNet med samma adressutrymme i en annan region. Om du vill återskapa samma miljö som fanns i den berörda regionen gör du API-anrop för att distribuera om resurserna i de virtuella nätverk som du hade. Om du har en lokal anslutning, till exempel i en hybriddistribution, måste du distribuera en ny VPN Gateway och ansluta till ditt lokala nätverk.

Information om hur du skapar ett virtuellt nätverk finns [i Skapa ett virtuellt nätverk](../virtual-network/manage-virtual-network.md#create-a-virtual-network).

**F: Kan en replik av ett VNet i en viss region skapas på nytt i en annan region i förväg?**

S: Ja, du kan skapa två virtuella nätverk med samma privata IP-adressutrymme och resurser i två olika regioner i förväg. Om du är värd för Internetriktade tjänster i det virtuella nätverket kan du ha ställt in Traffic Manager att geo-dirigera trafik till den region som är aktiv. Du kan dock inte ansluta två virtuella nätverk med samma adressutrymme till ditt lokala nätverk, eftersom det skulle orsaka routningsproblem. Vid en katastrof och förlust av ett VNet i en region kan du ansluta det andra virtuella nätverket i den tillgängliga regionen med matchande adressutrymme till ditt lokala nätverk.

Information om hur du skapar ett virtuellt nätverk finns [i Skapa ett virtuellt nätverk](../virtual-network/manage-virtual-network.md#create-a-virtual-network).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur [du skapar en DDoS-skyddsplan.](manage-ddos-protection.md)