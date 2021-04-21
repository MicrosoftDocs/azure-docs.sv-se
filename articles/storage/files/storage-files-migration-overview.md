---
title: Migrera till Azure-filresurser
description: Lär dig mer om migreringar till Azure-filresurser och hitta din migreringsguide.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: a6335d90625f860984ccbfd224955a97a32b731f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785225"
---
# <a name="migrate-to-azure-file-shares"></a>Migrera till Azure-filresurser

Den här artikeln beskriver de grundläggande aspekterna av en migrering till Azure-filresurser.

Den här artikeln innehåller grunderna för migrering och en tabell med migreringsguider. De här guiderna hjälper dig att flytta dina filer till Azure-filresurser. Guiderna är ordnade baserat på var dina data finns och vilken distributionsmodell (endast moln eller hybrid) du flyttar till.

## <a name="migration-basics"></a>Grundläggande om migrering

Azure har flera tillgängliga typer av molnlagring. En grundläggande aspekt av filmigrering till Azure är att avgöra vilket Azure-lagringsalternativ som är rätt för dina data.

[Azure-filresurser](storage-files-introduction.md) är lämpliga för allmänna fildata. Dessa data innehåller allt du använder en lokal SMB- eller NFS-resurs för. Med [Azure File Sync](../file-sync/file-sync-planning.md)kan du cachelagra innehållet i flera Azure-filresurser på servrar som kör Windows Server lokalt.

För en app som för närvarande körs på en lokal server kan det vara ett bra alternativ att lagra filer i en Azure-filresurs. Du kan flytta appen till Azure och använda Azure-filresurser som delad lagring. Azure [Disks](../../virtual-machines/managed-disks-overview.md) kan också användas i det här scenariot.

Vissa molnappar är inte beroende av SMB eller lokal dataåtkomst eller delad åtkomst. För dessa appar är objektlagring som [Azure-blobar](../blobs/storage-blobs-overview.md) ofta det bästa valet.

Nyckeln i migreringen är att samla in alla tillämpliga filåtergivningar när du flyttar dina filer från deras aktuella lagringsplats till Azure. Hur bra Azure Storage-alternativet stöder och hur mycket ditt scenario kräver hjälper dig också att välja rätt Azure-lagring. Allmänna fildata beror traditionellt på filmetadata. Appdata kanske inte gör det.

Här är de två grundläggande komponenterna i en fil:

- **Dataström:** Dataströmmen för en fil lagrar filinnehållet.
- **Filmetadata:** Filmetadata har dessa delkomponenter:
   * Filattribut som skrivskyddade
   * Filbehörigheter, som kan kallas *NTFS-behörigheter eller* *fil- och mapp-ACL:er*
   * Tidsstämplar, särskilt skapande och senast ändrade tidsstämplar
   * En alternativ dataström, som är ett utrymme för att lagra större mängder icke-standardegenskaper

Filåtergivning i en migrering kan definieras som möjligheten att:

- Lagra all tillämplig filinformation på källan.
- Överföra filer med migreringsverktyget.
- Lagra filer i mållagringen för migreringen.

