---
title: Planera kapacitet för VMware haveri beredskap med Azure Site Recovery
description: Den här artikeln hjälper dig att planera kapacitet och skalning när du konfigurerar haveri beredskap för virtuella VMware-datorer till Azure med hjälp av Azure Site Recovery.
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 4/9/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 4b86d0c189bcf0687a703f2338188df2090feaf0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92368034"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Planera kapacitet och skalning för VMware haveri beredskap till Azure

Använd den här artikeln för att planera för kapacitet och skalning när du replikerar lokala virtuella VMware-datorer och fysiska servrar till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Vill du Hur gör jag för att starta kapacitets planering?

Om du vill veta mer om Azure Site Recovery infrastruktur krav kan du samla in information om din replikeringspartner genom att köra [Distributionshanteraren för Azure Site Recovery](./site-recovery-deployment-planner.md) för VMware-replikering. Mer information finns i [About Site Recovery Deployment Planner för VMware till Azure](site-recovery-deployment-planner.md). 

Site Recovery Deployment Planner innehåller en rapport med fullständig information om kompatibla och inkompatibla virtuella datorer, diskar per virtuell dator och data omsättning per disk. Verktyget sammanfattar också kraven på nätverks bandbredd för att uppfylla mål återställnings miljön och den Azure-infrastruktur som krävs för lyckad replikering och redundanstest.

## <a name="capacity-considerations"></a>Överväganden för kapaciteter

Komponent | Information
--- | ---
**Replikering** | **Högsta dagliga ändrings frekvens**: en skyddad dator kan bara använda en processerver. En enda processerver kan hantera en daglig ändrings hastighet på upp till 2 TB. 2 TB är alltså den maximala dagliga data ändrings hastigheten som stöds för en skyddad dator.<br /><br /> **Maximalt data flöde**: en replikerad dator kan tillhöra ett lagrings konto i Azure. Ett standard Azure Storage konto kan hantera högst 20 000 förfrågningar per sekund. Vi rekommenderar att du begränsar antalet in-/utdata-åtgärder per sekund (IOPS) över en käll dator till 20 000. Om du till exempel har en käll dator som har fem diskar och varje disk genererar 120 IOPS (8 K i storlek) på käll datorn, är käll datorn i Azure per disk IOPS-gräns på 500. (Antalet lagrings konton som krävs motsvarar den totala käll datorns IOPS dividerat med 20 000.)
**Konfigurationsserver** | Konfigurations servern måste kunna hantera den dagliga ändrings takten för alla arbets belastningar som körs på skyddade datorer. Konfigurations datorn måste ha tillräcklig bandbredd för att kontinuerligt replikera data till Azure Storage.<br /><br /> Ett bra tips är att placera konfigurations servern på samma nätverks-och LAN-segment som de datorer som du vill skydda. Du kan placera konfigurations servern i ett annat nätverk, men datorer som du vill skydda bör ha nivå 3-nätverks synlighet.<br /><br /> Storleks rekommendationer för konfigurations servern sammanfattas i tabellen i följande avsnitt.
**Processerver** | Den första processervern installeras som standard på konfigurations servern. Du kan distribuera ytterligare process servrar för att skala din miljö. <br /><br /> Processervern tar emot replikeringsdata från skyddade datorer. Processervern optimerar data med hjälp av cachelagring, komprimering och kryptering. Sedan skickar processervern data till Azure. Processervern måste ha tillräckligt med resurser för att utföra dessa uppgifter.<br /><br /> Processervern använder en diskbaserad cache. Använd en separat cache-disk på 600 GB eller mer för att hantera data ändringar som lagras om en nätverks Flask hals eller ett avbrott inträffar.

## <a name="size-recommendations-for-the-configuration-server-and-inbuilt-process-server"></a>Storleks rekommendationer för konfigurations servern och den inbyggde processervern

En konfigurations server som använder en inbyggd processerver för att skydda arbets belastningen kan hantera upp till 200 virtuella datorer baserat på följande konfigurationer:

