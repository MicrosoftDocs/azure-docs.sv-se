---
title: MABS & System Center DPM support Matrix
description: I den här artikeln sammanfattas Azure Backup support när du använder Microsoft Azure Backup Server (MABS) eller System Center DPM för att säkerhetskopiera lokala och virtuella Azure-resurser.
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: e888b43ea5641f1943a096f045747d547c52fcfa
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102609761"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Support mat ris för säkerhets kopiering med Microsoft Azure Backup Server eller System Center DPM

Du kan använda [tjänsten Azure Backup](backup-overview.md) för att säkerhetskopiera lokala datorer och arbets belastningar och virtuella datorer i Azure. Den här artikeln sammanfattar support inställningar och begränsningar för att säkerhetskopiera datorer genom att använda Microsoft Azure Backup Server (MABS) eller System Center Data Protection Manager (DPM) och Azure Backup.

## <a name="about-dpmmabs"></a>Om DPM/MABS

[System Center DPM](/system-center/dpm/dpm-overview) är en företags lösning som konfigurerar, underlättar och hanterar säkerhets kopiering och återställning av företags datorer och data. Den är en del av [System Center](https://www.microsoft.com/system-center/pricing) -serien med produkter.

MABS är en server produkt som kan användas för att säkerhetskopiera lokala fysiska servrar, virtuella datorer och appar som körs på dem.

MABS baseras på System Center DPM och innehåller liknande funktioner med några få skillnader:

- Ingen System Center-licens krävs för att köra MABS.
- Azure tillhandahåller långsiktig lagring för säkerhets kopiering för både MABS och DPM. Dessutom tillåter DPM att du säkerhetskopierar data för långsiktig lagring på band. MABS tillhandahåller inte den här funktionen.
- [Du kan säkerhetskopiera en primär DPM-server med en Secondary DPM server](/system-center/dpm/back-up-the-dpm-server). Den sekundära servern skyddar den primära serverns databas och datakällreplikerna som lagras på den primära servern. Om den primära servern slutar fungera kan den sekundära servern ta över och fortsätta skydda arbetsbelastningarna som skyddas av den primära servern tills den primära servern är tillgänglig igen.  MABS tillhandahåller inte den här funktionen.

Du hämtar MABS från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57520). Den kan köras lokalt eller på en virtuell Azure-dator.

DPM och MABS har stöd för att säkerhetskopiera en mängd olika appar och server-och klient operativ system. De har flera säkerhets kopierings scenarier:

- Du kan säkerhetskopiera på dator nivå med system tillstånd eller Bare Metal-säkerhetskopiering.
- Du kan säkerhetskopiera vissa volymer, resurser, mappar och filer.
- Du kan säkerhetskopiera vissa appar med hjälp av optimerade app-medvetna inställningar.

## <a name="dpmmabs-backup"></a>DPM/MABS-säkerhetskopiering

Säkerhets kopiering med DPM/MABS och Azure Backup fungerar på följande sätt:

1. DPM/MABS-skyddsagenten är installerad på varje dator som säkerhets kopie ras.
1. Datorer och appar säkerhets kopie ras till lokal lagring på DPM/MABS.
1. Microsoft Azure Recovery Services MARS-agenten (MARS) är installerad på DPM-servern/-MABS.
1. MARS-agenten säkerhetskopierar DPM/MABS-diskarna till ett säkerhets kopierings Recovery Services valv i Azure med hjälp av Azure Backup.

Mer information:

