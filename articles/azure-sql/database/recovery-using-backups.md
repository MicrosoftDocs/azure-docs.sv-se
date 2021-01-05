---
title: Återställa en databas från en säkerhets kopia
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Lär dig mer om återställning av tidpunkter, vilket gör att du kan återställa en databas i Azure SQL Database eller en instans i Azure SQL-hanterad instans upp till 35 dagar.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein, danil
ms.date: 11/13/2020
ms.openlocfilehash: a70571dcf380fc2186565a40778991ac70a218d6
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797221"
---
# <a name="recover-using-automated-database-backups---azure-sql-database--sql-managed-instance"></a>Återställa med hjälp av automatiska databas säkerhets kopieringar – Azure SQL Database & SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Följande alternativ är tillgängliga för databas återställning med hjälp av [automatiska databas säkerhets kopieringar](automated-backups-overview.md). Du kan:

- Skapa en ny databas på samma server, återställd till en angiven tidpunkt inom kvarhållningsperioden.
- Skapa en databas på samma server, återställd till borttagnings tiden för en borttagen databas.
- Skapa en ny databas på vilken server som helst i samma region, återställd till de senaste säkerhets kopiorna.
- Skapa en ny databas på vilken server som helst i någon annan region, återställd till platsen för de senaste replikerade säkerhets kopiorna.

Om du har konfigurerat [långsiktig kvarhållning av säkerhets kopia](long-term-retention-overview.md)kan du också skapa en ny databas från valfri långsiktig kvarhållning av säkerhets kopior på valfri server.

> [!IMPORTANT]
> Du kan inte skriva över en befintlig databas under återställningen.