Processor | Minne | Cachestorlek för cache | Data ändrings takt | Skyddade datorer
--- | --- | --- | --- | ---
8 virtuella processorer (2 Sockets * 4 kärnor \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB eller mindre | Används för att replikera färre än 100 datorer.
12 virtuella processorer (2 Sockets * 6 kärnor \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB till 1 TB | Använd för att replikera 100 till 150-datorer.
16 virtuella processorer (2 Sockets * 8 kärnor \@ 2,5 GHz) | 32 GB | 1 TB | >1 TB till 2 TB | Använd för att replikera 151 till 200-datorer.
Distribuera en annan konfigurations server med hjälp av en [OVF-mall](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template). | | | | Distribuera en ny konfigurations server om du vill replikera fler än 200 datorer.
Distribuera en annan [processerver](vmware-azure-set-up-process-server-scale.md#download-installation-file). | | | >2 TB| Distribuera en ny Scale-Out-processerver om den totala data ändrings takten för daglig data är större än 2 TB.

I följande konfigurationer:

* Varje käll dator har tre diskar på 100 GB vardera.
* Vi använde benchmarking-lagring av åtta signaturer för delad åtkomst på 10 K RPM med RAID 10 för att cachelagra disk mått.

## <a name="size-recommendations-for-the-process-server"></a>Storleks rekommendationer för processervern

Processervern är den komponent som hanterar datareplikering i Azure Site Recovery. Om den dagliga ändrings hastigheten är större än 2 TB måste du lägga till skalbara process servrar för att hantera belastningen på replikeringen. För att skala ut kan du:

* Öka antalet konfigurations servrar genom att distribuera med hjälp av en [OVF-mall](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template). Du kan till exempel skydda upp till 400 datorer genom att använda två konfigurations servrar.
* Lägg till [skalbara process servrar](vmware-azure-set-up-process-server-scale.md#download-installation-file). Använd de skalbara process servrarna för att hantera replikeringstrafik i stället för (eller utöver) konfigurations servern.

Det här scenariot beskrivs i följande tabell:

* Du konfigurerar en skalbar processerver.
* Du konfigurerade skyddade virtuella datorer för att använda den skalbara processervern.
* Varje skyddad käll dator har tre diskar på 100 GB vardera.

Ytterligare processerver | Cachestorlek för cache | Data ändrings takt | Skyddade datorer
--- | --- | --- | ---
4 virtuella processorer (2 Sockets * 2 kärnor \@ 2,5 GHz), 8 GB minne | 300 GB | 250 GB eller mindre | Används för att replikera 85 eller färre datorer.
8 virtuella processorer (2 Sockets * 4 kärnor \@ 2,5 GHz), 12 GB minne | 600 GB | 251 GB till 1 TB | Använd för att replikera 86 till 150-datorer.
12 virtuella processorer (2 Sockets * 6 kärnor \@ 2,5 GHz) 24 GB minne | 1 TB | >1 TB till 2 TB | Använd för att replikera 151 till 225-datorer.

Hur du skalar dina servrar beror på din preferens för en skala upp-eller skala ut-modell. Om du vill skala upp distribuerar du några avancerade konfigurations servrar och process servrar. Distribuera fler servrar som har färre resurser för att skala ut. Om du till exempel vill skydda 200-datorer med en total daglig data ändrings hastighet på 1,5 TB kan du vidta någon av följande åtgärder:

* Konfigurera en enda processerver (16 vCPU, 24 GB RAM).
* Konfigurera två process servrar (2 x 8 vCPU, 2 * 12 GB RAM).

## <a name="control-network-bandwidth"></a>Kontrol lera nätverkets bandbredd

När du använder [Site Recovery distributions planeraren](site-recovery-deployment-planner.md) för att beräkna den bandbredd du behöver för replikering (inledande replikering och sedan delta) har du ett par alternativ för att kontrol lera mängden bandbredd som används för replikering:

* **Begränsa bandbredd**: VMware-trafik som replikeras till Azure går genom en speciell processerver. Du kan begränsa bandbredden på de datorer som kör som process servrar.
* **Påverka bandbredd**: du kan påverka bandbredden som används för replikering med hjälp av ett par register nycklar:
  * Registervärdet **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** anger antalet trådar som används för data överföring (inledande eller delta-replikering) på en disk. Ett högre värde ökar nätverks bandbredden som används för replikering.
  * Register värdet **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** anger antalet trådar som används för data överföring under återställning efter fel.

### <a name="throttle-bandwidth"></a>Begränsa bandbredden

1. Öppna snapin-modulen Azure Backup MMC på den dator som du använder som processerver. Som standard är en genväg till backup tillgänglig på Skriv bordet eller i följande mapp: C:\Program Files\Microsoft Azure Recovery Services Agent\bin.
2. Välj **ändra egenskaper** i snapin-modulen.

    ![Skärm bild av alternativet Azure Backup MMC-snapin-modulen för att ändra egenskaper](./media/site-recovery-vmware-to-azure/throttle1.png)
3. På fliken **begränsning** väljer du **Aktivera användnings begränsning för Internet bandbredd för säkerhets kopierings åtgärder**. Ange gränserna för arbets tid och ledig tid. Giltiga intervall är mellan 512 och 1 023 Mbit/s.

    ![Skärm bild av dialog rutan Azure Backup egenskaper](./media/site-recovery-vmware-to-azure/throttle2.png)

Du kan också ange begränsningar med hjälp av cmdleten [Set-OBMachineSetting](/previous-versions/windows/powershell-scripting/hh770409(v=wps.640)). Här är ett exempel:

```azurepowershell-interactive
$mon = [System.DayOfWeek]::Monday
$tue = [System.DayOfWeek]::Tuesday
Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)
```

**Set-OBMachineSetting -NoThrottle** anger att ingen begränsning krävs.

### <a name="alter-the-network-bandwidth-for-a-vm"></a>Ändra nätverks bandbredden för en virtuell dator

1. I VM-registret går du till **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Ändra värdet för **UploadThreadsPerVM** för att ändra bandbredds trafik på en replikerande disk. Skapa nyckeln om den inte finns.
   * Ändra värdet för **DownloadThreadsPerVM** för att ändra bandbredden för återställning av återställnings trafik från Azure.
2. Standardvärdet för varje nyckel är **4**. I ett ”överetablerat” nätverk bör du ändra registernycklarnas standardvärden. Det maximala värdet som du kan använda är **32**. Övervaka trafiken för att optimera värdet.

## <a name="set-up-the-site-recovery-infrastructure-to-protect-more-than-500-vms"></a>Konfigurera Site Recovery-infrastrukturen för att skydda över 500 virtuella datorer

Innan du konfigurerar Site Recovery-infrastrukturen får du åtkomst till miljön för att mäta följande faktorer: kompatibla virtuella datorer, den dagliga data ändrings takten, den nödvändiga nätverks bandbredden för den återställning som du vill uppnå, antalet Site Recovery komponenter som krävs och hur lång tid det tar att slutföra den inledande replikeringen. Utför följande steg för att samla in nödvändig information:

1. Om du vill mäta dessa parametrar kör Site Recovery Deployment Planner i din miljö. Användbara rikt linjer finns i [About Site Recovery Deployment Planner för VMware till Azure](site-recovery-deployment-planner.md).
2. Distribuera en konfigurations server som uppfyller [storleks rekommendationerna för konfigurations servern](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server). Om produktions arbets belastningen överskrider 650 virtuella datorer kan du distribuera en annan konfigurations Server.
3. Utifrån den uppmätta dagliga data ändrings takten distribuerar du [skalbara process servrar](vmware-azure-set-up-process-server-scale.md#download-installation-file) med hjälp av [rikt linjer för storlek](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).
4. Om du förväntar dig att data ändrings takten för en virtuell disk dator ska överskrida 2 Mbit/s måste du använda Premium Managed disks. Site Recovery Deployment Planner körs under en viss tids period. Hög belastning i data ändrings takten kanske inte registreras i rapporten.
5. [Ange nätverks bandbredden](site-recovery-plan-capacity-vmware.md#control-network-bandwidth) baserat på den återställnings punkt du vill uppnå.
6. När infrastrukturen har kon figurer ATS aktiverar du haveri beredskap för din arbets belastning. Mer information finns i [Konfigurera käll miljön för VMware till Azure-replikering](vmware-azure-set-up-source.md).

## <a name="deploy-additional-process-servers"></a>Distribuera ytterligare process servrar

Om du skalar ut distributionen utöver 200-käll datorer, eller om du har en total daglig omsättning på mer än 2 TB, måste du lägga till process servrar för att hantera trafik volymen. Vi har förbättrat produkten i 9,24-versionen för att tillhandahålla [process Server aviseringar](vmware-physical-azure-monitor-process-server.md#process-server-alerts) när du ska konfigurera en skalbar processerver. [Konfigurera processervern](vmware-azure-set-up-process-server-scale.md) för att skydda nya käll datorer eller [utjämna belastningen](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load).

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrera datorer till att använda den nya processervern

1. Välj **Inställningar**  >  **Site Recovery servrar**. Välj konfigurations servern och expandera sedan **process servrar**.

    ![Skärm bild av dialog rutan Processerver](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Högerklicka på den processerver som för närvarande används och välj sedan **switch**.

    ![Skärm bild av dialog rutan konfigurations Server](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. I **Välj mål process Server** väljer du den nya processervern som du vill använda. Välj sedan de virtuella datorer som servern ska hantera. Om du vill hämta information om servern väljer du informations ikonen. För att hjälpa dig att fatta inläsnings beslut visas det genomsnittliga utrymmet som krävs för att replikera varje vald virtuell dator till den nya processervern. Markera kryss rutan för att börja replikera till den nya processervern.

## <a name="deploy-additional-master-target-servers"></a>Distribuera ytterligare huvud mål servrar

I följande scenarier krävs mer än en huvud mål server:

*   Du vill skydda en Linux-baserad virtuell dator.
*   Huvud mål servern som är tillgänglig på konfigurations servern har inte åtkomst till den virtuella datorns data lager.
*   Det totala antalet diskar på huvud mål servern (antalet lokala diskar på servern plus antalet diskar som ska skyddas) är större än 60 diskar.

Information om hur du lägger till en huvud mål server för en Linux-baserad virtuell dator finns i [installera en Linux-huvud mål server för återställning efter fel](vmware-azure-install-linux-master-target.md).

Så här lägger du till en huvud mål server för en Windows-baserad virtuell dator:

1. Gå till **Recovery Services valv**  >  **Site Recovery infrastruktur**  >  **konfigurations servrar**.
2. Välj den konfigurations server som krävs och välj sedan **huvud mål server**.

    ![Skärm bild som visar knappen Lägg till huvud mål Server](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Hämta den enhetliga installations filen och kör sedan filen på den virtuella datorn för att konfigurera huvud mål servern.
4. Välj **Installera huvud mål**  >  **Nästa**.

    ![Skärm bild som visar val av alternativet för att installera huvud mål](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Välj standard installations plats och välj sedan **Installera**.

     ![Skärm bild som visar standard installations platsen](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. Om du vill registrera huvud målet med konfigurations servern väljer **du Fortsätt till konfiguration**.

    ![Skärm bild som visar knappen gå till konfiguration](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Ange konfigurations serverns IP-adress och ange lösen frasen. Information om hur du skapar en lösen fras finns i [skapa en lösen fras för konfigurations servern](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase). 

    ![Skärm bild som visar var du anger IP-adressen och lösen frasen för konfigurations servern](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. Välj **Register** (Registrera). När registreringen är klar väljer du **Slutför**.

När registreringen har slutförts visas servern i Azure Portal på **Recovery Services valv**  >  **Site Recovery infrastruktur**  >  **konfigurations servrar**, på huvud mål servrarna på konfigurations servern.

 > [!NOTE]
 > Ladda ned den senaste versionen av den [enhetliga installations filen för huvud mål servern för Windows](https://aka.ms/latestmobsvc).

## <a name="next-steps"></a>Nästa steg

Ladda ned och kör [Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner).