---
title: Stödmatris för MARS-agenten
description: Den här artikeln sammanfattar Azure Backup stöd när du återställning av datorer som kör Microsoft Azure Recovery Services(MARS)-agenten.
ms.date: 04/09/2021
ms.topic: conceptual
ms.openlocfilehash: 20bca0e9ca9dfd735501e68bd0e5a6d69d2ef68e
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576507"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Stödmatris för säkerhetskopiering med MARS Microsoft Azure agenten (Recovery Services)

Du kan använda [Azure Backup-tjänsten för](backup-overview.md) att backa upp lokala datorer och appar och för att backa upp virtuella Azure-datorer (VM). Den här artikeln sammanfattar supportinställningar och begränsningar när du använder mars Microsoft Azure agenten (Recovery Services) för att äldsta till en annan.

## <a name="the-mars-agent"></a>MARS-agenten

Azure Backup använder MARS-agenten för att säkerhetskopiera data från lokala datorer och virtuella Azure-datorer till ett Recovery Services-säkerhetskopieringsvalv i Azure. MARS-agenten kan:

- Kör lokala Windows-datorer så att de kan säkerhetskopiera direkt till ett Recovery Services-säkerhetskopieringsvalv i Azure.
- Kör på virtuella Windows-datorer så att de kan valva upp direkt.
- Kör på Microsoft Azure Backup Server (MABS) eller en System Center Data Protection Manager (DPM)-server. I det här scenariot ar datorer och arbetsbelastningar tillbaka till MABS eller till DPM-servern. MARS-agenten backar sedan upp den här servern till ett valv i Azure.

> [!NOTE]
>Azure Backup stöder inte automatisk justering av klockan för sommartid (DST). Ändra principen för att säkerställa att sommartid beaktas för att förhindra avvikelser mellan den faktiska tiden och den schemalagda säkerhetskopieringstiden.

