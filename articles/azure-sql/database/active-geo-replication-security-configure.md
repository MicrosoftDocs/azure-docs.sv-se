---
title: Konfigurera säkerhet för haveri beredskap
description: Lär dig mer om säkerhets överväganden för att konfigurera och hantera säkerhet efter en databas återställning eller redundansväxling till en sekundär server.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 12/18/2018
ms.openlocfilehash: 317b530fbaa34ca5689bb505126892e4eba06bd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92674798"
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Konfigurera och hantera Azure SQL Database säkerhet för geo-återställning eller redundans
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här artikeln beskrivs autentiseringskrav för att konfigurera och kontrol lera aktiva grupper för [geo-replikering](active-geo-replication-overview.md) och [Automatisk redundans](auto-failover-group-overview.md). Den innehåller också de steg som krävs för att konfigurera användar åtkomst till den sekundära databasen. Slutligen beskriver det också hur du aktiverar åtkomst till den återställda databasen efter att du använt [geo-återställning](recovery-using-backups.md#geo-restore). Mer information om återställnings alternativ finns i [Översikt över affärs kontinuitet](business-continuity-high-availability-disaster-recover-hadr-overview.md).

## <a name="disaster-recovery-with-contained-users"></a>Haveri beredskap med inneslutna användare

Till skillnad från traditionella användare, som måste mappas till inloggningar i huvud databasen, hanteras en innesluten användare fullständigt av själva databasen. Detta har två fördelar. I katastrof återställnings scenariot kan användarna fortsätta att ansluta till den nya primära databasen eller databasen återskapas med geo-återställning utan ytterligare konfiguration, eftersom databasen hanterar användarna. Det finns även potentiella skalbarhet och prestanda för delar av den här konfigurationen från ett inloggnings perspektiv. Mer information finns i [Användare av oberoende databas – göra databasen portabel](/sql/relational-databases/security/contained-database-users-making-your-database-portable).

Den främsta kompromissen är att hanteringen av haveri beredskap i stor skala är mer utmanande. Om du har flera databaser som använder samma inloggning, kan det vara en negation av fördelarna med inneslutna användare om du behåller de autentiseringsuppgifter som finns i befintliga användare i flera databaser. Principen för lösen ords rotation kräver till exempel att ändringar görs konsekvent i flera databaser i stället för att ändra lösen ordet till inloggningen en gång i huvud databasen. Om du har flera databaser som använder samma användar namn och lösen ord rekommenderar vi inte att du använder inkluderade användare.

## <a name="how-to-configure-logins-and-users"></a>Konfigurera inloggningar och användare

Om du använder inloggningar och användare (i stället för att inkluderade användare) måste du vidta ytterligare åtgärder för att säkerställa att samma inloggningar finns i huvud databasen. I följande avsnitt beskrivs de steg som beskrivs och ytterligare överväganden.

  >[!NOTE]
  > Du kan också använda Azure Active Directory (AAD)-inloggningar för att hantera dina databaser. Mer information finns i [Azure SQL-inloggningar och användare](./logins-create-manage.md).

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>Konfigurera användar åtkomst till en sekundär eller återställd databas

För att den sekundära databasen ska kunna användas som skrivskyddad sekundär databas, och för att säkerställa korrekt åtkomst till den nya primära databasen eller databasen som återställs med geo-återställning, måste huvud databasen på mål servern ha rätt säkerhets konfiguration på plats före återställningen.

De angivna behörigheterna för varje steg beskrivs senare i det här avsnittet.

Att förbereda användar åtkomst till en sekundär geo-replikering bör utföras när du konfigurerar geo-replikering. Att förbereda användar åtkomst till de geo-återställda databaserna bör utföras när som helst när den ursprungliga servern är online (t. ex. som en del av DR-granskningen).

> [!NOTE]
> Om du växlar över eller geo-återställning till en server som inte har korrekt konfigurerade inloggningar kommer åtkomsten till den att begränsas till Server administratörs kontot.

Att konfigurera inloggningar på mål servern omfattar tre steg som beskrivs nedan:

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. Bestäm inloggningar med åtkomst till den primära databasen

Det första steget i processen är att avgöra vilka inloggningar som måste dupliceras på mål servern. Detta åstadkoms med ett par med SELECT-instruktioner, en i den logiska huvud databasen på käll servern och en i själva databasen i den primära databasen.

Endast Server administratören eller en medlem i **LoginManager** -serverrollen kan bestämma inloggningarna på käll servern med följande SELECT-instruktion.

```sql
SELECT [name], [sid]
FROM [sys].[sql_logins]
WHERE [type_desc] = 'SQL_Login'
```

Endast en medlem i db_owner databas rollen, dbo-användaren eller Server administratören kan bestämma alla huvud konton för databas användare i den primära databasen.

```sql
SELECT [name], [sid]
FROM [sys].[database_principals]
WHERE [type_desc] = 'SQL_USER'
```

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. hitta SID för de inloggningar som identifierades i steg 1

Genom att jämföra utdata från frågorna från föregående avsnitt och matcha sid, kan du mappa Server inloggningen till databas användare. Inloggningar som har en databas användare med ett matchande SID har användar åtkomst till databasen som databasens huvud konto.

Följande fråga kan användas för att se alla användares huvud namn och deras sid i en databas. Endast en medlem i db_owner databas rollen eller Server administratören kan köra den här frågan.

```sql
SELECT [name], [sid]
FROM [sys].[database_principals]
WHERE [type_desc] = 'SQL_USER'
```

> [!NOTE]
> **INFORMATION_SCHEMA** -och **sys** -användare har *Null* -sid och **gäst** -sid är **0x00**. **Dbo** -sid kan starta med *0x01060000000001648000000000048454*, om databasens skapare var Server administratören i stället för en medlem i **DBManager**.

#### <a name="3-create-the-logins-on-the-target-server"></a>3. skapa inloggningar på mål servern

Det sista steget är att gå till mål servern eller servrarna och generera inloggningar med rätt sid. Den grundläggande syntaxen är följande.

```sql
CREATE LOGIN [<login name>]
WITH PASSWORD = <login password>,
SID = <desired login SID>
```

> [!NOTE]
> Om du vill ge användare åtkomst till den sekundära, men inte till den primära, kan du göra det genom att ändra användar inloggningen på den primära servern med hjälp av följande syntax.
>
> ```sql
> ALTER LOGIN <login name> DISABLE
> ```
>
> Inaktivera ändrar inte lösen ordet, så du kan alltid aktivera det om det behövs.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du hanterar databas åtkomst och inloggningar finns i [SQL Database säkerhet: Hantera databas åtkomst och inloggnings säkerhet](logins-create-manage.md).
* Mer information om inneslutna databas användare finns i [inneslutna databas användare – göra databasen portabel](/sql/relational-databases/security/contained-database-users-making-your-database-portable).
* Mer information om aktiv geo-replikering finns i [aktiv geo-replikering](active-geo-replication-overview.md).
* Mer information om grupper för automatisk redundans finns i [grupper för automatisk redundans](auto-failover-group-overview.md).
* Information om hur du använder geo-återställning finns i [geo-återställning](recovery-using-backups.md#geo-restore)