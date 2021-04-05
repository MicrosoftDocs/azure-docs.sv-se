---
title: Kända problem och begränsningar med online-migreringar till Azure SQL-hanterad instans
description: Läs om kända problem/migrerings begränsningar som är associerade med online-migreringar till Azure SQL-hanterad instans.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 46c5f5995c7a1d4eb074f6c1b25ecaad7e2da37e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98695548"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-managed-instance"></a>Kända problem/migrerings begränsningar med online-migreringar till Azure SQL-hanterad instans

Kända problem och begränsningar som är associerade med online-migrering från SQL Server till Azure SQL-hanterad instans beskrivs nedan.

> [!IMPORTANT]
> Migrering av SQL_variant data typer stöds inte för migrering av SQL Server Azure SQL Database.

## <a name="backup-requirements"></a>Säkerhets kopierings krav

- **Säkerhets kopior med kontroll Summa**

    Azure Database Migration Service använder säkerhets kopierings-och återställnings metoden för att migrera dina lokala databaser till SQL-hanterad instans. Azure Database Migration Service stöder endast säkerhets kopieringar som skapats med kontroll summa.

    [Aktivera eller inaktivera säkerhets kopiering av kontroll summor under säkerhets kopiering eller återställning (SQL Server)](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server).

    > [!NOTE]
    > Om du tar databas säkerhets kopior med komprimering, är kontroll summan ett standard beteende om det inte uttryckligen inaktive ras.

    Om du väljer att låta Azure Database Migration Service tas med offline-migrering, och om du väljer **att låta...**, kommer Azure Database migration service att ta säkerhets kopian av databasen med alternativet kontroll Summa aktiverat.

- **Säkerhetskopieringsmedia**

    Var noga med att ta varje säkerhets kopia på ett separat säkerhets kopierings medium (säkerhetskopieringsfiler). Azure Database Migration Service stöder inte säkerhets kopieringar som läggs till i en enda säkerhets kopia. Ta fullständig säkerhets kopiering och logg säkerhets kopior för att separera säkerhetskopieringsfiler.

## <a name="data-and-log-file-layout"></a>Layout för data och logg fil

- **Antal loggfiler**

    Azure Database Migration Service stöder inte databaser med flera loggfiler. Om du har flera loggfiler kan du krympa och organisera om dem i en enda transaktions logg fil. Eftersom du inte kan fjärrans luta till loggfiler som inte är tomma måste du säkerhetskopiera logg filen först.

## <a name="sql-server-features"></a>SQL Server funktioner

- **FileStream/FileTable**

    SQL-hanterad instans har för närvarande inte stöd för FileStream och FileTable. För arbets belastningar som är beroende av dessa funktioner rekommenderar vi att du väljer SQL-servrar som körs på virtuella Azure-datorer som Azure-mål.

- **InMemory-tabeller**

    InMemory OLTP är tillgängligt i Premium-och Affärskritisk-nivåerna för SQL-hanterad instans. Generell användning nivån har inte stöd för minnes intern OLTP.

## <a name="migration-resets"></a>Återställer migrering

- **Distributioner**

    SQL-hanterad instans är en PaaS-tjänst med automatisk uppdatering och versions uppdateringar. Vid migreringen av din SQL-hanterade instans hålls icke-kritiska uppdateringar i upp till 36 timmar. Efteråt (och för kritiska uppdateringar) återställs processen till ett fullständigt återställnings tillstånd om migreringen avbryts.

    Migrerings-start punkt kan bara anropas efter att den fullständiga säkerhets kopieringen har återställts och fångats upp med alla logg säkerhets kopior. Om migrering för produktion påverkas kan du kontakta [aliaset för Azure DMS-feedback](mailto:dmsfeedback@microsoft.com).

## <a name="smb-file-share-connectivity"></a>SMB fil resurs anslutning

Problem som ansluter till SMB-filresursen orsakas sannolikt av ett behörighets problem. 

Följ dessa steg om du vill testa SMB-fildelnings anslutning: 

1. Spara en säkerhets kopia till SMB-filresursen. 
1. Kontrol lera nätverks anslutningen mellan under nätet för Azure Database Migration Service och käll SQL Server. Det enklaste sättet att göra detta är att distribuera en SQL Server virtuell dator till DMS-undernätet och ansluta till käll SQL Server med hjälp av SQL Server Management Studio. 
1. Återställ sidhuvudet på käll SQL Server från säkerhets kopian på fil resursen: 

   ```sql
   RESTORE HEADERONLY   
   FROM DISK = N'\\<SMB file share path>\full.bak'
   ```

Om du inte kan ansluta till fil resursen konfigurerar du behörigheter med följande steg: 

1. Navigera till fil resursen med Utforskaren. 
1. Högerklicka på fil resursen och välj egenskaper. 
1. Välj fliken **delning** och välj **Avancerad delning**. 
1. Lägg till det Windows-konto som används för migrering och tilldela det fullständig behörighet. 
1. Lägg till SQL Server tjänst kontot och tilldela det fullständig behörighet. Kontrol lera **Konfigurationshanteraren för SQL Server** för SQL Server tjänst kontot om du inte är säker på vilket konto som används. 

   :::image type="content" source="media/known-issues-azure-sql-db-managed-instance-online/assign-fileshare-permissions.png" alt-text="Ge fullständig behörighet till de Windows-konton som används för migrering och för SQL Server tjänst kontot. ":::

