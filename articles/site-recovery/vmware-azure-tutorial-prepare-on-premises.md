---
title: Förbered för katastrof återställning av VMware VM med Azure Site Recovery
description: Lär dig att förbereda lokala VMware-servrar på haveriberedskap till Azure med Azure Site Recovery-tjänsten.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b8fd34c8f1e3a32a8252074941a49d61aa540207
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86134902"
---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>Förbereda lokala VMware-servrar på haveriberedskap till Azure

Den här artikeln beskriver hur du förbereder lokala VMware-servrar för haveri beredskap till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md) -tjänsterna. 

Det här är den fjärde kursen i en serie som illustrerar hur du konfigurerar haveriberedskap i Azure för lokala virtuella VMware-datorer. I den första självstudien [konfigurerade vi de Azure-komponenter](tutorial-prepare-azure.md) som krävs för katastrofåterställning för VMware.


I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Förbered ett konto på vCenter-servern eller vSphere ESXi-värden för att automatisera VM-identifiering.
> * Förbereda ett konto för automatisk installation av mobilitets tjänsten på virtuella VMware-datorer.
> * Granska krav och support för VMware Server och VM.
> * Förbered för att ansluta till virtuella Azure-datorer efter redundansväxling.

> [!NOTE]
> Självstudier visar den enklaste distributions vägen för ett scenario. De använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i artikeln i avsnittet så här i Site Recovery innehålls förteckningen.

## <a name="before-you-start"></a>Innan du börjar

Se till att du har för berett Azure enligt beskrivningen i den [första självstudien i den här serien](tutorial-prepare-azure.md).

## <a name="prepare-an-account-for-automatic-discovery"></a>Förbereda ett konto för automatisk identifiering

Site Recovery måste ha åtkomst till VMware-servrarna för att:

- Identifiera automatiskt virtuella datorer. Minst ett skrivskyddat konto krävs.
- Samordna replikering, redundans och återställning efter fel. Du behöver ett konto som kan köra åtgärder som till exempel att skapa och ta bort diskar samt starta virtuella datorer.

Skapa kontot enligt följande:

1. Om du vill använda ett särskilt konto skapar du en roll på vCenter-nivå. Ge rollen ett namn som t.ex. **Azure_Site_Recovery**.
2. Tilldela rollen behörigheterna som sammanfattas i tabellen nedan.
3. Skapa en användare på vCenter-servern eller vSphere-värden. Tilldela rollen till användaren.

### <a name="vmware-account-permissions"></a>Behörighet för VMware-konto

**Uppgift** | **Roll/behörigheter** | **Information**
--- | --- | ---
**VM-identifiering** | Minst en skrivskyddad användare<br/><br/> Data Center-objekt –> Sprid till underordnat objekt, roll = skrivskyddad | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten tilldelar du rollen **Ingen åtkomst** med objektet **Sprid till underordnad** till underordnade objekt (vSphere-värdar, data lager, virtuella datorer och nätverk).
**Fullständig replikering, redundans och återställning efter fel** |  Skapa en roll (Azure_Site_Recovery) med behörigheterna som krävs och tilldela sedan rollen till en VMware-användare eller grupp<br/><br/> Datacenterobjekt –> Sprid till underordnat objekt, roll=Azure_Site_Recovery<br/><br/> Datalager -> Allokera utrymme, bläddra i datalagret, filåtgärder på låg nivå, ta bort filen, uppdatera filer för virtuella datorer<br/><br/> Nätverk -> Tilldela nätverk<br/><br/> Resurs -> Tilldela VM till resurspool, migrera avstängd VM, migrera påslagen VM<br/><br/> Uppgifter -> Skapa uppgift, uppdatera uppgift<br/><br/> Virtuell dator -> Konfiguration<br/><br/> Virtuell dator -> Interagera -> Besvara fråga, enhetsanslutning, konfigurera CD-skiva, konfigurera diskettstation, stänga av, sätta på, installera VMware-verktyg<br/><br/> Virtuell dator -> Lager -> Skapa, registrera, avregistrera<br/><br/> Virtuell dator -> Etablering -> Tillåt nedladdning till virtuell dator, tillåt filuppladdning till virtuell dator<br/><br/> Virtuell dator -> Ögonblicksbilder -> Ta bort ögonblicksbilder | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten tilldelar du rollen **Ingen åtkomst** med objektet **Sprid till underordnad** till underordnade objekt (vSphere-värdar, data lager, virtuella datorer och nätverk).

## <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installation av mobilitetstjänsten

Mobilitetstjänsten måste vara installerad på datorer som du vill replikera. Site Recovery kan göra en push-installation av den här tjänsten när du aktiverar replikering för en dator eller så kan du installera den manuellt eller med installationsverktyg.

- I den här självstudien, installerar vi mobilitetstjänsten med push-installation.
- För den här push-installationen behöver du förbereda ett konto som Site Recovery kan använda för att komma åt den virtuella datorn. Du anger det här kontot när du konfigurerar haveriberedskap i Azure-konsolen.

