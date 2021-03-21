---
title: Behåll IP-adresserna efter redundansväxlingen av Azure VM med Azure Site Recovery
description: Beskriver hur du behåller IP-adresser när du växlar över virtuella Azure-datorer för haveri beredskap till en sekundär region med Azure Site Recovery
ms.service: site-recovery
ms.date: 4/9/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 650fb7f0877a98ef53ed3868550f9c084ecb5885
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023558"
---
# <a name="retain-ip-addresses-during-failover"></a>Behålla IP-adresser vid redundans

[Azure Site Recovery](site-recovery-overview.md) aktiverar haveri beredskap för virtuella Azure-datorer genom att replikera virtuella datorer till en annan Azure-region, redundansväxla om ett avbrott inträffar och sedan växla tillbaka till den primära regionen när något är tillbaka till normal.

Under redundansväxlingen kanske du vill behålla IP-adressering i mål regionen som är identisk med käll regionen:

- Som standard när du aktiverar haveri beredskap för virtuella Azure-datorer skapar Site Recovery mål resurser baserat på käll resurs inställningar. För virtuella Azure-datorer som kon figurer ATS med statiska IP-adresser försöker Site Recovery etablera samma IP-adress för den virtuella mål datorn, om den inte används. En fullständig förklaring av hur Site Recovery hanterar adresser finns i [den här artikeln](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).
- För enkla program räcker det med standard konfigurationen. För mer komplexa appar kan du behöva etablera ytterligare resurser för att se till att anslutningen fungerar som förväntat efter redundansväxlingen.


Den här artikeln innehåller några exempel på hur du behåller IP-adresser i mer komplexa exempel scenarier. Exempel på detta är:

- Redundans för ett företag med alla resurser som körs i Azure
- Redundans för ett företag med en hybrid distribution och resurser som kör både lokalt och i Azure

## <a name="resources-in-azure-full-failover"></a>Resurser i Azure: fullständig redundans

Företag A har alla sina appar som körs i Azure.

### <a name="before-failover"></a>Före redundans

Här är arkitekturen före redundansväxlingen.

- Företag A har identiska nätverk och undernät i käll-och mål Azure-regioner.
- För att minska återställnings tids målet (RTO) använder företaget replik-noder för SQL Server Always on, domänkontrollanter osv. Dessa replik-noder finns i ett annat VNet i mål regionen, så att de kan upprätta VPN plats-till-plats-anslutning mellan käll-och mål regionerna. Detta är inte möjligt om samma IP-adressutrymme används i källan och målet.  
- Innan redundansväxling är nätverks arkitekturen följande:
    - Primär region är Azure Asien, östra
        - Asien, östra har ett VNet (**käll-VNet**) med adress utrymme 10.1.0.0/16.
        - Asien, östra har arbets belastningar som delas över tre undernät i VNet:
            - **Undernät 1**: 10.1.1.0/24
            - **Undernät 2**: 10.1.2.0/24
            - **Undernät 3**: 10.1.3.0/24
    - Sekundär (mål) region är Azure Sydostasien
        - Sydostasien har ett återställnings-VNet (**återställnings-VNet**) identiskt med det **virtuella käll** nätverket
        - Sydostasien har ytterligare ett VNet (**Azure VNet**) med adress utrymme 10.2.0.0/16.
        - **Azure VNet** innehåller ett undernät (**undernät 4**) med adress utrymmet 10.2.4.0/24.
        - Replik-noder för SQL Server Always on, domänkontrollant osv. finns i **undernät 4**.
    - Det **virtuella käll** nätverket och **Azure VNet** är anslutet till en VPN-anslutning från plats till plats.
    - **Återställnings-VNet** är inte anslutet till något annat virtuellt nätverk.
    - **Företag A** tilldelar/verifierar mål-IP-adresser för replikerade objekt. Mål-IP-adressen är samma som käll-IP för varje virtuell dator.

