---
title: Ansluta till virtuella Azure-datorer med en lokal redundans med Azure Site Recovery
description: Beskriver hur du ansluter till virtuella Azure-datorer efter en redundansväxling från en lokal plats till Azure med hjälp av Azure Site Recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: harshacs
ms.openlocfilehash: 123a68885346062b9e8a53b8d5066204b6b20f5e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89568786"
---
# <a name="connect-to-azure-vms-after-failover-from-on-premises"></a>Ansluta till virtuella Azure-datorer efter redundansväxling från lokal plats 


Den här artikeln beskriver hur du konfigurerar anslutning så att du kan ansluta till virtuella Azure-datorer efter en redundansväxling.

När du konfigurerar haveri beredskap för lokala virtuella datorer och fysiska servrar till Azure börjar [Azure Site Recovery](site-recovery-overview.md) replikera datorer till Azure. Sedan kan du redundansväxla till Azure från den lokala platsen när det uppstår avbrott. När redundansväxlingen inträffar skapar Site Recovery virtuella Azure-datorer med replikerade lokala data. Som en del av Disaster Recovery-planeringen måste du ta reda på hur du ansluter till appar som körs på dessa virtuella Azure-datorer efter en redundansväxling.

I den här artikeln lär du dig hur du:

> [!div class="checklist"]
> * Förbered lokala datorer före redundans.
> * Förbered virtuella Azure-datorer efter redundansväxlingen. 
> * Behåll IP-adresser på virtuella Azure-datorer efter redundansväxling.
> * Tilldela nya IP-adresser till virtuella Azure-datorer efter redundansväxling.

## <a name="prepare-on-premises-machines"></a>Förbereda lokala datorer

Förbered dina lokala datorer innan du växlar vid fel för att säkerställa anslutning till virtuella Azure-datorer.

### <a name="prepare-windows-machines"></a>Förbereda Windows-datorer

Gör följande på lokala Windows-datorer:

1. Konfigurera Windows-inställningar. Detta inkluderar att ta bort alla statiska beständiga vägar eller WinHTTP-proxy och att ställa in diskens SAN-princip på **OnlineAll**. [Följ](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure) de här anvisningarna.

2. Kontrol lera att [dessa tjänster](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) körs.

3. Aktivera fjärr skrivbord (RDP) om du vill tillåta fjärr anslutningar till den lokala datorn. [Lär dig hur](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) du aktiverar RDP med PowerShell.

4. För att få åtkomst till en virtuell Azure-dator via Internet efter redundansväxlingen, i Windows-brandväggen på den lokala datorn, Tillåt TCP och UDP i den offentliga profilen och ange RDP som en tillåten app för alla profiler.

5. Om du vill ha åtkomst till en virtuell Azure-dator via en plats-till-plats-VPN efter redundansväxlingen, i Windows-brandväggen på den lokala datorn, Tillåt RDP för domänen och privata profiler. [Lär dig](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) hur du tillåter RDP-trafik.
6. Se till att inga Windows-uppdateringar väntar på den lokala virtuella datorn när du aktiverar en redundansväxling. Om så är fallet kan uppdateringar börja installeras på den virtuella Azure-datorn efter redundansväxlingen och du kan inte logga in på den virtuella datorn förrän uppdateringarna har slutförts.

### <a name="prepare-linux-machines"></a>Förbereda Linux-datorer

Gör följande på lokala Linux-datorer:

1. Kontrol lera att Secure Shell-tjänsten är inställd på att starta automatiskt vid system start.
2. Kontrollera att brandväggsreglerna tillåter en SSH-anslutning.


## <a name="configure-azure-vms-after-failover"></a>Konfigurera virtuella Azure-datorer efter redundans

Efter redundansväxlingen gör du följande på de virtuella Azure-datorer som skapas.

1. Om du vill ansluta till den virtuella datorn via Internet tilldelar du en offentlig IP-adress till den virtuella datorn. Du kan inte använda samma offentliga IP-adress för den virtuella Azure-dator som du använde för din lokala dator. [Läs mer](../virtual-network/virtual-network-public-ip-address.md)
2. Kontrol lera att reglerna för nätverks säkerhets gruppen (NSG) på den virtuella datorn tillåter inkommande anslutningar till RDP-eller SSH-porten.
3. Kontrol lera [startdiagnostik](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) för att visa den virtuella datorn.


