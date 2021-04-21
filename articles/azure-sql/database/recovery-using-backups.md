---
title: Återställa en databas från en säkerhetskopia
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Lär dig mer om återställning till tidpunkt, vilket gör att du kan återställa en databas i Azure SQL Database eller en instans i Azure SQL Managed Instance upp till 35 dagar.
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
ms.openlocfilehash: 670176d7478ddab3d17e15526df512dfa7e99fd4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762091"
---
# <a name="recover-using-automated-database-backups---azure-sql-database--sql-managed-instance"></a>Återställa med hjälp av automatiserade databassäkerhetskopior – Azure SQL Database & SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Följande alternativ är tillgängliga för databasåterställning med hjälp av [automatiserade databassäkerhetskopior.](automated-backups-overview.md) Du kan:

- Skapa en ny databas på samma server som återställts till en angiven tidpunkt inom kvarhållningsperioden.
- Skapa en databas på samma server som återställts till borttagningstiden för en borttagen databas.
- Skapa en ny databas på valfri server i samma region som återställts till den punkt där de senaste säkerhetskopiorna har återställts.
- Skapa en ny databas på valfri server i någon annan region som återställts till den punkt där de senaste replikerade säkerhetskopiorna har återställts.

Om du har konfigurerat [långsiktig kvarhållning av säkerhetskopior](long-term-retention-overview.md)kan du även skapa en ny databas från valfri säkerhetskopia för långsiktig kvarhållning på valfri server.

> [!IMPORTANT]
> Du kan inte skriva över en befintlig databas under återställningen.