När du använder standard-eller premium-tjänst nivån kan databas återställningen medföra en extra lagrings kostnad. Den extra kostnaden uppkommer när den återställda databasens maximala storlek är större än den mängd lagrings utrymme som ingår i mål databasens tjänste nivå och prestanda nivå. Pris information för extra lagrings utrymme finns på [sidan SQL Database priser](https://azure.microsoft.com/pricing/details/sql-database/). Om den faktiska mängden använt utrymme är mindre än den mängd lagring som ingår, kan du undvika den här extra kostnaden genom att ställa in den maximala databas storleken på den inkluderade mängden.

## <a name="recovery-time"></a>Återställnings tid

Återställnings tiden för att återställa en databas med hjälp av automatisk säkerhets kopiering av databasen påverkas av flera faktorer:

- Databasens storlek.
- Databasens beräknings storlek.
- Antalet transaktions loggar som ingår.
- Den mängd aktivitet som behöver upprepas för att återställas till återställnings punkten.
- Nätverks bandbredden om återställningen är till en annan region.
- Antalet samtidiga återställnings begär Anden som bearbetas i mål regionen.

För en stor eller mycket aktiv databas kan återställningen ta flera timmar. Om det finns ett långvarigt avbrott i en region, är det möjligt att ett stort antal geo-återställnings begär Anden kommer att initieras för haveri beredskap. När det finns många begär Anden kan återställnings tiden för enskilda databaser öka. De flesta databas återställningar slutförs på mindre än 12 timmar.

För en enskild prenumeration finns det begränsningar för antalet samtidiga återställnings begär Anden. Dessa begränsningar gäller för valfri kombination av återställningar av tidpunkter, geo-Restore och återställningar från säkerhets kopia med långsiktig kvarhållning.

| **Distributions alternativ** | **Max antal samtidiga begär Anden som bearbetas** | **Max antal samtidiga förfrågningar som skickas** |
| :--- | --: | --: |
|**Enkel databas (per prenumeration)**|10|60|
|**Elastisk pool (per pool)**|4|200|


Det finns inte någon inbyggd metod för att återställa hela servern. Ett exempel på hur du utför den här uppgiften finns [Azure SQL Database: fullständig Server återställning](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666).

> [!IMPORTANT]
> Om du vill återställa med hjälp av automatiska säkerhets kopieringar måste du vara medlem i rollen SQL Server deltagar roll eller SQL-hanterad instans deltagare (beroende på återställnings målet) i prenumerationen eller så måste du vara prenumerations ägare. Mer information finns i [Azure RBAC: inbyggda roller](../../role-based-access-control/built-in-roles.md). Du kan återställa med hjälp av Azure Portal, PowerShell eller REST API. Du kan inte använda Transact-SQL.

## <a name="point-in-time-restore"></a>Återställning från tidpunkt

Du kan återställa en fristående, poolad eller instans databas till en tidigare tidpunkt med hjälp av Azure Portal, [PowerShell](/powershell/module/az.sql/restore-azsqldatabase)eller [REST API](/rest/api/sql/databases/createorupdate#creates-a-database-from-pointintimerestore.). Begäran kan ange valfri tjänst nivå eller beräknings storlek för den återställda databasen. Se till att du har tillräckligt med resurser på den server som du återställer databasen till. 

När du är klar skapar återställningen en ny databas på samma server som den ursprungliga databasen. Den återställda databasen debiteras enligt normal taxa, baserat på tjänst nivå och beräknings storlek. Du debiteras inte förrän databas återställningen är klar.

Du återställer vanligt vis en databas till en tidigare tidpunkt för återställnings syfte. Du kan behandla den återställda databasen som en ersättning för den ursprungliga databasen eller använda den som en data källa för att uppdatera den ursprungliga databasen.

- **Databas utbyte**

  Om du vill att den återställda databasen ska ersätta den ursprungliga databasen, bör du ange den ursprungliga databasens beräknings storlek och tjänst nivå. Du kan sedan byta namn på den ursprungliga databasen och ge den återställda databasen det ursprungliga namnet med hjälp av kommandot [Alter Database](/sql/t-sql/statements/alter-database-azure-sql-database) i T-SQL.

- **Dataåterställning**

  Om du planerar att hämta data från den återställda databasen för att återställa från ett användar-eller program fel måste du skriva och köra ett data återställnings skript som extraherar data från den återställda databasen och gäller för den ursprungliga databasen. Även om återställnings åtgärden kan ta lång tid att slutföra, visas återställnings databasen i databas listan under hela återställnings processen. Om du tar bort databasen under återställningen avbryts återställnings åtgärden och du debiteras inte för den databas som inte slutförde återställningen.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Återställning av tidpunkt med hjälp av Azure Portal

Du kan återställa en enskild databas eller instans databas till en tidpunkt från översikts bladet för den databas som du vill återställa i Azure Portal.

#### <a name="sql-database"></a>SQL Database

Om du vill återställa en databas till en tidpunkt genom att använda Azure Portal öppnar du sidan databas översikt och väljer **Återställ** i verktygsfältet. Välj säkerhets kopierings källa och välj den tidpunkt för säkerhets kopiering som en ny databas ska skapas från.

  ![Skärm bild av databas återställnings alternativ för SQL Database.](./media/recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>SQL-hanterad instans

Om du vill återställa en hanterad instans databas till en tidpunkt genom att använda Azure Portal öppnar du sidan databas översikt och väljer **Återställ** i verktygsfältet. Välj den säkerhets kopierings punkt för tidpunkt från vilken en ny databas ska skapas.

  ![Skärm bild av databas återställnings alternativ för SQL-hanterad instans.](./media/recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> För att program mässigt återställa en databas från en säkerhets kopia, se [programmatisk återställning med automatiserade säkerhets kopieringar](recovery-using-backups.md).

## <a name="deleted-database-restore"></a>Återställning av borttagen databas

Du kan återställa en borttagen databas till borttagnings tiden, eller en tidigare tidpunkt, på samma server eller samma hanterade instans. Du kan göra detta via Azure Portal, [PowerShell](/powershell/module/az.sql/restore-azsqldatabase)eller [rest (CreateMode = Restore)](/rest/api/sql/databases/createorupdate). Du återställer en borttagen databas genom att skapa en ny databas från säkerhets kopian.

> [!IMPORTANT]
> Om du tar bort en server eller en hanterad instans tas alla dess databaser också bort och kan inte återställas. Du kan inte återställa en borttagen Server eller hanterad instans.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Återställningen av databasen har tagits bort med hjälp av Azure Portal

Du återställer borttagna databaser från Azure Portal från resursen Server eller hanterad instans.

> [!TIP]
> Det kan ta flera minuter innan nyligen borttagna databaser visas på sidan **borttagna databaser** i Azure Portal, eller när de visar borttagna databaser [program mässigt](#programmatic-recovery-using-automated-backups).

#### <a name="sql-database"></a>SQL Database

Om du vill återställa en borttagen databas till borttagnings tiden genom att använda Azure Portal öppnar du sidan Server översikt och väljer **borttagna databaser**. Välj en borttagen databas som du vill återställa och skriv namnet på den nya databasen som ska skapas med data som återställs från säkerhets kopian.

  ![Skärm bild av Återställ borttagen databas](./media/recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>SQL-hanterad instans

Om du vill återställa en hanterad databas med hjälp av Azure Portal öppnar du översikts sidan för hanterade instanser och väljer **borttagna databaser**. Välj en borttagen databas som du vill återställa och skriv namnet på den nya databasen som ska skapas med data som återställs från säkerhets kopian.

  ![Skärm bild av återställning borttagen Azure SQL-hanterad instans databas](./media/recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Borttagen databas återställning med hjälp av PowerShell

Använd följande exempel skript för att återställa en borttagen databas för antingen SQL Database eller SQL-hanterad instans med hjälp av PowerShell.

#### <a name="sql-database"></a>SQL Database

Ett exempel på PowerShell-skript som visar hur du återställer en borttagen databas i Azure SQL Database finns i [återställa en databas med hjälp av PowerShell](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>SQL-hanterad instans

Ett exempel på PowerShell-skript som visar hur du återställer en borttagen instans databas finns i [återställa borttagna instans databaser med PowerShell](../managed-instance/point-in-time-restore.md#restore-a-deleted-database)

> [!TIP]
> För att program mässigt återställa en borttagen databas, se [program mässigt utföra återställning med automatiska säkerhets kopieringar](recovery-using-backups.md).

## <a name="geo-restore"></a>Geo-återställning

> [!IMPORTANT]
> Geo-återställning är endast tillgängligt för SQL-databaser eller hanterade instanser som kon figurer ATS med Geo-redundant [lagring av säkerhets kopior](automated-backups-overview.md#backup-storage-redundancy).

Du kan återställa en databas på en SQL Database Server eller en instans databas på en hanterad instans i valfri Azure-region från de senaste geo-replikerade säkerhets kopiorna. Geo-återställning använder en geo-replikerad säkerhets kopiering som källa. Du kan begära geo-återställning även om databasen eller data centret inte går att komma åt på grund av ett avbrott.

Geo-återställning är standard alternativet för återställning när databasen inte är tillgänglig på grund av en incident i värd regionen. Du kan återställa databasen till en server i valfri annan region. Det uppstår en fördröjning mellan när en säkerhets kopia tas och när den är geo-replikerad till en Azure-blob i en annan region. Det innebär att den återställda databasen kan vara upp till en timme bakom den ursprungliga databasen. Följande bild visar en databas återställning från den senaste tillgängliga säkerhets kopian i en annan region.

![Grafik för geo-återställning](./media/recovery-using-backups/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Geo-återställning med hjälp av Azure Portal

Från Azure Portal skapar du en ny databas för enkel eller hanterad instans och väljer en tillgänglig säkerhets kopia för geo-återställning. Den nyligen skapade databasen innehåller geo-återställda säkerhets kopierings data.

#### <a name="sql-database"></a>SQL Database

Om du vill geo-återställa en enskild databas från Azure Portal i den region och server du väljer, följer du dessa steg:

1. Från **instrument panelen** väljer du **Lägg till**  >  **skapa SQL Database**. Ange den information som krävs på fliken **grundläggande** .
2. Välj **ytterligare inställningar**.
3. Om du vill **använda befintliga data** väljer du **säkerhets kopiering**.
4. För **säkerhets kopiering** väljer du en säkerhets kopia i listan över tillgängliga säkerhets kopior för geo-återställning.

    ![Skärm bild av alternativen för att skapa SQL Database](./media/recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Slutför processen med att skapa en ny databas från säkerhets kopian. När du skapar en databas i Azure SQL Database, innehåller den återställda säkerhets kopiering med geo-återställning.

#### <a name="sql-managed-instance"></a>SQL-hanterad instans

Om du vill geo-återställa en hanterad instans databas från Azure Portal till en befintlig hanterad instans i valfri region väljer du en hanterad instans som du vill att en databas ska återställas på. Följ de här stegen:

1. Välj **ny databas**.
2. Ange önskat databas namn.
3. Under **Använd befintliga data** väljer du **säkerhets kopiering**.
4. Välj en säkerhets kopia i listan över tillgängliga säkerhets kopior för geo-återställning.

    ![Skärm bild av nya databas alternativ](./media/recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Slutför processen med att skapa en ny databas. När du skapar instans databasen innehåller den återställda säkerhets kopiering med geo-återställning.

### <a name="geo-restore-by-using-powershell"></a>Geo-återställning med hjälp av PowerShell

#### <a name="sql-database"></a>SQL Database

Ett PowerShell-skript som visar hur du utför geo-återställning för en enskild databas finns i [använda PowerShell för att återställa en enskild databas till en tidigare tidpunkt](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>SQL-hanterad instans

Ett PowerShell-skript som visar hur du utför geo-återställning för en hanterad instans databas finns i [använda PowerShell för att återställa en hanterad instans databas till en annan geo-region](../managed-instance/scripts/restore-geo-backup.md).

### <a name="geo-restore-considerations"></a>Överväganden för geo-återställning

Du kan inte utföra en tidpunkts återställning på en geo-sekundär databas. Du kan bara göra det på en primär databas. Detaljerad information om hur du använder geo-återställning för att återställa från ett avbrott finns i [återställa från ett avbrott](../../key-vault/general/disaster-recovery-guidance.md).

> [!IMPORTANT]
> Geo-återställning är den mest grundläggande katastrof återställnings lösningen som finns i SQL Database och SQL-hanterad instans. Den förlitar sig på automatiskt skapade geo-replikerade säkerhets kopieringar med en återställnings punkt mål på upp till 1 timme och en beräknad återställnings tid på upp till 12 timmar. Det garanterar inte att mål regionen har kapaciteten att återställa dina databaser efter ett regionalt avbrott, eftersom en kraftig ökning av efter frågan är sannolik. Om programmet använder relativt små databaser och inte är kritiskt för verksamheten, är geo-Restore en lämplig lösning för katastrof återställning. 
>
> För verksamhets kritiska program som kräver stora databaser och som måste garantera affärs kontinuitet, använder du [grupper för automatisk redundans](auto-failover-group-overview.md). Det erbjuder ett mycket lägre drift-och återställnings mål och kapaciteten är alltid garanterad. 
>
> Mer information om val av affärs kontinuitet finns i [Översikt över affärs kontinuitet](business-continuity-high-availability-disaster-recover-hadr-overview.md).

## <a name="programmatic-recovery-using-automated-backups"></a>Program återställning med automatiserade säkerhets kopieringar

Du kan också använda Azure PowerShell eller REST API för återställning. I följande tabeller beskrivs en uppsättning kommandon som är tillgängliga.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av SQL Database-och SQL-hanterad instans, men all framtida utveckling är för AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i Azure Resource Manager modulerna är i ett bra GRADS identiska.

#### <a name="sql-database"></a>SQL Database

Information om hur du återställer en fristående databas eller en databas finns i [restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Cmdlet | Beskrivning |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Hämtar en eller flera databaser. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Hämtar en borttagen databas som du kan återställa. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Hämtar en geo-redundant säkerhetskopia av en databas. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Återställer en databas. |

  > [!TIP]
  > Ett exempel på ett PowerShell-skript som visar hur du utför en tidpunkts återställning av en databas finns i [återställa en databas med hjälp av PowerShell](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>SQL-hanterad instans

Information om hur du återställer en hanterad instans databas finns i [restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Cmdlet | Beskrivning |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Hämtar en eller flera hanterade instanser. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Hämtar en instans databas. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Återställer en instans databas. |

### <a name="rest-api"></a>REST-API

Så här återställer du en databas med hjälp av REST API:

| API | Beskrivning |
| --- | --- |
| [REST (createMode = återställning)](/rest/api/sql/databases) |Återställer en databas. |
| [Hämta databas status för att skapa eller uppdatera](/rest/api/sql/operations) |Returnerar status under en återställnings åtgärd. |

### <a name="azure-cli"></a>Azure CLI

#### <a name="sql-database"></a>SQL Database

Information om hur du återställer en databas med hjälp av Azure CLI finns i [AZ SQL DB Restore](/cli/azure/sql/db#az-sql-db-restore).

#### <a name="sql-managed-instance"></a>SQL-hanterad instans

Om du vill återställa en hanterad instans databas med hjälp av Azure CLI, se [AZ SQL EXTEXTB Restore](/cli/azure/sql/midb#az-sql-midb-restore).

## <a name="summary"></a>Sammanfattning

Automatiska säkerhets kopieringar skyddar dina databaser från användar-och program fel, oavsiktlig databas borttagning och långvariga avbrott. Den här inbyggda funktionen är tillgänglig för alla tjänst nivåer och beräknings storlekar.

## <a name="next-steps"></a>Nästa steg

- [Översikt över affärskontinuitet](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- [SQL Database automatiserade säkerhets kopieringar](automated-backups-overview.md)
- [Långsiktig kvarhållning](long-term-retention-overview.md)
- Mer information om snabbare återställnings alternativ finns i [Active geo-replikering](active-geo-replication-overview.md) eller [grupper för automatisk redundans](auto-failover-group-overview.md).