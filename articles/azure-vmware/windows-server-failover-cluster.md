---
title: Windows Server-redundanskluster på Azure VMware-virtuellt San med interna delade diskar
description: Konfigurera WSFC (Windows Server failover Cluster) i Azure VMware-lösningen och dra nytta av lösningar som kräver WSFC-funktioner.
ms.topic: how-to
ms.date: 03/09/2021
ms.openlocfilehash: 8162e15675d8bbde9267126c785f152d1cb860bd
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562247"
---
# <a name="windows-server-failover-cluster-on-azure-vmware-solution-vsan-with-native-shared-disks"></a>Windows Server-redundanskluster på Azure VMware-virtuellt San med interna delade diskar

I den här artikeln går vi igenom hur du konfigurerar Windows Server-redundanskluster på Azure VMware-lösning. Implementeringen i den här artikeln är avsedd för koncept bevis och pilot syfte. Vi rekommenderar att du använder en konfiguration för kluster i en ruta (CIB) tills placerings principerna är tillgängliga.

Windows Server failover Cluster (WSFC), som tidigare kallades Microsoft Service Cluster service (MSCS), är en funktion i operativ systemet Windows Server (OS). WSFC är en verksamhets kritisk funktion och för många program krävs. Till exempel krävs WSFC för följande konfigurationer:

- SQL Server har kon figurer ATS som:
  - Alltid på kluster instans för redundans (FCI), för hög tillgänglighet på instans nivå.
  - Always on tillgänglighets grupp (AG) för hög tillgänglighet på databas nivå.
- Windows-fil tjänster:
  - Allmän fil resurs som körs på den aktiva klusternoden.
  - Scale-Out fil server (SOFS), som lagrar filer på klusterdelade volymer (CSV).
  - Lagringsdirigering (S2D); lokala diskar som används för att skapa lagringspooler mellan olika klusternoder.

Du kan vara värd för WSFC-klustret på olika Azure VMware-lösningar, som kallas för kluster över-Box (CAB). Du kan också placera WSFC-klustret på en enda Azure VMware-lösning. Den här konfigurationen kallas kluster-i-en-box (CIB). Vi rekommenderar inte att du använder en CIB-lösning för en produktions implementering. Var den enkla Azure VMware-noden som inte fungerar, alla WSFC-klusternoder skulle stängas av och programmet skulle uppleva stillestånds tid. Azure VMware-lösningen kräver minst tre noder i ett privat moln kluster.

