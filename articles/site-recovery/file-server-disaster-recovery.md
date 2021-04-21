---
title: Skydda en filserver med hjälp av Azure Site Recovery
description: I den här artikeln beskrivs hur du skyddar en filserver med hjälp av Azure Site Recovery
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: sharrai
ms.custom: mvc
ms.openlocfilehash: 5209e715fab422a50e31810b5eb0d370d5fc61cd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792533"
---
# <a name="protect-a-file-server-by-using-azure-site-recovery"></a>Skydda en filserver med hjälp av Azure Site Recovery 

[Azure Site Recovery](site-recovery-overview.md) bidrar till din BCDR-strategi för affärskontinuitet och haveriberedskap genom att hålla dina företagsprogram igång och köra dem vid planerade och oplanerade avbrott. Site Recovery hanterar och samordnar haveriberedskap för lokala datorer och virtuella Azure-datorer (VM). Haveriberedskap omfattar replikering, redundans och återställning av olika arbetsbelastningar.

I den här artikeln beskrivs hur du skyddar en filserver med hjälp av Site Recovery och gör andra rekommendationer som passar olika miljöer. 

- [Replikera Azure IaaS-filservrar](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Replikera en lokal filserver med hjälp av Site Recovery](#replicate-an-on-premises-file-server-by-using-site-recovery)

## <a name="file-server-architecture"></a>Filserverarkitektur
Målet med ett öppet distribuerat fildelningssystem är att tillhandahålla en miljö där en grupp geografiskt spridda användare kan samarbeta effektivt med filer och försäkra sig om att deras integritetskrav uppfylls. Ett typiskt lokalt filserverekosystem som stöder ett stort antal innehållsobjekt använder Distributed File System Replication (DFSR) för replikeringsschemaläggning och bandbreddsbegränsning. 

DFSR använder en komprimeringsalgoritm som kallas Remote Differential Compression (RDC) som kan användas för att effektivt uppdatera filer över ett nätverk med begränsad band. Den identifierar tillägg, borttagningar och omflyttningar av data i filer. DFSR aktiveras för att bara replikera de ändrade filblocken när filer uppdateras. Det finns också filservermiljöer, där dagliga säkerhetskopior görs under tider med låg arbetsbelastning, som tillgodoser haveriberedskapsbehoven. DFSR är inte implementerad.

Följande diagram illustrerar filservermiljön med DFSR implementerad.
        
![DFSR-arkitektur](media/site-recovery-file-server/dfsr-architecture.JPG)

I det föregående diagrammet deltar flera filservrar som kallas för medlemmar aktivt i replikeringen av filer i en replikeringsgrupp. Innehållet i den replikerade mappen är tillgängligt för alla klienter som skickar begäranden till någon av medlemmarna, även om en medlem koppas från.

## <a name="disaster-recovery-recommendations-for-file-servers"></a>Rekommendationer för haveriberedskap för filservrar

* **Replikera en filserver med hjälp av Site Recovery**: Filservrar kan replikeras i Azure med hjälp av Site Recovery. När en eller flera lokala filservrar är otillgängliga kan de virtuella återställningsdatorerna göras tillgängliga i Azure. De virtuella datorerna kan sedan hantera begäranden från klienter, lokalt, förutsatt att det finns VPN-anslutningar för plats till plats och Active Directory har konfigurerats i Azure. Du kan använda den här metoden när det gäller en DFSR-konfigurerad miljö eller en enkel filservermiljö utan DFSR. 

* **Utöka DFSR till en Azure IaaS-VM**: I en klustrad filservermiljö med DFSR implementerad kan du utöka lokal DFSR till Azure. En virtuell Azure-dator aktiveras sedan för att utföra filserverrollen. 

    * När beroenden för VPN-anslutning för plats till plats och Active Directory hanteras och DFSR är på plats, och när en eller flera lokala filservrar inte är tillgängliga, kan klienter ansluta till den virtuella Azure-datorn, som hanterar begäranden.

    * Du kan använda den här metoden om dina virtuella datorer har konfigurationer som inte stöds av Site Recovery. Ett exempel är en delad klusterdisk, som ibland används i filservermiljöer. DFSR fungerar också bra i miljöer med låg bandbredd med medelhög kundomsättning. Du måste ta hänsyn till den extra kostnaden med att ha en virtuell Azure-dator igång hela tiden. 

* **Använd Azure File Sync för att replikera** dina filer: Om du planerar att använda molnet eller redan använder en virtuell Azure-dator kan du använda Azure File Sync. Azure File Sync erbjuder synkronisering av fullständigt hanterade filresurser i molnet som är tillgängliga via SMB-protokollet (industry-standard [Server Message Block).](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) Azure-filresurser kan sedan monteras samtidigt av molndistributioner eller lokala distributioner av Windows, Linux och macOS. 

Följande diagram hjälper dig att avgöra vilken strategi du ska använda för din filservermiljö.

![Beslutsträd](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-in-your-decisions-about-disaster-recovery-to-azure"></a>Faktorer att tänka på dina beslut om haveriberedskap i Azure

|Miljö  |Rekommendation  |Några saker att tänka på |
|---------|---------|---------|
|Filservermiljö med eller utan DFSR|   [Använda Site Recovery för replikering](#replicate-an-on-premises-file-server-by-using-site-recovery)   |    Site Recovery stöder inte delade diskkluster eller nätverksansluten lagring (NAS). Om din miljö använder de här konfigurationerna kan du använda någon av de andra metoderna. <br> Site Recovery stöder inte SMB 3.0. Den replikerade virtuella datorn ändras bara när ändringar som gjorts av filen uppdateras på den ursprungliga platser för filerna.<br>  Site Recovery erbjuder en nästan synkron datareplikeringsprocess, och i händelse av ett oplanerat redundansscenario kan det därför finnas potentiell dataförlust och kan skapa USN-matchningsproblem.
|Filservermiljö med DFSR     |  [Utöka DFSR till en virtuell Azure IaaS-dator](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |    DFSR fungerar bra i miljöer med extrem bandbreddsbegränsning. Den här metoden kräver en virtuell Azure-dator som är igång hela tiden. Du måste kunna redovisa kostnaden för den virtuella datorn i planeringen.         |
|Virtuell Azure IaaS-dator     |     File Sync     |     Om du använder File Sync i ett haveriberedskapsscenario måste du under redundansväxling vidta manuella åtgärder för att se till att filresurserna är tillgängliga för klientdatorn på ett transparent sätt. File Sync kräver att port 445 är öppen från klientdatorn.     |


### <a name="site-recovery-support"></a>Site Recovery-stöd
Eftersom Site Recovery-replikering är programoberoende förväntas dessa rekommendationer att gälla för följande scenarier.

| Källa  |Till en sekundär plats  |Till Azure
|---------|---------|---------|
|Azure|  -|Yes|
|Hyper-V|  Ja  |Ja
|VMware  |Ja|  Ja
|Fysisk server|  Ja  |Ja
 

> [!IMPORTANT]
> Innan du fortsätter med någon av följande tre metoder ser du till att åtgärda dessa beroenden.



**Plats-till plats-anslutning**: En direktanslutning mellan den lokala platsen och Azure-nätverket måste upprättas för att tillåta kommunikation mellan servrar. Använd en säker VPN-anslutning för plats till plats till ett virtuellt Azure-nätverk som används som haveriberedskapsplats. Mer information finns i den här artikeln om att [upprätta en VPN-anslutning för plats-till-plats mellan en lokal plats och ett virtuellt Azure-nätverk](../vpn-gateway/tutorial-site-to-site-portal.md).

**Active Directory**: DFSR är beroende av Active Directory. Det innebär att Active Directory-skogen med lokala domänkontrollanter utökas till haveriberedskapsplatsen i Azure. Även om du inte använder DFSR måste du, om de avsedda användarna måste beviljas åtkomst eller verifieras för åtkomst, vidta dessa åtgärder. Mer information finns i [Utöka lokal Active Directory till Azure](./site-recovery-active-directory.md).

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Rekommendation för haveriberedskap för virtuella Azure IaaS-datorer

Om du konfigurerar och hanterar haveriberedskap för filservrar som finns på virtuella Azure IaaS-datorer kan du välja mellan två alternativ, baserat på om du vill flytta till [Azure Files](../storage/files/storage-files-introduction.md):

* [Använda File Sync](#use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine)
* [Använda Site Recovery](#replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery)

## <a name="use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine"></a>Använda File Sync för att replikera filer som finns på en virtuell IaaS-dator

Azure Files kan användas för att fullständigt ersätta eller komplettera traditionella lokala filservrar eller NAS-enheter. Azure-filresurser kan också replikeras med File Sync till Windows-servrar, antingen lokalt eller i molnet, för högpresterande och distribuerad cachelagring av data där den används. Följande steg beskriver haveriberedskapsrekommendationen för virtuella Azure-datorer som utför samma funktioner som traditionella filservrar:
* Skydda datorer med hjälp av Site Recovery. Följ stegen i [Replikera en virtuell Azure-dator till en annan region](azure-to-azure-quickstart.md).
* Använd File Sync för att replikera filer från den virtuella dator som fungerar som filservern i molnet.
* Använd funktionen [återställningsplan](site-recovery-create-recovery-plans.md) i Site Recovery för att [montera Azure-filresursen](../storage/files/storage-how-to-use-files-windows.md) och öppna resursen på den virtuella datorn.

Följande steg beskriver kort hur du använder File Sync:

1. [Skapa ett lagringskonto i Azure](../storage/common/storage-account-create.md?toc=/azure/storage/files/toc.json). Om du har valt geo-redundant lagring med läsåtkomst får du läsåtkomst för dina data från den sekundära regionen vid ett haveri. Mer information finns i [Haveriberedskap och redundans för lagringskonto.](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2ffiless%2ftoc.json)
2. [Skapa en filresurs](../storage/files/storage-how-to-create-file-share.md).
3. [Starta File Sync](../storage/file-sync/file-sync-deployment-guide.md) på din Azure-filserver.
4. Skapa en synkroniseringsgrupp. Slutpunkter i en synkroniseringsgrupp synkroniseras med varandra. En synkroniseringsgrupp måste innehålla minst en molnslutpunkt, som representerar en Azure-filresurs. En synkroniseringsgrupp måste även innehålla en serverslutpunkt, som representerar en sökväg på en Windows-server.
5. Filerna synkroniseras nu i Azure-filresursen och på din lokala server.
6. Vid ett haveri i den lokala miljön utför du en redundansväxling med hjälp av en [återställningsplan](site-recovery-create-recovery-plans.md). Lägg till skriptet i [montera Azure-filresursen](../storage/files/storage-how-to-use-files-windows.md) och öppna resursen på den virtuella datorn.

### <a name="replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery"></a>Replikera en virtuell IaaS-filserverdator med hjälp av Site Recovery

Om du har lokala klienter som har åtkomst till den virtuella IaaS-filserverdatorn utför du alla av de följande stegen. Annars går du till steg 3.

1. Upprätta en VPN-anslutning för plats-till-plats mellan den lokala platsen och Azure-nätverket.
2. Utöka lokal Active Directory.
3. [Konfigurera haveriberedskap](azure-to-azure-tutorial-enable-replication.md) för IaaS-filserverdatorn för en sekundär region.


Mer information om haveriberedskap för en sekundär region finns i [den här artikeln](./azure-to-azure-architecture.md).


## <a name="replicate-an-on-premises-file-server-by-using-site-recovery"></a>Replikera en lokal filserver med hjälp av Site Recovery

I följande steg beskrivs replikering för en virtuell VMware-dator. Stegen för replikering av en virtuell Hyper-V-dator finns i [den här självstudien](./hyper-v-azure-tutorial.md).

1. [Förbered Azure-resurser](tutorial-prepare-azure.md) för replikering av lokala datorer.
2. Upprätta en VPN-anslutning för plats-till-plats mellan den lokala platsen och Azure-nätverket. 
3. Utöka lokal Active Directory.
4. [Förbered lokala VMware-servrar.](./vmware-azure-tutorial-prepare-on-premises.md)
5. [Konfigurera haveriberedskap](./vmware-azure-tutorial.md) för Azure för lokala virtuella datorer.

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Utöka DFSR till en virtuell Azure IaaS-dator

1. Upprätta en VPN-anslutning för plats-till-plats mellan den lokala platsen och Azure-nätverket. 
2. Utöka lokal Active Directory.
3. [Skapa och etablera en virtuell filserverdator](../virtual-machines/windows/quick-create-portal.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) på det virtuella Azure-nätverket.
Kontrollera att den virtuella datorn har lagts till i samma virtuella Azure-nätverk, som är ansluten till den lokala miljön. 
4. Installera och [konfigurera DFSR](https://techcommunity.microsoft.com/t5/storage-at-microsoft/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of/ba-p/424877) på Windows Server.
5. [Implementera en DFS-namnrymd](/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6. Med DFS-namnrymden implementerad kan redundansväxling av delade mappar från produktions- till haveriberedskapsplatser göras genom att uppdatera DFS-namnrymdens mappmål. Efter dessa ändringar av DFS-namnrymden replikeras via Active Directory ansluts användare till lämpliga mappmål transparent.

## <a name="use-file-sync-to-replicate-your-on-premises-files"></a>Använda File Sync för att replikera dina lokala filer
Du kan använda File Sync för att replikera filer i molnet. Vid ett haveri och när din lokala filserver blir otillgänglig kan du montera de önskade filplatserna från molnet och fortsätta till tjänstbegäranden från klientdatorer.
Integrera File Sync med Site Recovery:

* Skydda filserverdatorerna med hjälp av Site Recovery. Följ stegen i [den här självstudien](./vmware-azure-tutorial.md).
* Använd File Sync för att replikera filer i molnet från den dator som fungerar som filserver.
* Använd funktionen återställningsplan i Site Recovery för att lägga till skript för att montera Azure-filresursen på den redundansväxlade virtuella filserverdatorn i Azure.

Följ dessa steg om du vill använda File Sync:

1. [Skapa ett lagringskonto i Azure](../storage/common/storage-account-create.md?toc=/azure/storage/files/toc.json). Om du har valt geo-redundant lagring (rekommenderas) med läsåtkomst har du läsåtkomst för dina data från den sekundära regionen vid ett haveri. Mer information finns i [Haveriberedskap och redundans för lagringskonto.](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2ffiless%2ftoc.json)
2. [Skapa en filresurs](../storage/files/storage-how-to-create-file-share.md).
3. [Distribuera File Sync](../storage/file-sync/file-sync-deployment-guide.md) på den lokala filservern.
4. Skapa en synkroniseringsgrupp. Slutpunkter i en synkroniseringsgrupp synkroniseras med varandra. En synkroniseringsgrupp måste innehålla minst en molnslutpunkt, som representerar en Azure-filresurs. Synkroniseringsgruppen måste även innehålla en serverslutpunkt, som representerar en sökväg på den lokala Windows-servern.
5. Filerna synkroniseras nu i Azure-filresursen och på din lokala server.
6. Vid ett haveri i den lokala miljön utför du en redundansväxling med hjälp av en [återställningsplan](site-recovery-create-recovery-plans.md). Lägg till skriptet i montera Azure-filresursen och öppna resursen på den virtuella datorn.

> [!NOTE]
> Kontrollera att port 445 är öppen. Azure Files använder SMB-protokollet. SMB kommunicerar via TCP-port 445. Kontrollera att brandväggen inte blockerar TCP-port 445 från en klientdator.


## <a name="do-a-test-failover"></a>Utföra ett redundanstest

1. Gå till Azure-portalen och välj ditt Recovery Service-valv.
2. Välj den återställningsplan som skapats för filservermiljön.
3. Välj **Testa redundans**.
4. Välj återställningspunkt och det virtuella Azure-nätverket för att börja redundanstestet.
5. När den sekundära miljön är igång utför du dina verifieringar.
6. När verifieringarna är klara väljer du **Rensa redundanstestning** i återställningsplanen så rensas redundanstestmiljön.

Mer information om hur du utför ett redundanstest finns i [Testa redundans till Site Recovery](site-recovery-test-failover-to-azure.md).

Anvisningar om hur du utför redundanstestningen för Active Directory och DNS finns i [Överväganden för redundanstest för Active Directory och DNS](site-recovery-active-directory.md).

## <a name="do-a-failover"></a>Göra en redundansväxling

1. Gå till Azure-portalen och välj ditt Recovery Services-valv.
2. Välj den återställningsplan som skapats för filservermiljön.
3. Välj **Redundans**.
4. Välj återställningspunkten för att starta redundansväxlingen.

Mer information om hur du utför en redundansväxling finns i [Redundans i Site Recovery](site-recovery-failover.md).