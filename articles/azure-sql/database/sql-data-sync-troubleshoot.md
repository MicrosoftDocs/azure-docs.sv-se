---
title: Felsöka SQL Data Sync
description: Lär dig att identifiera, felsöka och lösa vanliga problem med SQL Data Sync i Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync, sqldbrb=1
ms.devlang: ''
ms.topic: troubleshooting
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/20/2018
ms.openlocfilehash: 02eaec4c86c934e8d2638de1b60aa9267babf7a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92790176"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>Felsöka problem med SQL Data Sync
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Den här artikeln beskriver hur du felsöker kända problem med SQL Data Sync i Azure. Om det finns en lösning på problemet finns den här.

En översikt över SQL Data Sync finns i [synkronisera data i flera moln-och lokala databaser med SQL Data Sync i Azure](sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> SQL Data Sync stöder **inte** Azure SQL-hanterad instans för tillfället.

## <a name="sync-issues"></a>Synkroniseringsproblem

- [Det går inte att synkronisera i Portal gränssnittet för lokala databaser som är associerade med klient agenten](#sync-fails)

- [Min Sync-grupp fastnar i bearbetnings läget](#sync-stuck)

- [Jag ser felaktiga data i mina tabeller](#sync-baddata)

- [Jag ser inkonsekventa primär nyckel data efter en lyckad synkronisering](#sync-pkdata)

- [Jag ser en betydande försämring i prestandan](#sync-perf)

- [Jag ser det här meddelandet: "det går inte att infoga värdet NULL i kolumnen \<column> . Kolumnen tillåter inte null-värden. " Vad betyder detta, och hur kan jag åtgärda det?](#sync-nulls)

- [Hur hanterar data synkronisering cirkel referenser? Det vill säga när samma data har synkroniserats i flera Sync-grupper och håller på att ändras till följd av detta?](#sync-circ)

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a><a name="sync-fails"></a> Det går inte att synkronisera i Portal gränssnittet för lokala databaser som är associerade med klient agenten

Det går inte att synkronisera i SQL Data Sync portalens användar gränssnitt för lokala databaser som är associerade med klient agenten. På den lokala dator som kör-agenten visas system. IO. IOException-fel i händelse loggen. Felen säger att disken har otillräckligt med utrymme.

- **Orsak**. Det finns inte tillräckligt med utrymme på enheten.

- **Lösning**. Skapa mer utrymme på den enhet där katalogen% TEMP% finns.

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a><a name="sync-stuck"></a> Min Sync-grupp fastnar i bearbetnings läget

En Sync-grupp i SQL Data Sync har bearbetats under en längre tid. Den svarar inte på kommandot **Stop** och loggarna visar inga nya poster.

Något av följande villkor kan resultera i att en synkroniseringsresurs fastnar i bearbetnings tillståndet:

- **Orsak**. Klientagenten är offline

- **Lösning**. Se till att klientagenten är online och försök sedan igen.

- **Orsak**. Klientagenten är avinstallerad eller saknas.

- **Lösning**. Om klientagenten är avinstallerad eller saknas:

    1. Ta bort agentens XML-fil från installationsmappen för SQL Data Sync, om filen finns.
    1. Installera agenten på en lokal dator (det kan vara samma eller en annan dator). Skicka agentnyckeln som genereras i portalen för den agent som visas som offline.

- **Orsak**. SQL Data Sync-tjänsten har stoppats.

- **Lösning**. Starta om tjänsten SQL Data Sync.

    1. Sök efter **tjänster** på **Start** -menyn.
    1. I Sök resultaten väljer du **tjänster**.
    1. Hitta **SQL data syncs** tjänsten.
    1. Om tjänstens status är **stoppad** högerklickar du på tjänstens namn och väljer sedan **Starta**.

> [!NOTE]
> Om föregående information inte flyttar din synkroniserade grupp från bearbetnings tillståndet kan Microsoft Support återställa statusen för din Sync-grupp. Om du vill återställa statusen för din synkroniseringsstatus på [sidan Microsoft Q&en fråga för Azure SQL Database](/answers/topics/azure-sql-database.html)skapar du ett inlägg. I inlägget inkluderar du ditt prenumerations-ID och ID för Sync-gruppen för den grupp som måste återställas. En Microsoft Support-tekniker kommer att svara på ditt inlägg och kommer att meddela dig när statusen har återställts.

### <a name="i-see-erroneous-data-in-my-tables"></a><a name="sync-baddata"></a> Jag ser felaktiga data i mina tabeller

Om tabeller som har samma namn men som kommer från olika databas scheman ingår i en synkronisering visas felaktiga data i tabellerna efter synkroniseringen.

- **Orsak**. SQL Data Sync etablerings processen använder samma spårnings tabeller för tabeller som har samma namn men som finns i olika scheman. Därför återspeglas ändringar från båda tabellerna i samma spårnings tabell. Detta leder till felaktiga data ändringar under synkroniseringen.

- **Lösning**. Se till att namnen på tabellerna som ingår i en synkronisering skiljer sig, även om tabellerna tillhör olika scheman i en databas.

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a><a name="sync-pkdata"></a> Jag ser inkonsekventa primär nyckel data efter en lyckad synkronisering

En synkronisering rapporteras som lyckad och loggen visar inga misslyckade eller överhoppade rader, men du ser att primär nyckel data är inkonsekventa bland databaserna i Sync-gruppen.

- **Orsak**. Resultatet är avsiktligt. Ändringar i en primär nyckel kolumn resulterar i inkonsekventa data i raderna där primär nyckeln ändrades.

- **Lösning**. Förhindra det här problemet genom att se till att inga data i en primär nyckel kolumn ändras. Om du vill åtgärda det här problemet när det har inträffat tar du bort raden som innehåller inkonsekventa data från alla slut punkter i Sync-gruppen. Infoga sedan raden igen.

### <a name="i-see-a-significant-degradation-in-performance"></a><a name="sync-perf"></a> Jag ser en betydande försämring i prestandan

Prestandan försämras kraftigt, eventuellt till den punkt där du inte ens kan öppna användar gränssnittet för data synkronisering.

- **Orsak**. Den mest sannolika orsaken är en synkroniseringskonflikt. En synkroniseringskonflikt inträffar när en synkroniserad av Sync-grupp A utlöser en synkronisering med Sync Group B, som sedan utlöser en synkronisering med Sync Group A. Den faktiska situationen kan vara mer komplex och kan omfatta fler än två synkroniserade grupper i slingan. Problemet är att det finns en cirkulär utlösning av synkronisering som orsakas av Sync-grupper som överlappar varandra.

- **Lösning**. Den bästa korrigeringen är förebyggande. Se till att du inte har cirkel referenser i dina Sync-grupper. Alla rader som synkroniseras av en Sync-grupp kan inte synkroniseras av en annan Sync-grupp.

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a><a name="sync-nulls"></a> Jag ser det här meddelandet: "det går inte att infoga värdet NULL i kolumnen \<column> . Kolumnen tillåter inte null-värden. " Vad betyder detta, och hur kan jag åtgärda det? 
Det här fel meddelandet anger att ett av följande två problem har inträffat:
-  En tabell har ingen primär nyckel. Åtgärda problemet genom att lägga till en primär nyckel i alla tabeller som du synkroniserar.
-  Det finns en WHERE-sats i CREATE INDEX-instruktionen. Datasynkroniseringen hanterar inte det här tillståndet. Åtgärda problemet genom att ta bort WHERE-satsen eller manuellt göra ändringarna i alla databaser. 
 
### <a name="how-does-data-sync-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a><a name="sync-circ"></a> Hur hanterar data synkronisering cirkel referenser? Det vill säga när samma data har synkroniserats i flera Sync-grupper och håller på att ändras till följd av detta?
Datasynkronisering hanterar inte cirkel referenser. Se till att undvika dem. 

## <a name="client-agent-issues"></a>Problem med klient agent

Information om hur du felsöker problem med klient agenten finns i [Felsöka problem med Data Sync-agenten](sql-data-sync-agent-overview.md#agent-tshoot).

## <a name="setup-and-maintenance-issues"></a>Problem med installation och underhåll

- [Jag får meddelandet "disk disk utrymme"](#setup-space)

- [Jag kan inte ta bort min Sync-grupp](#setup-delete)

- [Det går inte att avregistrera en SQL Server databas](#setup-unreg)

- [Jag har inte behörighet att starta system tjänster](#setup-perms)

- [En databas har statusen inaktuell](#setup-date)

- [En Sync-grupp har statusen "inaktuell"](#setup-date2)

- [Det går inte att ta bort en Sync-grupp inom tre minuter från avinstallation eller stoppa agenten](#setup-delete2)

- [Vad händer när jag återställer en förlorad eller skadad databas?](#setup-restore)

### <a name="i-get-a-disk-out-of-space-message"></a><a name="setup-space"></a> Jag får meddelandet "disk disk utrymme"

- **Orsak**. Meddelandet "disk slut på utrymme" kan visas om överblivna filer behöver tas bort. Detta kan bero på antivirus program eller att filer är öppna när borttagnings åtgärder görs.

- **Lösning**. Ta bort de synkroniserade filer som finns i mappen% Temp% ( `del \*sync\* /s` ) manuellt. Ta sedan bort under katalogerna i mappen% Temp%.

> [!IMPORTANT]
> Ta inte bort några filer medan synkroniseringen pågår.

### <a name="i-cant-delete-my-sync-group"></a><a name="setup-delete"></a> Jag kan inte ta bort min Sync-grupp

Det gick inte att ta bort en synkroniseringsresurs. Något av följande scenarier kan resultera i att det inte går att ta bort en Sync-grupp:

- **Orsak**. Klientagenten är offline.

- **Lösning**. Se till att klient agenten är online och försök sedan igen.

- **Orsak**. Klientagenten är avinstallerad eller saknas.

- **Lösning**. Om klientagenten är avinstallerad eller saknas:  
    a. Ta bort agentens XML-fil från installationsmappen för SQL Data Sync, om filen finns.  
    b. Installera agenten på en lokal dator (det kan vara samma eller en annan dator). Skicka agentnyckeln som genereras i portalen för den agent som visas som offline.

- **Orsak**. En databas är offline.

- **Lösning**. Se till att dina databaser är online.

- **Orsak**. Sync-gruppen är etablering eller synkronisering.

- **Lösning**. Vänta tills etableringen eller synkroniseringsprocessen har slutförts och försök sedan att ta bort synkroniseringsresursen igen.

### <a name="i-cant-unregister-a-sql-server-database"></a><a name="setup-unreg"></a> Det går inte att avregistrera en SQL Server databas

- **Orsak**. Förmodligen försöker du avregistrera en databas som redan har tagits bort.

- **Lösning**. Om du vill avregistrera en SQL Server databas markerar du databasen och väljer sedan **Framtvinga borttagning**.

  Om den här åtgärden inte går att ta bort databasen från Sync-gruppen:

  1. Stoppa och starta sedan om värd tjänsten för klient agent:  
    a. Välj **Start**-menyn.  
    b. I rutan Sök anger du **Services. msc**.  
    c. I avsnittet **program** i rutan Sök Resultat dubbelklickar du på **tjänster**.  
    d. Högerklicka på tjänsten **SQL Data Sync** .  
    e. Om tjänsten körs stoppar du den.  
    f. Högerklicka på tjänsten och välj sedan **Starta**.  
    ex. Kontrol lera om databasen fortfarande är registrerad. Om den inte längre är registrerad är du klar. Annars fortsätter du med nästa steg.
  1. Öppna klient agent appen (SqlAzureDataSyncAgent).
  1. Välj **Redigera autentiseringsuppgifter** och ange sedan autentiseringsuppgifterna för databasen.
  1. Fortsätt med avregistreringen.

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a><a name="setup-perms"></a> Jag har inte behörighet att starta system tjänster

- **Orsak**. Det här felet uppstår i två situationer:
  -   Användar namnet och/eller lösen ordet är felaktigt.
  -   Det angivna användar kontot har inte behörighet att logga in som en tjänst.

- **Lösning**. Tilldela användar kontot inloggnings uppgifter för logga in som en tjänst:

  1. Gå till **Start**  >  **kontroll panelen**  >  **administrations verktyg**  >  **lokal**  >  **princip**  >  **användare Rights Management** på lokal säkerhets princip.
  1. Välj **Logga in som en tjänst**.
  1. Lägg till användar kontot i dialog rutan **Egenskaper** .
  1. Tryck på **Tillämpa** och välj sedan **OK**.
  1. Stäng alla fönster.

### <a name="a-database-has-an-out-of-date-status"></a><a name="setup-date"></a> En databas har statusen inaktuell

- **Orsak**. SQL Data Sync tar bort databaser som har varit offline från tjänsten i 45 dagar eller mer (som räknas från den tidpunkt då databasen gick offline). Om en databas är offline i 45 dagar eller mer och sedan blir online igen är dess status **inaktuell**.

- **Lösning**. Du kan undvika en **inaktuell** status genom att se till att ingen av dina databaser går offline i 45 dagar eller mer.

  Om statusen för en databas är **inaktuell**:

  1. Ta bort databasen som har en **inaktuell** status från Sync-gruppen.
  1. Lägg till databasen i Sync-gruppen igen.

  > [!WARNING]
  > Du förlorar alla ändringar som gjorts i databasen när den var offline.

### <a name="a-sync-group-has-an-out-of-date-status"></a><a name="setup-date2"></a> En Sync-grupp har statusen "inaktuell"

- **Orsak**. Om en eller flera ändringar inte kan tillämpas för hela kvarhållningsperioden på 45 dagar kan en Sync-grupp bli inaktuell.

- **Lösning**. Om du vill undvika en **inaktuell** status för en Sync-grupp granskar du resultatet av dina synkroniseringsjobb i historik visaren regelbundet. Undersök och åtgärda eventuella ändringar som inte tillämpas.

  Om statusen för synkroniseringsresursen är **inaktuell** tar du bort synkroniseringsresursen och återskapar den.

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a><a name="setup-delete2"></a> Det går inte att ta bort en Sync-grupp inom tre minuter från avinstallation eller stoppa agenten

Du kan inte ta bort en Sync-grupp inom tre minuter från avinstallation eller stopp av den associerade SQL Data Sync klient agenten.

- **Lösning**.

  1. Ta bort en Sync-grupp medan de associerade Sync-agenterna är online (rekommenderas).
  1. Om agenten är offline men har installerats kan du ta den online på den lokala datorn. Vänta tills agentens status visas som **online** i SQL Data Sync portalen. Ta sedan bort Sync-gruppen.
  1. Om agenten är offline eftersom den avinstallerades:  
    a.  Ta bort agentens XML-fil från installationsmappen för SQL Data Sync, om filen finns.  
    b.  Installera agenten på en lokal dator (det kan vara samma eller en annan dator). Skicka agentnyckeln som genereras i portalen för den agent som visas som offline.  
    c. Försök att ta bort Sync-gruppen.

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a><a name="setup-restore"></a> Vad händer när jag återställer en förlorad eller skadad databas?

Om du återställer en förlorad eller skadad databas från en säkerhets kopia kan det finnas en icke-konvergens av data i de Sync-grupper som databasen tillhör.

## <a name="next-steps"></a>Nästa steg
Mer information om SQL Data Sync finns i:

-   Översikt – [synkronisera data över flera molnbaserade och lokala databaser med SQL Data Sync i Azure](sql-data-sync-data-sql-server-sql-database.md)
-   Konfigurera Data Sync
    - I portalen – [Självstudier: konfigurera SQL Data Sync för att synkronisera data mellan Azure SQL Database och SQL Server](sql-data-sync-sql-server-configure.md)
    - Med PowerShell
        -  [Använd PowerShell för att synkronisera mellan flera databaser i Azure SQL Database](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Använda PowerShell för att synkronisera mellan en databas i Azure SQL Database och en databas i en SQL Server instans](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Data Sync-agent – [Data Sync-agent för SQL Data Sync i Azure](sql-data-sync-agent-overview.md)
-   Bästa praxis – [metod tips för SQL Data Sync i Azure](sql-data-sync-best-practices.md)
-   Övervaka [SQL Data Sync med Azure Monitor loggar](./monitor-tune-overview.md)
-   Uppdatera synkroniseringsschemat
    -   Med Transact-SQL – [automatisera replikeringen av schema ändringar i SQL Data Sync i Azure](sql-data-sync-update-sync-schema.md)
    -   Med PowerShell – [Använd PowerShell för att uppdatera synkroniseringsschemat i en befintlig synkroniseringsgrupp](scripts/update-sync-schema-in-sync-group.md)

Mer information om SQL Database finns i:

-   [Översikt över SQL Database](sql-database-paas-overview.md)
-   [Livscykelhantering för databas](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))