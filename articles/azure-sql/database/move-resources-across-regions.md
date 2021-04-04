---
title: Flytta resurser till ny region
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Lär dig hur du flyttar databasen eller en hanterad instans till en annan region.
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: ae6c87c9eabea837ba9c43676d4ca712caa385cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94594172"
---
# <a name="move-resources-to-new-region---azure-sql-database--azure-sql-managed-instance"></a>Flytta resurser till ny region – Azure SQL Database & Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

I den här artikeln lär du dig ett allmänt arbets flöde för hur du flyttar databasen eller den hanterade instansen till en ny region.

## <a name="overview"></a>Översikt

Det finns olika scenarier där du vill flytta din befintliga databas eller hanterade instans från en region till en annan. Till exempel expanderar du din verksamhet till en ny region och vill optimera den för den nya kund basen. Eller så måste du flytta åtgärderna till en annan region för att kunna följa dessa orsaker. Eller Azure har publicerat en ny region som ger bättre närhet och förbättrar kund upplevelsen.  

Den här artikeln innehåller ett allmänt arbets flöde för att flytta resurser till en annan region. Arbets flödet består av följande steg:

1. Kontrol lera kraven för flytten.
1. Förbered för att flytta resurserna i omfånget.
1. Övervaka förberedelse processen.
1. Testa flyttnings processen.
1. Påbörja den faktiska flyttningen.
1. Ta bort resurserna från käll regionen.

> [!NOTE]
> Den här artikeln gäller migreringar i det offentliga Azure-molnet eller i samma suveräna moln.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="move-a-database"></a>Flytta en databas

### <a name="verify-prerequisites"></a>Verifiera kraven

1. Skapa en mål server för varje käll Server.
1. Konfigurera brand väggen med rätt undantag med hjälp av [PowerShell](scripts/create-and-configure-database-powershell.md).  
1. Konfigurera servrarna med korrekta inloggningar. Om du inte är prenumerations administratör eller SQL Server-administratör kan du arbeta med administratören för att tilldela de behörigheter som du behöver. Mer information finns i [hantera Azure SQL Database säkerhet efter haveri beredskap](active-geo-replication-security-configure.md).
1. Om dina databaser är krypterade med transparent data kryptering och använder din egen krypterings nyckel i Azure Key Vault, se till att rätt krypterings material är etablerad i mål regionerna. Mer information finns i avsnittet om [transparent data kryptering i Azure SQL med Kundhanterade nycklar i Azure Key Vault](transparent-data-encryption-byok-overview.md).
1. Om granskning på databas nivå är aktiverat inaktiverar du den och aktiverar granskning på server nivå i stället. Efter redundansväxlingen kräver granskning på databas nivå trafiken över flera regioner, vilket inte är önskvärt eller möjligt efter flytten.
1. För granskning på server nivå, se till att:
   - Lagrings behållaren, Log Analytics eller händelsehubben med befintliga gransknings loggar flyttas till mål regionen.
   - Granskning har kon figurer ATS på mål servern. Mer information finns i [Kom igång med SQL Database granskning](../../azure-sql/database/auditing-overview.md).
1. Om instansen har en långsiktig bevarande princip (brv) förblir de befintliga säkerhets kopieringarna fortfarande kopplade till den aktuella servern. Eftersom mål servern är annorlunda kommer du att kunna komma åt äldre LTR-säkerhetskopieringar i käll regionen genom att använda käll servern, även om servern tas bort.

      > [!NOTE]
      > Detta är inte tillräckligt för att flytta mellan det suveräna molnet och en offentlig region. En sådan migrering kräver att säkerhets kopieringen av säkerhets kopian flyttas till mål servern, vilket inte stöds för närvarande.

### <a name="prepare-resources"></a>Förbereda resurser

