---
title: Hög tillgänglighet, haveri beredskap, verksamhets kontinuitet
description: Lär dig mer om de alternativ för hög tillgänglighet, haveri beredskap (HADR) och affärs kontinuitet som är tillgängliga för SQL Server på virtuella Azure-datorer, t. ex. Always on-tillgänglighetsgrupper, kluster instans för växling vid fel, databas spegling, logg överföring och säkerhets kopiering & återställa till Azure Storage.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2020
ms.author: mathoma
ms.openlocfilehash: 31d22be5ee5480878633b9742837e3f5d6119043
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078952"
---
# <a name="business-continuity-and-hadr-for-sql-server-on-azure-virtual-machines"></a>Affärs kontinuitet och HADR för SQL Server på Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Verksamhets kontinuitet innebär att du kan fortsätta din verksamhet i händelse av en katastrof, planera för återställning och se till att dina data är hög tillgängliga. SQL Server på Azure Virtual Machines kan sänka kostnaden för en databas lösning med hög tillgänglighet och haveri beredskap (HADR). 

De flesta SQL Server HADR-lösningar stöds på virtuella datorer (VM) som både Azure-enda och hybrid lösningar. I en Azure-lösning körs hela HADR-systemet i Azure. I en hybrid konfiguration körs en del av lösningen i Azure och den andra delen körs lokalt i din organisation. Med flexibiliteten i Azure-miljön kan du flytta helt eller delvis till Azure för att uppfylla budget-och HADR krav för dina SQL Server databas system.

Den här artikeln jämför och kontrasterar de affärs kontinuitets lösningar som är tillgängliga för SQL Server på virtuella Azure-datorer. 

## <a name="overview"></a>Översikt

Det är upp till dig att se till att databas systemet har de HADR-funktioner som krävs för service nivå avtalet (SLA). Det faktum att Azure tillhandahåller mekanismer med hög tillgänglighet, till exempel tjänst återställning för moln tjänster och identifiering av haveri återställning för virtuella datorer, garanterar inte att du kan uppfylla service avtalet. Även om dessa mekanismer hjälper till att skydda den virtuella datorns hög tillgänglighet, skyddar de inte tillgängligheten för SQL Server som körs i den virtuella datorn. 

Det är möjligt att SQL Server-instansen kraschar medan den virtuella datorn är online och felfri. Även de funktioner för hög tillgänglighet som tillhandahålls av Azure tillåter drift stopp av de virtuella datorerna på grund av händelser som återställning från program varu-eller maskin varu problem och uppgraderingar av operativ system.