![Resurser i Azure före fullständig redundans](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Efter redundans

Om ett regionalt käll avbrott inträffar kan företagets A redundansväxla alla sina resurser till mål regionen.

- Med mål-IP-adresser som redan finns på plats före redundansväxlingen kan företag A dirigera redundans och automatiskt upprätta anslutningar efter redundansväxlingen mellan **återställnings-VNet** och **Azure VNet**. Detta illustreras i följande diagram..
- Beroende på kraven i appen kan anslutningar mellan de två virtuella nätverk (**återställnings-VNet** och **Azure VNet**) i mål regionen upprättas före, under (som ett mellanliggande steg) eller efter redundansväxlingen.
  - Företaget kan använda [återställnings planer](site-recovery-create-recovery-plans.md) för att ange när anslutningar ska upprättas.
  - De kan ansluta mellan virtuella nätverk med VNet-peering eller plats-till-plats-VPN.
      - VNET-peering använder ingen VPN-gateway och har även andra restriktioner.
      - [Prissättningen](https://azure.microsoft.com/pricing/details/virtual-network) för VNet-peering beräknas annorlunda än VNet-till-VNet VPN gateway [prissättning](https://azure.microsoft.com/pricing/details/vpn-gateway). För redundans rekommenderar vi vanligt vis att använda samma anslutnings metod som käll nätverk, inklusive anslutnings typen, för att minimera oförutsägbara nätverks incidenter.

    ![Resurser i fullständig Azure-redundans](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Resurser i Azure: isolerad app-redundans

Du kan behöva redundansväxla på App-nivå. Till exempel för att redundansväxla en särskild app eller app-nivå som finns i ett dedikerat undernät.

- I det här scenariot kan du behålla IP-adresser, men det är inte allmänt lämpligt eftersom det ökar risken för inkonsekvenser i anslutningen. Du förlorar också under nätets anslutning till andra undernät i samma virtuella Azure-nätverk.
- Ett bättre sätt att använda app-redundans på undernäts nivå är att använda olika mål-IP-adresser för redundans (om du behöver anslutning till andra undernät i ett virtuellt nätverk) eller om du vill isolera varje app i ett eget dedikerat VNet i käll regionen. Med den senare metoden kan du upprätta anslutningar mellan nätverk i käll regionen och emulera samma beteende när du växlar över till mål regionen.  

I det här exemplet placerar företag A appar i käll regionen i dedikerade virtuella nätverk och upprättar anslutningen mellan dessa virtuella nätverk. Med den här designen kan de utföra isolerad app-redundans och behålla de privata IP-adresserna för källan i mål nätverket.

### <a name="before-failover"></a>Före redundans

Innan redundansväxlingen är arkitekturen följande:

- Virtuella program virtuella datorer finns i den primära Azure Asien, östra-regionen:
    - **APP1** Virtuella datorer finns i VNet- **källans VNet 1**: 10.1.0.0/16.
    - **APP2** Virtuella datorer finns i VNet- **källans VNet 2**: 10.2.0.0/16.
    - **Käll-VNet 1** har två undernät.
    - **Käll-VNet 2** har två undernät.
- Sekundär (mål) region är Azure Sydostasien-Sydostasien har en återställnings virtuella nätverk (återställnings-**VNet 1** och **återställnings-VNet 2**) som är identiska med **VNet 1** och **källans VNet 2**.
        - **Återställnings-VNet 1** och **återställnings-VNet 2** båda har två undernät som matchar under näten i **VNet 1** och **källans VNet 2** – Sydostasien har ytterligare ett VNet (**Azure VNet**) med adress utrymme 10.3.0.0/16.
        - **Azure VNet** innehåller ett undernät (**undernät 4**) med adress utrymmet 10.3.4.0/24.
        -Replikera noder för SQL Server Always on, domänkontrollant osv. finns i **undernät 4**.
- Det finns ett antal VPN-anslutningar för plats-till-plats: 
    - **Käll-VNet 1** och **Azure VNet**
    - **VNet-VNet 2** och **Azure VNet**
    - **VNet 1** och **VNet-VNet 2** är anslutna med VPN plats-till-plats
- **Återställnings-VNet 1** och **återställnings-VNet 2** är inte anslutet till någon annan virtuella nätverk.
- **Företag A** konfigurerar VPN-gatewayer på **återställnings-VNet 1** och **återställnings-VNet 2**, för att minska RTO.  
- **Återställnings-VNet1** och **återställnings-VNet2** är inte anslutna till något annat virtuellt nätverk.
- För att minska återställnings tids målet (RTO) konfigureras VPN-gatewayer på **återställnings VNet1** och **återställnings VNet2** före redundansväxling.

    ![Resurser i Azure före redundans i appar](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>Efter redundans

I händelse av ett avbrott eller problem som påverkar en enda app (i * * käll-VNet 2 i vårt exempel) kan företag A återställa den berörda appen på följande sätt:


- Koppla från VPN-anslutningar mellan **käll-VNet1** och käll- **VNet2**, och mellan **käll-VNet2** och **Azure VNet** .
- Upprätta VPN-anslutningar **mellan käll-VNet1** och **återställnings-VNet2**, och mellan **återställnings VNet2** och **Azure VNet**.
- Redundansväxla virtuella datorer i **käll VNet2** till **återställnings VNet2**.

![Resurser i Azure App-redundans](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- Det här exemplet kan utökas för att inkludera fler program och nätverks anslutningar. Rekommendationen är att följa en like-liknande anslutnings modell, så långt som möjligt, vid växling från källa till mål.
- VPN-gatewayer använder offentliga IP-adresser och gateway-hopp för att upprätta anslutningar. Om du inte vill använda offentliga IP-adresser, eller om du vill undvika extra hopp, kan du använda [Azure VNet-peering](../virtual-network/virtual-network-peering-overview.md) för att distribuera virtuella nätverk i [Azure-regioner som stöds](../virtual-network/virtual-network-manage-peering.md#cross-region).

## <a name="hybrid-resources-full-failover"></a>Hybrid resurser: fullständig redundans

I det här scenariot kör **företag B** en hybrid verksamhet, som är en del av program infrastrukturen som körs på Azure och den kvarvarande som körs lokalt. 

### <a name="before-failover"></a>Före redundans

Så här ser nätverks arkitekturen ut före redundansväxlingen.

- Virtuella program virtuella datorer finns i Azure Asien, östra.
- Asien, östra har ett VNet (**käll-VNet**) med adress utrymme 10.1.0.0/16.
  - Asien, östra har arbets belastningar som delas över tre undernät i det **virtuella käll-VNet**:
    - **Undernät 1**: 10.1.1.0/24
    - **Undernät 2**: 10.1.2.0/24
    - **Undernät 3**: 10.1.3.0/24, använder ett virtuellt Azure-nätverk med adress utrymme 10.1.0.0/16. Det här virtuella nätverket har namnet **Source VNet**
      - Den sekundära (mål) regionen är Azure Sydostasien:
  - Sydostasien har ett återställnings-VNet (**återställnings-VNet**) identiskt med det **virtuella käll** nätverket
- Virtuella datorer i Asien, östra är anslutna till ett lokalt Data Center med Azure ExpressRoute eller VPN för plats till plats.
- För att minska RTO, etablerar företag B gateways på återställnings-VNet i Azure Sydostasien före redundansväxlingen.
- Företag B tilldelar/verifierar mål-IP-adresser för replikerade virtuella datorer. Mål-IP-adressen är samma som käll-IP-adressen för varje virtuell dator.


![Lokal-till-Azure-anslutning före redundans](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Efter redundans


Om ett regionalt käll avbrott inträffar kan företag B redundansväxla alla sina resurser till mål regionen.

- Med mål-IP-adresser som redan finns på plats före redundansväxlingen kan företag B dirigera redundans och automatiskt upprätta anslutningar efter redundansväxlingen mellan **återställnings-VNet** och **Azure VNet**.
- Beroende på kraven i appen kan anslutningar mellan de två virtuella nätverk (**återställnings-VNet** och **Azure VNet**) i mål regionen upprättas före, under (som ett mellanliggande steg) eller efter redundansväxlingen. Företaget kan använda [återställnings planer](site-recovery-create-recovery-plans.md) för att ange när anslutningar ska upprättas.
- Den ursprungliga anslutningen mellan Azure Asien, östra och det lokala data centret bör kopplas från innan du upprättar anslutningen mellan Azure Sydostasien och det lokala data centret.
- Den lokala routningen konfigureras om så att den pekar på mål regionen och gatewayer efter redundans.

![Lokal-till-Azure-anslutning efter redundans](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>Hybrid resurser: isolerad app-redundans

Företag B kan inte redundansväxla isolerade appar på under näts nivån. Detta beror på att adress utrymmet på käll-och återställnings-virtuella nätverk är detsamma, och den ursprungliga källan till lokal anslutning är aktiv.

 - För att program återhämtnings företag B ska behöva placera varje app i sitt eget dedikerade Azure VNet.
 - Med varje app i ett separat VNet kan företag B växla över isolerade appar och dirigera käll anslutningar till mål regionen.

## <a name="next-steps"></a>Nästa steg

Läs mer om [återställnings planer](site-recovery-create-recovery-plans.md).
