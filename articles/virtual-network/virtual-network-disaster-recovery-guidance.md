---
title: Verksamhets kontinuitet för virtuella nätverk | Microsoft Docs
description: Lär dig vad du gör om ett avbrott i Azure-tjänsten påverkar virtuella Azure-nätverk.
services: virtual-network
documentationcenter: ''
author: NarayanAnnamalai
manager: jefco
editor: ''
ms.assetid: ad260ab9-d873-43b3-8896-f9a1db9858a5
ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan
ms.reviewer: aglick
ms.openlocfilehash: 3f91d24bff0bec540ff0e7964f21c2f47c03638c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "67876175"
---
# <a name="virtual-network--business-continuity"></a>Virtual Network – affärs kontinuitet

## <a name="overview"></a>Översikt
En Virtual Network (VNet) är en logisk representation av ditt nätverk i molnet. Det gör att du kan definiera ditt eget privata IP-adressutrymme och segmentera nätverket i undernät. Virtuella nätverk fungerar som en förtroende gränser som värd för dina beräknings resurser, till exempel Azure Virtual Machines och Cloud Services (webb-och arbets roller). Ett VNet tillåter direkt privat IP-kommunikation mellan de resurser som finns i den. Du kan länka ett virtuellt nätverk till ett lokalt nätverk via en VPN Gateway eller ExpressRoute.

Ett VNet skapas inom en regions omfång. Du kan *skapa* virtuella nätverk med samma adress utrymme i två olika regioner (till exempel USA, östra och västra USA), men eftersom de har samma adress utrymme kan du inte ansluta dem till varandra. 

## <a name="business-continuity"></a>Verksamhetskontinuitet

Det kan finnas flera olika sätt att avbryta ditt program. En region kan vara helt avhuggen på grund av en naturlig katastrof eller en delvis katastrof på grund av ett problem med flera enheter eller tjänster. Påverkan på VNet-tjänsten skiljer sig i var och en av dessa situationer.

**F: Vad gör jag om ett avbrott inträffar för en hel region? Om en region till exempel är helt avhuggen på grund av en naturlig katastrof? Vad händer med de virtuella nätverk som finns i regionen?**

A: det virtuella nätverket och resurserna i den berörda regionen är inte tillgängliga under tiden för avbrott i tjänsten.

![Enkelt Virtual Network diagram](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**F: Vad kan jag göra för att återskapa samma virtuella nätverk i en annan region?**

A: virtuella nätverk är ganska enkla resurser. Du kan anropa Azure-API: er för att skapa ett VNet med samma adress utrymme i en annan region. Om du vill återskapa samma miljö som fanns i den berörda regionen gör du API-anrop för att distribuera om Cloud Services Web-och Worker-roller och de virtuella datorer som du har. Om du har lokal anslutning, till exempel i en hybrid distribution, måste du distribuera en ny VPN Gateway och ansluta till ditt lokala nätverk.

Information om hur du skapar ett virtuellt nätverk finns i [skapa ett virtuellt nätverk](manage-virtual-network.md#create-a-virtual-network).

**F: kan en replik av ett VNet i en angiven region återskapas i en annan region i förväg?**

A: Ja, du kan skapa två virtuella nätverk med samma privata IP-adressutrymme och resurser i två olika regioner i förväg. Om du är värd för Internetbaserade tjänster i VNet kan du ha konfigurerat Traffic Manager till geo-Route-trafik till den region som är aktiv. Du kan dock inte ansluta två virtuella nätverk med samma adress utrymme till ditt lokala nätverk, eftersom det skulle orsaka problem med routningen. Vid en katastrof och förlust av ett VNet i en region kan du ansluta det andra virtuella nätverket i den tillgängliga regionen med det matchande adress utrymmet till ditt lokala nätverk.

Information om hur du skapar ett virtuellt nätverk finns i [skapa ett virtuellt nätverk](manage-virtual-network.md#create-a-virtual-network).