1. Skapa en [redundans grupp](failover-group-add-single-database-tutorial.md#2---create-the-failover-group) mellan servern för källan och målets Server.  
1. Lägg till de databaser som du vill flytta till gruppen redundans.
  
    Replikeringen av alla tillagda databaser kommer att initieras automatiskt. Mer information finns i [metod tips för att använda failover-grupper med enskilda databaser](auto-failover-group-overview.md#best-practices-for-sql-database).

### <a name="monitor-the-preparation-process"></a>Övervaka förberedelse processen

Du kan regelbundet anropa [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) för att övervaka replikeringen av dina databaser från källan till målet. Objektet utdata i `Get-AzSqlDatabaseFailoverGroup` innehåller en egenskap för **ReplicationState**:

- **ReplicationState = 2** (CATCH_UP) anger att databasen är synkroniserad och kan växlas över på ett säkert sätt.
- **ReplicationState = 0** (seeding) visar att databasen inte har dirigerats än och att ett försök att redundansväxla kommer att Miss lyckas.

### <a name="test-synchronization"></a>Testa synkronisering

Efter **ReplicationState** `2` ansluter du till varje databas eller delmängd av databaser med hjälp av den sekundära slut punkten `<fog-name>.secondary.database.windows.net` och utför en fråga mot databaserna för att säkerställa anslutning, rätt säkerhets konfiguration och datareplikering.

### <a name="initiate-the-move"></a>Påbörja flytten

1. Anslut till mål servern med hjälp av den sekundära slut punkten `<fog-name>.secondary.database.windows.net` .
1. Använd [switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) för att växla den sekundära hanterade instansen som primär med fullständig synkronisering. Den här åtgärden kommer att lyckas eller återställas.
1. Kontrol lera att kommandot har slutförts genom `nslook up <fog-name>.secondary.database.windows.net` att använda för att kontrol lera att DNS CNAME-posten pekar på mål regionens IP-adress. Om switch-kommandot Miss lyckas kommer CNAME inte att uppdateras.

### <a name="remove-the-source-databases"></a>Ta bort käll databaserna

När flyttningen är klar tar du bort resurserna i käll regionen för att undvika onödiga kostnader.

1. Ta bort gruppen redundans med [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Ta bort varje käll databas med [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) för varje databas på käll servern. Detta avslutar Länkar för geo-replikering automatiskt.
1. Ta bort käll servern med hjälp av [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver).
1. Ta bort nyckel valvet, granska lagrings behållare, händelsehubben, Azure Active Directory (Azure AD)-instansen och andra beroende resurser för att sluta faktureras för dem.

## <a name="move-elastic-pools"></a>Flytta elastiska pooler

### <a name="verify-prerequisites"></a>Verifiera kraven

1. Skapa en mål server för varje käll Server.
1. Konfigurera brand väggen med rätt undantag med hjälp av [PowerShell](scripts/create-and-configure-database-powershell.md).
1. Konfigurera servrarna med korrekta inloggningar. Om du inte är prenumerations administratör eller Server administratör kan du arbeta med administratören för att tilldela de behörigheter som du behöver. Mer information finns i [hantera Azure SQL Database säkerhet efter haveri beredskap](active-geo-replication-security-configure.md).
1. Om dina databaser är krypterade med transparent data kryptering och använder din egen krypterings nyckel i Azure Key Vault, se till att rätt krypterings material är etablerad i mål regionen.
1. Skapa en elastisk mål grupp för varje elastisk pool och se till att poolen skapas på samma tjänst nivå, med samma namn och samma storlek.
1. Om en databas nivå granskning är aktive rad inaktiverar du den och aktiverar granskning på server nivå i stället. Efter redundansväxlingen kräver granskning på databas nivå trafik över flera regioner, vilket inte önskas, eller möjligt efter flytten.
1. För granskning på server nivå, se till att:
    - Lagrings behållaren, Log Analytics eller händelsehubben med befintliga gransknings loggar flyttas till mål regionen.
    - Gransknings konfigurationen konfigureras på mål servern. Mer information finns i [SQL Database granskning](../../azure-sql/database/auditing-overview.md).
1. Om instansen har en långsiktig bevarande princip (brv) förblir de befintliga säkerhets kopieringarna fortfarande kopplade till den aktuella servern. Eftersom mål servern är annorlunda kommer du att kunna komma åt äldre LTR-säkerhetskopieringar i käll regionen med käll servern, även om servern tas bort.

      > [!NOTE]
      > Detta är inte tillräckligt för att flytta mellan det suveräna molnet och en offentlig region. En sådan migrering kräver att säkerhets kopieringen av säkerhets kopian flyttas till mål servern, vilket inte stöds för närvarande.

### <a name="prepare-to-move"></a>Förbered för att flytta

1. Skapa en separat [failover-grupp](failover-group-add-elastic-pool-tutorial.md#3---create-the-failover-group) mellan varje elastisk pool på käll servern och dess motsvarande elastiska pool på mål servern.
1. Lägg till alla databaser i poolen i gruppen redundans.

    Replikering av tillagda databaser initieras automatiskt. Mer information finns i [metod tips för failover-grupper med elastiska pooler](auto-failover-group-overview.md#best-practices-for-sql-database).

      > [!NOTE]
      > Även om det är möjligt att skapa en failover-grupp som innehåller flera elastiska pooler, rekommenderar vi starkt att du skapar en separat failover-grupp för varje pool. Om du har ett stort antal databaser över flera elastiska pooler som du behöver flytta kan du köra förberedelse stegen parallellt och sedan starta flytt steget parallellt. Den här processen skalas bättre och tar mindre tid jämfört med att ha flera elastiska pooler i samma failover-grupp.

### <a name="monitor-the-preparation-process"></a>Övervaka förberedelse processen

Du kan regelbundet anropa [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) för att övervaka replikeringen av dina databaser från källan till målet. Objektet utdata i `Get-AzSqlDatabaseFailoverGroup` innehåller en egenskap för **ReplicationState**:

- **ReplicationState = 2** (CATCH_UP) anger att databasen är synkroniserad och kan växlas över på ett säkert sätt.
- **ReplicationState = 0** (seeding) visar att databasen inte har dirigerats än och att ett försök att redundansväxla kommer att Miss lyckas.

### <a name="test-synchronization"></a>Testa synkronisering

När **ReplicationState** är `2` det ansluter du till varje databas eller delmängd av databaser med hjälp av den sekundära slut punkten `<fog-name>.secondary.database.windows.net` och utför en fråga mot databaserna för att säkerställa anslutning, korrekt säkerhets konfiguration och datareplikering.

### <a name="initiate-the-move"></a>Påbörja flytten

1. Anslut till mål servern med hjälp av den sekundära slut punkten `<fog-name>.secondary.database.windows.net` .
1. Använd [switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) för att växla den sekundära hanterade instansen som primär med fullständig synkronisering. Den här åtgärden kommer att lyckas eller återställas.
1. Kontrol lera att kommandot har slutförts genom `nslook up <fog-name>.secondary.database.windows.net` att använda för att kontrol lera att DNS CNAME-posten pekar på mål regionens IP-adress. Om switch-kommandot Miss lyckas kommer CNAME inte att uppdateras.

### <a name="remove-the-source-elastic-pools"></a>Ta bort de elastiska källorna

När flyttningen är klar tar du bort resurserna i käll regionen för att undvika onödiga kostnader.

1. Ta bort gruppen redundans med [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Ta bort varje elastisk källa på käll servern med hjälp av [Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool).
1. Ta bort käll servern med hjälp av [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver).
1. Ta bort nyckel valvet, granska lagrings behållare, Event Hub, Azure AD-instansen och andra beroende resurser för att sluta faktureras för dem.

## <a name="move-a-managed-instance"></a>Flytta en hanterad instans

### <a name="verify-prerequisites"></a>Verifiera kraven

1. För varje hanterad käll instans skapar du en mål instans av SQL-hanterad instans av samma storlek i mål regionen.  
1. Konfigurera nätverket för en hanterad instans. Mer information finns i [nätverks konfiguration](../managed-instance/how-to-content-reference-guide.md#network-configuration).
1. Konfigurera mål huvud databasen med korrekta inloggningar. Om du inte är prenumerations-eller SQL-hanterad instans administratör kan du arbeta med administratören för att tilldela de behörigheter som du behöver.
1. Om dina databaser är krypterade med transparent data kryptering och använder din egen krypterings nyckel i Azure Key Vault, se till att Azure Key Vault med identiska krypterings nycklar finns i både käll-och mål regionerna. Mer information finns i [transparent data kryptering med Kundhanterade nycklar i Azure Key Vault](transparent-data-encryption-byok-overview.md).
1. Om granskning har Aktiver ATS för den hanterade instansen kontrollerar du att:
    - Lagrings containern eller händelsehubben med befintliga loggar flyttas till mål regionen.
    - Granskning har kon figurer ATS på mål instansen. Mer information finns i [granskning med SQL-hanterad instans](../managed-instance/auditing-configure.md).
1. Om instansen har en långsiktig bevarande princip (brv) förblir de befintliga säkerhets kopieringarna fortfarande kopplade till den aktuella instansen. Eftersom mål instansen är annorlunda kommer du att kunna komma åt äldre LTR-säkerhetskopieringar i käll regionen med käll instansen, även om instansen tas bort.

  > [!NOTE]
  > Detta är inte tillräckligt för att flytta mellan det suveräna molnet och en offentlig region. En sådan migrering kräver att säkerhets kopieringarna flyttas till mål instansen, vilket inte stöds för närvarande.

### <a name="prepare-resources"></a>Förbereda resurser

Skapa en redundans grupp mellan varje hanterad instans av källan och motsvarande mål instans av SQL-hanterad instans.

Replikeringen av alla databaser på varje instans kommer att initieras automatiskt. Mer information finns i [grupper för automatisk redundans](auto-failover-group-overview.md).

### <a name="monitor-the-preparation-process"></a>Övervaka förberedelse processen

Du kan regelbundet anropa [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) för att övervaka replikeringen av dina databaser från källan till målet. Objektet utdata i `Get-AzSqlDatabaseFailoverGroup` innehåller en egenskap för **ReplicationState**:

- **ReplicationState = 2** (CATCH_UP) anger att databasen är synkroniserad och kan växlas över på ett säkert sätt.
- **ReplicationState = 0** (seeding) visar att databasen inte har dirigerats än och att ett försök att redundansväxla Miss lyckas.

### <a name="test-synchronization"></a>Testa synkronisering

När **ReplicationState** är `2` det ansluter du till varje databas eller en delmängd av databaser med hjälp av den sekundära slut punkten `<fog-name>.secondary.database.windows.net` och utför en fråga mot databaserna för att säkerställa anslutning, korrekt säkerhets konfiguration och datareplikering.

### <a name="initiate-the-move"></a>Påbörja flytten

1. Anslut till den hanterade mål instansen med hjälp av den sekundära slut punkten `<fog-name>.secondary.database.windows.net` .
1. Använd [switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) för att växla den sekundära hanterade instansen som primär med fullständig synkronisering. Den här åtgärden kommer att lyckas, eller så återställs den igen.
1. Kontrol lera att kommandot har slutförts genom `nslook up <fog-name>.secondary.database.windows.net` att använda för att kontrol lera att DNS CNAME-posten pekar på mål regionens IP-adress. Om switch-kommandot Miss lyckas kommer CNAME inte att uppdateras.

### <a name="remove-the-source-managed-instances"></a>Ta bort de hanterade käll instanserna

När flyttningen är klar tar du bort resurserna i käll regionen för att undvika onödiga kostnader.

1. Ta bort gruppen redundans med [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). Detta tar bort konfigurationen av redundanskonfiguration och avslutar geo-replikeringslänken mellan de två instanserna.
1. Ta bort den hanterade käll instansen med hjälp av [Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance).
1. Ta bort eventuella ytterligare resurser i resurs gruppen, till exempel det virtuella klustret, det virtuella nätverket och säkerhets gruppen.

## <a name="next-steps"></a>Nästa steg

[Hantera](manage-data-after-migrating-to-database.md) din databas när den har migrerats.