Dina säkerhetskopieringsalternativ beror på var agenten är installerad. Mer information finns i Azure Backup [med MARS-agenten](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Information om MABS- och DPM-säkerhetskopieringsarkitektur finns [i Säkerhetskopiera till DPM eller MABS.](backup-architecture.md#architecture-back-up-to-dpmmabs) Se även [kraven för](backup-support-matrix-mabs-dpm.md) säkerhetskopieringsarkitekturen.

**Installation** | **Information**
--- | ---
Ladda ned den senaste MARS-agenten | Du kan ladda ned den senaste versionen av agenten från valvet eller [ladda ned den direkt.](https://aka.ms/azurebackup_agent)
Installera direkt på en dator | Du kan installera MARS-agenten direkt på en lokal Windows-server eller på en virtuell Windows-dator som kör något [av de operativsystem som stöds.](./backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)
Installera på en säkerhetskopieringsserver | När du laddade ned DPM eller MABS för att servera till Azure laddar du ned och installerar MARS-agenten på servern. Du kan installera agenten på operativsystem [som stöds i](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) supportmatrisen för säkerhetskopieringsservern.

> [!NOTE]
> Som standard har virtuella Azure-datorer som är aktiverade för säkerhetskopiering en Azure Backup installation av tillägget. Det här tillägget backar upp hela den virtuella datorn. Du kan installera och köra MARS-agenten på en virtuell Azure-dator tillsammans med tillägget om du vill backa upp specifika mappar och filer i stället för den fullständiga virtuella datorn.
> När du kör MARS-agenten på en virtuell Azure-dator, backar den upp filer eller mappar som finns i tillfällig lagring på den virtuella datorn. Säkerhetskopieringar misslyckas om filerna eller mapparna tas bort från det tillfälliga lagringsutrymmet eller om det tillfälliga lagringsutrymmet tas bort.

## <a name="cache-folder-support"></a>Stöd för cachemappar

När du använder MARS-agenten för att säkerhetskopiera data tar agenten en ögonblicksbild av data och lagrar dem i en lokal cachemapp innan den skickar data till Azure. Cachelagringsmappen (scratch) har flera krav:

**Cache** | **Information**
--- | ---
Storlek |  Ledigt utrymme i cachemappen bör vara minst 5 till 10 procent av den totala storleken på dina säkerhetskopierade data.
Location | Cachelagringsmappen måste lagras lokalt på den dator som säkerhetskopieras och den måste vara online. Cachelagringsmappen får inte finnas på en nätverksresurs, på flyttbara medier eller på en offlinevolym.
Mapp | Cachelagringsmappen får inte krypteras på en deduplicerad volym eller i en komprimerad mapp, som är gles eller som har en reparspunkt.
Platsändringar | Du kan ändra cacheplatsen genom att stoppa säkerhetskopieringsmotorn ( `net stop bengine` ) och kopiera cachemappen till en ny enhet. (Se till att den nya enheten har tillräckligt med utrymme.) Uppdatera sedan två registerposter under **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** (**Config/ScratchLocation** och **Config/CloudBackupProvider/ScratchLocation**) till den nya platsen och starta om motorn.

## <a name="networking-and-access-support"></a>Stöd för nätverk och åtkomst

### <a name="url-and-ip-access"></a>URL- och IP-åtkomst

MARS-agenten behöver åtkomst till dessa URL:er:

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.WindowsAzure.com
- *. MicrosoftOnline.com
- *. Windows.net
- `www.msftconnecttest.com`

Och till dessa IP-adresser:

- 20.190.128.0/18
- 40.126.0.0/18

Åtkomst till alla URL:er och IP-adresser som anges ovan använder HTTPS-protokollet på port 443.

När du konfigurerar filer och mappar från virtuella Azure-datorer med MARS-agenten måste även det virtuella Azure-nätverket konfigureras för att tillåta åtkomst. Om du använder nätverkssäkerhetsgrupper (NSG) använder du *tjänsttaggen AzureBackup* för att tillåta utgående åtkomst till Azure Backup. Förutom taggen Azure Backup måste du även tillåta anslutning för autentisering och dataöverföring genom att skapa liknande [NSG-regler](../virtual-network/network-security-groups-overview.md#service-tags) för Azure AD (*AzureActiveDirectory*) och Azure Storage(*Storage).* Följande steg beskriver processen för att skapa en regel för Azure Backup taggen:

1. I **Alla tjänster** går du till **Nätverkssäkerhetsgrupper** och väljer nätverkssäkerhetsgruppen.
2. Välj **Utgående säkerhetsregler** under **Inställningar.**
3. Välj **Lägg till**. Ange all information som krävs för att skapa en ny regel enligt beskrivningen i [säkerhetsregelinställningarna](../virtual-network/manage-network-security-group.md#security-rule-settings). Kontrollera att alternativet **Mål är** inställt på *Tjänsttagg* och **Måltjänsttagg** har angetts *till AzureBackup.*
4. Välj **Lägg till** för att spara den nyligen skapade utgående säkerhetsregeln.

På samma sätt kan du skapa utgående säkerhetsregler för NSG för Azure Storage och Azure AD. Mer information om tjänsttaggar finns i den [här artikeln.](../virtual-network/service-tags-overview.md)

### <a name="azure-expressroute-support"></a>Azure ExpressRoute support

Du kan backa upp dina data Azure ExpressRoute med offentlig peering (tillgängligt för gamla kretsar) och Microsoft-peering. Säkerhetskopiering via privat peering stöds inte.

Med offentlig peering: Se till att du har åtkomst till följande domäner/adresser:

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

- Azure Backup (enligt platsen för Recovery Services-valvet)
- Azure Active Directory (12076:5060)
- Azure Storage (enligt platsen för Recovery Services-valvet)

Mer information finns i [ExpressRoute-routningskraven.](../expressroute/expressroute-routing.md#bgp)

>[!NOTE]
>Offentlig peering är inaktuell för nya kretsar.

### <a name="private-endpoint-support"></a>Stöd för privat slutpunkt

Nu kan du använda privata slutpunkter för att valva dina data på ett säkert sätt från servrar till Recovery Services-valvet. Eftersom Azure Active Directory för närvarande inte stöder privata slutpunkter måste IP-adresser och FQDN som krävs för Azure Active Directory tillåtas separat utgående åtkomst.

När du använder MARS-agenten för att säkerhetskopiera dina lokala resurser ska du se till att ditt lokala nätverk (som innehåller dina resurser som ska säkerhetskopieras) är peer-peering med det virtuella Azure-nätverk som innehåller en privat slutpunkt för valvet. Du kan sedan fortsätta att installera MARS-agenten och konfigurera säkerhetskopiering. Du måste dock se till att all kommunikation för säkerhetskopiering endast sker via det peer-ade nätverket.

Om du tar bort privata slutpunkter för valvet när en MARS-agent har registrerats till det måste du registrera containern på nytt med valvet. Du behöver inte sluta skydda dem.

Läs mer om [privata slutpunkter för Azure Backup](private-endpoints.md).

### <a name="throttling-support"></a>Begränsningsstöd

**Funktion** | **Information**
--- | ---
Bandbreddskontroll | Stöds. I MARS-agenten använder du **Ändra egenskaper för** att justera bandbredden.
Nätverksbegränsning | Inte tillgängligt för säkerhetskopierade datorer som kör Windows Server 2008 R2, Windows Server 2008 SP2 eller Windows 7.

## <a name="supported-operating-systems"></a>Operativsystem som stöds

>[!NOTE]
> MARS-agenten stöder inte Windows Server Core-SKU:er.

Du kan använda MARS-agenten för att backa upp direkt till Azure på de operativsystem som anges nedan som körs på:

1. Lokala Windows-servrar
2. Virtuella Azure-datorer som kör Windows

Operativsystemen måste vara 64-bitars och bör köra de senaste service packen och uppdateringarna. I följande tabell sammanfattas dessa operativsystem:

**Operativsystem** | **Filer/mappar** | **Systemtillstånd** | **Programvaru-/modulkrav**
--- | --- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Ja | Inga |  Kontrollera motsvarande serverversion för program-/modulkrav
Windows 8.1 (Enterprise, Pro)| Ja |Inga | Kontrollera motsvarande serverversion för program-/modulkrav
Windows 8 (Enterprise, Pro) | Ja | Inga | Kontrollera motsvarande serverversion för program-/modulkrav
Windows Server 2016 (Standard, Datacenter, Essentials) | Ja | Ja | – .NET 4.5 <br> – Windows PowerShell <br> – Senaste kompatibla Microsoft VC++ Redistributable <br> – Microsoft Management Console (MMC) 3.0
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Ja | Ja | – .NET 4.5 <br> – Windows PowerShell <br> – Senaste kompatibla Microsoft VC++ Redistributable <br> – Microsoft Management Console (MMC) 3.0
Windows Server 2012 (Standard, Datacenter, Foundation) | Ja | Ja |– .NET 4.5 <br> -Windows PowerShell <br> – Senaste kompatibla Microsoft VC++ Redistributable <br> – Microsoft Management Console (MMC) 3.0 <br> – Deployment Image Servicing and Management (DISM.exe)
Windows Storage Server 2016/2012 R2/2012 (Standard, Workgroup) | Ja | Inga | – .NET 4.5 <br> – Windows PowerShell <br> – Senaste kompatibla Microsoft VC++ Redistributable <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2019 (Standard, Datacenter, Essentials) | Ja | Ja | – .NET 4.5 <br> – Windows PowerShell <br> – Senaste kompatibla Microsoft VC++ Redistributable <br> - Microsoft Management Console (MMC) 3.0

Mer information finns i [MABS- och DPM-operativsystem som stöds.](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)

### <a name="operating-systems-at-end-of-support"></a>Operativsystem i slutet av supporten

Följande operativsystem är i slutet av supporten och vi rekommenderar starkt att du uppgraderar operativsystemet så att det fortsätter att vara skyddat.

Om befintliga åtaganden förhindrar uppgradering av operativsystemet bör du överväga att migrera Windows-servrarna till virtuella Azure-datorer och utnyttja säkerhetskopior av virtuella Azure-datorer för att fortsätta skydda dem. Besök [migreringssidan här för](https://azure.microsoft.com/migration/windows-server/) mer information om hur du migrerar din Windows-server.

För lokala eller värdbaserade miljöer, där du inte kan uppgradera operativsystemet eller migrera till Azure, aktiverar du utökade säkerhetsuppdateringar för datorerna så att de fortsätter att vara skyddade och stöds. Observera att endast vissa utgåvor är berättigade till utökade säkerhetsuppdateringar. Besök sidan med [vanliga frågor och svar](https://www.microsoft.com/windows-server/extended-security-updates) om du vill veta mer.

| **Operativsystem**                                       | **Filer/mappar** | **Systemtillstånd** | **Programvaru-/modulkrav**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7 (Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | Ja               | Inga                 | Kontrollera motsvarande serverversion för program-/modulkrav |
| Windows Server 2008 R2 (Standard, Enterprise, Datacenter, Foundation) | Ja               | Ja                | – .NET 3.5, .NET 4.5 <br>  – Windows PowerShell <br>  – Kompatibelt Microsoft VC++ Redistributable <br>  – Microsoft Management Console (MMC) 3.0 <br>  – Deployment Image Servicing and Management (DISM.exe) |
| Windows Server 2008 SP2 (Standard, Datacenter, Foundation)  | Ja               | Inga                 | – .NET 3.5, .NET 4.5 <br>  – Windows PowerShell <br>  – Kompatibelt Microsoft VC++ Redistributable <br>  – Microsoft Management Console (MMC) 3.0 <br>  – Deployment Image Servicing and Management (DISM.exe) <br>  – Virtual Server 2005 base + KB KB948515 |

## <a name="backup-limits"></a>Gränser för säkerhetskopiering

### <a name="size-limits"></a>Storleksgränser

Azure Backup begränsar storleken på en fil- eller mappdatakälla som kan säkerhetskopieras. De objekt som du kommer tillbaka från en enda volym får inte överskrida de storlekar som sammanfattas i den här tabellen:

**Operativsystem** | **Storleksgräns**
--- | ---
Windows Server 2012 eller senare |54 400 GB
Windows Server 2008 R2 SP1 |1 700 GB
Windows Server 2008 SP2| 1 700 GB
Windows 8 eller senare| 54 400 GB
Windows 7| 1 700 GB

### <a name="minimum-retention-limits"></a>Minsta kvarhållningsgränser

Följande är de minsta kvarhållningstiderna som kan anges för de olika återställningspunkterna:

|Återställningspunkt |Varaktighet  |
|---------|---------|
|Daglig återställningspunkt    |   7 dagar      |
|Veckovis återställningspunkt     |    4 veckor     |
|Månatlig återställningspunkt    |   3 månader      |
|Årliga återställningspunkt  |      1 år   |

### <a name="other-limitations"></a>Andra begränsningar

- MARS stöder inte skydd av flera datorer med samma namn till ett enda valv.

## <a name="supported-file-types-for-backup"></a>Filtyper som stöds för säkerhetskopiering

**Typ** | **Support**
--- | ---
Krypterade<sup>*</sup>| Stöds.
Komprimerade | Stöds.
Utspridda | Stöds.
Komprimerade och glesa |Stöds.
Hårda länkar| Stöds inte. Överhoppade.
Referenspunkt| Stöds inte. Överhoppade.
Krypterad och gles |Stöds inte. Överhoppade.
Komprimerad dataström| Stöds inte. Överhoppade.
Gles ström| Stöds inte. Överhoppade.
OneDrive (synkroniserade filer är glesa strömmar)| Stöds inte.
Mappar med DFS Replication aktiverat | Stöds inte.

\* Kontrollera att MARS-agenten har åtkomst till de certifikat som krävs för att komma åt de krypterade filerna. Otillgängliga filer hoppas över.

## <a name="supported-drives-or-volumes-for-backup"></a>Enheter eller volymer som stöds för säkerhetskopiering

**Enhet/volym** | **Support** | **Information**
--- | --- | ---
Skrivskyddade volymer| Stöds inte | VSS (Volume Copy Shadow Service) fungerar bara om volymen är skrivbar.
Offlinevolymer| Stöds inte |VSS fungerar bara om volymen är online.
Nätverksresurs| Stöds inte |Volymen måste vara lokal på servern.
BitLocker-låsta volymer| Stöds inte |Volymen måste låsas upp innan säkerhetskopieringen startar.
Identifiering av filsystem| Stöds inte |Endast NTFS stöds.
Flyttbart medium| Stöds inte |Alla källor för säkerhetskopieringsobjekt måste ha *en fast* status.
Deduplicerade enheter | Stöds | Azure Backup deduplicerade data till normala data. Den optimerar, krypterar, lagrar och skickar data till valvet.

## <a name="support-for-initial-offline-backup"></a>Stöd för inledande offlinesäkerhetskopiering

Azure Backup har stöd *för offline-seeding* för att överföra inledande säkerhetskopierade data till Azure med hjälp av diskar. Det här stödet är användbart om din första säkerhetskopiering sannolikt ligger inom storleksintervallet terabyte (TB). Offlinesäkerhetskopiering stöds för:

- Direkt säkerhetskopiering av filer och mappar på lokala datorer som kör MARS-agenten.
- Säkerhetskopiering av arbetsbelastningar och filer från en DPM-server eller MABS.

Offlinesäkerhetskopiering kan inte användas för systemtillståndsfiler.

## <a name="support-for-data-restoration"></a>Stöd för dataåterställning

Med funktionen [Omedelbar återställning](backup-instant-restore-capability.md) i Azure Backup kan du återställa data innan de kopieras till valvet. Den dator som du backar upp måste köra .NET Framework 4.5.2 eller senare.

Säkerhetskopior kan inte återställas till en måldator som kör en tidigare version av operativsystemet. Till exempel kan en säkerhetskopia som tas från en dator som kör Windows 7 återställas på Windows 8 eller senare. Men en säkerhetskopia som tas från en dator som kör Windows 8 kan inte återställas på en dator som kör Windows 7.

## <a name="previous-mars-agent-versions"></a>Tidigare MARS-agentversioner

I följande tabell visas tidigare versioner av agenten med deras nedladdningslänkar. Vi rekommenderar att du uppgraderar agentversionen till den senaste, så att du kan utnyttja de senaste funktionerna och optimala prestanda.

**Versioner** | **KB-artiklar**
--- | ---
[2.0.9145.0](https://download.microsoft.com/download/4/5/E/45EB38B4-2DA7-45FA-92E1-5CA1E23D18D1/MARSAgentInstaller.exe) | Inte tillgängligt
[2.0.9151.0](https://download.microsoft.com/download/7/1/7/7177B70A-51E8-434D-BDF2-FA3A09E917D6/MARSAgentInstaller.exe) | Inte tillgängligt
[2.0.9153.0](https://download.microsoft.com/download/3/D/D/3DD8A2FF-AC48-4A62-8566-B2C05F0BCCD0/MARSAgentInstaller.exe) | Inte tillgängligt
[2.0.9162.0](https://download.microsoft.com/download/0/1/0/010E598E-6289-47DB-872A-FFAF5030E6BE/MARSAgentInstaller.exe) | Inte tillgängligt
[2.0.9169.0](https://download.microsoft.com/download/f/7/1/f716c719-24bc-4337-af48-113baddc14d8/MARSAgentInstaller.exe) | [4515971](https://support.microsoft.com/help/4538314)
[2.0.9170.0](https://download.microsoft.com/download/1/8/7/187ca9a9-a6e5-45f0-928f-9a843d84aed5/MARSAgentInstaller.exe) | Inte tillgängligt
[2.0.9173.0](https://download.microsoft.com/download/7/9/2/79263a35-de87-4ba6-9732-65563a4274b6/MARSAgentInstaller.exe) | [4538314](https://support.microsoft.com/help/4538314)
[2.0.9177.0](https://download.microsoft.com/download/3/0/4/304d3cdf-b123-42ee-ad03-98fb895bc38f/MARSAgentInstaller.exe) | Inte tillgängligt
[2.0.9181.0](https://download.microsoft.com/download/6/6/9/6698bc49-e30b-4a3e-a1f4-5c859beafdcc/MARSAgentInstaller.exe) | Inte tillgängligt
[2.0.9190.0](https://download.microsoft.com/download/a/c/e/aceffec0-794e-4259-8107-92a3f6c10f55/MARSAgentInstaller.exe) | [4575948](https://support.microsoft.com/help/4575948)
[2.0.9195.0](https://download.microsoft.com/download/6/1/3/613b70a7-f400-4806-9d98-ae26aeb70be9/MARSAgentInstaller.exe) | [4582474](https://support.microsoft.com/help/4582474)
[2.0.9197.0](https://download.microsoft.com/download/2/7/5/27531ace-3100-43bc-b4af-7367680ea66b/MARSAgentInstaller.exe) | [4589598](https://support.microsoft.com/help/4589598)
[2.0.9207.0](https://download.microsoft.com/download/b/5/a/b5a29638-1cef-4906-b704-4d3d914af76e/MARSAgentInstaller.exe) | [5001305](https://support.microsoft.com/help/5001305)

>[!NOTE]
>MARS-agentversioner med mindre tillförlitlighet och prestandaförbättringar har ingen KB-artikel.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [säkerhetskopieringsarkitektur som använder MARS-agenten](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Lär dig vad som stöds när du [kör MARS-agenten på MABS eller en DPM-server.](backup-support-matrix-mabs-dpm.md)
