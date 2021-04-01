---
title: Anpassa nätverkskonfigurationer för en virtuell dator med redundans | Microsoft Docs
description: Innehåller en översikt över hur du anpassar nätverkskonfigurationer för en virtuell dator med redundans i replikeringen av virtuella Azure-datorer med hjälp av Azure Site Recovery.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: sideeksh
ms.openlocfilehash: f63021275574e294fa372357d6e62724f5efe0f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98541198"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Anpassa nätverkskonfigurationer för den virtuella Azure-måldatorn

Den här artikeln innehåller vägledning om hur du anpassar nätverkskonfigurationer på den virtuella Azure-datorn (VM) när du replikerar och återställer virtuella Azure-datorer från en region till en annan med hjälp av [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Innan du börjar

Lär dig hur Site Recovery ger haveri beredskap för [det här scenariot](azure-to-azure-architecture.md).

## <a name="supported-networking-resources"></a>Nätverks resurser som stöds

Du kan ange följande nyckel resurs konfiguration för den virtuella datorn som ska återställas vid replikering av virtuella Azure-datorer:

- [Intern belastningsutjämnare](../load-balancer/load-balancer-overview.md)
- [Offentlig IP-adress](../virtual-network/public-ip-addresses.md)
- [Nätverks säkerhets grupp](../virtual-network/manage-network-security-group.md) både för under nätet och för nätverkskortet

## <a name="prerequisites"></a>Förutsättningar

- Se till att du planerar konfigurationen för återställnings sidan i förväg.
- Skapa nätverks resurserna i förväg. Ange den som inmatad så att Azure Site Recovery tjänst kan följa dessa inställningar och se till att den virtuella redundansväxlingen följer de här inställningarna.

## <a name="customize-failover-and-test-failover-networking-configurations"></a>Anpassa nätverks konfigurationerna för redundans och testning av redundans

1. Gå till **replikerade objekt**. 
2. Välj önskad virtuell Azure-dator.
3. Välj **beräkning och nätverk** och välj **Redigera**. Observera att konfigurations inställningarna för NÄTVERKSKORTet inkluderar motsvarande resurser på källan. 

     ![Anpassa nätverks konfigurationerna för redundans](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Välj ett virtuellt nätverk för redundanstest.
5. Redundansklustret väljer du **Redigera** nära det nätverkskort som du vill konfigurera. I nästa blad som öppnas väljer du motsvarande förskapade resurser på platsen redundanstest och redundansväxlingen.

    ![Redigera NIC-konfigurationen](media/azure-to-azure-customize-networking/nic-drilldown.png) 

6. Välj **OK**.

Site Recovery kommer nu att respektera dessa inställningar och se till att den virtuella datorn vid redundansväxling är ansluten till den valda resursen via motsvarande nätverkskort.

När du utlöser redundanstest via återställnings planen kommer den alltid att fråga det virtuella Azure-nätverket. Det här virtuella nätverket kommer att användas för redundanstest för datorer som inte har några konfigurerade inställningar för redundanstest.

## <a name="troubleshooting"></a>Felsökning

### <a name="unable-to-view-or-select-a-resource"></a>Det går inte att visa eller välja en resurs

Om du inte kan välja eller Visa en nätverks resurs går du igenom följande kontroller och villkor:

- Mål fältet för en nätverks resurs är bara aktive rad om den virtuella käll datorn har motsvarande ininformation. Detta baseras på principen om ett haveri återställnings scenario vill du ha antingen exakt eller en skalad version av källan.
- För varje nätverks resurs tillämpas vissa filter i list rutan för att se till att den virtuella datorn som går att koppla till den valda resursen och tillförlitligheten upprätthålls. Dessa filter baseras på samma nätverks villkor som skulle ha verifierats när du konfigurerade den virtuella käll datorn.

Verifieringar av interna belastningsutjämnare:

- Prenumerationen och regionen för belastningsutjämnaren och den virtuella mål datorn måste vara samma.
- Det virtuella nätverk som är kopplat till den interna belastningsutjämnaren och den virtuella mål datorn måste vara samma.
- Den virtuella mål datorns offentliga IP-adress (SKU) och den interna belastningsutjämnarens SKU: n måste vara samma.
- Om den virtuella mål datorn har kon figurer ATS för att placeras i en tillgänglighets zon kontrollerar du om belastningsutjämnaren är zon redundant eller ingår i en tillgänglighets zon. (Basic SKU load BALANCERS stöder inte zoner och visas inte i list rutan i det här fallet.)
- Se till att den interna belastningsutjämnaren har en förskapad backend-pool och konfiguration på klient sidan.

Offentlig IP-adress:

- Prenumerationen och regionen för den offentliga IP-adressen och den virtuella mål datorn måste vara samma.
- Den virtuella mål datorns offentliga IP-adress (SKU) och den interna belastningsutjämnarens SKU: n måste vara samma.

Nätverks säkerhets grupp:
- Prenumerationen och regionen för nätverks säkerhets gruppen och den virtuella mål datorn måste vara samma.


> [!WARNING]
> Om den virtuella mål datorn är associerad med en tillgänglighets uppsättning måste du associera den offentliga IP-adressen och den interna belastningsutjämnaren med samma SKU med den andra virtuella datorns offentliga IP-adress och intern belastningsutjämnare i tillgänglighets uppsättningen. Om du inte gör det kan redundansväxlingen Miss lyckas.
