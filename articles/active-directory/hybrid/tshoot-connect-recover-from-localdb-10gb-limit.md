---
title: 'Azure AD Connect: återställa från LocalDB 10 GB Limit-problem | Microsoft Docs'
description: I det här avsnittet beskrivs hur du återställer Azure AD Connect-synkroniseringstjänsten när den påträffar LocalDB 10 GB gräns problem.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 41d081af-ed89-4e17-be34-14f7e80ae358
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/17/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e10aa5d96722b414d7384ceb81f393575d57e2a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101688781"
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Azure AD Connect: Så här återställer du från LocalDB med en gräns på 10 GB
Azure AD Connect kräver en SQL Server-databas för att lagra identitetsdata. Du kan antingen använda SQL Server 2012 Express LocalDB som är installerat som standard med Azure AD Connect eller använda din egen fullständiga SQL. SQL Server Express har en storleksgräns på 10 GB. När du använder LocalDB och gränsen har uppnåtts kan synkroniseringstjänsten för Azure AD Connect inte längre starta eller synkronisera korrekt. Den här artikeln innehåller återställnings stegen.

## <a name="symptoms"></a>Symtom
Det finns två vanliga symtom:

* Azure AD Connect synkroniseringstjänst **körs** , men det går inte att synkronisera med fel meddelandet *"stoppad-Database-disk-full"* .

* **Det gick inte att starta tjänsten för** Azure AD Connect-synkronisering. När du försöker starta tjänsten Miss lyckas den med händelse 6323 och fel meddelande *"servern påträffade ett fel eftersom SQL Server har slut på disk utrymme."*

