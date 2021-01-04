---
title: Säkerhets kopiering och återställning av SQL Server på virtuella Azure-datorer | Microsoft Docs
description: Beskriver säkerhets kopierings-och återställnings överväganden för SQL Server databaser som körs på Azure Virtual Machines.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: 2fcba81bcd20db321d791fcda589f40fb0699702
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733081"
---
# <a name="backup-and-restore-for-sql-server-on-azure-vms"></a>Säkerhets kopiering och återställning av SQL Server på virtuella Azure-datorer
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här artikeln innehåller rikt linjer för säkerhets kopierings-och återställnings alternativ som är tillgängliga för SQL Server som körs på en virtuell Windows-dator i Azure. Azure Storage behåller tre kopior av varje Azure VM-disk för att garantera skydd mot data förlust eller fysiska data skador. Till skillnad från SQL Server lokalt behöver du alltså inte fokusera på maskin varu problem. Du bör dock fortfarande säkerhetskopiera SQL Server-databaser för att skydda mot program-eller användar fel, till exempel oavsiktliga data infogningar eller borttagningar. I den här situationen är det viktigt att kunna återställa till en viss tidpunkt.

Den första delen av den här artikeln ger en översikt över tillgängliga alternativ för säkerhets kopiering och återställning. Detta följs av avsnitt som innehåller mer information om varje strategi.

## <a name="backup-and-restore-options"></a>Alternativ för säkerhets kopiering och återställning

Följande tabell innehåller information om olika alternativ för säkerhets kopiering och återställning av SQL Server på virtuella Azure-datorer:

