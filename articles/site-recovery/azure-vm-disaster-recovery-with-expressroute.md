---
title: Integrera haveri beredskap för Azure ExpressRoute Azure VM med Azure Site Recovery
description: Beskriver hur du konfigurerar haveri beredskap för virtuella Azure-datorer med Azure Site Recovery och Azure ExpressRoute
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 0e1f670f2ba5ad31f29d56b2de40acd6e2bf18a9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88654386"
---
# <a name="integrate-expressroute-with-disaster-recovery-for-azure-vms"></a>Integrera ExpressRoute med haveri beredskap för virtuella Azure-datorer


Den här artikeln beskriver hur du integrerar Azure-ExpressRoute med [Azure Site Recovery](site-recovery-overview.md)när du konfigurerar haveri beredskap för virtuella Azure-datorer till en sekundär Azure-region.

Site Recovery möjliggör haveri beredskap för virtuella Azure-datorer genom att replikera virtuella Azure-Datadata till Azure.

- Om virtuella Azure-datorer använder [Azure Managed disks](../virtual-machines/managed-disks-overview.md)replikeras VM-data till en replikerad hanterad disk i den sekundära regionen.
- Om virtuella Azure-datorer inte använder hanterade diskar replikeras VM-data till ett Azure Storage-konto.
- Replikeringens slut punkter är offentliga, men replikeringstrafik för virtuella Azure-datorer korsar inte Internet.

Med ExpressRoute kan du utöka lokala nätverk till Microsoft Azure molnet över en privat anslutning, vilket fören klar av en anslutnings leverantör. Om du har konfigurerat ExpressRoute integreras det med Site Recovery enligt följande:

- **Vid replikering mellan Azure-regioner** är replikeringstrafik för haveri beredskap i Azure VM bara inom Azure och ExpressRoute behövs inte eller används för replikering. Men om du ansluter från en lokal plats till de virtuella Azure-datorerna på den primära Azure-platsen, finns det ett antal problem som du bör känna till när du konfigurerar haveri beredskap för de virtuella Azure-datorerna.
- **Redundans mellan Azure-regioner**: när avbrott uppstår växlar du över virtuella Azure-datorer från den primära till den sekundära Azure-regionen. Efter växling till en sekundär region finns det ett antal steg att vidta för att få åtkomst till de virtuella Azure-datorerna i den sekundära regionen med ExpressRoute.


## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar ska du kontrol lera att du förstår följande begrepp:

