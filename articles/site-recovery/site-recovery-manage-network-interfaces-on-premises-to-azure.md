---
title: Hantera nätverkskort för lokal haveri beredskap med Azure Site Recovery
description: Beskriver hur du hanterar nätverks gränssnitt för lokal haveri beredskap till Azure med Azure Site Recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: harshacs
ms.openlocfilehash: 4dad7f76edf34782131c7c844978763cda53acc7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90068123"
---
# <a name="manage-vm-network-interfaces-for-on-premises-disaster-recovery-to-azure"></a>Hantera nätverksgränssnitt för virtuella datorer för haveriberedskap från lokal plats till Azure

En virtuell dator (VM) i Azure måste ha minst ett nätverks gränssnitt kopplat till sig. Det kan finnas flera nätverks gränssnitt som är kopplade till det eftersom VM-storleken stöder.

Som standard definieras det första nätverks gränssnittet som är kopplat till en virtuell Azure-dator som det primära nätverks gränssnittet. Alla andra nätverks gränssnitt på den virtuella datorn är sekundära nätverks gränssnitt. Som standard skickas all utgående trafik från den virtuella datorn ut från IP-adressen som har tilldelats den primära IP-konfigurationen för det primära nätverks gränssnittet.

I en lokal miljö kan virtuella datorer eller servrar ha flera nätverks gränssnitt för olika nätverk i miljön. Olika nätverk används vanligt vis för att utföra vissa åtgärder, till exempel uppgraderingar, underhåll och Internet-åtkomst. När du migrerar eller växlar över till Azure från en lokal miljö bör du tänka på att nätverks gränssnitt på samma virtuella dator måste vara anslutna till samma virtuella nätverk.

Som standard skapar Azure Site Recovery så många nätverks gränssnitt på en virtuell Azure-dator som är anslutna till den lokala servern. Du kan undvika att skapa redundanta nätverks gränssnitt under migreringen eller redundansväxlingen genom att redigera inställningarna för nätverks gränssnittet under inställningarna för den replikerade virtuella datorn.

## <a name="select-the-target-network"></a>Välj mål nätverket

För VMware och fysiska datorer, och för Hyper-V (utan System Center Virtual Machine Manager) virtuella datorer, kan du ange det virtuella mål nätverket för enskilda virtuella datorer. För virtuella Hyper-V-datorer som hanteras med Virtual Machine Manager använder du [nätverks mappning](./hyper-v-vmm-network-mapping.md) för att mappa virtuella dator nätverk på en käll Virtual Machine Manager Server och riktar in dig på Azure-nätverk.

1. Under **replikerade objekt** i ett Recovery Services valv väljer du ett replikerat objekt för att komma åt inställningarna för det replikerade objektet.

2. Välj fliken **beräkning och nätverk** för att få åtkomst till nätverks inställningarna för det replikerade objektet.

3. Under **nätverks egenskaper** väljer du ett virtuellt nätverk i listan över tillgängliga nätverks gränssnitt.

    ![Nätverksinställningar](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

Att ändra mål nätverket påverkar alla nätverks gränssnitt för den specifika virtuella datorn.

För Virtual Machine Manager moln påverkar ändring av nätverks mappning alla virtuella datorer och deras nätverks gränssnitt.

## <a name="select-the-target-interface-type"></a>Välj mål gränssnitts typ

Under avsnittet **nätverks gränssnitt** i fönstret **beräkning och nätverk** kan du Visa och redigera inställningar för nätverks gränssnitt. Du kan också ange mål nätverkets gränssnitts typ.

- Det krävs ett **primärt** nätverks gränssnitt för redundans.
- Alla andra valda nätverks gränssnitt, om sådana finns, **sekundära** nätverks gränssnitt.
- Välj **Använd inte** för att undanta ett nätverks gränssnitt från att skapas vid redundans.

Som standard när du aktiverar replikering, Site Recovery väljer alla identifierade nätverks gränssnitt på den lokala servern. Den markerar en som **primär** och alla andra som **sekundär**. Alla efterföljande gränssnitt som läggs till på den lokala servern markeras **inte** som standard. När du lägger till fler nätverks gränssnitt bör du kontrol lera att rätt storlek för virtuella Azure-datorer har valts för att hantera alla nätverks gränssnitt som krävs.

## <a name="modify-network-interface-settings"></a>Ändra inställningar för nätverks gränssnittet

Du kan ändra under nätet och IP-adressen för ett replikerat objekts nätverks gränssnitt. Om ingen IP-adress har angetts tilldelar Site Recovery nästa tillgängliga IP-adress från under nätet till nätverks gränssnittet vid redundans.

1. Välj ett tillgängligt nätverks gränssnitt för att öppna inställningarna för nätverks gränssnittet.

2. Välj önskat undernät i listan över tillgängliga undernät.

3. Ange önskad IP-adress (om det behövs).

    ![Inställningar för nätverks gränssnitt](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Klicka på **OK** för att slutföra redigeringen och gå tillbaka till fönstret **beräkning och nätverk** .

5. Upprepa steg 1-4 för andra nätverks gränssnitt.

6. Välj **Spara** för att spara alla ändringar.

## <a name="next-steps"></a>Nästa steg
  [Lär dig mer](../virtual-network/virtual-network-network-interface-vm.md) om nätverks gränssnitt för Azure Virtual Machines.