| Strategi | SQL-versioner | Beskrivning |
|---|---|---|
| [Automatisk säkerhetskopiering](#automated) | 2014<br/> 2016<br/> 2017 | Med automatisk säkerhets kopiering kan du schemalägga regelbundna säkerhets kopieringar för alla databaser på en SQL Server VM. Säkerhets kopior lagras i Azure Storage i upp till 30 dagar. Från och med SQL Server 2016 erbjuder automatisk säkerhets kopiering v2 ytterligare alternativ som att konfigurera manuell schemaläggning och frekvensen för fullständiga säkerhets kopior och logg säkerhets kopior. |
| [Azure Backup för virtuella SQL-datorer](#azbackup) | 2008<br/> 2012<br/> 2014<br/> 2016<br/> 2017<br/> 2019 | Azure Backup tillhandahåller en affärs kopierings funktion i företags klass för SQL Server på virtuella Azure-datorer. Med den här tjänsten kan du centralt hantera säkerhets kopior för flera servrar och tusentals databaser. Databaser kan återställas till en viss tidpunkt i portalen. Den erbjuder en anpassningsbar bevarande princip som kan upprätthålla säkerhets kopieringar i flera år. |
| [Manuell säkerhets kopiering](#manual) | Alla | Beroende på din version av SQL Server finns det olika metoder för att manuellt säkerhetskopiera och återställa SQL Server på virtuella Azure-datorer. I det här scenariot ansvarar du för hur dina databaser säkerhets kopie ras och lagrings platsen och hantering av dessa säkerhets kopior. |

I följande avsnitt beskrivs varje alternativ i detalj. Den sista delen av den här artikeln innehåller en sammanfattning i form av en funktions mat ris.

## <a name="automated-backup"></a><a id="automated"></a> Automatisk säkerhets kopiering

Automatisk säkerhets kopiering tillhandahåller en automatisk säkerhets kopierings tjänst för SQL Server Standard-och Enterprise-versioner som körs på en virtuell Windows-dator i Azure. Den här tjänsten tillhandahålls av [SQL Server IaaS agent Extension](sql-server-iaas-agent-extension-automate-management.md), som installeras automatiskt på SQL Server avbildningar av virtuella Windows-datorer i Azure Portal.

Alla databaser säkerhets kopie ras till ett Azure Storage-konto som du konfigurerar. Säkerhets kopieringar kan krypteras och bevaras i upp till 30 dagar.

SQL Server 2016 och högre virtuella datorer erbjuder fler anpassnings alternativ med automatisk säkerhets kopiering v2. Följande förbättringar är:

- Säkerhets kopior av system databasen
- Schemalagt schema och tids period för manuell säkerhets kopiering
- Fullständig säkerhets kopierings frekvens för filer och loggfiler

Om du vill återställa en databas måste du hitta de nödvändiga säkerhets kopiorna i lagrings kontot och utföra en återställning på den virtuella SQL-datorn med hjälp av SQL Server Management Studio (SSMS) eller Transact-SQL-kommandon.

Mer information om hur du konfigurerar automatisk säkerhets kopiering för virtuella SQL-datorer finns i någon av följande artiklar:

- **SQL Server 2016/2017**: [Automatisk säkerhets kopiering v2 för Azure Virtual Machines](automated-backup.md)
- **SQL Server 2014**: [Automatisk säkerhets kopiering för SQL Server 2014 Virtual Machines](automated-backup-sql-2014.md)

## <a name="azure-backup-for-sql-vms"></a><a id="azbackup"></a> Azure Backup för virtuella SQL-datorer

[Azure Backup](../../../backup/index.yml) tillhandahåller en affärs kopierings funktion i företags klass för SQL Server på virtuella Azure-datorer. Alla säkerhets kopior lagras och hanteras i ett Recovery Services valv. Det finns flera fördelar som den här lösningen ger, särskilt för företag:

- **Ingen säkerhets kopia av infrastrukturen**: du behöver inte hantera säkerhets kopierings servrar eller lagrings platser.
- **Skala**: skydda flera virtuella SQL-datorer och tusentals databaser.
- **Betala per** användning: den här funktionen är en separat tjänst som tillhandahålls av Azure Backup, men som med alla Azure-tjänster betalar du bara för det du använder.
- **Central hantering och övervakning: Central hantering** av alla dina säkerhets kopior, inklusive andra arbets belastningar som Azure Backup stöder, från en enda instrument panel i Azure.
- **Princip driven säkerhets kopiering och kvarhållning**: skapa standard principer för säkerhets kopiering för regelbundna säkerhets kopieringar. Upprätta bevarande principer för att upprätthålla säkerhets kopieringar i flera år.
- **Stöd för SQL Always on**: identifiera och skydda en SQL Server alltid på konfigurationen och respektera inställningarna för säkerhets kopiering av tillgänglighets grupp för säkerhets kopiering.
- **15 minuters återställnings punkt mål (återställnings punkt mål)**: Konfigurera säkerhets kopior av SQL-transaktionsloggar upp till var 15: e
- **Tidpunkt för återställning**: Använd portalen för att återställa databaser till en viss tidpunkt utan att manuellt återställa flera fullständiga, differentiella och logg säkerhets kopior.
- **Sammanställda e-postaviseringar för haverier**: Konfigurera konsoliderade e-postaviseringar för eventuella problem.
- **Rollbaserad åtkomst kontroll i Azure**: avgör vem som kan hantera säkerhets kopierings-och återställnings åtgärder via portalen.

För en snabb översikt över hur det fungerar tillsammans med en demo, se följande videoklipp:

> [!VIDEO https://www.youtube.com/embed/wmbANpHos_E]

Den här Azure Backup-lösningen för virtuella SQL-datorer är allmänt tillgänglig. Mer information finns i [säkerhetskopiera SQL Server Database till Azure](../../../backup/backup-azure-sql-database.md).

## <a name="manual-backup"></a><a id="manual"></a> Manuell säkerhets kopiering

Om du vill hantera säkerhets kopierings-och återställnings åtgärder manuellt på dina virtuella SQL-datorer finns det flera alternativ beroende på vilken version av SQL Server du använder. En översikt över säkerhets kopiering och återställning finns i någon av följande artiklar baserat på din version av SQL Server:

- [Säkerhets kopiering och återställning för SQL Server 2016 och senare](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [Säkerhets kopiering och återställning för SQL Server 2014](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases?viewFallbackFrom=sql-server-2014)
- [Säkerhets kopiering och återställning för SQL Server 2012](/previous-versions/sql/sql-server-2012/ms187048(v=sql.110))
- [Säkerhets kopiering och återställning av SQL Server SQL Server 2008 R2](/previous-versions/sql/sql-server-2008-r2/ms187048(v=sql.105))
- [Säkerhets kopiering och återställning för SQL Server 2008](/previous-versions/sql/sql-server-2008/ms187048(v=sql.100))

I följande avsnitt beskrivs flera manuella alternativ för säkerhets kopiering och återställning i detalj.

### <a name="backup-to-attached-disks"></a>Säkerhetskopiera till anslutna diskar

För SQL Server på virtuella Azure-datorer kan du använda interna säkerhets kopierings-och återställnings tekniker med anslutna diskar på den virtuella datorn för att säkerhetskopiera filernas mål. Det finns dock en gräns för antalet diskar som du kan ansluta till en virtuell Azure-dator, baserat på storleken på [den virtuella datorn](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Det finns också utrymme för disk hantering att överväga.

Ett exempel på hur du manuellt skapar en fullständig säkerhets kopia av databasen med SQL Server Management Studio (SSMS) eller Transact-SQL finns i [skapa en fullständig säkerhets kopia av databasen](/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server).

### <a name="backup-to-url"></a>Säkerhetskopiera till URL

Från och med SQL Server 2012 SP1 CU2 kan du säkerhetskopiera och återställa direkt till Microsoft Azure Blob Storage, som även kallas säkerhets kopiering till URL. SQL Server 2016 introducerade även följande förbättringar för den här funktionen:

| 2016-förbättringar | Information |
| --- | --- |
| **Striping** |När du säkerhetskopierar till Microsoft Azure Blob Storage stöder SQL Server 2016 säkerhets kopiering till flera blobbar för att aktivera säkerhets kopiering av stora databaser, upp till högst 12,8 TB. |
| **Säkerhets kopiering av ögonblicks bild** |Genom att använda Azure-ögonblicksbilder ger SQL Server File-Snapshot säkerhets kopieringen nästan momentan säkerhets kopiering och snabb återställning av databasfiler som lagras med Azure Blob Storage-tjänsten. Med den här funktionen kan du förenkla säkerhets kopierings-och återställnings principerna. Säkerhets kopiering av fil-ögonblicks bilder stöder även återställning av tidpunkter. Mer information finns i [ögonblicks bilder av säkerhets kopior för databasfiler i Azure](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

Mer information finns i följande artiklar som baseras på din version av SQL Server:

- **SQL Server 2016/2017**: [SQL Server säkerhets kopiering till URL](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [SQL Server 2014 säkerhets kopiering till URL](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service?viewFallbackFrom=sql-server-2014)
- **SQL Server 2012**: [SQL Server 2012 säkerhets kopiering till URL](/previous-versions/sql/sql-server-2012/jj919148(v=sql.110))

### <a name="managed-backup"></a>Hanterad säkerhetskopiering

Från och med SQL Server 2014 automatiserar hanterad säkerhets kopiering skapandet av säkerhets kopieringar till Azure Storage. Hanterad säkerhets kopiering i bakgrunden använder funktionen säkerhetskopiera till URL som beskrivs i föregående avsnitt i den här artikeln. Hanterad säkerhets kopiering är också den underliggande funktionen som stöder SQL Server VM automatisk säkerhets kopierings tjänst.

Från och med SQL Server 2016 har Managed backup ytterligare alternativ för schemaläggning, säkerhets kopiering av system databasen och fullständig säkerhets kopierings frekvens.

Mer information finns i någon av följande artiklar baserat på din version av SQL Server:

- [Hanterad säkerhets kopiering till Microsoft Azure för SQL Server 2016 och senare](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Hanterad säkerhets kopiering till Microsoft Azure för SQL Server 2014](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?viewFallbackFrom=sql-server-2014)

## <a name="decision-matrix"></a>Besluts mat ris

I följande tabell sammanfattas funktionerna för varje säkerhets kopierings-och återställnings alternativ för SQL Server virtuella datorer i Azure.

| Alternativ | Automatisk säkerhetskopiering | Azure Backup för SQL | Manuell säkerhets kopiering |
|---|---|---|---|
| Kräver ytterligare Azure-tjänst |   | ![Grön bock.](./media/backup-restore/yes.png) |   |
| Konfigurera säkerhets kopierings princip i Azure Portal | ![Grön bock.](./media/backup-restore/yes.png) | ![Grön bock.](./media/backup-restore/yes.png) |   |
| Återställ databaser i Azure Portal |   | ![Grön bock.](./media/backup-restore/yes.png) |   |
| Hantera flera servrar på en instrument panel |   | ![Grön bock.](./media/backup-restore/yes.png) |   |
| Återställning från tidpunkt | ![Grön bock.](./media/backup-restore/yes.png) | ![Grön bock.](./media/backup-restore/yes.png) | ![Grön bock.](./media/backup-restore/yes.png) |
| 15-minuters återställnings punkt mål (återställnings punkt mål) | ![Grön bock.](./media/backup-restore/yes.png) | ![Grön bock.](./media/backup-restore/yes.png) | ![Grön bock.](./media/backup-restore/yes.png) |
| Bevarande princip för kortsiktig säkerhets kopiering (dagar) | ![Grön bock.](./media/backup-restore/yes.png) | ![Grön bock.](./media/backup-restore/yes.png) |   |
| Långsiktig bevarande princip för säkerhets kopior (månader, år) |   | ![Grön bock.](./media/backup-restore/yes.png) |   |
| Inbyggt stöd för SQL Server Always on |   | ![Grön bock.](./media/backup-restore/yes.png) |   |
| Säkerhetskopiera till Azure Storage konto (n) | ![Grön bock.](./media/backup-restore/yes.png)Autokorrigering | ![Grön bock.](./media/backup-restore/yes.png)Autokorrigering | ![Grön bock.](./media/backup-restore/yes.png)(kund hanterad) |
| Hantering av lagrings-och säkerhets kopierings filer | | ![Grön bock.](./media/backup-restore/yes.png) |  |
| Säkerhetskopiera till anslutna diskar på den virtuella datorn |   |   | ![Grön bock.](./media/backup-restore/yes.png) |
| Centrala anpassningsbara säkerhets kopierings rapporter |   | ![Grön bock.](./media/backup-restore/yes.png) |   |
| Sammanställda e-postaviseringar för problem |   | ![Grön bock.](./media/backup-restore/yes.png) |   |
| Anpassa övervakning baserat på Azure Monitor loggar |   | ![Grön bock.](./media/backup-restore/yes.png) |   |
| Övervaka säkerhets kopierings jobb med SSMS-eller Transact-SQL-skript | ![Grön bock.](./media/backup-restore/yes.png) | ![Grön bock.](./media/backup-restore/yes.png) | ![Grön bock.](./media/backup-restore/yes.png) |
| Återställa databaser med SSMS-eller Transact-SQL-skript | ![Grön bock.](./media/backup-restore/yes.png) |   | ![Grön bock.](./media/backup-restore/yes.png) |

## <a name="next-steps"></a>Nästa steg

Om du planerar distributionen av SQL Server på den virtuella Azure-datorn kan du hitta etablerings vägledning i följande guide: [så här etablerar du en virtuell Windows SQL Server-dator i Azure Portal](create-sql-vm-portal.md).

Även om säkerhets kopiering och återställning kan användas för att migrera dina data, finns det potentiellt enklare sökvägar för datamigrering till SQL Server på den virtuella datorn. En fullständig beskrivning av alternativen och rekommendationerna för migrering finns i [Migrera en databas till SQL Server på den virtuella Azure-datorn](migrate-to-vm-from-sql-server.md).