- ExpressRoute- [kretsar](../expressroute/expressroute-circuit-peerings.md)
- ExpressRoute- [routningsdomäner](../expressroute/expressroute-circuit-peerings.md#routingdomains)
- ExpressRoute- [platser](../expressroute/expressroute-locations.md).
- Arkitektur för Azure VM- [replikering](azure-to-azure-architecture.md)
- Så [här konfigurerar du replikering](azure-to-azure-tutorial-enable-replication.md) för virtuella Azure-datorer.
- Så här [växlar du över](azure-to-azure-tutorial-failover-failback.md) virtuella Azure-datorer.


## <a name="general-recommendations"></a>Allmänna rekommendationer

För bästa praxis rekommenderar vi att du gör följande när du konfigurerar Site Recovery att integrera med ExpressRoute för att säkerställa effektiva återställnings tider (återställnings tider) för haveri beredskap:

- Etablera nätverks komponenter före redundans till en sekundär region:
    - När du aktiverar replikering för virtuella Azure-datorer kan Site Recovery automatiskt distribuera nätverks resurser som nätverk, undernät och gateways i Azure-regionen, baserat på käll nätverks inställningar.
    - Site Recovery kan inte automatiskt konfigurera nätverks resurser som VNet-gatewayer.
    - Vi rekommenderar att du etablerar dessa ytterligare nätverks resurser före redundans. En liten nedtid är kopplad till den här distributionen och kan påverka den totala återställnings tiden, om du inte har använt det under distributions planeringen.
- Kör regelbunden återställnings granskning:
    - Testet verifierar din replikeringsstrategi utan dataförlust eller driftstopp och påverkar inte din produktionsmiljö. Det hjälper till att undvika konfigurations problem på sista minuten som kan påverka RTO negativt.
    - När du kör ett redundanstest för visningen rekommenderar vi att du använder ett separat Azure VM-nätverk, i stället för det standard nätverk som är konfigurerat när du aktiverar replikering.
- Använd olika IP-adressutrymme om du har en enda ExpressRoute-krets.
    - Vi rekommenderar att du använder ett annat IP-adressutrymme för det virtuella mål nätverket. Detta undviker problem när du upprättar anslutningar under regionala drifts avbrott.
    - Om du inte kan använda ett separat adress utrymme, måste du köra redundans för haveri beredskap på ett separat test nätverk med olika IP-adresser. Du kan inte ansluta två virtuella nätverk med överlappande IP-adressutrymme till samma ExpressRoute-krets.

## <a name="replicate-azure-vms-when-using-expressroute"></a>Replikera virtuella Azure-datorer när du använder ExpressRoute


Om du vill konfigurera replikering för virtuella Azure-datorer på en primär plats och du ansluter till de virtuella datorerna från din lokala plats via ExpressRoute, så behöver du göra följande:

1. [Aktivera replikering](azure-to-azure-tutorial-enable-replication.md) för varje virtuell Azure-dator.
2. Du kan också låta Site Recovery Konfigurera nätverk:
    - När du konfigurerar och aktiverar replikering ställer Site Recovery in nätverk, undernät och gateway-undernät i Azure-regionen för Azure för att matcha dem i käll regionen. Site Recovery mappar också mellan käll-och mål nätverk.
    - Om du inte vill att Site Recovery ska göra detta automatiskt skapar du nätverks resurserna på mål sidan innan du aktiverar replikeringen.
3. Skapa andra nätverks element:
    - Site Recovery skapar inte routningstabeller, VNet-gatewayer, VNet Gateway-anslutningar, VNet-peering eller andra nätverks resurser och anslutningar i den sekundära regionen.
    - Du måste skapa dessa ytterligare nätverks element i den sekundära regionen, när som helst innan du kör en redundansväxling från den primära regionen.
    - Du kan använda [återställnings planer](site-recovery-create-recovery-plans.md) och automatiserade skript för att konfigurera och ansluta dessa nätverks resurser.
1. Observera följande om du har en virtuell nätverks installation (NVA) distribuerad för att styra flödet av nätverks trafik:
    - Azures standard system väg för replikering av virtuella Azure-datorer är 0.0.0.0/0.
    - Vanligt vis definierar NVA-distributioner även en standard väg (0.0.0.0/0) som tvingar utgående Internet-trafik att flöda genom NVA. Standard vägen används när ingen annan speciell väg konfiguration kan hittas.
    - I så fall kan NVA bli överbelastad om all replikeringstrafik passerar genom NVA.
    - Samma begränsning gäller också när standard vägar används för routning av all Azure VM-trafik till lokala distributioner.
    - I det här scenariot rekommenderar vi att du [skapar en nätverks tjänst slut punkt](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) i ditt virtuella nätverk för tjänsten Microsoft. Storage, så att replikeringstrafiken inte lämnar Azure-gränser.

## <a name="replication-example"></a>Exempel på replikering

Företags distributioner har vanligt vis arbets belastningar som delas över flera Azure-virtuella nätverk, med en central anslutnings hubb för extern anslutning till Internet och lokala platser. En nav-och eker-topologi används vanligt vis tillsammans med ExpressRoute.

![Lokal-till-Azure med ExpressRoute före redundans](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **Region**. Appar distribueras i Azure Asien, östra-regionen.
- **Eker-virtuella nätverk**. Appar distribueras i två eker-virtuella nätverk:
    - **Källa vNet1**: 10.1.0.0/24.
    - **Källa vNet2**: 10.2.0.0/24.
    - Varje eker-virtuellt nätverk är anslutet till **hubb-vNet**.
- **Hubb-vNet**. Det finns ett nav vNet- **källport VNet**: 10.10.10.0/24.
  - Detta nav-vNet fungerar som gatekeeper.
  - All kommunikation mellan undernät går genom den här hubben.
    - **Hubbens vNet-undernät**. Hubbens vNet har två undernät:
    - **NVA-undernät**: 10.10.10.0/25. Det här under nätet innehåller en NVA (10.10.10.10).
    - **Gateway-undernät**: 10.10.10.128/25. Det här under nätet innehåller en ExpressRoute-gateway som är ansluten till en ExpressRoute-anslutning som dirigerar till den lokala platsen via en privat peering-routningsdomän.
- Det lokala data centret har en ExpressRoute-krets anslutning via en partner gräns i Hong Kong.
- All routning styrs via Azure Route-tabeller (UDR).
- All utgående trafik mellan virtuella nätverk eller det lokala data centret dirigeras via NVA.

### <a name="hub-and-spoke-peering-settings"></a>Inställningar för hubb och eker-peering

#### <a name="spoke-to-hub"></a>Spoke till hub

**Riktning** | **Inställning** | **Tillstånd**
--- | --- | ---
Spoke till hub | Tillåt virtuell nätverks adress | Enabled
Spoke till hub | Tillåt vidarebefordrad trafik | Enabled
Spoke till hub | Tillåt Gateway-överföring | Inaktiverad
Spoke till hub | Använd ta bort gatewayer | Enabled

 ![Konfiguration av eker-peering](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

#### <a name="hub-to-spoke"></a>Hub till spoke

**Riktning** | **Inställning** | **Tillstånd**
--- | --- | ---
Hub till spoke | Tillåt virtuell nätverks adress | Enabled
Hub till spoke | Tillåt vidarebefordrad trafik | Enabled
Hub till spoke | Tillåt Gateway-överföring | Enabled
Hub till spoke | Använd ta bort gatewayer | Inaktiverad

 ![Hubb till eker-peering-konfiguration](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="example-steps"></a>Exempel steg

I vårt exempel bör följande inträffa när du aktiverar replikering för virtuella Azure-datorer i käll nätverket:

1. Du [aktiverar replikering](azure-to-azure-tutorial-enable-replication.md) för en virtuell dator.
2. Site Recovery skapar en replik virtuella nätverk, undernät och gateway-undernät i mål regionen.
3. Site Recovery skapar mappningar mellan käll nätverken och de replik mål nätverk som skapas.
4. Du skapar virtuella nätverks-gatewayer manuellt, anslutningar för virtuell nätverksgateway, virtuell nätverks-peering eller andra nätverks resurser eller anslutningar.


## <a name="fail-over-azure-vms-when-using-expressroute"></a>Redundansväxla virtuella Azure-datorer när du använder ExpressRoute

När du har misslyckats med att skicka virtuella Azure-datorer till Azure-regionen med Site Recovery kan du komma åt dem med hjälp av ExpressRoute [privat peering](../expressroute/expressroute-circuit-peerings.md#privatepeering).

- Du måste ansluta ExpressRoute till mål-vNet med en ny anslutning. Den befintliga ExpressRoute-anslutningen överförs inte automatiskt.
- Hur du konfigurerar din ExpressRoute-anslutning till mål-vNet beror på din ExpressRoute-topologi.


### <a name="access-with-two-circuits"></a>Åtkomst med två kretsar

#### <a name="two-circuits-with-two-peering-locations"></a>Två kretsar med två peering-platser

Den här konfigurationen skyddar ExpressRoute-kretsar mot regional haveri. Om din primära peering-plats slutar fungera kan anslutningarna fortsätta från den andra platsen.

- Den krets som är ansluten till produktions miljön är vanligt vis den primära. Den sekundära kretsen har vanligt vis lägre bandbredd, vilket kan öka om en katastrof inträffar.
- Efter redundansväxlingen kan du upprätta anslutningar från den sekundära ExpressRoute-kretsen till målets vNet. Du kan också ha anslutningar konfigurerade och klara i händelse av katastrof, för att minska den totala återställnings tiden.
- Med samtidiga anslutningar till både primär-och mål-virtuella nätverk, se till att din lokala routning endast använder den sekundära kretsen och anslutningen efter redundansväxlingen.
- Käll-och mål virtuella nätverk kan ta emot nya IP-adresser eller behålla samma, efter redundansväxlingen. I båda fallen kan de sekundära anslutningarna upprättas före redundansväxlingen.


#### <a name="two-circuits-with-single-peering-location"></a>Två kretsar med en enda peering-plats

Den här konfigurationen skyddar mot att den primära ExpressRoute-kretsen Miss lyckas, men inte om ExpressRoute-peering-platsen fungerar, vilket påverkar båda kretsarna.

- Du kan ha samtidiga anslutningar från det lokala data centret till ett virtuellt nätverk med den primära kretsen och till målets vNet med den sekundära kretsen.
- Med samtidiga anslutningar till primär och mål, se till att lokal routning endast använder den sekundära kretsen och anslutningen efter redundansväxlingen.
-   Du kan inte ansluta båda kretsarna till samma vNet när kretsar skapas på samma peering-plats.

### <a name="access-with-a-single-circuit"></a>Åtkomst med en enda krets

I den här konfigurationen är det bara en ExpressRoute-krets. Även om kretsen har en redundant anslutning om en sådan går ned, kan en enda väg krets inte ge återhämtning om din peering-region slutar fungera. Tänk på följande:

- Du kan replikera virtuella Azure-datorer till alla Azure-regioner på [samma geografiska plats](azure-to-azure-support-matrix.md#region-support). Om mål Azure-regionen inte finns på samma plats som källan måste du aktivera ExpressRoute Premium om du använder en enda ExpressRoute-krets. Lär dig mer om [ExpressRoute-platser](../expressroute/expressroute-locations.md) och [ExpressRoute-priser](https://azure.microsoft.com/pricing/details/expressroute/).
- Du kan inte ansluta käll-och mål virtuella nätverk samtidigt till kretsen om samma IP-adressutrymme används i mål regionen. I det här scenariot:    
    -  Koppla från anslutning till käll sidan och upprätta sedan mål sidans anslutning. Den här anslutnings ändringen kan följas av skript som en del av en Site Recovery återställnings plan. Tänk på följande:
        - Om den primära regionen inte är tillgänglig i ett regionalt haveri kan det Miss lyckas med från kopplings åtgärden. Detta kan påverka skapandet av anslutningar till mål regionen.
        - Om du har skapat anslutningen i mål regionen och den primära regionen återkommer senare kan det uppstå paket om två samtidiga anslutningar försöker ansluta till samma adress utrymme.
        - Undvik detta genom att avsluta den primära anslutningen direkt.
        - När den primära anslutningen till den primära regionen har återställts kan den primära anslutningen upprättas när du har kopplat från den sekundära anslutningen.
-   Om ett annat adress utrymme används på det virtuella mål nätverket kan du samtidigt ansluta till käll-och mål virtuella nätverk från samma ExpressRoute-krets.


## <a name="failover-example"></a>Exempel på redundans

I vårt exempel använder vi följande topologi:

- Två olika ExpressRoute-kretsar i två olika peering-platser.
- Behåll privata IP-adresser för virtuella Azure-datorer efter redundansväxlingen.
- Mål återställnings regionen är Azure sydöstra Asien.
- En sekundär ExpressRoute krets-anslutning upprättas via en partner gräns i Singapore.

[Läs den här artikeln](site-recovery-retain-ip-azure-vm-failover.md#hybrid-resources-full-failover)för en enkel topologi som använder en enda ExpressRoute-krets med samma IP-adress efter redundansväxlingen.

### <a name="example-steps"></a>Exempel steg
För att automatisera återställning i det här exemplet behöver du göra följande:

1. Följ stegen för att konfigurera replikering.
2. [Redundansväxla de virtuella Azure-datorerna](azure-to-azure-tutorial-failover-failback.md)med dessa ytterligare steg under eller efter redundansväxlingen.

    a. Skapa Azure ExpressRoute-gatewayen i mål regionen Hub VNet. Detta måste du ansluta Hub-vNet till ExpressRoute-kretsen.

    b. Skapa anslutningen från Hub-vNet till mål ExpressRoute-kretsen.

    c. Konfigurera VNet-peering mellan mål regionens hubb och ekrar virtuella nätverk. Peering-egenskaperna i mål regionen kommer att vara desamma som de i käll regionen.

    d. Konfigurera UDR i hubb-VNet och de två ekrarna virtuella nätverk.

    - Egenskaperna för mål sidans UDR är desamma som de på käll sidan när du använder samma IP-adresser.
    - UDR bör ändras i enlighet med olika mål-IP-adresser.


Ovanstående steg kan följas av skript som en del av en [återställnings plan](site-recovery-create-recovery-plans.md). Beroende på kraven för program anslutningen och återställnings tiden kan ovanstående steg också slutföras innan du startar redundansväxlingen.

#### <a name="after-recovery"></a>Efter återställning

När du har återställt de virtuella datorerna och slutfört anslutningen är återställnings miljön följande.

![Lokal-till-Azure med ExpressRoute efter redundans](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## <a name="next-steps"></a>Nästa steg

Läs mer om hur du använder [återställnings planer](site-recovery-create-recovery-plans.md) för att automatisera redundanstestning av appar.
