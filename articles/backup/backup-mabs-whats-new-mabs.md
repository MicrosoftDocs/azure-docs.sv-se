---
title: Nyheter i Microsoft Azure Backup Server
description: Microsoft Azure Backup Server ger dig förbättrade säkerhets kopierings funktioner för att skydda virtuella datorer, filer och mappar, arbets belastningar med mera.
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: 39050d0f658e29b82f270f1fe53026e2fb80bfa1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91332771"
---
# <a name="whats-new-in-microsoft-azure-backup-server-mabs"></a>Vad är nytt i Microsoft Azure Backup Server (MABS)

## <a name="whats-new-in-mabs-v3-ur1"></a>Vad är nytt i MABS v3-UR1

Microsoft Azure Backup Server (MABS) version 3 UR1 är den senaste uppdateringen och innehåller viktiga fel korrigeringar och andra funktioner och förbättringar. Om du vill visa en lista över program fel som har åtgärd ATS och Installationsinstruktioner för MABS v3 UR1, se KB-artikel [4534062](https://support.microsoft.com/help/4534062).

>[!NOTE]
>Stöd för 32-bitars skydds agenten är föråldrad med MABS v3-UR1. Se [32-bitars skydds agentens utfasning](#32-bit-protection-agent-deprecation).

### <a name="faster-backups-with-tiered-storage-using-ssds"></a>Snabbare säkerhets kopiering med skiktad lagring med SSD

MABS v2 introducerade [modern backup Storage](backup-mabs-add-storage.md) (MB), vilket förbättrar lagrings användningen och prestanda. MB använder ReFS som underliggande fil system och har utformats för att användas med hybrid lagring, till exempel nivå lagring.

För att uppnå skalning och prestanda från MB rekommenderar vi att du använder en liten procent andel (4% av det totala lagrings utrymmet) för Flash Storage (SSD) med MABS v3-UR1 som en nivå volym i kombination med DPM HDD-lagring. MABS v3-UR1 med skiktat lagring ger 50-70% snabbare säkerhets kopieringar. Mer information om hur du konfigurerar lagring på lagrings nivå hittar du i DPM-artikeln [Konfigurera MB med Tiered Storage](/system-center/dpm/add-storage#set-up-mbs-with-tiered-storage) .

### <a name="support-for-refs-volumes"></a>Stöd för ReFS-volymer

Med MABS v3-UR1 kan du säkerhetskopiera ReFS-volymer och arbets belastningar som har distribuerats på ReFS-volymen. Du kan säkerhetskopiera följande arbets belastningar som har distribuerats på ReFS-volymerna:

* Operativ system (64 bitar): Windows Server 2019, 2016, 2012 R2, 2012.
* SQL Server: SQL Server 2019, SQL Server 2017, 2016.
* Exchange: Exchange 2019, 2016.
* SharePoint: SharePoint 2019, 2016 med senaste SP.

>[!NOTE]
> Säkerhets kopiering av virtuella Hyper-V-datorer som lagras på en ReFS-volym stöds med MABS v3

>OVIKTIG Vi har identifierat några problem med säkerhets kopiering av deduplicerade ReFS-volymer. Vi arbetar med att åtgärda dessa och kommer att uppdatera det här avsnittet så snart vi har en tillgänglig lösning. Fram till dess tar vi bort stödet för säkerhets kopiering av deduplicerade ReFS-volymer från MABSv3 UR1.

### <a name="azure-vmware-solution-protection-support"></a>Stöd för Azure VMware-lösnings skydd

Med MABS v3-UR1 kan du nu skydda virtuella datorer som distribueras i [Azure VMware-lösningen](../azure-vmware/index.yml).

### <a name="vmware-parallel-backups"></a>Parallella VMware-säkerhetskopieringar

Med MABS v3-UR1 kommer alla virtuella VMware-VM: er i en enda skydds grupp att vara parallella, vilket leder till 25% snabbare säkerhets kopiering av virtuella datorer.
I tidigare versioner av MABS utfördes parallella säkerhets kopieringar endast över skydds grupper. Med MABS v3-UR1 körs VMware delta-replikering parallellt. Som standard är antalet jobb som ska köras parallellt inställt på 8. Lär dig mer om [parallella VMware-säkerhetskopieringar](backup-azure-backup-server-vmware.md#vmware-parallel-backups).

### <a name="disk-exclusion-for-vmware-vm-backup"></a>Disk undantag för säkerhets kopiering av VMware-dator

Med MABS v3-UR1 kan du undanta vissa diskar från en virtuell VMware-säkerhetskopiering. Läs mer om att [utesluta diskar från säkerhets kopiering av virtuella VMware-datorer](backup-azure-backup-server-vmware.md#exclude-disk-from-vmware-vm-backup).  

### <a name="support-for-additional-layer-of-authentication-to-delete-online-backup"></a>Stöd för ytterligare lager av autentisering för borttagning av onlinesäkerhetskopiering

Med MABS v3-UR1 läggs ytterligare ett lager med autentisering till för kritiska åtgärder. Du uppmanas att ange en säkerhets-PIN-kod när du gör ett **stopp skydd med åtgärder för att ta bort data** .

### <a name="offline-backup-improvements"></a>Förbättringar av offline-säkerhetskopiering

MABS v3-UR1 förbättrar upplevelsen av offline-säkerhetskopiering med Azure import/export-tjänsten. Mer information finns i de uppdaterade stegen [här](./backup-azure-backup-server-import-export.md).

>[!NOTE]
>Uppdateringen ger även för hands versionen för säkerhets kopiering offline med Azure Data Box i MABS. Kontakta [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com) för att få mer information.

### <a name="new-cmdlet-parameter"></a>Ny cmdlet-parameter

MABS v3-UR1 innehåller en ny parameter **[-CheckReplicaFragmentation]**. Den nya parametern beräknar fragmenteringen i procent för en replik och ingår i cmdleten **copy-DPMDatasourceReplica** .

### <a name="32-bit-protection-agent-deprecation"></a>32-bitars skydds agent-utfasning

Med MABS v3-UR1 stöds inte längre stöd för 32-bitars skydds agent. Du kan inte skydda 32-bitars arbets belastningar när du har uppgraderat MABS v3-servern till UR1. Alla befintliga 32-bitars skydds agenter kommer att vara i inaktiverat tillstånd och schemalagda säkerhets kopieringar Miss lyckas med **agenten är inaktive rad** . Om du vill behålla säkerhetskopierade data för de här agenterna kan du stoppa skyddet med alternativet Kvarhåll data. Annars kan skydds agenten tas bort.

>[!NOTE]
>Granska den [uppdaterade skydds matrisen](./backup-mabs-protection-matrix.md) för att lära dig om de arbets belastningar som stöds för skydd med Mabs ur 1.

## <a name="whats-new-in-mabs-v3-rtm"></a>Vad är nytt i MABS v3 RTM

Microsoft Azure Backup Server version 3 (MABS v3) innehåller viktiga fel korrigeringar, Windows Server 2019-stöd, SQL 2017-support och andra funktioner och förbättringar. Om du vill visa en lista över program fel som har åtgärd ATS och Installationsinstruktioner för MABS v3, se KB-artikel [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3).

Följande funktioner ingår i MABS v3:

### <a name="volume-to-volume-migration"></a>Volym-till-volym-migrering

Med Modern Backup Storage (MB) i MABS v2 presenterade vi arbets belastnings medveten lagring, där du konfigurerar vissa arbets belastningar som ska säkerhets kopie ras till specifika lagrings enheter, baserat på lagrings egenskaper. Efter konfigurationen kan du dock behöva flytta säkerhets kopior av vissa data källor till annan lagrings plats för optimerad resursutnyttjande. MABS v3 ger dig möjlighet att migrera dina säkerhets kopior och konfigurera dem så att de lagras på en annan volym i [tre steg](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842).

### <a name="prevent-unexpected-data-loss"></a>Förhindra oväntad data förlust

I företag hanteras MABS av ett team av administratörer. Även om det finns rikt linjer för lagring som ska användas för säkerhets kopieringar kan en felaktig volym ges till MABS som säkerhets kopierings lagring leda till förlust av kritiska data. Med MABS v3 kan du förhindra sådana scenarier genom att konfigurera volymerna som de som inte är tillgängliga för lagring med hjälp av [dessa PowerShell-cmdletar](./backup-mabs-add-storage.md).

### <a name="custom-size-allocation"></a>Allokering av anpassad storlek

Modern Backup Storage (MB) förbrukar lagringen tunt, som och vid behov. För att göra det, beräknar MABS storleken på de data som säkerhets kopie ras när den har kon figurer ATS för skydd. Men om många filer och mappar säkerhets kopie ras tillsammans, som i fallet med en fil server, kan storleks beräkningen ta lång tid. Med MABS v3 kan du konfigurera MABS för att godkänna volymens storlek som standard, i stället för att beräkna storleken på varje fil, vilket sparar tid.

### <a name="optimized-cc-for-rct-vms"></a>Optimerad CC för virtuella RCT-datorer

MABS använder RCT (den interna ändrings spårningen i Hyper-V), vilket minskar behovet av konsekvens kontroller i scenarier som VM kraschar. RCT ger bättre flexibilitet än den ändringsspårning som tillhandahålls av säkerhetskopiering med VSS-ögonblicksbild. MABS v3 optimerar användningen av nätverks-och lagrings utrymme ytterligare genom att endast överföra ändrade data under en konsekvens kontroll.

### <a name="support-to-tls-12"></a>Stöd till TLS 1,2

TLS 1,2 är det säkra sättet för kommunikation som föreslås av Microsoft med förstklassig kryptering. MABS stöder nu TLS 1,2-kommunikation mellan MABS och skyddade servrar, för certifikatbaserad autentisering och för moln säkerhets kopieringar.

### <a name="vmware-vm-protection-support"></a>Stöd för VMware VM-skydd

VM-säkerhetskopiering för virtuella VMware-datorer stöds nu för produktions distributioner. MABS v3 erbjuder följande för VMware VM-skydd:

* Stöd för vCenter och ESXi 6,5, tillsammans med stöd för 5,5 och 6,0.
* Automatiskt skydd av virtuella VMware-datorer till molnet. Om nya virtuella VMware-datorer läggs till i en skyddad mapp är de automatiskt skyddade till disk och moln.
* Förbättringar av återställnings effektivitet för alternativ plats återställning i VMware.

### <a name="sql-2017-support"></a>Stöd för SQL 2017

MABS v3 kan installeras med SQL 2017 som MABS-databas. Du kan uppgradera SQL-servern från SQL 2016 till SQL 2017 eller installera den på ett nytt. Du kan också säkerhetskopiera SQL 2017-arbetsbelastningar både i klustrade och icke-klustrade miljöer med MABS v3.

### <a name="windows-server-2019-support"></a>Stöd för Windows Server 2019

MABS v3 kan installeras på Windows Server 2019. Om du vill använda MABS v3 med WS2019 kan du antingen uppgradera operativ systemet till WS2019 innan du installerar/uppgraderar till MABS v3 eller så kan du uppgradera operativ systemet när du har installerat/uppgraderat v3 på WS2016.

MABS v3 är en fullständig version och kan installeras direkt på Windows Server 2016, Windows Server 2019 eller kan uppgraderas från MABS v2. Innan du uppgraderar till eller installerar backup server v3 bör du läsa om installations kraven.
Mer information om installations-och uppgraderings stegen för MABS finns [här](./backup-azure-microsoft-azure-backup.md#software-package).

> [!NOTE]
>
> MABS har samma kodbas som System Center-Data Protection Manager. MABS v3 motsvarar Data Protection Manager 1807. MABS v3-UR1 motsvarar Data Protection Manager 2019 UR1.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du förbereder servern eller börjar skydda en arbets belastning:

* [Förbered arbets belastningar för säkerhets kopierings Server](backup-azure-microsoft-azure-backup.md)
* [Säkerhetskopiera en VMware-server med hjälp av Backup Server](backup-azure-backup-server-vmware.md)
* [Säkerhetskopiera SQL Server med hjälp av Backup Server](backup-azure-sql-mabs.md)
* [Använd Modern Backup Storage med säkerhets kopierings Server](backup-mabs-add-storage.md)