Det är viktigt att distribuera en WSFC-konfiguration som stöds. Du vill att din lösning ska stödjas på vSphere och med Azure VMware-lösningen. VMware innehåller ett detaljerat dokument om WSFC på vSphere 6,7, [installation av kluster för växling vid fel och Microsoft Cluster service](https://docs.vmware.com/en/VMware-vSphere/6.7/vsphere-esxi-vcenter-server-67-setup-mscs.pdf).

Den här artikeln fokuserar på WSFC på Windows Server 2016 och Windows Server 2019. Äldre versioner av Windows Server är inte av [vanlig support](https://support.microsoft.com/lifecycle/search?alpha=windows%20server) och vi bör inte ta hänsyn till dem här.

Du måste först [skapa en WSFC](/windows-server/failover-clustering/create-failover-cluster). Mer information om WSFC finns [i kluster för växling vid fel i Windows Server](/windows-server/failover-clustering/failover-clustering-overview). Använd informationen i den här artikeln för att få information om en WSFC-distribution på Azure VMware-lösningen.

## <a name="prerequisites"></a>Förutsättningar

- Azure VMware-lösnings miljö
- Installations medium för Microsoft Windows Server OS

## <a name="reference-architecture"></a>Referensarkitektur

Azure VMware-lösningen ger inbyggt stöd för virtualiserad WSFC. Den har stöd för SCSI-3 permanenta reservationer (SCSI3PR) på en virtuell disk nivå. Det här stödet krävs av WSFC för att arbitrate åtkomst till en delad disk mellan noder. Stöd för SCSI3PRs gör det möjligt att konfigurera WSFC med en disk resurs som delas mellan virtuella datorer internt på virtuellt San-datalager.

Följande diagram illustrerar arkitekturen för virtuella WSFC-noder i ett privat moln i Azure VMware-lösningar. Den visar var Azure VMware-lösningen finns, inklusive virtuella WSFC-servrar (röd ruta) i förhållande till den bredare Azure-plattformen. Det här diagrammet illustrerar en typisk hubb-eker-arkitektur, men en liknande konfiguration är möjlig med användningen av Azure Virtual WAN. Båda erbjuder allt värde som andra Azure-tjänster kan ta dig av.

[![Diagram som visar arkitekturen för virtuella WSFC-noder i ett privat moln i Azure VMware-lösningen.](media/windows-server-failover-cluster/windows-server-failover-architecture.png)](media/windows-server-failover-cluster/windows-server-failover-architecture.png#lightbox)

## <a name="supported-configurations"></a>Konfigurationer som stöds

För närvarande stöds följande konfigurationer:

- Microsoft Windows Server 2012 eller senare
- Upp till fem noder i redundansklustret per kluster
- Upp till fyra PVSCSI-kort per virtuell dator
- Upp till 64 diskar per PVSCSI-kort

## <a name="virtual-machine-configuration-requirements"></a>Konfigurations krav för virtuell dator

### <a name="wsfc-node-configuration-parameters"></a>Konfigurations parametrar för WSFC-noden

- Installera de senaste VMware-verktygen på varje WSFC-nod.
- Det finns inte stöd för att blanda icke-delade och delade diskar på ett enda virtuellt SCSI-kort. Till exempel, om system disken (enhet C:) är kopplat till SCSI0:0, den första delade disken kopplas till SCSI1:0. En VM-nod i en WSFC har samma virtuella SCSI-styrenhet som en vanlig VM-upp till fyra (4) virtuella SCSI-styrenheter.
- Virtuella skivor SCSI-ID: n måste vara konsekventa mellan alla virtuella datorer som är värdar för samma WSFC.

| **Komponent** | **Krav** |
| --- | --- |
| Maskin varu version för VM | 11 eller senare för att stödja Live-vMotion. |
| Virtuellt nätverkskort | VMXNET3 paravirtualiserade Network Interface Card (NIC); Aktivera Windows Receive-skalning (Windows Receive Side Scaling) i gäst på det virtuella NÄTVERKSKORTet. |
| Minne | Använd fullständigt reservations minne för virtuella datorer för noder i WSFC-klustret. |
| Öka i/O-tidsgräns för varje WSFC-nod. | Ändra den \_ lokala HKEY- \_ MACHINE\System\CurrentControlSet\Services\Disk\TimeOutValueSet till 60 sekunder eller mer. (Om du återskapar klustret kan det här värdet återställas till standardvärdet, så du måste ändra det igen.) |
| Hälso övervakning av Windows-kluster | Värdet för SameSubnetThreshold-parametern för hälso övervakning av Windows-kluster måste ändras för att tillåta minst 10 missade pulsslag. Detta är [standard i Windows Server 2016](https://techcommunity.microsoft.com/t5/failover-clustering/tuning-failover-cluster-network-thresholds/ba-p/371834). Den här rekommendationen gäller för alla program som använder WSFC, inklusive delade och icke-delade diskar. |

### <a name="wsfc-node---boot-disks-configuration-parameters"></a>Konfigurations parametrar för WSFC Node-Boot disks


| **Komponent** | **Krav** |
| --- | --- |
| Typ av SCSI-styrenhet | LSI Logic SAS |
| Disk läge | Virtuell |
| SCSI-Bus-delning | Inget |
| Ändra avancerade inställningar för en virtuell SCSI-styrenhet som är värd för startenheten. | Lägg till följande avancerade inställningar till varje WSFC-nod:<br /> scsiX. returnNoConnectDuringAPD = "TRUE"<br />scsiX. returnBusyOnNoConnectStatus = "FALSe"<br />Där X är startenhetens ID-nummer för SCSI-buss styrenheten. Som standard är X inställt på 0. |

### <a name="wsfc-node---shared-disks-configuration-parameters"></a>Konfigurations parametrar för WSFC-nod – delade diskar


| **Komponent** | **Krav** |
| --- | --- |
| Typ av SCSI-styrenhet | VMware-Paravirtualize (PVSCSI) |
| Disk läge | Oberoende – beständig (steg 2 i bilden nedan). Genom att använda den här inställningen ser du till att alla diskar undantas från ögonblicks bilder. Ögonblicks bilder stöds inte för WSFC-baserade virtuella datorer. |
| SCSI-Bus-delning | Fysisk (steg 1 i bilden nedan) |
| Flagga för flera skrivare | Används inte |
| Diskformat | Tjockt etablerad. (Eager = EZT) krävs inte med virtuellt San.) |

:::image type="content" source="media/windows-server-failover-cluster/edit-settings-virtual-hardware.png" alt-text="En skärm bild som visar sidan Redigera inställningar för virtuell maskin vara.":::

## <a name="non-supported-scenarios"></a>Scenarier som inte stöds

Följande funktioner stöds inte för WSFC på Azure VMware-lösningen:

- NFS-datalager
- Lagringsutrymmen
- Virtuellt San med iSCSI-tjänsten
- Virtuellt San utsträckt kluster
- Enhanced vMotion-kompatibilitet (EVC)
- vSphere fel tolerans (FT)
- Ögonblicksbilder
- Live-lagring (online) vMotion
- E-Port ID Virtualization (NPIV)

Frekventa ändringar av den virtuella datorns maskin vara kan störa pulsslag mellan WSFC-noderna.

Följande aktiviteter stöds inte och kan orsaka redundans av WSFC-noder:

- Frekvent tillägg av minne
- Frekvent tillägg av CPU
- Använda ögonblicks bilder
- Öka storleken på en delad disk
- Pausa och återuppta den virtuella datorns tillstånd
- Minnes överanvändnings engagemang som leder till ESXi växling eller minnes ballonger för virtuella datorer
- Utöka den lokala VMDK-filen, även om den inte är kopplad till SCSI-styrenheten för Bus-delning

## <a name="configure-wsfc-with-shared-disks-on-azure-vmware-solution-vsan"></a>Konfigurera WSFC med delade diskar på Azure VMware-lösningen virtuellt San

1. Se till att en Active Directorys miljö är tillgänglig.
2. Skapa virtuella datorer (VM) på virtuellt San-datalagret.
3. Slå på alla virtuella datorer, konfigurera värd namnet, IP-adresserna, Anslut alla virtuella datorer till en Active Directory domän och installera de senaste tillgängliga OS-uppdateringarna.
4. Installera de senaste VMware-verktygen.
5. Aktivera och konfigurera funktionen Windows Server-redundanskluster på varje virtuell dator.
6. Konfigurera ett kluster vittne för kvorum (ett fil resurs vittne fungerar bra).
7. Stäng av alla noder i WSFC-klustret.
8. Lägg till en eller flera paravirtuell SCSI-styrenheter (upp till fyra) till varje VM-del av WSFC. Använd inställningarna enligt föregående stycken.
9. På den första klusternoden lägger du till alla nödvändiga delade diskar med hjälp av **Lägg till ny enhets**  >  **hård disk**. Disk delning ska vara kvar som **Ospecificerat** (standard) och disk läge som **oberoende-beständigt**. Koppla den till den eller de styrenheter som skapades i föregående steg.
10. Fortsätt med de återstående WSFC-noderna. Lägg till diskarna som skapades i föregående steg genom att välja **Lägg till ny enhet**  >  **befintlig hård disk**. Se till att behålla samma disk SCSI-ID på alla WSFC-noder.
11. Slå på den första WSFC-noden. Logga in och öppna konsolen för disk hantering (MMC). Se till att de tillagda delade diskarna kan hanteras av operativ systemet och initieras. Formatera diskarna och tilldela en enhets beteckning.
12. Slå på de andra WSFC-noderna.
13. Lägg till disken i WSFC-klustret med hjälp av **guiden Lägg till disk** och Lägg till dem i en klusterdelad volym.
14. Testa en redundansväxling med hjälp av **guiden Flytta disk** och kontrol lera att WSFC-klustret med delade diskar fungerar korrekt.
15. Kör **guiden Verifiera kluster** för att bekräfta om klustret och dess noder fungerar korrekt.

    Det är viktigt att du behåller följande element från kluster verifierings testet i åtanke:

       - **Verifiera PR (persistent reservation) för lagrings utrymmen**. Om du inte använder lagrings utrymmen med ditt kluster (till exempel på Azure VMware-lösning virtuellt SAN) är det här testet inte tillämpligt. Du kan ignorera eventuella resultat från testet verifiera lagrings utrymmen beständigt reservation, inklusive den här varningen. För att undvika varningar kan du undanta det här testet.
        
      - **Verifiera nätverkskommunikation**. Kluster verifierings testet genererar en varning om att endast ett nätverks gränssnitt per klusternod är tillgängligt. Du kan ignorera den här varningen. Azure VMware-lösningen ger nödvändig tillgänglighet och prestanda, eftersom noderna är anslutna till något av NSX-T-segmenten. Tänk dock på att det här objektet ingår i kluster verifierings testet, eftersom det validerar andra aspekter av nätverkskommunikation.

16. Skapa en DRS-regel för att placera de virtuella WSFC-datorerna på samma Azure VMware-lösningar noder. Om du vill göra det behöver du en regel för tillhörighet mellan värdar och virtuell dator. På så sätt kommer klusternoderna att köras på samma Azure VMware-lösnings värd. Återigen är detta för pilot syfte tills placerings principer är tillgängliga.

    >[!NOTE]
    > För detta måste du skapa ett support ärende. Vår support organisation för Azure kan hjälpa dig med detta.

## <a name="related-information"></a>Relaterad information

- [Redundanskluster för Windows Server](/windows-server/failover-clustering/failover-clustering-overview)
- [Rikt linjer för Microsoft-klustring på vSphere (1037959) (vmware.com)](https://kb.vmware.com/s/article/1037959)
- [Om installation av kluster för växling vid fel och Microsoft Cluster service (vmware.com)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.mscs.doc/GUID-1A2476C0-CA66-4B80-B6F9-8421B6983808.html)
- [Virtuellt San 6,7 U3-WSFC med delade diskar &amp; SCSI-3 permanenta reservationer (VMware.com)](https://blogs.vmware.com/virtualblocks/2019/08/23/vsan67-u3-wsfc-shared-disksupport/)
- [Azure VMware-lösnings gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-vmware-solution-limits)

## <a name="next-steps"></a>Nästa steg

Nu när du har täckt konfigurationen av en WSFC i Azure VMware-lösningen kanske du vill lära dig mer om:

- Konfigurera din nya WSFC genom att lägga till fler program som kräver WSFC-funktionen. Till exempel SQL Server och SAP ASCS.
- Konfigurera en lösning för säkerhets kopiering.
  - [Konfigurera Azure Backup Server för Azure VMware-lösning](./set-up-backup-server-for-azure-vmware-solution.md)
  - [Säkerhets kopierings lösningar för virtuella datorer i Azure VMware-lösningen](./ecosystem-back-up-vms.md)