> [!NOTE]
> Azure skydds-tjänsten erbjuder privat RDP och SSH-åtkomst till virtuella Azure-datorer. [Läs mer](../bastion/bastion-overview.md) om den här tjänsten.

## <a name="set-a-public-ip-address"></a>Ange en offentlig IP-adress

Som ett alternativ till att tilldela en offentlig IP-adress manuellt till en virtuell Azure-dator kan du tilldela adressen under redundans med hjälp av ett skript eller en Azure Automation-Runbook i en Site Recovery [återställnings plan](site-recovery-create-recovery-plans.md), eller så kan du konfigurera routning på DNS-nivå med Azure Traffic Manager. [Läs mer](concepts-public-ip-address-with-site-recovery.md) om hur du konfigurerar en offentlig adress.


## <a name="assign-an-internal-address"></a>Tilldela en intern adress

Om du vill ange den interna IP-adressen för en virtuell Azure-dator efter redundansväxlingen har du ett par alternativ:

- **Behåll samma IP-adress**: du kan använda samma IP-adress på den virtuella Azure-datorn som den som du allokerat till den lokala datorn.
- **Använd en annan IP-adress**: du kan använda en annan IP-adress för den virtuella Azure-datorn.


## <a name="retain-ip-addresses"></a>Behåll IP-adresser

Site Recovery låter dig behålla samma IP-adresser vid växling till Azure. Om samma IP-adress behålls undviks eventuella nätverks problem efter redundansväxlingen, men detta ger en viss komplexitet.

- Om den virtuella Azure-datorn använder samma IP-adress/undernät som din lokala plats kan du inte ansluta mellan dem med en plats-till-plats-VPN-anslutning eller ExpressRoute på grund av att adressen överlappar varandra. Undernät måste vara unika.
- Du behöver en anslutning från lokalt till Azure efter redundansväxlingen, så att apparna är tillgängliga på virtuella Azure-datorer. Azure har inte stöd för utsträckta VLAN-nätverk, så om du vill behålla IP-adresser som du behöver för att få IP-adresser över till Azure genom att redundansväxla hela under nätet, förutom den lokala datorn.
- Under nätets redundans garanterar att ett särskilt undernät inte är tillgängligt samtidigt lokalt och i Azure.

Att behålla IP-adresser kräver följande steg:

- I beräknings &ens nätverks egenskaper för det replikerade objektet, anger du nätverks-och IP-adresser för den virtuella Azure-datorn för att spegla den lokala inställningen.
- Undernät måste hanteras som en del av haveri återställnings processen. Du behöver ett Azure VNet för att matcha det lokala nätverket och efter att nätverks vägar för redundans måste ändras för att återspegla att under nätet har flyttats till Azure och nya IP-adress platser.  

### <a name="failover-example"></a>Exempel på redundans

Låt oss ta en titt på ett exempel.

- Det fiktiva företagets Sparbanken är värd för sina affärsappar lokalt som de är värdar för sina mobilappar i Azure.
- De ansluter lokalt till Azure via plats-till-plats-VPN. 
- Sparbanken använder Site Recovery för att replikera lokala datorer till Azure.
- Sina lokala appar använder hårdkodade IP-adresser så att de vill behålla samma IP-adresser i Azure.
- Lokala datorer som kör apparna körs i tre undernät:
    - 192.168.1.0/24
    - 192.168.2.0/24
    - 192.168.3.0/24
- Deras appar som körs i Azure finns i Azure VNet **Azure-nätverket** i två undernät:
    - 172.16.1.0/24
    - 172.16.2.0/24

För att behålla adresserna är det här det som de gör.

1. När de aktiverar replikering anger de att datorerna ska replikeras till **Azure-nätverket**.
2. De skapar **återställnings nätverk** i Azure. Detta VNet speglar 192.168.1.0/24-undernätet i sitt lokala nätverk.
3. Sparbanken konfigurerar en [VNet-till-VNET-anslutning](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) mellan de två nätverken. 

    > [!NOTE]
    > Beroende på program krav kan en VNet-till-VNet-anslutning konfigureras före redundans, som en manuell steg/skriptad steg-eller Azure Automation-Runbook i en Site Recovery [återställnings plan](site-recovery-create-recovery-plans.md)eller efter att redundansväxlingen har slutförts.