När du använder tjänstnivån Standard eller Premium kan databasåterställningen medföra en extra lagringskostnad. Den extra kostnaden uppstår när den maximala storleken på den återställda databasen är större än mängden lagringsutrymme som ingår i måldatabasens tjänstnivå och prestandanivå. Prisinformation om extra lagring finns på SQL Database [sidan med priser.](https://azure.microsoft.com/pricing/details/sql-database/) Om den faktiska mängden använt utrymme är mindre än mängden lagringsutrymme som ingår kan du undvika den här extra kostnaden genom att ange den maximala databasstorleken till den inkluderade mängden.

## <a name="recovery-time"></a>Återställningstid

Återställningstiden för att återställa en databas med hjälp av automatiserade databassäkerhetskopior påverkas av flera faktorer:

- Databasens storlek.
- Databasens beräkningsstorlek.
- Antalet berörda transaktionsloggar.
- Mängden aktivitet som måste spelas upp igen för att återställa till återställningspunkten.
- Nätverksbandbredden om återställningen är till en annan region.
- Antalet samtidiga återställningsbegäranden som bearbetas i målregionen.

För en stor eller mycket aktiv databas kan återställningen ta flera timmar. Efter ett långvarigt avbrott i en region kan ett stort antal förfrågningar om geo-återställning för haveriberedskap initieras ungefär samtidigt. Om många sådana förfrågningar körs samtidigt kan återställningstiden för enskilda databaser öka. De flesta databasåterställningar slutförs på mindre än 12 timmar.

Det finns begränsningar för antalet samtidiga återställningsförfrågningar för enskilda prenumerationer. De här begränsningarna gäller för alla kombinationer av återställningar till en tidpunkt, geo-återställningar och återställningar från säkerhetskopior med långsiktig kvarhållning.

| **Distributionsalternativ** | **Max antal samtidiga förfrågningar som bearbetas** | **Max antal samtidiga förfrågningar som skickas** |
| :--- | --: | --: |
|**Enstaka databas (per prenumeration)**|30|100|
|**Elastisk pool (per pool)**|4|2000|


Det finns ingen inbyggd metod för att återställa hela servern. Ett exempel på hur du utför den här uppgiften finns i [Azure SQL Database: Fullständig serveråterställning](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666).

> [!IMPORTANT]
> Om du vill återställa med hjälp av automatiska säkerhetskopieringar måste du vara medlem i rollen SQL Server-deltagare eller SQL Managed Instance-deltagare (beroende på återställningsmålet) i prenumerationen, eller så måste du vara prenumerationsägare. Mer information finns i [Azure RBAC: Inbyggda roller.](../../role-based-access-control/built-in-roles.md) Du kan återställa med hjälp av Azure Portal, PowerShell eller REST API. Du kan inte använda Transact-SQL.

## <a name="point-in-time-restore"></a>Återställning från tidpunkt

Du kan återställa en fristående databas, pooldatabas eller instansdatabas till en tidigare tidpunkt med hjälp av Azure Portal, [PowerShell](/powershell/module/az.sql/restore-azsqldatabase) [eller REST API](/rest/api/sql/databases/createorupdate#creates-a-database-from-pointintimerestore.). Begäran kan ange valfri tjänstnivå eller beräkningsstorlek för den återställda databasen. Kontrollera att du har tillräckligt med resurser på den server som du återställer databasen till. 

När återställningen är klar skapas en ny databas på samma server som den ursprungliga databasen. Den återställda databasen debiteras enligt normala priser, baserat på tjänstnivå och beräkningsstorlek. Du debiteras inte förrän databasåterställningen är klar.

Vanligtvis återställer du en databas till en tidigare punkt i återställningssyfte. Du kan behandla den återställda databasen som en ersättning för den ursprungliga databasen eller använda den som en datakälla för att uppdatera den ursprungliga databasen.

- **Databasersättning**

  Om du vill att den återställda databasen ska ersätta den ursprungliga databasen bör du ange den ursprungliga databasens beräkningsstorlek och tjänstnivå. Du kan sedan byta namn på den ursprungliga databasen och ge den återställda databasen det ursprungliga namnet med hjälp av [kommandot ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) i T-SQL.

- **Dataräddning**

  Om du planerar att hämta data från den återställda databasen för att återställa från en användare eller ett programfel måste du skriva och köra ett dataåterställningsskript som extraherar data från den återställda databasen och som gäller för den ursprungliga databasen. Återställningen kan ta lång tid att slutföra, men återställningsdatabasen visas i databaslistan under hela återställningsprocessen. Om du tar bort databasen under återställningen avbryts återställningen och du debiteras inte för databasen som inte slutförde återställningen.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Återställning till tidpunkt med hjälp av Azure Portal

Du kan återställa en enkel databas eller instansdatabas till en tidpunkt från översiktsbladet för den databas som du vill återställa i Azure Portal.

#### <a name="sql-database"></a>SQL Database

Om du vill återställa en databas till en tidpunkt med Azure Portal databasen öppnar du översiktssidan för databasen och **väljer Återställ** i verktygsfältet. Välj säkerhetskopieringskälla och välj den tidpunkt då en ny databas ska skapas.

  ![Skärmbild av alternativ för databasåterställning för SQL Database.](./media/recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>SQL-hanterad instans

Om du vill återställa en hanterad instansdatabas till en tidpunkt med Azure Portal databasen öppnar du översiktssidan för databasen och väljer **Återställ** i verktygsfältet. Välj den tidpunkt för säkerhetskopiering från vilken en ny databas ska skapas.

  ![Skärmbild av databasåterställningsalternativ för SQL Managed Instance.](./media/recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Information om hur du programmässigt återställer en databas från en säkerhetskopia finns [i Programmatisk återställning med hjälp av automatiserade säkerhetskopieringar.](recovery-using-backups.md)

## <a name="deleted-database-restore"></a>Återställning av borttagna databaser

Du kan återställa en borttagen databas till borttagningstiden, eller en tidigare tidpunkt, på samma server eller samma hanterade instans. Du kan göra detta via Azure Portal, [PowerShell](/powershell/module/az.sql/restore-azsqldatabase)eller [REST (createMode=Restore).](/rest/api/sql/databases/createorupdate) Du återställer en borttagna databas genom att skapa en ny databas från säkerhetskopian.

> [!IMPORTANT]
> Om du tar bort en server eller hanterad instans tas även alla dess databaser bort och kan inte återställas. Du kan inte återställa en borttagna server eller hanterad instans.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Databasåterställning har tagits bort med hjälp av Azure Portal

Du återställer borttagna databaser från Azure Portal från servern eller den hanterade instansresursen.

> [!TIP]
> Det kan ta flera minuter innan nyligen  borttagna databaser visas på sidan Borttagna databaser i Azure Portal eller när borttagna databaser [visas programmatiskt.](#programmatic-recovery-using-automated-backups)

#### <a name="sql-database"></a>SQL Database

Om du vill återställa en borttagen databas till borttagningstiden med hjälp av Azure Portal öppnar du översiktssidan för servern och väljer **Borttagna databaser.** Välj en borttagna databas som du vill återställa och ange namnet på den nya databasen som ska skapas med data som återställs från säkerhetskopian.

  ![Skärmbild av återställning av borttagna databaser](./media/recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>SQL-hanterad instans

Om du vill återställa en hanterad databas Azure Portal databasen öppnar du översiktssidan för den hanterade instansen och väljer **Borttagna databaser.** Välj en borttagna databas som du vill återställa och ange namnet på den nya databasen som ska skapas med data som återställs från säkerhetskopian.

  ![Skärmbild av återställning av borttagna Azure SQL Managed Instance databasen](./media/recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Databasåterställning har tagits bort med hjälp av PowerShell

Använd följande exempelskript för att återställa en borttagna databas för antingen SQL Database eller SQL Managed Instance med hjälp av PowerShell.

#### <a name="sql-database"></a>SQL Database

Ett PowerShell-exempelskript som visar hur du återställer en borttagna databas i Azure SQL Database finns i [Återställa en databas med Hjälp av PowerShell.](scripts/restore-database-powershell.md)

#### <a name="sql-managed-instance"></a>SQL-hanterad instans

Ett PowerShell-exempelskript som visar hur du återställer en borttagna instansdatabas finns i [Återställa borttagna instansdatabaser med PowerShell](../managed-instance/point-in-time-restore.md#restore-a-deleted-database)

> [!TIP]
> Information om hur du programmässigt återställer en borttagna databas finns [i Programmässigt utföra återställning med hjälp av automatiska säkerhetskopieringar.](recovery-using-backups.md)

## <a name="geo-restore"></a>Geo-återställning

> [!IMPORTANT]
> Geo-återställning är endast tillgängligt för SQL-databaser eller hanterade instanser som konfigurerats med geo-redundant lagring [av säkerhetskopior.](automated-backups-overview.md#backup-storage-redundancy)

Du kan återställa en databas på valfri SQL Database server eller en instansdatabas på valfri hanterad instans i valfri Azure-region från de senaste geo-replikerade säkerhetskopiorna. Geo-återställning använder en geo-replikerad säkerhetskopia som källa. Du kan begära geo-återställning även om databasen eller datacentret inte är tillgänglig på grund av ett avbrott.

Geo-återställning är standardalternativet för återställning när databasen inte är tillgänglig på grund av en incident i värdregionen. Du kan återställa databasen till en server i valfri annan region. Det finns en fördröjning mellan när en säkerhetskopia tas och när den geo-replikeras till en Azure-blob i en annan region. Det innebär att den återställda databasen kan vara upp till en timme bakom den ursprungliga databasen. Följande bild visar en databasåterställning från den senaste tillgängliga säkerhetskopian i en annan region.

![Bild av geo-återställning](./media/recovery-using-backups/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Geo-återställning med hjälp av Azure Portal

Från Azure Portal skapar du en ny enkel eller hanterad instansdatabas och väljer en tillgänglig säkerhetskopia för geo-återställning. Den nyligen skapade databasen innehåller geo-återställda säkerhetskopierade data.

#### <a name="sql-database"></a>SQL Database

Om du vill geo-återställa en enkel databas från Azure Portal i den region och server du väljer följer du dessa steg:

1. Från **Instrumentpanel** väljer du **Lägg**  >  **till SQL Database**. På **fliken Grundläggande anger** du nödvändig information.
2. Välj **Ytterligare inställningar.**
3. För **Använd befintliga data väljer** du **Säkerhetskopiera.**
4. För **Säkerhetskopiering** väljer du en säkerhetskopia i listan över tillgängliga geo-återställningssäkerhetskopior.

    ![Skärmbild av alternativ SQL Database Skapa SQL Database inställningar](./media/recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Slutför processen med att skapa en ny databas från säkerhetskopian. När du skapar en databas i Azure SQL Database innehåller den den återställda geo-återställningssäkerhetskopia.

#### <a name="sql-managed-instance"></a>SQL-hanterad instans

Om du vill geo-återställa en hanterad instansdatabas från Azure Portal till en befintlig hanterad instans i valfri region väljer du en hanterad instans där du vill att en databas ska återställas. Följ de här stegen:

1. Välj **Ny databas.**
2. Ange ett önskat databasnamn.
3. Under **Använd befintliga data väljer** du **Säkerhetskopiera.**
4. Välj en säkerhetskopia i listan över tillgängliga geo-återställningssäkerhetskopior.

    ![Skärmbild av alternativ för ny databas](./media/recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Slutför processen med att skapa en ny databas. När du skapar instansdatabasen innehåller den den återställda geo-återställningssäkerhetskopia.

### <a name="geo-restore-by-using-powershell"></a>Geo-återställning med hjälp av PowerShell

#### <a name="sql-database"></a>SQL Database

Ett PowerShell-skript som visar hur du utför geo-återställning för en enkel databas finns i Använda PowerShell för att återställa en enkel databas till [en tidigare tidpunkt.](scripts/restore-database-powershell.md)

#### <a name="sql-managed-instance"></a>SQL-hanterad instans

Ett PowerShell-skript som visar hur du utför geo-återställning för en hanterad instansdatabas finns i Använda PowerShell för att återställa en hanterad instansdatabas till [en annan geo-region.](../managed-instance/scripts/restore-geo-backup.md)

### <a name="geo-restore-considerations"></a>Överväganden för geo-återställning

Du kan inte utföra en återställning till en tidpunkt på en geo-sekundär databas. Du kan bara göra det på en primär databas. Detaljerad information om hur du använder geo-återställning för att återställa efter ett avbrott finns [i Återställa efter ett avbrott.](../../key-vault/general/disaster-recovery-guidance.md)

> [!IMPORTANT]
> Geo-återställning är den mest grundläggande haveriberedskapslösningen i SQL Database och SQL Managed Instance. Den förlitar sig på automatiskt skapade geo-replikerade säkerhetskopior med ett mål för återställningspunkt (RPO) upp till 1 timme och en uppskattad återställningstid på upp till 12 timmar. Det garanterar inte att målregionen har kapacitet att återställa dina databaser efter ett regionalt avbrott, eftersom det är troligt att efterfrågan ökar kraftigt. Om ditt program använder relativt små databaser och inte är kritiskt för verksamheten är geo-återställning en lämplig katastrofåterställningslösning. 
>
> För verksamhetskritiska program som kräver stora databaser och som måste garantera affärskontinu kontinuitet använder [du automatiska redundansgrupper.](auto-failover-group-overview.md) Det ger ett mycket lägre mål för återställningstid och återställningstid, och kapaciteten garanteras alltid. 
>
> Mer information om alternativ för affärskontinuhet finns i [Översikt över affärskontinuhet.](business-continuity-high-availability-disaster-recover-hadr-overview.md)

## <a name="programmatic-recovery-using-automated-backups"></a>Programmatisk återställning med automatiska säkerhetskopieringar

Du kan också använda Azure PowerShell eller REST API för återställning. Följande tabeller beskriver uppsättningen kommandon som är tillgängliga.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modulen stöds fortfarande av SQL Database SQL Managed Instance, men all framtida utveckling är till för Az.Sql-modulen. Dessa cmdlets finns i [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az-modulen och Azure Resource Manager moduler är i stor utsträckning identiska.

#### <a name="sql-database"></a>SQL Database

Information om hur du återställer en fristående databas eller en pooldatabas finns [i Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Cmdlet | Beskrivning |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Hämtar en eller flera databaser. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Hämtar en borttagen databas som du kan återställa. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Hämtar en geo-redundant säkerhetskopia av en databas. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Återställer en databas. |

  > [!TIP]
  > Ett PowerShell-exempelskript som visar hur du utför en återställning till en tidpunkt av en databas finns i Återställa en databas med [hjälp av PowerShell.](scripts/restore-database-powershell.md)

#### <a name="sql-managed-instance"></a>SQL-hanterad instans

Information om hur du återställer en hanterad instansdatabas [finns i Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Cmdlet | Beskrivning |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Hämtar en eller flera hanterade instanser. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Hämtar en instansdatabas. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Återställer en instansdatabas. |

### <a name="rest-api"></a>REST-API

Så här återställer du en databas med hjälp av REST API:

| API | Beskrivning |
| --- | --- |
| [REST (createMode=Recovery)](/rest/api/sql/databases) |Återställer en databas. |
| [Hämta status för skapa eller uppdatera databas](/rest/api/sql/operations) |Returnerar statusen under en återställningsåtgärd. |

### <a name="azure-cli"></a>Azure CLI

#### <a name="sql-database"></a>SQL Database

Information om hur du återställer en databas med hjälp av Azure CLI finns [i az sql db restore](/cli/azure/sql/db#az_sql_db_restore).

#### <a name="sql-managed-instance"></a>SQL-hanterad instans

Information om hur du återställer en hanterad instansdatabas med hjälp av Azure CLI finns [i az sql midb restore](/cli/azure/sql/midb#az_sql_midb_restore).

## <a name="summary"></a>Sammanfattning

Automatiska säkerhetskopieringar skyddar dina databaser mot användar- och programfel, oavsiktlig databasborttagning och långvariga avbrott. Den här inbyggda funktionen är tillgänglig för alla tjänstnivåer och beräkningsstorlekar.

## <a name="next-steps"></a>Nästa steg

- [Översikt över affärskontinuitet](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- [SQL Database automatiska säkerhetskopieringar](automated-backups-overview.md)
- [Långsiktig kvarhållning](long-term-retention-overview.md)
- Mer information om snabbare återställningsalternativ finns i [Aktiv geo-replikering](active-geo-replication-overview.md) eller [Automatiska redundansgrupper.](auto-failover-group-overview.md)
