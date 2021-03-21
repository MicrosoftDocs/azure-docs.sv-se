---
title: Mappa virtuella nätverk mellan två regioner i Azure Site Recovery
description: Lär dig mer om att mappa virtuella nätverk mellan två Azure-regioner för haveri beredskap i Azure VM med Azure Site Recovery.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: harshacs
ms.openlocfilehash: ff1f80641dc3db1f6b69fc0223c60022f8cf8435
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95811634"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>Konfigurera nätverksmappning och IP-adressering för virtuella nätverk

Den här artikeln beskriver hur du mappar två instanser av virtuella Azure-nätverk (virtuella nätverk) som finns i olika Azure-regioner och hur du konfigurerar IP-adresser mellan nätverk. Nätverks mappning är ett standard beteende för val av mål nätverk baserat på käll nätverk vid tidpunkten för att aktivera replikering.

## <a name="prerequisites"></a>Förutsättningar

Innan du mappar nätverk bör du ha [Azure-virtuella nätverk](../virtual-network/virtual-networks-overview.md) i Azure-regionerna källa och mål. 

## <a name="set-up-network-mapping-manually-optional"></a>Konfigurera nätverks mappning manuellt (valfritt)

Mappa nätverk på följande sätt:

1. Klicka på **+ nätverks mappning** i **Site Recovery-infrastruktur**.

    ![ Skapa en nätverks mappning](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. I **Lägg till nätverks mappning** väljer du käll-och mål platserna. I vårt exempel körs den virtuella käll datorn i Asien, östra region och replikeras till Sydostasien region.

    ![Välj källa och mål](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. Skapa nu en nätverks mappning i motsatt riktning. I vårt exempel kommer källan nu att Sydostasiens och målet kommer att Asien, östra.

    ![Fönstret Lägg till nätverks mappning – Välj käll-och mål platser för mål nätverket](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>Mappa nätverk när du aktiverar replikering

Om du inte har för berett nätverks mappning innan du konfigurerar haveri beredskap för virtuella Azure-datorer kan du ange ett mål nätverk när du konfigurerar [och aktiverar replikering](azure-to-azure-how-to-enable-replication.md). När du gör detta händer följande:

- Baserat på det mål du väljer skapar Site Recovery automatiskt nätverks mappningar från källan till mål regionen och från målet till käll regionen.
- Som standard skapar Site Recovery ett nätverk i mål regionen som är identiskt med käll nätverket. Site Recovery lägger till **ASR** som suffix till namnet på käll nätverket. Du kan anpassa mål nätverket.
- Om nätverks mappning redan har inträffat för ett käll nätverk kommer det mappade mål nätverket alltid att vara standard vid tidpunkten för att aktivera replikeringar för fler virtuella datorer. Du kan välja att ändra det virtuella mål nätverket genom att välja andra tillgängliga alternativ i list rutan. 
- Om du vill ändra det virtuella standard mål nätverket för nya replikeringar måste du ändra den befintliga nätverks mappningen.
- Om du vill ändra en nätverks mappning från region A till region B, se till att du först tar bort nätverks mappningen från region B till region A. När den omvända mappningen har tagits bort ändrar du nätverks mappningen från region A till region B och skapar sedan den relevanta omvända mappningen.

>[!NOTE]
>* Om du ändrar nätverks mappningen ändras standardinställningarna för nya VM-replikeringar. Det påverkar inte de virtuella mål nätverks valen för befintliga replikeringar. 
>* Om du vill ändra mål nätverket för en befintlig replikering går du till beräknings-och nätverks inställningarna för det replikerade objektet.

## <a name="specify-a-subnet"></a>Ange ett undernät

Under nätet för den virtuella mål datorn väljs baserat på namnet på under nätet för den virtuella käll datorn.

- Om ett undernät med samma namn som käll-VM-undernätet är tillgängligt i mål nätverket, anges det under nätet för den virtuella mål datorn.
- Om det inte finns något undernät med samma namn i mål nätverket, anges det första under nätet i den alfabetiska ordningen som mål under nätet.
- Du kan ändra mål under nätet i **beräknings-och nätverks** inställningarna för den virtuella datorn.

    ![Fönstret beräknings egenskaper för beräkning och nätverk](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>Konfigurera IP-adresser för virtuella mål datorer

IP-adressen för varje nätverkskort på en virtuell mål dator konfigureras enligt följande:

- **DHCP**: om nätverkskortet för den virtuella käll datorn använder DHCP, ställs även nätverkskortet på den virtuella mål datorn in för att använda DHCP.
- **Statisk IP-adress**: om nätverkskortet för den virtuella käll datorn använder statisk IP-adressering, använder den virtuella mål datorns nätverkskort även en statisk IP-adress.


## <a name="ip-address-assignment-during-failover"></a>IP-adresstilldelning under redundans

**Käll-och mål under nät** | **Information**
--- | ---
Samma adress utrymme | IP-adressen för den virtuella käll datorn NIC har angetts som mål-IP-adress för VM-nätverkskort.<br/><br/> Om adressen inte är tillgänglig anges nästa tillgängliga IP-adress som mål.
Annat adress utrymme | Nästa tillgängliga IP-adress i mål under nätet har angetts som den virtuella mål datorns NIC-adress.



## <a name="ip-address-assignment-during-test-failover"></a>Tilldelning av IP-adress vid redundanstest

**Mål nätverk** | **Information**
--- | ---
Mål nätverket är det virtuella nätverket med redundans | -Mål-IP-adressen kommer att vara statisk med samma IP-adress. <br/><br/>  -Om samma IP-adress redan har tilldelats är IP-adressen nästa som är tillgänglig i slutet av under nätets intervall. Exempel: om käll-IP-adressen är 10.0.0.19 och redundansväxlingen använder intervallet 10.0.0.0/24, är nästa IP-adress som tilldelats den virtuella mål datorn 10.0.0.254.
Mål nätverket är inte det virtuella nätverkets VNet | -Mål-IP-adressen kommer att vara statisk med samma IP-adress.<br/><br/>  -Om samma IP-adress redan har tilldelats är IP-adressen nästa som är tillgänglig i slutet av under nätets intervall.<br/><br/> Exempel: om den statiska käll-IP-adressen är 10.0.0.19 och redundansväxlingen finns i ett nätverk som inte är redundansklustret, med intervallet 10.0.0.0/24, blir den statiska IP-adressen 10.0.0.19 om den är tillgänglig och annars kommer den att vara 10.0.0.254.

- Redundansväxlingen VNet är det mål nätverk som du väljer när du konfigurerar haveri beredskap.
- Vi rekommenderar att du alltid använder ett nätverk som inte är för produktion för redundanstest.
- Du kan ändra mål-IP-adressen i **beräknings-och nätverks** inställningarna för den virtuella datorn.


## <a name="next-steps"></a>Nästa steg

- Granska [nätverks vägledning](./azure-to-azure-about-networking.md) för haveri beredskap för Azure VM.
- [Läs mer](site-recovery-retain-ip-azure-vm-failover.md) om hur du behåller IP-adresser efter redundansväxling.