4. Innan redundansväxlingen, på dator egenskaperna i Site Recovery, anger de mål-IP-adressen till den lokala datorns adress, enligt beskrivningen i nästa procedur.
5. Efter redundansväxlingen skapas de virtuella Azure-datorerna med samma IP-adress. Sparbanken ansluter från **Azure-nätverket** till att **återställa nätverks** -VNet med VNet-peering (med överförings anslutning aktiverat).
6. På plats måste Sparbanken göra ändringar i nätverket, inklusive ändra vägar för att avspegla att 192.168.1.0/24 har flyttats till Azure.  

**Infrastruktur före redundans**

![Före redundansväxling av undernät](./media/site-recovery-network-design/network-design7.png)


**Infrastruktur efter redundans**

![Efter redundansväxling av undernät](./media/site-recovery-network-design/network-design9.png)


### <a name="set-target-network-settings"></a>Ange mål nätverks inställningar

Före redundans anger du nätverks inställningar och IP-adress för den virtuella Azure-datorn.

1.  I Recovery Services valv – > **replikerade objekt** väljer du den lokala datorn.
2. På sidan **beräkning och nätverk** för datorn klickar du på **Redigera** för att konfigurera nätverks-och nätverkskorts inställningar för den virtuella Azure-datorn.
3. I **nätverks egenskaper** väljer du det mål nätverk där den virtuella Azure-datorn ska placeras när den har skapats efter redundansväxlingen.
4. I **nätverks gränssnitt** konfigurerar du nätverkskorten i mål nätverket. Som standard visar Site Recovery alla identifierade nätverkskort på den lokala datorn.
    - I **mål nätverks gränssnitts typ** kan du ange varje nätverkskort som **primär**, **sekundär** eller **skapa inte** om du inte behöver det specifika nätverkskortet i mål nätverket. Ett nätverkskort måste anges som primärt för redundans. Observera att ändringar i mål nätverket påverkar alla nätverkskort för den virtuella Azure-datorn.
    - Klicka på nätverkskort namnet för att ange under nätet där den virtuella Azure-datorn ska distribueras.
    - Skriv över **dynamisk** med den privata IP-adress som du vill tilldela till målet för virtuella Azure-datorer. Om en IP-adress inte anges Site Recovery tilldelar nätverkskortet nästa tillgängliga IP-adress i under nätet till nätverkskortet vid redundansväxling.
    - [Lär dig mer](site-recovery-manage-network-interfaces-on-premises-to-azure.md) om att hantera nätverkskort för lokal redundans till Azure.


## <a name="get-new-ip-addresses"></a>Hämta nya IP-adresser

I det här scenariot hämtar den virtuella Azure-datorn en ny IP-adress efter redundansväxlingen. Om du vill konfigurera en ny IP-adress för den virtuella datorn som skapats efter en redundansväxling kan du referera till följande steg:

1. Gå till **replikerade objekt**.
2. Välj önskad virtuell Azure-dator.
3. Välj **beräkning och nätverk** och välj **Redigera**.

     ![Anpassa nätverks konfigurationerna för redundans](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Om du vill uppdatera inställningarna för nätverks växling vid fel väljer du **Redigera** för det nätverkskort som du vill konfigurera. På nästa sida som öppnas anger du motsvarande förskapad IP-adress på platsen för redundanstest och redundansväxlingen.

    ![Redigera NIC-konfigurationen](media/azure-to-azure-customize-networking/nic-drilldown.png)

5. Välj **OK**.

Site Recovery kommer nu att respektera dessa inställningar och se till att den virtuella datorn vid redundansväxling är ansluten till den valda resursen via motsvarande IP-adress, om den är tillgänglig i mål-IP-intervallet. I det här scenariot behöver du inte redundansväxla hela under nätet. En DNS-uppdatering krävs för att uppdatera poster för att det ska gå att peka på den nya IP-adressen för den virtuella datorn.

## <a name="next-steps"></a>Nästa steg
[Lär dig mer om](site-recovery-active-directory.md) att replikera lokala Active Directory och DNS till Azure.
