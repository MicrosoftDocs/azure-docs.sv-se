---
title: Support mat ris för MARS-agenten
description: I den här artikeln sammanfattas Azure Backup support när du säkerhetskopierar datorer som kör Microsoft Azure Recovery Services-agenten (MARS).
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: 2170440b7b47861b75801b8dbd334686b4cabc8b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98985622"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Support mat ris för säkerhets kopiering med Microsoft Azure Recovery Services MARS-agenten

Du kan använda [tjänsten Azure Backup](backup-overview.md) för att säkerhetskopiera lokala datorer och appar och för att säkerhetskopiera virtuella datorer i Azure. Den här artikeln sammanfattar support inställningar och begränsningar när du använder den Microsoft Azure Recovery Services (MARS) för att säkerhetskopiera datorer.

## <a name="the-mars-agent"></a>MARS-agenten

Azure Backup använder MARS-agenten för att säkerhetskopiera data från lokala datorer och virtuella Azure-datorer till ett säkerhets kopierings Recovery Services valv i Azure. MARS-agenten kan:

- Kör på lokala Windows-datorer så att de kan säkerhetskopiera direkt till ett säkerhets kopierings Recovery Services valv i Azure.
- Körs på virtuella Windows-datorer så att de kan säkerhetskopiera direkt till ett valv.
- Kör på Microsoft Azure Backup Server (MABS) eller en System Center Data Protection Manager-Server (DPM). I det här scenariot kan datorer och arbets belastningar säkerhetskopiera till MABS eller till DPM-servern. MARS-agenten säkerhetskopierar sedan servern till ett valv i Azure.

> [!NOTE]
>Azure Backup stöder inte automatisk justering av klockan för sommar tid (DST). Ändra principen för att säkerställa att sommar tid tas med i beräkningen för att förhindra avvikelse mellan faktisk tid och schemalagd tid för säkerhets kopiering.