Förbered kontot enligt följande:

Förbereda en domän eller ett lokalt konto med behörighet för att installera på den virtuella datorn.

- **Virtuella Windows-datorer**: Om du vill installera på virtuella Windows-datorer och inte använder ett domänkonto, inaktiverar du åtkomstkontroll för fjärranvändare på den lokala datorn. Du gör det genom att gå till registret > **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** och lägga till DWORD-posten **LocalAccountTokenFilterPolicy**, med värdet 1.
- **Virtuella Linux-datorer**: För att installera på virtuella Linux-datorer, förbereder du ett rotkonto på Linux-källservern.


## <a name="check-vmware-requirements"></a>Kontrollera VMware-kraven

Kontrollera att VMware-servrar och virtuella datorer uppfyller kraven.

1. [Kontrollera](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers) VMware-serverkraven.
2. För virtuella Linux-datorer [kontrollerar](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) du filsystem- och lagringskraven. 
3. Kontrollera stödet för lokalt [nätverk](vmware-physical-azure-support-matrix.md#network) och [lagring](vmware-physical-azure-support-matrix.md#storage). 
4. Kontrollera vad som stöds när det gäller [Azure-nätverk](vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [lagring](vmware-physical-azure-support-matrix.md#azure-storage) och [compute](vmware-physical-azure-support-matrix.md#azure-compute) efter redundansväxling.
5. Dina lokala virtuella datorer som du replikerar till Azure måste uppfylla [kraven för virtuella Azure-datorer](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
6. I virtuella Linux-datorer så ska enhetsnamn eller monteringspunktsnamn vara unika. Kontrollera att inga två enheter/monteringspunkter har samma namn. Observera att namnet inte är Skift läges känsligt. Det är till exempel inte tillåtet att namnge två enheter för samma virtuella dator som _enhet1_ och _Enhet1_.


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Efter redundansväxling, kan du vilja ansluta till virtuella Azure-datorer från ditt lokala nätverk.

Om du vill ansluta till virtuella Windows-datorer med RDP efter en redundans, gör du följande:

- **Internet åtkomst**. Innan redundans, aktiverar du RDP på den lokala virtuella datorn före redundans. Kontrollera att TCP- och UDP-regler har lagts till för den **offentliga** profilen och att RDP tillåts i **Windows-brandväggen** > **Tillåtna appar** för alla profiler.
- **Plats-till-plats VPN-åtkomst**:
    - Innan redundans, aktiverar du RDP på den lokala datorn.
    - RDP bör tillåtas i **Windows-brandväggen**  ->  **tillåtna appar och funktioner** för **domän nätverk och privata** nätverk.
    - Kontrollera att operativsystemets SAN-princip har angetts till **OnlineAll**. [Läs mer](https://support.microsoft.com/kb/3031135).
- Det får inte finnas några väntande Windows-uppdateringar på den virtuella datorn när du utlöser en redundans. I så fall kan du inte logga in på den virtuella datorn förrän uppdateringen är klar.
- Efter en redundans av en virtuell Windows Azure-dator, kontrollerar du att **Startdiagnostik** visar en skärmbild av den virtuella datorn. Om du inte kan ansluta kontrollerar du att den virtuella datorn körs. Granska sedan dessa [felsökningstips](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Om du vill ansluta till virtuella Linux-datorer med SSH efter en redundans, gör du följande:

- Kontrollera att Secure Shell-tjänsten är inställd att starta automatiskt vid systemstart på den lokala datorn före redundans.
- Kontrollera att brandväggsreglerna tillåter en SSH-anslutning.
- Den virtuella Azure-datorn måste tillåta inkommande anslutningar till SSH-porten efter redundans för reglerna för nätverkssäkerhetsgrupper på den redundansväxlade virtuella datorn och Azure-undernätet som den är ansluten till.
- [Lägg till en offentlig IP-adress](./site-recovery-monitor-and-troubleshoot.md) för den virtuella datorn.
- Du kan kontrollera att **Startdiagnostik** visar en skärmbild av den virtuella datorn.


## <a name="failback-requirements"></a>Krav för återställning efter fel
Om du planerar att växla tillbaka till din lokala plats finns det ett antal [krav för återställning efter fel](vmware-azure-reprotect.md#before-you-begin). Du kan förbereda dessa nu, men du behöver inte. Du kan förbereda dig efter att du har växlat över till Azure.



## <a name="next-steps"></a>Nästa steg

Konfigurera katastrof återställning. Om du replikerar flera virtuella datorer bör du planera kapaciteten.
> [!div class="nextstepaction"]
> [Konfigurera katastrof återställning till Azure för virtuella VMware-datorer](vmware-azure-tutorial.md) 
>  [Utför kapacitets planering](site-recovery-deployment-planner.md).