## <a name="short-term-recovery-steps"></a>Kortsiktiga återställnings steg
Det här avsnittet innehåller stegen för att frigöra databas utrymme som krävs för att Azure AD Connect-synkroniseringstjänsten ska återupptas. Stegen är:
1. [Fastställ synkroniseringsstatus för tjänsten](#determine-the-synchronization-service-status)
2. [Krymp databasen](#shrink-the-database)
3. [Ta bort körnings historik data](#delete-run-history-data)
4. [Förkorta kvarhållningsperioden för körning av historik data](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>Fastställ synkroniseringsstatus för tjänsten
Kontrol lera först om synkroniseringstjänsten fortfarande körs eller inte:

1. Logga in på Azure AD Connect servern som administratör.

2. Gå till **tjänst kontroll hanteraren**.

3. Kontrol lera statusen för **Microsoft Azure AD Sync**.


4. Om den körs ska du inte stoppa eller starta om tjänsten. Hoppa över att [minska databas](#shrink-the-database) steget och gå till [data steget ta bort körnings historik](#delete-run-history-data) .

5. Försök starta tjänsten om den inte körs. Om tjänsten har startats kan du hoppa över [databas](#shrink-the-database) steget och gå till [data steget ta bort körnings historik](#delete-run-history-data) . Annars fortsätter du med [att krympa databas](#shrink-the-database) steget.

### <a name="shrink-the-database"></a>Krymp databasen
Använd Shrink-åtgärden för att frigöra tillräckligt med DB-utrymme för att starta synkroniseringstjänsten. Den frigör DB-utrymme genom att ta bort blank steg i databasen. Det här steget är bästa ansträngning eftersom det inte garanterar att du alltid kan återställa utrymme. Läs mer om Krympnings åtgärden i den här artikeln [Krymp en databas](/sql/relational-databases/databases/shrink-a-database).

> [!IMPORTANT]
> Hoppa över det här steget om du kan få synkroniseringstjänsten att köras. Vi rekommenderar inte att du krymper SQL DB eftersom den kan leda till dåliga prestanda på grund av ökad fragmentering.

Namnet på databasen som skapades för Azure AD Connect är **ADSync**. Om du vill utföra en Krympnings åtgärd måste du logga in som sysadmin eller DBO för databasen. Under Azure AD Connect installationen beviljas följande konton sysadmin-rättigheter:
* Lokala administratörer
* Det användar konto som användes för att köra Azure AD Connect installationen.
* Det Sync Service-konto som används som kontext för Azure AD Connect-synkroniseringstjänsten.
* Den lokala gruppen ADSyncAdmins som skapades under installationen.

1. Säkerhetskopiera databasen genom att kopiera **AdSync. mdf** -och **ADSync_log. ldf** -filer som finns på `%ProgramFiles%\Microsoft Azure AD Sync\Data` en säker plats.

2. Starta en ny PowerShell-session.

3. Navigera till mapp `%ProgramFiles%\Microsoft SQL Server\110\Tools\Binn` .

4. Starta **SQLCMD** -verktyget genom att köra kommandot `./SQLCMD.EXE -S "(localdb)\.\ADSync" -U <Username> -P <Password>` med autentiseringsuppgifterna för en sysadmin eller databasen dbo.

5. För att krympa databasen går du till SQLCMD-prompten ( `1>` ) och anger `DBCC Shrinkdatabase(ADSync,1);` , följt av `GO` Nästa rad.

6. Försök att starta synkroniseringstjänsten igen om åtgärden lyckas. Om du kan starta synkroniseringstjänsten går du till steget [ta bort körnings historik data](#delete-run-history-data) . Annars kontaktar du supporten.

### <a name="delete-run-history-data"></a>Ta bort körnings historik data
Som standard behåller Azure AD Connect upp till sju dagar till att köra historik data. I det här steget tar vi bort körnings historik data för att frigöra databas utrymme så att Azure AD Connect Sync-tjänsten kan starta synkroniseringen igen.

1. Starta **Synchronization Service Manager** genom att gå till Start → synkroniseringstjänst.

2. Gå till fliken **åtgärder** .

3. Under **åtgärder** väljer du **Rensa körningar**...

4. Du kan antingen välja **Rensa alla körningar** eller **Rensa körningar före. \<date> ..** alternativet. Vi rekommenderar att du börjar genom att rensa körnings historik data som är äldre än två dagar. Om du fortsätter att köra fel i DB-storlek väljer du alternativet **Rensa alla körningar** .

### <a name="shorten-retention-period-for-run-history-data"></a>Förkorta kvarhållningsperioden för körning av historik data
Det här steget är att minska sannolikheten för att det ska gå att köra problem med 10 GB-gränsen efter flera cykler.

1. Öppna en ny PowerShell-session.

2. Kör `Get-ADSyncScheduler` och anteckna egenskapen PurgeRunHistoryInterval som anger den aktuella kvarhållningsperioden.

3. Kör `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00` för att ange kvarhållningsperioden till två dagar. Justera kvarhållningsperioden efter behov.

## <a name="long-term-solution--migrate-to-full-sql"></a>Långsiktig lösning – migrera till fullständig SQL
I allmänhet är problemet ett exempel på att databas storleken på 10 GB inte längre räcker för Azure AD Connect att synkronisera din lokala Active Directory till Azure AD. Vi rekommenderar att du växlar till att använda den fullständiga versionen av SQL Server. Du kan inte direkt ersätta en LocalDB i en befintlig Azure AD Connect-distribution med den fullständiga SQL-versionens databas. Istället måste du distribuera en ny Azure AD Connect-server med den fullständiga versionen av SQL. Vi rekommenderar att du gör en swingmigrering med den nya Azure AD Connect-servern (med SQL DB) distribuerad som en mellanlagringsserver bredvid den befintliga Azure AD Connect-servern (med LocalDB). 
* Instruktioner för hur du konfigurerar fjärr-SQL med Azure AD Connect finns i artikeln [Anpassad installation av Azure AD Connect](./how-to-connect-install-custom.md).
* I artikeln [Azure AD Connect: Uppgradera från en tidigare version till den senaste](./how-to-upgrade-previous-version.md#swing-migration) finns det information om swingmigrering för Azure AD Connect-uppgradering.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