Geo-redundant lagring (GRS) i Azure implementeras med en funktion som kallas geo-replikering. GRS kanske inte är en lämplig lösning för katastrof återställning för dina databaser. Eftersom geo-replikering skickar data asynkront kan de senaste uppdateringarna gå förlorade vid en katastrof. Mer information om begränsningar för geo-replikering beskrivs i avsnittet [stöd för geo-replikering](#geo-replication-support) .

## <a name="deployment-architectures"></a>Distributions arkitekturer
Azure har stöd för dessa SQL Server tekniker för affärs kontinuitet:

* [Always on-tillgänglighetsgrupper](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)
* [Always on-instanser för redundanskluster (skyddas)](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
* [Logg överföring](/sql/database-engine/log-shipping/about-log-shipping-sql-server)
* [SQL Server säkerhets kopiering och återställning med Azure Blob Storage](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
* [Databas spegling](/sql/database-engine/database-mirroring/database-mirroring-sql-server) -inaktuell i SQL Server 2016

Du kan kombinera teknikerna för att implementera en SQL Server-lösning som har både funktioner för hög tillgänglighet och katastrof återställning. Beroende på vilken teknik du använder kan en hybrid distribution kräva en VPN-tunnel med det virtuella Azure-nätverket. I följande avsnitt visas några exempel på distributions arkitekturer.

## <a name="azure-only-high-availability-solutions"></a>Endast Azure: lösningar med hög tillgänglighet

Du kan ha en lösning med hög tillgänglighet för SQL Server på en databas nivå med Always on-tillgänglighetsgrupper. Du kan också skapa en lösning med hög tillgänglighet på en instans nivå med Always on Cluster instances. Om du vill ha ytterligare skydd kan du skapa redundans på båda nivåerna genom att skapa tillgänglighets grupper för kluster instanser för växling vid fel. 

| Teknik | Exempel arkitekturer |
| --- | --- |
| **Tillgänglighetsgrupper** |Tillgänglighets repliker som körs i virtuella Azure-datorer i samma region ger hög tillgänglighet. Du måste konfigurera en virtuell domänkontrollant, eftersom Windows-redundanskluster kräver en Active Directory-domän.<br/><br/> För högre redundans och tillgänglighet kan virtuella Azure-datorer distribueras i olika [tillgänglighets zoner](../../../availability-zones/az-overview.md) enligt beskrivningen i [tillgänglighets gruppens översikt](availability-group-overview.md). Om SQL Server de virtuella datorerna i en tillgänglighets grupp har distribuerats i tillgänglighets zoner använder du [Azure-standard Load Balancer](../../../load-balancer/load-balancer-overview.md) för lyssnaren, som dokumenteras i artiklarna för [Azure SQL VM CLI](./availability-group-az-commandline-configure.md) och [Azures snabb starts mallar](availability-group-quickstart-template-configure.md) .<br/> ![Diagram som visar "domänkontrollanten" ovanför "WSFC-kluster" som gjorts av "primär replik", "sekundär replik" och "fil resurs vittne".](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-ha-always-on.png)<br/>Mer information finns i [Konfigurera tillgänglighets grupper i Azure (GUI)](./availability-group-quickstart-template-configure.md). |
| **Instanser av kluster för växling vid fel** |Instanser av kluster för växling vid fel stöds på SQL Server virtuella datorer. Eftersom FCI-funktionen kräver delad lagring, kommer fem lösningar att fungera med SQL Server på virtuella Azure-datorer: <br/><br/> – Använda [Azure delade diskar](failover-cluster-instance-azure-shared-disks-manually-configure.md) för Windows Server 2019. Delade hanterade diskar är en Azure-produkt som gör det möjligt att koppla en hanterad disk till flera virtuella datorer samtidigt. Virtuella datorer i klustret kan läsa eller skriva till din anslutna disk baserat på den reservation som valts av det klustrade programmet via SCSI-beständiga reservationer (SCSI-PR). SCSI PR är en lösning för bransch standard lagring som används av program som körs på en lokal storage area network (SAN). Genom att aktivera SCSI PR på en hanterad disk kan du migrera dessa program till Azure som de är. <br/><br/>-Använd [Lagringsdirigering \( S2D \) ](failover-cluster-instance-storage-spaces-direct-manually-configure.md) för att tillhandahålla en programvarubaserad virtuell SAN för Windows Server 2016 och senare.<br/><br/>– Användning av en [Premium-filresurs](failover-cluster-instance-premium-file-share-manually-configure.md) för Windows Server 2012 och senare. Premium-filresurser är SSD-baserade, har en konsekvent låg latens och stöds fullt ut för användning med FCI.<br/><br/>-Använda lagring som stöds av en partner lösning för klustring. Ett särskilt exempel som använder SIOS-DataKeeper finns i blogg inlägget [kluster för växling vid fel och SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>-Använder delad block lagring för ett fjärran sluten iSCSI-mål via Azure ExpressRoute. NetApp Private Storage (NPS) visar till exempel ett iSCSI-mål via ExpressRoute med Equinix till virtuella Azure-datorer.<br/><br/>För delade lösningar för lagring och datareplikering från Microsoft-partners kontaktar du leverantören för eventuella problem som rör åtkomst till data vid redundans.<br/><br/>|

## <a name="azure-only-disaster-recovery-solutions"></a>Endast Azure: katastrof återställnings lösningar
Du kan ha en katastrof återställnings lösning för dina SQL Server-databaser i Azure med hjälp av tillgänglighets grupper, databas spegling eller säkerhets kopiering och återställning med Storage-blobbar.

| Teknik | Exempel arkitekturer |
| --- | --- |
| **Tillgänglighetsgrupper** |Tillgänglighets repliker som körs över flera data Center i virtuella Azure-datorer för haveri beredskap. Den här lösningen över flera regioner hjälper till att skydda mot ett fullständigt avbrott i webbplatsen. <br/> ![Diagram som visar två regioner med en "primär replik" och "sekundär replik" ansluten med ett "asynkront genomförande".](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-alwayson.png)<br/>Inom en region bör alla repliker finnas inom samma moln tjänst och samma virtuella nätverk. Eftersom varje region har ett separat virtuellt nätverk, kräver de här lösningarna nätverks-till-nätverks anslutning. Mer information finns i [Konfigurera en anslutning från nätverk till nätverk med hjälp av Azure Portal](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Detaljerade anvisningar finns i [Konfigurera en SQL Server Always on-tillgänglighets grupp i olika Azure-regioner](availability-group-manually-configure-multiple-regions.md).|
| **Databas spegling** |Huvud servrar och spegling och servrar som körs i olika data Center för haveri beredskap. Du måste distribuera dem med hjälp av Server certifikat. SQL Server databas spegling stöds inte för SQL Server 2008 eller SQL Server 2008 R2 på en virtuell Azure-dator. <br/>![Diagram som visar "huvud namn" i en region som är ansluten till "spegel i en annan region med" höga prestanda ".](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-dbmirroring.png) |
| **Säkerhets kopiering och återställning med Azure Blob Storage** |Produktions databaser som säkerhets kopie ras direkt till Blob Storage i ett annat data Center för haveri beredskap.<br/>![Diagram som visar en "databas" i en region som backar upp till "Blob Storage" i en annan region.](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-backup-restore.png)<br/>Mer information finns i [säkerhets kopiering och återställning för SQL Server på virtuella Azure-datorer](../../../azure-sql/virtual-machines/windows/backup-restore.md). |
| **Replikera och redundansväxla SQL Server till Azure med Azure Site Recovery** |Produktion SQL Server instans i ett Azure-datacenter replikeras direkt till Azure Storage i ett annat Azure-datacenter för haveri beredskap.<br/>![Diagram som visar en "databas" i ett Azure-datacenter med "ASR-replikering" för haveri beredskap i ett annat data Center. ](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-standalone-sqlserver-asr.png)<br/>Mer information finns i [skydda SQL Server att använda SQL Server haveri beredskap och Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hybrid IT: lösningar för katastrof återställning
Du kan ha en katastrof återställnings lösning för dina SQL Server-databaser i en hybrid IT-miljö med hjälp av tillgänglighets grupper, databas spegling, logg överföring och säkerhets kopiering och återställning med Azure Blob Storage.

| Teknik | Exempel arkitekturer |
| --- | --- |
| **Tillgänglighetsgrupper** |Vissa tillgänglighets repliker som körs i virtuella Azure-datorer och andra repliker som körs lokalt för haveri beredskap mellan platser. Produktions platsen kan vara antingen lokalt eller i ett Azure-datacenter.<br/>![Tillgänglighetsgrupper](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-alwayson.png)<br/>Eftersom alla tillgänglighets repliker måste finnas i samma redundanskluster måste klustret omfatta båda nätverken (ett redundanskluster med flera undernät). Den här konfigurationen kräver en VPN-anslutning mellan Azure och det lokala nätverket.<br/><br/>För lyckad haveri beredskap för dina databaser bör du också installera en replik-domänkontrollant på webbplatsen för haveri beredskap.|
| **Databas spegling** |En partner som körs i en virtuell Azure-dator och den andra som körs lokalt för haveri beredskap mellan platser med hjälp av Server certifikat. Partners behöver inte vara i samma Active Directory domän och ingen VPN-anslutning krävs.<br/>![Databas spegling](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-dbmirroring.png)<br/>Ett annat scenario för databas spegling omfattar en partner som körs i en virtuell Azure-dator och den andra som körs lokalt i samma Active Directory domän för haveri beredskap mellan platser. Det krävs en [VPN-anslutning mellan det virtuella Azure-nätverket och det lokala nätverket](../../../vpn-gateway/tutorial-site-to-site-portal.md) .<br/><br/>För lyckad haveri beredskap för dina databaser bör du också installera en replik-domänkontrollant på webbplatsen för haveri beredskap. SQL Server databas spegling stöds inte för SQL Server 2008 eller SQL Server 2008 R2 på en virtuell Azure-dator. |
| **Logg överföring** |En server som körs på en virtuell Azure-dator och den andra som körs lokalt för haveri beredskap mellan platser. Logg överföring är beroende av Windows fildelning, så en VPN-anslutning mellan det virtuella Azure-nätverket och det lokala nätverket krävs.<br/>![Loggöverföring](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-log-shipping.png)<br/>För lyckad haveri beredskap för dina databaser bör du också installera en replik-domänkontrollant på webbplatsen för haveri beredskap. |
| **Säkerhets kopiering och återställning med Azure Blob Storage** |Lokala produktions databaser som säkerhets kopie ras direkt till Azure Blob Storage för haveri beredskap.<br/>![Säkerhetskopiering och återställning](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-backup-restore.png)<br/>Mer information finns i [säkerhets kopiering och återställning för SQL Server på Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/backup-restore.md). |
| **Replikera och redundansväxla SQL Server till Azure med Azure Site Recovery** |Den lokala produktions SQL Servers instansen replikeras direkt till Azure Storage för haveri beredskap.<br/>![Replikera med Azure Site Recovery](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-standalone-sqlserver-asr.png)<br/>Mer information finns i [skydda SQL Server att använda SQL Server haveri beredskap och Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="free-dr-replica-in-azure"></a>Kostnads fri DR-replikering i Azure

Om du har [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)kan du implementera återställnings planer för Hybrid haveri beredskap (Dr) med SQL Server utan att det uppstår ytterligare licens kostnader för den passiva haveri beredskaps instansen.

Du kan till exempel ha två kostnads fria passiva sekundär zoner när alla tre repliker finns i Azure: 

![Två kostnads fria passiva passiva när allt i Azure](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/failover-with-primary-in-azure.png)

Eller så kan du konfigurera en hybrid växlings miljö med en licensierad primär lokal, ett kostnads fritt passivt för HA, en kostnads fri passiv passiv för DR lokalt och en kostnads fri passiv passiv för DR i Azure:

![Tre kostnads fria passiva datorer när miljön är hybrid med en primär lokal replik](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-with-primary-on-prem.png)

Mer information finns i avsnittet om [villkor för produktlicensiering](https://www.microsoft.com/licensing/product-licensing/products). 

Om du vill aktivera den här förmånen går du till din [SQL Server virtuella dator resursen](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource). Välj **Konfigurera** under **Inställningar** och välj sedan alternativet för **haveri beredskap** under **SQL Server licens**. Markera kryss rutan för att bekräfta att SQL Server VM ska användas som passiv replik och välj sedan **Använd** för att spara inställningarna. 

![Konfigurera en katastrof återställnings replik i Azure](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Viktiga överväganden för SQL Server HADR i Azure
Virtuella Azure-datorer, lagring och nätverk har olika operativa egenskaper än en lokal, icke-virtualiserad IT-infrastruktur. En lyckad implementering av en HADR SQL Server-lösning i Azure kräver att du förstår dessa skillnader och utformar din lösning för att hantera dem.

### <a name="high-availability-nodes-in-an-availability-set"></a>Noder med hög tillgänglighet i en tillgänglighets uppsättning
Med tillgänglighets uppsättningar i Azure kan du placera noderna med hög tillgänglighet i separata fel domäner och uppdaterings domäner. Azure-plattformen tilldelar en uppdaterings domän och en feldomän till varje virtuell dator i din tillgänglighets uppsättning. Den här konfigurationen i ett Data Center garanterar att minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhålls händelse och uppfyller Azure-SLA på 99,95 procent. 

Om du vill konfigurera en hög tillgänglighets installation placerar du alla deltagande SQL Server virtuella datorer i samma tillgänglighets uppsättning för att undvika program-eller data förlust under en underhålls händelse. Endast noder i samma moln tjänst kan ingå i samma tillgänglighets uppsättning. Mer information finns i [Hantera tillgängligheten för virtuella datorer](../../../virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="high-availability-nodes-in-an-availability-zone"></a>Noder med hög tillgänglighet i en tillgänglighets zon
Tillgänglighets zoner är unika fysiska platser inom en Azure-region. Varje zon består av ett eller flera data Center som är utrustade med oberoende strömförsörjning, kylning och nätverk. Den fysiska avgränsningen av tillgänglighets zoner inom en region hjälper till att skydda program och data från data Center problem genom att se till att minst en virtuell dator är tillgänglig och uppfyller Azure-SLA på 99,99 procent. 

Du konfigurerar hög tillgänglighet genom att placera deltagande SQL Server virtuella datorer sprids över tillgänglighets zoner i regionen. Det kommer att finnas ytterligare kostnader för överföringar från nätverk till nätverk mellan tillgänglighets zoner. Mer information finns i [tillgänglighets zoner](../../../availability-zones/az-overview.md). 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Funktions sätt för redundanskluster i Azure-nätverk
En icke-RFC-kompatibel DHCP-tjänst i Azure kan orsaka att vissa konfigurationer för redundanskluster inte kan skapas. Detta fel uppstår eftersom klustrets nätverks namn har tilldelats en dubblett av IP-adressen, till exempel samma IP-adress som en av klusternoderna. Det här är ett problem när du använder tillgänglighets grupper, som är beroende av Windows-funktionen kluster för växling vid fel.

Överväg scenariot när ett kluster med två noder skapas och kopplas från:

1. Klustret är online och NOD1 begär en dynamiskt tilldelad IP-adress för klustrets nätverks namn.
2. DHCP-tjänsten ger ingen annan IP-adress än NODE1's egen IP-adress, eftersom DHCP-tjänsten känner av att begäran kommer från NOD1.
3. Windows upptäcker att en duplicerad adress är tilldelad både till NOD1 och klustrets nätverks namn och att standard kluster gruppen inte kan anslutas.
4. Standard kluster gruppen flyttas till NOD2. NOD2 behandlar NODE1's IP-adress som kluster-IP-adress och ansluter till standard kluster gruppen online.
5. När NOD2 försöker upprätta en anslutning med NOD1 lämnar paket som dirigeras till NOD1 aldrig NOD2 eftersom den löser NODE1's IP-adress till sig själv. NOD2 kan inte upprätta en anslutning till NOD1 och sedan förlora kvorum och stänga av klustret.
6. NOD1 kan skicka paket till NOD2, men NOD2 kan inte svara. NOD1 förlorar kvorum och stänger av klustret.

Du kan undvika det här scenariot genom att tilldela en oanvänd statisk IP-adress till kluster nätverks namnet för att ta klustrets nätverks namn online. Du kan till exempel använda en länk lokal IP-adress som 169.254.1.1. För att förenkla den här processen, se [Konfigurera Windows-redundanskluster i Azure för tillgänglighets grupper](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Mer information finns i [Konfigurera tillgänglighets grupper i Azure (GUI)](./availability-group-quickstart-template-configure.md).

### <a name="support-for-availability-group-listeners"></a>Stöd för tillgänglighets grupps lyssnare
Tillgänglighets grupps lyssnare stöds på virtuella Azure-datorer som kör Windows Server 2012 och senare. Det här stödet möjliggörs med hjälp av belastningsutjämnade slut punkter som är aktiverade på de virtuella Azure-datorer som är tillgänglighets grupps noder. Du måste följa särskilda konfigurations steg för att lyssnarna ska fungera för både klient program som körs i Azure och de som körs lokalt.

Det finns två huvud alternativ för att konfigurera din lyssnare: extern (offentlig) eller intern. Den externa (offentliga) lyssnaren använder en Internetbaserad belastningsutjämnare och är associerad med en offentlig virtuell IP-adress som är tillgänglig via Internet. En intern lyssnare använder en intern belastningsutjämnare och stöder endast klienter inom samma virtuella nätverk. För antingen belastnings Utjämnings typ måste du aktivera direkt Server RETUR. 

Om tillgänglighets gruppen sträcker sig över flera Azure-undernät (till exempel en distribution som korsar Azure-regioner) måste klient anslutnings strängen innehålla `MultisubnetFailover=True` . Detta resulterar i parallella anslutnings försök till replikerna i de olika under näten. Anvisningar om hur du konfigurerar en lyssnare finns i [Konfigurera en ILB-lyssnare för tillgänglighets grupper i Azure](availability-group-listener-powershell-configure.md).


Du kan fortfarande ansluta till varje tillgänglighets replik separat genom att ansluta direkt till tjänst instansen. Eftersom tillgänglighets grupper är bakåtkompatibla med databas speglings klienter, kan du också ansluta till tillgänglighets replikerna som databas speglings partner så länge replikerna har kon figurer ATS på samma sätt som databas spegling:

* Det finns en primär replik och en sekundär replik.
* Den sekundära repliken är konfigurerad som icke läsbar (**läsbar sekundär** alternativ uppsättning till **Nej**).

Här är ett exempel på en klient anslutnings sträng som motsvarar den här databas speglings konfigurationen med ADO.NET eller SQL Server Native Client:

```console
Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;
```

Mer information om klient anslutningar finns i:

* [Använda nyckelord för anslutnings strängen med SQL Server Native Client](/sql/relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client)
* [Ansluta klienter till en databas speglings session (SQL Server)](/sql/database-engine/database-mirroring/connect-clients-to-a-database-mirroring-session-sql-server)
* [Ansluter till tillgänglighets gruppens lyssnare i hybrid IT](/archive/blogs/sqlalwayson/connecting-to-availability-group-listener-in-hybrid-it)
* [Tillgänglighets grupps lyssnare, klient anslutning och programredundans (SQL Server)](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)
* [Använda Database-Mirroring anslutnings strängar med tillgänglighets grupper](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)

### <a name="network-latency-in-hybrid-it"></a>Nätverks fördröjning i hybrid IT
Distribuera din HADR-lösning med antagandet att det kan finnas perioder med hög nätverks fördröjning mellan ditt lokala nätverk och Azure. När du distribuerar repliker till Azure ska du använda asynkron incheckning i stället för synkront genomförande för Synkroniseringsläge. När du distribuerar databas speglings servrar både lokalt och i Azure använder du läget för hög prestanda i stället för hög säkerhets läge.

### <a name="geo-replication-support"></a>Stöd för geo-replikering
Geo-replikering i Azure-diskar har inte stöd för data filen och logg filen för samma databas som ska lagras på separata diskar. GRS replikerar ändringar på varje disk oberoende av varandra och asynkront. Den här mekanismen garanterar Skriv ordningen inom en enskild disk på den geo-replikerade kopian, men inte över geo-replikerade kopior av flera diskar. Om du konfigurerar en databas för att lagra data filen och dess loggfil på separata diskar kan de återställda diskarna efter en katastrof innehålla en uppdaterad kopia av data filen än logg filen, vilket bryter loggen för Skriv åtgärder i SQL Server och egenskaperna (Atomicitet, konsekvens, isolering och hållbarhet) för transaktioner. 

Om du inte har alternativet att inaktivera geo-replikering på lagrings kontot bör du behålla alla data och loggfiler för en databas på samma disk. Om du måste använda mer än en disk på grund av databasens storlek, distribuerar du en av de katastrof återställnings lösningar som listas tidigare för att säkerställa dataredundans.

## <a name="next-steps"></a>Nästa steg

Bestäm om en [tillgänglighets grupp](availability-group-overview.md) eller en [instans av redundanskluster](failover-cluster-instance-overview.md) är den bästa affärs kontinuitets lösningen för ditt företag. Granska sedan de [bästa metoderna](hadr-cluster-best-practices.md) för att konfigurera din miljö för hög tillgänglighet och haveri beredskap.