Dina säkerhets kopierings alternativ beror på var agenten är installerad. Mer information finns i [Azure Backup-arkitektur med mars-agenten](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Information om MABS och DPM backup-arkitekturen finns i [säkerhetskopiera till DPM eller Mabs](backup-architecture.md#architecture-back-up-to-dpmmabs). Se även [kraven](backup-support-matrix-mabs-dpm.md) för säkerhets kopierings arkitekturen.

**Installation** | **Information**
--- | ---
Hämta den senaste MARS-agenten | Du kan ladda ned den senaste versionen av agenten från valvet eller [Ladda ned den direkt](https://aka.ms/azurebackup_agent).
Installera direkt på en dator | Du kan installera MARS-agenten direkt på en lokal Windows Server eller på en virtuell Windows-dator som kör något av de [operativ system som stöds](./backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).
Installera på en säkerhets kopierings Server | När du konfigurerar DPM eller MABS för att säkerhetskopiera till Azure laddar du ned och installerar MARS-agenten på-servern. Du kan installera agenten på [operativ system som stöds](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) i matrisen för säkerhets kopierings servern.

> [!NOTE]
> Som standard har virtuella Azure-datorer som har Aktiver ATS för säkerhets kopiering en Azure Backup Extension-installation. Det här tillägget säkerhetskopierar hela den virtuella datorn. Du kan installera och köra MARS-agenten på en virtuell Azure-dator bredvid tillägget om du vill säkerhetskopiera vissa mappar och filer i stället för hela den virtuella datorn.
> När du kör MARS-agenten på en virtuell Azure-dator säkerhetskopierar den filer eller mappar som finns i tillfällig lagring på den virtuella datorn. Säkerhets kopieringar fungerar inte om filerna eller mapparna tas bort från det tillfälliga lagrings utrymmet eller om den tillfälliga lagringen tas bort.

## <a name="cache-folder-support"></a>Stöd för cache-mapp

När du använder MARS-agenten för att säkerhetskopiera data tar agenten en ögonblicks bild av data och lagrar den i en lokal cache-mapp innan data skickas till Azure. Mappen cache (scratch) har flera krav:

**Cache** | **Information**
--- | ---
Storlek |  Det lediga utrymmet i cache-mappen bör vara minst 5 till 10 procent av den totala storleken på dina säkerhets kopierings data.
Location | Cache-mappen måste lagras lokalt på den dator som säkerhets kopie ras och måste vara online. Cache-mappen får inte finnas på en nätverks resurs, på flyttbara medier eller på en frånkopplad volym.
Mapp | Cache-mappen bör inte vara krypterad på en deduplicerad volym eller i en mapp som är komprimerad, som är sparse eller som har en referens punkt.
Plats ändringar | Du kan ändra cache-platsen genom att stoppa säkerhets kopierings motorn ( `net stop bengine` ) och kopiera cache-mappen till en ny enhet. (Kontrol lera att det finns tillräckligt med utrymme på den nya enheten.) Uppdatera sedan två register poster under **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** (**config/ScratchLocation** och **config/CloudBackupProvider/ScratchLocation**) till den nya platsen och starta om motorn.

## <a name="networking-and-access-support"></a>Nätverks-och åtkomst stöd

### <a name="url-and-ip-access"></a>URL och IP-åtkomst

MARS-agenten behöver åtkomst till följande URL: er:

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.WindowsAzure.com
- *. MicrosoftOnline.com
- *. Windows.net
- `www.msftconnecttest.com`

Och till följande IP-adresser:

- 20.190.128.0/18
- 40.126.0.0/18

Åtkomst till alla webb adresser och IP-adresser som anges ovan använder HTTPS-protokollet på port 443.

När du säkerhetskopierar filer och mappar från virtuella Azure-datorer med MARS-agenten måste det virtuella Azure-nätverket också konfigureras för att tillåta åtkomst. Om du använder nätverks säkerhets grupper (NSG) använder du tjänst tag gen *AzureBackup* för att tillåta utgående åtkomst till Azure Backup. Förutom taggen Azure Backup måste du också tillåta anslutning för autentisering och data överföring genom att skapa liknande [NSG-regler](../virtual-network/network-security-groups-overview.md#service-tags) för Azure AD (*AzureActiveDirectory*) och Azure Storage (*lagring*). Följande steg beskriver processen för att skapa en regel för taggen Azure Backup:

1. I **alla tjänster** går du till **nätverks säkerhets grupper** och väljer Nätverks säkerhets gruppen.
2. Välj **utgående säkerhets regler** under **Inställningar**.
3. Välj **Lägg till**. Ange all information som krävs för att skapa en ny regel enligt beskrivningen i [säkerhets regel inställningar](../virtual-network/manage-network-security-group.md#security-rule-settings). Se till att alternativet **destination** har angetts till *service tag* och **mål tjänst tag gen** är inställt på *AzureBackup*.
4. Välj **Lägg till** för att spara den nyligen skapade utgående säkerhets regeln.

Du kan också skapa NSG utgående säkerhets regler för Azure Storage och Azure AD. Mer information om service märken finns i [den här artikeln](../virtual-network/service-tags-overview.md).

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

- Azure Backup (enligt platsen för ditt Recovery Services-valv)
- Azure Active Directory (12076:5060)
- Azure Storage (enligt platsen för ditt Recovery Services-valv)

Mer information finns i krav för [ExpressRoute-routning](../expressroute/expressroute-routing.md#bgp).

>[!NOTE]
>Offentlig peering är föråldrad för nya kretsar.

### <a name="private-endpoint-support"></a>Stöd för privat slut punkt

Du kan nu använda privata slut punkter för att säkerhetskopiera dina data på ett säkert sätt från servrar till ditt Recovery Services-valv. Eftersom Azure Active Directory för närvarande inte stöder privata slut punkter måste de IP-adresser och FQDN: er som krävs för Azure Active Directory vara tillåtna utgående åtkomst separat.

När du använder MARS-agenten för att säkerhetskopiera lokala resurser, se till att ditt lokala nätverk (som innehåller dina resurser som ska säkerhets kopie RAS) är peer-kopplat med det virtuella Azure-nätverket som innehåller en privat slut punkt för valvet. Sedan kan du fortsätta att installera MARS-agenten och konfigurera säkerhets kopiering. Du måste dock se till att all kommunikation för säkerhets kopiering sker via endast peer-nätverket.

Om du tar bort privata slut punkter för valvet efter att en MARS-agent har registrerats på det måste du registrera om behållaren med valvet. Du behöver inte sluta skydda dem.

Läs mer om [privata slut punkter för Azure Backup](private-endpoints.md).

### <a name="throttling-support"></a>Stöd för begränsning

**Funktion** | **Information**
--- | ---
Bandbredds kontroll | Stöds. I MARS-agenten använder du **ändra egenskaper** för att justera bandbredden.
Nätverksbegränsning | Inte tillgängligt för säkerhetskopierade datorer som kör Windows Server 2008 R2, Windows Server 2008 SP2 eller Windows 7.

## <a name="supported-operating-systems"></a>Operativsystem som stöds

>[!NOTE]
> MARS-agenten stöder inte Windows Server Core SKU: er.

Du kan använda MARS-agenten för att säkerhetskopiera direkt till Azure i de operativ system som anges nedan och som körs på:

1. Lokala Windows-servrar
2. Virtuella Azure-datorer som kör Windows

Operativ systemen måste vara 64-bitars och köra de senaste paketen och uppdateringarna. I följande tabell sammanfattas dessa operativ system:

**Operativsystem** | **Filer/mappar** | **System tillstånd** | **Krav för program/modul**
--- | --- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Ja | Inga |  Kontrol lera motsvarande Server version för program-/modul krav
Windows 8,1 (Enterprise, Pro)| Ja |Inga | Kontrol lera motsvarande Server version för program-/modul krav
Windows 8 (Enterprise, Pro) | Ja | Inga | Kontrol lera motsvarande Server version för program-/modul krav
Windows Server 2016 (standard, data Center, Essentials) | Ja | Ja | – .NET 4,5 <br> – Windows PowerShell <br> -Senaste kompatibla Microsoft VC + + Redistributable <br> – Microsoft Management Console (MMC) 3,0
Windows Server 2012 R2 (standard, data Center, Foundation, Essentials) | Ja | Ja | – .NET 4,5 <br> – Windows PowerShell <br> -Senaste kompatibla Microsoft VC + + Redistributable <br> – Microsoft Management Console (MMC) 3,0
Windows Server 2012 (standard, data Center, Foundation) | Ja | Ja |– .NET 4,5 <br> – Windows PowerShell <br> -Senaste kompatibla Microsoft VC + + Redistributable <br> – Microsoft Management Console (MMC) 3,0 <br> – Underhåll och hantering av distributions avbildningar (DISM.exe)
Windows Storage Server 2016/2012 R2/2012 (standard, arbets grupp) | Ja | Inga | – .NET 4,5 <br> – Windows PowerShell <br> -Senaste kompatibla Microsoft VC + + Redistributable <br> – Microsoft Management Console (MMC) 3,0
Windows Server 2019 (standard, data Center, Essentials) | Ja | Ja | – .NET 4,5 <br> – Windows PowerShell <br> -Senaste kompatibla Microsoft VC + + Redistributable <br> – Microsoft Management Console (MMC) 3,0

Mer information finns i [Mabs-och DPM-operativsystem som stöds](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

### <a name="operating-systems-at-end-of-support"></a>Operativ system i slutet av supporten

Följande operativ system är i slutet av supporten och vi rekommenderar starkt att du uppgraderar operativ systemet för att fortsätta att vara skyddat.

Om befintliga åtaganden förhindrar uppgradering av operativ systemet bör du överväga att migrera Windows-servrar till virtuella Azure-datorer och utnyttja virtuella Azure-säkerhetskopieringar för att fortsätta vara skyddade. Besök [sidan migrering här](https://azure.microsoft.com/migration/windows-server/) om du vill ha mer information om hur du migrerar din Windows Server.

För lokala eller värdbaserade miljöer, där du inte kan uppgradera operativ systemet eller migrera till Azure, aktiverar du utökade säkerhets uppdateringar för datorerna så att de fortsätter att skyddas och stöds. Observera att endast vissa utgåvor är kvalificerade för utökade säkerhets uppdateringar. Besök [sidan med vanliga frågor och svar](https://www.microsoft.com/windows-server/extended-security-updates) för mer information.

| **Operativ system**                                       | **Filer/mappar** | **System tillstånd** | **Krav för program/modul**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7 (Ultimate, Enterprise, Pro, Home Premium/Basic, starter) | Ja               | Inga                 | Kontrol lera motsvarande Server version för program-/modul krav |
| Windows Server 2008 R2 (standard, Enterprise, data Center, Foundation) | Ja               | Ja                | – .NET 3,5, .NET 4,5 <br>  – Windows PowerShell <br>  -Kompatibel Microsoft VC + + Redistributable <br>  – Microsoft Management Console (MMC) 3,0 <br>  – Underhåll och hantering av distributions avbildningar (DISM.exe) |
| Windows Server 2008 SP2 (standard, data Center, Foundation)  | Ja               | Inga                 | – .NET 3,5, .NET 4,5 <br>  – Windows PowerShell <br>  -Kompatibel Microsoft VC + + Redistributable <br>  – Microsoft Management Console (MMC) 3,0 <br>  – Underhåll och hantering av distributions avbildningar (DISM.exe) <br>  – Virtual Server 2005 Base + KB KB948515 |

## <a name="backup-limits"></a>Säkerhets kopierings gränser

### <a name="size-limits"></a>Storleks begränsningar

Azure Backup begränsar storleken på en fil eller mapp data källa som kan säkerhets kopie ras. De objekt som du säkerhetskopierar från en enskild volym får inte överstiga storlekarna som sammanfattas i den här tabellen:

**Operativsystem** | **Storleks gräns**
--- | ---
Windows Server 2012 eller senare |54 400 GB
Windows Server 2008 R2 SP1 |1 700 GB
Windows Server 2008 SP2| 1 700 GB
Windows 8 eller senare| 54 400 GB
Windows 7| 1 700 GB

### <a name="minimum-retention-limits"></a>Lägsta gräns för kvarhållning

Följande är de minsta Retentions tiderna som kan anges för de olika återställnings punkterna:

|Återställnings punkt |Varaktighet  |
|---------|---------|
|Daglig återställnings punkt    |   7 dagar      |
|Veckovis återställnings punkt     |    4 veckor     |
|Månatlig återställnings punkt    |   3 månader      |
|Årlig återställnings punkt  |      1 år   |

### <a name="other-limitations"></a>Andra begränsningar

- MARS stöder inte skydd av flera datorer med samma namn i ett enda valv.

## <a name="supported-file-types-for-backup"></a>Filtyper som stöds för säkerhets kopiering

**Typ** | **Support**
--- | ---
Krypterade<sup>*</sup>| Stöds.
Komprimerade | Stöds.
Utspridda | Stöds.
Komprimerad och sparse |Stöds.
Hårda länkar| Stöds inte. Hoppades.
Referenspunkt| Stöds inte. Hoppades.
Krypterad och sparse |Stöds inte. Hoppades.
Komprimerad ström| Stöds inte. Hoppades.
Sparse-dataström| Stöds inte. Hoppades.
OneDrive (synkroniserade filer är sparse-strömmar)| Stöds inte.
Mappar med DFS Replication aktiverat | Stöds inte.

\* Se till att MARS-agenten har åtkomst till de certifikat som krävs för att komma åt de krypterade filerna. Otillgängliga filer kommer att hoppas över.

## <a name="supported-drives-or-volumes-for-backup"></a>Enheter eller volymer som stöds för säkerhets kopiering

**Enhet/volym** | **Support** | **Information**
--- | --- | ---
Skrivskyddade volymer| Stöds inte | VSS (Volume Copy Shadow service) fungerar bara om volymen är skrivbar.
Offline-volymer| Stöds inte |VSS fungerar bara om volymen är online.
Nätverks resurs| Stöds inte |Volymen måste vara lokal på servern.
BitLocker-låsta volymer| Stöds inte |Volymen måste låsas upp innan säkerhets kopieringen startar.
Identifiering av fil system| Stöds inte |Endast NTFS stöds.
Flyttbart medium| Stöds inte |Alla säkerhets kopierings objekt källor måste ha en *fast* status.
Deduplicerade enheter | Stöds | Azure Backup konverterar deduplicerade data till normala data. Den optimerar, krypterar, lagrar och skickar data till valvet.

## <a name="support-for-initial-offline-backup"></a>Stöd för första säkerhets kopiering offline

Azure Backup stöder *offlineåtkomst* för att överföra första säkerhets kopierings data till Azure med hjälp av diskar. Det här stödet är användbart om den första säkerhets kopieringen troligen ligger inom storleks intervallet på terabyte (TBs). Offline-säkerhetskopiering stöds för:

- Direkt säkerhets kopiering av filer och mappar på lokala datorer som kör MARS-agenten.
- Säkerhets kopiering av arbets belastningar och filer från en DPM-server eller MABS.

Det går inte att använda säkerhets kopiering offline för system tillstånds filer.

## <a name="support-for-data-restoration"></a>Stöd för data återställning

Genom att använda funktionen [omedelbar återställning](backup-instant-restore-capability.md) i Azure Backup kan du återställa data innan de kopieras till valvet. Datorn som du säkerhetskopierar måste köra .NET Framework 4.5.2 eller senare.

Säkerhets kopieringar kan inte återställas till en måldator som kör en tidigare version av operativ systemet. Till exempel kan en säkerhets kopia som tas från en dator som kör Windows 7 återställas på Windows 8 eller senare. Men en säkerhets kopia som har tagits från en dator som kör Windows 8 kan inte återställas på en dator som kör Windows 7.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [säkerhets kopierings arkitektur som använder mars-agenten](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Lär dig vad som stöds när du [Kör mars-agenten på Mabs eller en DPM-server](backup-support-matrix-mabs-dpm.md).