- [Lär dig mer](backup-architecture.md#architecture-back-up-to-dpmmabs) om Mabs-arkitekturen.
- [Granska vad som stöds](backup-support-matrix-mars-agent.md) för mars-agenten.

## <a name="supported-scenarios"></a>Scenarier som stöds

**Scenario** | **Gent** | **Plats**
--- | --- | ---
**Säkerhetskopiera lokala datorer/arbets belastningar** | DPM/MABS Protection Agent körs på de datorer som du vill säkerhetskopiera.<br/><br/> MARS-agenten på DPM/MABS-servern.<br/> Den lägsta versionen av Microsoft Azure Recovery Services agenten eller Azure Backup Agent som krävs för att aktivera den här funktionen är 2.0.8719.0.  | DPM/MABS måste köras lokalt.

## <a name="supported-deployments"></a>Distributioner som stöds

DPM/MABS kan distribueras som sammanfattas i följande tabell.

**Distribution** | **Support** | **Information**
--- | --- | ---
**Distribuerad lokalt** | Fysisk server, men inte i ett fysiskt kluster.<br/><br/>Virtuell Hyper-V-dator. Du kan distribuera MABS som en gäst dator på en fristående hypervisor eller ett kluster. Den kan inte distribueras på en nod i ett kluster eller en fristående hypervisor. Azure Backup Server är utformad för att köras på en dedikerad server med ett enda syfte.<br/><br/> Som en virtuell Windows-dator i en VMware-miljö. | Lokala MABS-servrar kan inte skydda Azure-baserade arbets belastningar. <br><br> Mer information finns i [skydds mat ris](backup-mabs-protection-matrix.md).
**Distribuerad som en Azure Stack VM** | Endast MABS | DPM kan inte användas för att säkerhetskopiera Azure Stack virtuella datorer.
**Distribuerad som en virtuell Azure-dator** | Skyddar virtuella Azure-datorer och arbets belastningar som körs på dessa virtuella datorer | DPM/MABS som körs i Azure kan inte säkerhetskopiera lokala datorer. Den kan bara skydda arbets belastningar som körs i virtuella Azure IaaS-datorer.

## <a name="supported-mabs-and-dpm-operating-systems"></a>MABS-och DPM-operativsystem som stöds

Azure Backup kan säkerhetskopiera DPM/MABS-instanser som kör något av följande operativ system. De senaste Service Pack-versionerna och-uppdateringarna körs på operativ system.

**Scenario** | **DPM/MABS**
--- | ---
**MABS på en virtuell Azure-dator** |  Windows 2016 Data Center.<br/><br/> Windows 2019 Data Center.<br/><br/> Vi rekommenderar att du börjar med en avbildning från Marketplace.<br/><br/> Minsta Standard_A4_v2 med fyra kärnor och 8 GB RAM-minne.
**DPM på en virtuell Azure-dator** | System Center 2012 R2 med uppdatering 3 eller senare.<br/><br/> Windows operativ system som [krävs av System Center](/system-center/dpm/prepare-environment-for-dpm#dpm-server).<br/><br/> Vi rekommenderar att du börjar med en avbildning från Marketplace.<br/><br/> Minsta Standard_A4_v2 med fyra kärnor och 8 GB RAM-minne.
**MABS lokalt** |  MABS v3 och senare: Windows Server 2016 eller Windows Server 2019
**Lokalt DPM** | Fysisk server/Hyper-V VM: System Center 2012 SP1 eller senare.<br/><br/> VMware VM: System Center 2012 R2 med uppdatering 5 eller senare.

>[!NOTE]
>Installation av Azure Backup Server stöds inte på Windows Server Core eller Microsoft Hyper-V server.

## <a name="management-support"></a>Hanterings support

**Ge** | **Information**
--- | ---
**Installation** | Installera DPM/MABS på en dator med ett enda syfte.<br/><br/> Installera inte DPM/MABS på en domänkontrollant på en dator med rollen som program server roll på en dator som kör Microsoft Exchange Server eller System Center Operations Manager eller på en klusternod.<br/><br/> [Granska alla system krav för DPM](/system-center/dpm/prepare-environment-for-dpm#dpm-server).
**Domän** | DPM/MABS ska vara ansluten till en domän. Installera först och Anslut sedan till DPM/MABS till en domän. Det finns inte stöd för att flytta DPM/MABS till en ny domän efter distribution.
**Storage** | Modern backup Storage (MB) stöds från DPM 2016/MABS v2 och senare. Den är inte tillgänglig för MABS v1.
**MABS-uppgradering** | Du kan installera MABS v3 direkt eller uppgradera till MABS v3 från MABS v2. [Läs mer](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**Flyttar MABS** | Det finns stöd för att flytta MABS till en ny server samtidigt som lagringen behålls om du använder MB.<br/><br/> Servern måste ha samma namn som originalet. Du kan inte ändra namnet om du vill behålla samma lagringspool och använda samma MABS-databas för att lagra data återställnings punkter.<br/><br/> Du behöver en säkerhets kopia av MABS-databasen eftersom du behöver återställa den.

>[!NOTE]
>Det finns inte stöd för att byta namn på DPM/MABS-servern.

## <a name="mabs-support-on-azure-stack"></a>MABS-stöd för Azure Stack

Du kan distribuera MABS på en Azure Stack VM så att du kan hantera säkerhets kopiering av Azure Stack virtuella datorer och arbets belastningar från en enda plats.

**Komponent** | **Information**
--- | ---
**MABS på Azure Stack VM** | Minst storlek a2. Vi rekommenderar att du börjar med en Windows Server 2012 R2-eller Windows Server 2016-avbildning från Azure Marketplace.<br/><br/> Installera inte något annat på den virtuella datorn MABS.
**MABS-lagring** | Använd ett separat lagrings konto för den virtuella MABS-datorn. MARS-agenten som körs på MABS behöver tillfällig lagring för en cache-plats och för att lagra data som återställs från molnet.
**MABS-lagringspool** | Storleken på MABS-lagringspoolen bestäms av antalet och storleken på diskar som är anslutna till den virtuella MABS-datorn. Varje Azure Stack VM-storlek har ett maximalt antal diskar. Till exempel är a2 fyra diskar.
**MABS kvarhållning** | Behåll inte säkerhetskopierade data på de lokala MABS-diskarna i mer än fem dagar.
**MABS skala upp** | För att skala upp distributionen kan du öka storleken på den virtuella MABS-datorn. Du kan till exempel ändra från A till D-serien.<br/><br/> Du kan också se till att du avlastar data med säkerhets kopiering till Azure regelbundet. Om det behövs kan du distribuera ytterligare MABS-servrar.
**.NET Framework på MABS** | Den virtuella MABS-datorn behöver .NET Framework 3,3 SP1 eller senare installerad.
**MABS-domän** | Den virtuella MABS-datorn måste vara ansluten till en domän. En domän användare med administratörs behörighet måste installera MABS på den virtuella datorn.
**Säkerhets kopiering av Azure Stack VM-data** | Du kan säkerhetskopiera filer, mappar och appar.
**Säkerhets kopiering som stöds** | Dessa operativ system stöds för virtuella datorer som du vill säkerhetskopiera:<br/><br/> Windows Server Semi-Annual-kanal (Data Center, Enterprise, standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**SQL Server stöd för Azure Stack virtuella datorer** | Säkerhetskopiera SQL Server 2016, SQL Server 2014 SQL Server 2012 SP1.<br/><br/> Säkerhetskopiera och återställa en databas.
**SharePoint-stöd för Azure Stack virtuella datorer** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Säkerhetskopiera och återställa en Server grupp, databas, klient del och webb server.
**Nätverks krav för säkerhetskopierade virtuella datorer** | Alla virtuella datorer i Azure Stack-arbetsbelastning måste tillhöra samma virtuella nätverk och tillhör samma prenumeration.

## <a name="dpmmabs-networking-support"></a>Stöd för DPM/MABS-nätverk

### <a name="url-access"></a>URL-åtkomst

DPM-servern/MABS-servern behöver åtkomst till dessa URL: er och IP-adresser:

* Webbadresser
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP-adresser
  * 20.190.128.0/18
  * 40.126.0.0/18:

### <a name="azure-expressroute-support"></a>Stöd för Azure ExpressRoute

Du kan säkerhetskopiera dina data via Azure ExpressRoute med offentlig peering (tillgänglig för gamla kretsar) och Microsoft-peering. Säkerhets kopiering över privat peering stöds inte.

Med offentlig peering: säkerställa åtkomst till följande domäner/adresser:

* Webbadresser
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP-adresser
  * 20.190.128.0/18
  * 40.126.0.0/18

Med Microsoft-peering väljer du följande tjänster/regioner och relevanta community-värden:

- Azure Active Directory (12076:5060)
- Microsoft Azure region (enligt platsen för ditt Recovery Services-valv)
- Azure Storage (enligt platsen för ditt Recovery Services-valv)

Mer information finns i krav för [ExpressRoute-routning](../expressroute/expressroute-routing.md).

>[!NOTE]
>Offentlig peering är föråldrad för nya kretsar.

### <a name="dpmmabs-connectivity-to-azure-backup"></a>DPM/MABS-anslutning till Azure Backup

Anslutning till Azure Backups tjänsten krävs för att säkerhets kopieringarna ska fungera korrekt och Azure-prenumerationen ska vara aktiv. I följande tabell visas beteendet om dessa två saker inte inträffar.

**MABS till Azure** | **Prenumeration** | **Säkerhets kopiering/återställning**
--- | --- | ---
Ansluten | Aktiv | Säkerhetskopiera till DPM/MABS disk.<br/><br/> Säkerhetskopiera till Azure.<br/><br/> Återställ från disk.<br/><br/> Återställ från Azure.
Ansluten | Upphört/avetablerats | Ingen säkerhets kopiering till disk eller Azure.<br/><br/> Om prenumerationen har upphört att gälla kan du återställa från disk eller Azure.<br/><br/> Om prenumerationen är inaktive rad kan du inte återställa från disk eller Azure. Azures återställnings punkter tas bort.
Ingen anslutning under mer än 15 dagar | Aktiv | Ingen säkerhets kopiering till disk eller Azure.<br/><br/> Du kan återställa från disk eller Azure.
Ingen anslutning under mer än 15 dagar | Upphört/avetablerats | Ingen säkerhets kopiering till disk eller Azure.<br/><br/> Om prenumerationen har upphört att gälla kan du återställa från disk eller Azure.<br/><br/> Om prenumerationen är inaktive rad kan du inte återställa från disk eller Azure. Azures återställnings punkter tas bort.

## <a name="domain-and-domain-trusts-support"></a>Stöd för domän-och domän förtroenden

|Krav |Information |
|---------|---------|
|Domain    | DPM/MABS-servern måste vara i en Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012-domän.        |
|Domänförtroende   |  DPM/MABS har stöd för data skydd mellan skogar, förutsatt att du etablerar ett dubbelriktat förtroende på skogs nivå mellan separata skogar.   <BR><BR>   DPM/MABS kan skydda servrar och arbets stationer mellan domäner inom en skog som har en dubbelriktad förtroende relation med DPM/MABS-Server domänen. Information om hur du skyddar datorer i arbets grupper eller ej betrodda domäner finns i [säkerhetskopiera och återställa arbets belastningar i arbets grupper och obetrodda domäner.](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains) <br><br> Om du vill säkerhetskopiera Hyper-V-serverkluster måste de finnas i samma domän som MABS-servern eller i en betrodd eller underordnad domän. Du kan säkerhetskopiera servrar och kluster i domäner eller arbetsbelastningar som inte är betrodda med hjälp av NTLM eller certifikatautentisering för en enskild server, eller certifikatautentisering endast för ett kluster.  |

## <a name="dpmmabs-storage-support"></a>Stöd för DPM/MABS-lagring

Data som säkerhets kopie ras till DPM/MABS lagras på den lokala disk lagringen.

USB-enheter eller flyttbara enheter stöds inte.

NTFS-komprimering stöds inte på DPM/MABS-volymer.

BitLocker kan bara aktive ras när du har lagt till disken i lagringspoolen. Aktivera inte BitLocker innan du lägger till det.

Nätverksansluten lagring (NAS) stöds inte för användning i DPM-lagringspoolen.

**Storage** | **Information**
--- | ---
**MB** | Modern backup Storage (MB) stöds från DPM 2016/MABS v2 och senare. Den är inte tillgänglig för MABS v1.
**MABS-lagring på virtuell Azure-dator** | Data lagras på Azure-diskar som är anslutna till den virtuella DPM-MABS och som hanteras i DPM/MABS. Antalet diskar som kan användas för DPM/MABS-lagringspoolen begränsas av storleken på den virtuella datorn.<br/><br/> A2 VM: 4 diskar; A3 VM: 8 diskar; A4 VM: 16 diskar, med en maximal storlek på 1 TB för varje disk. Detta avgör den totala tillgängliga lagringspoolen för säkerhets kopiering.<br/><br/> Mängden data som du kan säkerhetskopiera beror på antalet och storleken på de anslutna diskarna.
**MABS datakvarhållning på virtuell Azure-dator** | Vi rekommenderar att du behåller data för en dag på DPM/MABS Azure-disken och säkerhetskopierar från DPM/MABS till valvet för längre kvarhållning. På så sätt kan du skydda en större mängd data genom att avlasta den till Azure Backup.

### <a name="modern-backup-storage-mbs"></a>Modern säkerhets kopierings lagring (MB)

Från DPM 2016/MABS v2 (som körs på Windows Server 2016) och senare kan du dra nytta av modern backup Storage (MB).

- MB säkerhets kopior lagras på en ReFS-disk (elastiskt fil system).
- MB använder inkloning av ReFS-block för snabbare säkerhets kopiering och effektivare användning av lagrings utrymme.
- När du lägger till volymer i den lokala DPM/MABS-lagringspoolen konfigurerar du dem med enhets beteckningar. Du kan sedan konfigurera arbets belastnings lagring på olika volymer.
- När du skapar skydds grupper för att säkerhetskopiera data till DPM/MABS väljer du den enhet som du vill använda. Du kan till exempel lagra säkerhets kopior för SQL eller andra hög IOPS-arbetsbelastningar på en hög prestanda enhet och lagra arbets belastningar som säkerhets kopie ras mindre ofta på en lägre prestanda enhet.

## <a name="supported-backups-to-mabs"></a>Säkerhets kopior som stöds för MABS

Information om de olika servrar och arbets belastningar som du kan skydda med Azure Backup Server finns i [matrisen Azure Backup Server skydd](./backup-mabs-protection-matrix.md#protection-support-matrix).

## <a name="supported-backups-to-dpm"></a>Säkerhets kopior som stöds för DPM

Information om de olika servrar och arbets belastningar som du kan skydda med Data Protection Manager finns i artikeln [vad kan DPM säkerhetskopiera?](/system-center/dpm/dpm-protection-matrix).

- Klustrade arbets belastningar som säkerhets kopie ras av DPM/MABS bör vara i samma domän som DPM/MABS eller i en underordnad/betrodd domän.
- Du kan använda NTLM/certifikatautentisering för att säkerhetskopiera data i ej betrodda domäner eller arbets grupper.

## <a name="deduplicated-volumes-support"></a>Stöd för deduplicerade volymer

>[!NOTE]
> Deduplicerings stöd för MABS är beroende av stöd för operativ system.

### <a name="for-ntfs-volumes"></a>För NTFS-volymer

| Operativ system för skyddad Server  | Operativ system för MABS-Server  | MABS-version  | Dedup-stöd |
| ------------------------------------------ | ------------------------------------- | ------------------ | -------------------- |
| Windows Server 2019                       | Windows Server 2019                  | MABS v3            | Y                    |
| Windows Server 2016                       | Windows Server 2019                  | MABS v3            | Ja                   |
| Windows Server 2012 R2                    | Windows Server 2019                  | MABS v3            | N                    |
| Windows Server 2012                       | Windows Server 2019                  | MABS v3            | N                    |
| Windows Server 2019                       | Windows Server 2016                  | MABS v3            | Y * *                  |
| Windows Server 2016                       | Windows Server 2016                  | MABS v3            | Y                    |
| Windows Server 2012 R2                    | Windows Server 2016                  | MABS v3            | Y                    |
| Windows Server 2012                       | Windows Server 2016                  | MABS v3            | Y                    |

- \* När du skyddar en WS 2016 NTFS-deduplicerad volym med MABS v3 som körs på WS 2019 kan återställningarna påverkas. Vi har en åtgärd för att utföra återställningar på ett icke-deduplicerat sätt. Kontakta MABS-supporten om du behöver den här korrigeringen på MABS v3 UR1.
- \** När du skyddar en WS 2019 NTFS-deduplicerad volym med MABS v3 på WS 2016, kommer säkerhets kopiorna och återställningarna inte att dedupliceras. Det innebär att säkerhets kopieringarna förbrukar mer utrymme på MABS-servern än den ursprungliga NTFS-deduplicerade volymen.

**Problem**: om du uppgraderar det skyddade serveroperativ systemet från windows Server 2016 till windows Server 2019 kommer säkerhets kopian av den NTFS-deduplicerade volymen att påverkas på grund av ändringar i Deduplicerings logiken.

**Lösning**: kontakta Mabs support om du behöver den här korrigeringen för Mabs v3 UR1.

### <a name="for-refs-volumes"></a>För ReFS-volymer

>[!NOTE]
> Vi har identifierat några problem med säkerhets kopiering av deduplicerade ReFS-volymer. Vi arbetar med att åtgärda dessa och kommer att uppdatera det här avsnittet så snart vi har en tillgänglig lösning. Fram till dess tar vi bort stödet för säkerhets kopiering av deduplicerade ReFS-volymer från MABS v3.
>
> MABS v3-UR1 och senare fortsätter att stödja skydd och återställning av normala ReFS-volymer.

## <a name="next-steps"></a>Nästa steg

- [Lär dig mer](backup-architecture.md#architecture-back-up-to-dpmmabs) om Mabs-arkitekturen.
- [Granska](backup-support-matrix-mars-agent.md) vad som stöds för mars-agenten.
- [Konfigurera](backup-azure-microsoft-azure-backup.md) en Mabs-Server.
- [Konfigurera DPM](/system-center/dpm/install-dpm).