För att säkerställa att migreringen går smidigt kan du [identifiera det bästa](#migration-toolbox) kopieringsverktyget för dina behov och matcha ett lagringsmål mot din källa.

Med den tidigare informationen i beräkningen kan du se att mållagringen för allmänna filer i Azure är [Azure-filresurser](storage-files-introduction.md).

Till skillnad från objektlagring i Azure-blobar kan en Azure-filresurs lagra filmetadata inbyggt. Azure-filresurser bevarar även fil- och mapphierarkin, attributen och behörigheterna. NTFS-behörigheter kan lagras på filer och mappar eftersom de är lokala.

En användare av Active Directory, som är deras lokala domänkontrollant, har inbyggt åtkomst till en Azure-filresurs. Det kan även en användare av Azure Active Directory Domain Services (Azure AD DS). Var och en använder sin aktuella identitet för att få åtkomst baserat på resursbehörigheter och fil- och mapp-ACL:er. Det här beteendet liknar en användare som ansluter till en lokal filresurs.

Den alternativa dataströmmen är den primära aspekten av filåtergivning som för närvarande inte kan lagras på en fil i en Azure-filresurs. Den bevaras lokalt när Azure File Sync används.

Läs mer om [Azure AD-autentisering](storage-files-identity-auth-active-directory-enable.md) Azure AD DS [autentisering för](storage-files-identity-auth-active-directory-domain-service-enable.md) Azure-filresurser.

## <a name="migration-guides"></a>Migreringsguider

I följande tabell visas detaljerade migreringsguider.

Så här använder du tabellen:

1. Leta upp raden för det källsystem som dina filer för närvarande lagras på.

1. Välj något av följande mål:

   - En hybriddistribution som använder Azure File Sync för att cachelagra innehållet i lokala Azure-filresurser
   - Azure-filresurser i molnet

   Välj den målkolumn som matchar ditt val.

1. I skärningspunkten för källa och mål visar en tabellcell tillgängliga migreringsscenarier. Välj en för att länka direkt till den detaljerade migreringsguiden.

Ett scenario utan en länk har ännu inte någon publicerad migreringsguide. Kontrollera den här tabellen ibland för uppdateringar. Nya guider publiceras när de är tillgängliga.

| Källa | Mål: </br>Hybriddistribution | Mål: </br>Distribution endast i molnet |
|:---|:--|:--|
| | Kombination av verktyg:| Kombination av verktyg: |
| Windows Server 2012 R2 och senare | <ul><li>[Azure File Sync](../file-sync/file-sync-deployment-guide.md)</li><li>[Azure File Sync och Azure DataBox](../file-sync/file-sync-offline-data-transfer.md)</li></ul> | <ul><li>Via RoboCopy till en monterad Azure-filresurs</li><li>Via Azure File Sync</li></ul> |
| Windows Server 2012 och tidigare | <ul><li>Via DataBox och Azure File Sync till det senaste serveroperativsystemet</li><li>Via Storage Migration Service till den senaste servern med Azure File Sync och överför sedan</li></ul> | <ul><li>Via Storage Migration Service till den senaste servern med Azure File Sync</li><li>Via RoboCopy till en monterad Azure-filresurs</li></ul> |
| Nätverkskopplad lagring (NAS) | <ul><li>[Via Azure File Sync uppladdning](storage-files-migration-nas-hybrid.md)</li><li>[Via DataBox + Azure File Sync](storage-files-migration-nas-hybrid-databox.md)</li></ul> | <ul><li>[Via DataBox](storage-files-migration-nas-cloud-databox.md)</li><li>Via RoboCopy till en monterad Azure-filresurs</li></ul> |
| Linux/Samba | <ul><li>[Azure File Sync och RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>Via RoboCopy till en monterad Azure-filresurs</li></ul> |
| Microsoft Azure StorSimple Cloud Appliance 8100 eller StorSimple Cloud Appliance 8600 | <ul><li>[Via molntjänst för dedikerad datamigrering](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple Cloud Appliance 1200 | <ul><li>[Via Azure File Sync](storage-files-migration-storsimple-1200.md)</li></ul> | |

## <a name="migration-toolbox"></a>Verktygslåda för migrering

### <a name="file-copy-tools"></a>Filkopieringsverktyg

Det finns flera filkopieringsverktyg från Microsoft och andra. Om du vill välja rätt verktyg för ditt migreringsscenario måste du tänka på dessa grundläggande frågor:

* Stöder verktyget käll- och målplatserna för filkopian?

* Stöder verktyget din nätverkssökväg eller tillgängliga protokoll (till exempel REST, SMB eller NFS) mellan käll- och mållagringsplatserna?

* Bevarar verktyget nödvändig filåtergivning som stöds av käll- och målplatserna?

    I vissa fall har mållagringen inte stöd för samma återgivning som din källa. Om mållagringen är tillräcklig för dina behov måste verktyget endast matcha målets filåtergivningsfunktioner.

* Har verktyget funktioner som gör att det passar din migreringsstrategi?

    Överväg till exempel om verktyget gör att du kan minimera stilleståndstiden.
    
    När ett verktyg stöder ett alternativ för att spegla en källa till ett mål kan du ofta köra den flera gånger på samma källa och mål medan källan är tillgänglig.

    Första gången du kör verktyget kopieras stora mängder data. Den här första körningen kan vara en stund. Det tar ofta längre tid än du vill för att ta datakällan offline för dina affärsprocesser.

    Genom att spegla en källa till ett mål (som **med robocopy /ROB**) kan du köra verktyget igen på samma källa och mål. Körningen går mycket snabbare eftersom den bara behöver transport av källändringar som inträffar efter den föregående körningen. Om du kör ett kopieringsverktyg på det här sättet kan stilleståndstiden minskas avsevärt.

Följande tabell klassificerar Microsoft-verktyg och deras aktuella lämplighet för Azure-filresurser:

| Rekommenderas | Verktyg | Stöd för Azure-filresurser | Bevara filåtergivning |
| :-: | :-- | :---- | :---- |
|![Ja, rekommenderas](media/storage-files-migration-overview/circle-green-checkmark.png)| Robocopy | Stöds. Azure-filresurser kan monteras som nätverksenheter. | Fullständig återgivning.* |
|![Ja, rekommenderas](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure File Sync | Inbyggt i Azure-filresurser. | Fullständig återgivning.* |
|![Ja, rekommenderas](media/storage-files-migration-overview/circle-green-checkmark.png)| Storage Migration Service | Indirekt stöd. Azure-filresurser kan monteras som nätverksenheter på SMS-målservrar. | Fullständig återgivning.* |
|![Ja, rekommenderas](media/storage-files-migration-overview/circle-green-checkmark.png)| AzCopy </br>version 10.6 | Stöds. | Stöder inte kopiering av källrots-ACL, annars fullständig återgivning.* </br>[Lär dig hur du använder AzCopy med Azure-filresurser](../common/storage-use-azcopy-files.md) |
|![Ja, rekommenderas](media/storage-files-migration-overview/circle-green-checkmark.png)| Data Box | Stöds. | DataBox har fullständigt stöd för metadata. |
|![Rekommenderas inte fullt ut](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Azure Storage Explorer </br>version 1.14 | Stöds. | Kopierar inte ACL:er. Stöder tidsstämplar.  |
|![Rekommenderas inte](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | Stöds. | Kopierar inte metadata. |
|||||

*\* Fullständig återgivning: uppfyller eller överskrider funktionerna i Azure-filresursen.*

### <a name="migration-helper-tools"></a>Verktyg för migreringshjälp

I det här avsnittet beskrivs verktyg som hjälper dig att planera och köra migreringar.

#### <a name="robocopy-from-microsoft-corporation"></a>RoboCopy från Microsoft Corporation

RoboCopy är ett av de verktyg som är mest tillämpligt för filmigrering. Den ingår i Windows. [Huvuddokumentationen för RoboCopy](/windows-server/administration/windows-commands/robocopy) är en användbar resurs för det här verktygets många alternativ.

#### <a name="treesize-from-jam-software-gmbh"></a>TreeSize från JAM Software GmbH

Azure File Sync skalas främst med antalet objekt (filer och mappar) och inte med den totala lagringsmängden. Med verktyget TreeSize kan du fastställa antalet objekt på Dina Windows Server-volymer.

Du kan använda verktyget för att skapa ett perspektiv före en [Azure File Sync distribution](../file-sync/file-sync-deployment-guide.md). Du kan också använda den när molnnivåindelad lagring används efter distributionen. I det scenariot ser du antalet objekt och vilka kataloger som använder serverns cacheminne mest.

Den testade versionen av verktyget är version 4.4.1. Det är kompatibelt med filer på molnnivå. Verktyget kommer inte att orsaka återkallande av nivåindelade filer under normal drift.

## <a name="next-steps"></a>Nästa steg

1. Skapa en plan för vilken distribution av Azure-filresurser (endast moln eller hybrid) du vill ha.
1. Granska listan över tillgängliga migreringsguider för att hitta den detaljerade guide som matchar din källa och distribution av Azure-filresurser.

Mer information om de Azure Files som nämns i den här artikeln:

* [Översikt över Azure-filresurs](storage-files-introduction.md)
* [Planera för distribution av Azure File Sync](../file-sync/file-sync-planning.md)
* [Azure File Sync: Molnnivåindelad](../file-sync/file-sync-cloud-tiering-overview.md)