---
title: Skala enkla databasresurser
description: Den här artikeln beskriver hur du skalar beräknings- och lagringsresurser som är tillgängliga för en enkel databas i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1, references_regions
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 04/09/2021
ms.openlocfilehash: ae1b3cc41d709c28ba517d672eb98cb60a837a8d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779083"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Skala enkla databasresurser i Azure SQL Database

I den här artikeln beskrivs hur du skalar de beräknings- och lagringsresurser som är Azure SQL Database på den etablerade beräkningsnivån. Du kan också använda [den serverlösa beräkningsnivån](serverless-tier-overview.md) för automatisk skalning och faktureras per sekund för beräkning som används.

När du har valde antalet virtuella kärnor eller DPU:er kan du skala upp eller ned en enkel databas dynamiskt baserat på den faktiska upplevelsen med hjälp av:

* [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database)
* [Azure-portalen](single-database-manage.md#the-azure-portal)
* [PowerShell](/powershell/module/az.sql/set-azsqldatabase)
* [Azure CLI](/cli/azure/sql/db#az_sql_db_update)
* [REST-API](/rest/api/sql/databases/update)


Följande videoklipp visar hur du dynamiskt ändrar tjänstnivå och beräkningsstorlek för att öka tillgängliga DPU:er för en enskild databas.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> I vissa fall kan du behöva krympa en databas för att frigöra outnyttjat utrymme. Mer information finns i [Hantera filutrymme i Azure SQL Database](file-space-manage.md).

## <a name="impact"></a>Påverkan

Att ändra tjänstnivån eller beräkningsstorleken för omfattar huvudsakligen den tjänst som utför följande steg:

1. Skapa en ny beräkningsinstans för databasen. 

    En ny beräkningsinstans skapas med den begärda tjänstnivån och beräkningsstorleken. För vissa kombinationer av ändringar av tjänstnivå och beräkningsstorlek måste en replik av databasen skapas i den nya beräkningsinstansen, vilket inbegriper kopiering av data och kan starkt påverka den totala svarstiden. Oavsett detta förblir databasen online under det här steget och anslutningarna fortsätter att dirigeras till databasen i den ursprungliga beräkningsinstansen.

2. Växla routning av anslutningar till en ny beräkningsinstans.

    Befintliga anslutningar till databasen i den ursprungliga beräkningsinstansen tas bort. Alla nya anslutningar upprättas till databasen i den nya beräkningsinstansen. För vissa kombinationer av ändringar av tjänstnivå och beräkningsstorlek frånkopplas och återansluts databasfilerna under växeln.  Oavsett vilket kan växeln resultera i ett kort tjänstavbrott när databasen i allmänhet är otillgänglig i mindre än 30 sekunder och ofta bara under några sekunder. Om det finns långvariga transaktioner som körs när anslutningar avbryts kan varaktigheten för det här steget ta längre tid för att återställa avbrutna transaktioner. [Accelererad databasåterställning](../accelerated-database-recovery.md) kan minska påverkan från att avbryta långvariga transaktioner.

> [!IMPORTANT]
> Inga data går förlorade under något steg i arbetsflödet. Kontrollera att du har [](troubleshoot-common-connectivity-issues.md) implementerat viss omprövningslogik i de program och komponenter som använder Azure SQL Database medan tjänstnivån ändras.

## <a name="latency"></a>Svarstid

Den uppskattade svarstiden för att ändra tjänstnivå, skala beräkningsstorleken för en enkel databas eller elastisk pool, flytta en databas in/ut från en elastisk pool eller flytta en databas mellan elastiska pooler parametriseras på följande sätt:

|Tjänstenivå|Enkel enkel databas,</br>Standard (S0-S1)|Grundläggande elastisk pool,</br>Standard (S2-S12), </br>Generell användning enkel databas eller elastisk pool|Premium eller Affärskritisk enkel databas eller elastisk pool|Hyperskala
|:---|:---|:---|:---|:---|
|**Enkel enkel databas, </br> Standard (S0-S1)**|&bull;&nbsp;Konstant tidsfördröjning oberoende av använt utrymme</br>&bull;&nbsp;Normalt mindre än 5 minuter|&bull;&nbsp;Svarstiden är proportionell mot databasutrymmet som används på grund av datakopiering</br>&bull;&nbsp;Normalt mindre än 1 minut per GB använt utrymme|&bull;&nbsp;Svarstiden är proportionell mot databasutrymmet som används på grund av datakopiering</br>&bull;&nbsp;Normalt mindre än 1 minut per GB använt utrymme|&bull;&nbsp;Svarstiden är proportionell mot databasutrymmet som används på grund av datakopiering</br>&bull;&nbsp;Vanligtvis mindre än 1 minut per GB använt utrymme|
|**Grundläggande elastisk pool, </br> Standard (S2-S12), </br> Generell användning enkel databas eller elastisk pool**|&bull;&nbsp;Svarstid som är proportionell mot databasutrymmet som används på grund av datakopiering</br>&bull;&nbsp;Normalt mindre än 1 minut per GB använt utrymme|&bull;&nbsp;För enskilda databaser, konstant tidsfördröjning oberoende av använt utrymme</br>&bull;&nbsp;Normalt mindre än 5 minuter för enskilda databaser</br>&bull;&nbsp;För elastiska pooler är det proportionellt mot antalet databaser|&bull;&nbsp;Svarstid som är proportionell mot databasutrymmet som används på grund av datakopiering</br>&bull;&nbsp;Normalt mindre än 1 minut per GB använt utrymme|&bull;&nbsp;Svarstid som är proportionell mot databasutrymmet som används på grund av datakopiering</br>&bull;&nbsp;Normalt mindre än 1 minut per GB använt utrymme|
|**Premium eller Affärskritisk enkel databas eller elastisk pool**|&bull;&nbsp;Svarstid som är proportionell mot databasutrymmet som används på grund av datakopiering</br>&bull;&nbsp;Normalt mindre än 1 minut per GB använt utrymme|&bull;&nbsp;Svarstid som är proportionell mot databasutrymmet som används på grund av datakopiering</br>&bull;&nbsp;Normalt mindre än 1 minut per GB använt utrymme|&bull;&nbsp;Svarstid som är proportionell mot databasutrymmet som används på grund av datakopiering</br>&bull;&nbsp;Normalt mindre än 1 minut per GB använt utrymme|&bull;&nbsp;Svarstid som är proportionell mot databasutrymmet som används på grund av datakopiering</br>&bull;&nbsp;Normalt mindre än 1 minut per GB använt utrymme|
|**Hyperskala**|Saknas|Saknas|Saknas|&bull;&nbsp;Konstant tidsfördröjning oberoende av använt utrymme</br>&bull;&nbsp;Normalt mindre än 2 minuter|

> [!NOTE]
> För Standard (S2-S12) och Generell användning-databaser är svarstiden för att flytta en databas in/ut från en elastisk pool eller mellan elastiska pooler proportionell mot databasstorleken om databasen använder Premium-filresurs[(PFS)](../../storage/files/storage-files-introduction.md)lagring.
>
> Kör följande fråga i kontexten för databasen för att avgöra om en databas använder PFS-lagring. Om värdet i kolumnen AccountType är `PremiumFileStorage` eller `PremiumFileStorage-ZRS` använder databasen PFS-lagring.

```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!NOTE]
> Den zonredundanta egenskapen förblir densamma som standard när du skalar från Affärskritisk till Generell användning nivån. Svarstiden för den här nedgradering när zonredundans är aktiverad samt svarstiden för att växla till zonredundans för Generell användning-nivån är proportionell mot databasens storlek.

> [!TIP]
> Information om hur du övervakar pågående åtgärder finns i: Hantera åtgärder med [SQL REST API,](/rest/api/sql/operations/list)Hantera åtgärder med [CLI,](/cli/azure/sql/db/op)Övervaka åtgärder med [T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) och dessa två PowerShell-kommandon: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) och [Stop-AzSqlDatabaseActivity.](/powershell/module/az.sql/stop-azsqldatabaseactivity)

## <a name="cancelling-changes"></a>Avbryta ändringar

En åtgärd för ändring av tjänstnivå eller beräkningsskalning kan avbrytas.

### <a name="the-azure-portal"></a>Azure Portal

På bladet databasöversikt går du **till Meddelanden** och klickar på panelen som anger att en pågående åtgärd pågår:

![Pågående drift](./media/single-database-scale/ongoing-operations.png)

Klicka sedan på knappen Avbryt **den här åtgärden.**

![Avbryta pågående åtgärd](./media/single-database-scale/cancel-ongoing-operation.png)

### <a name="powershell"></a>PowerShell

Från en PowerShell-kommandotolk `$resourceGroupName` anger du , och och kör sedan följande `$serverName` `$databaseName` kommando:

```azurecli
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

## <a name="additional-considerations"></a>Annat som är bra att tänka på

- Om du uppgraderar till en högre tjänstnivå eller beräkningsstorlek ökar inte databasens maxstorlek om du inte uttryckligen anger en större storlek (maxstorlek).
- Om du vill nedgradera en databas måste det databasutrymme som används vara mindre än den största tillåtna storleken på måltjänstnivån och beräkningsstorleken.
- När du nedgraderar från **Premium** till **Standard-nivån** gäller en extra lagringskostnad om både (1) databasens maximala storlek stöds i målberäkningsstorleken och (2) den maximala storleken överskrider den inkluderade lagringsmängden för målberäkningsstorleken. Om till exempel en P1-databas med en maximal storlek på 500 GB är nedsbitad till S3 så tillämpas en extra lagringskostnad eftersom S3 stöder en maximal storlek på 1 TB och den inkluderade lagringsmängden bara är 250 GB. Det extra lagringsutrymmet är alltså 500 GB – 250 GB = 250 GB. Priser för extra lagring finns i [Azure SQL Database priser.](https://azure.microsoft.com/pricing/details/sql-database/) Om den faktiska mängden utrymme som används är mindre än den inkluderade lagringsmängden kan du undvika den här extra kostnaden genom att minska databasens maximala storlek till den inkluderade mängden.
- När du uppgraderar en databas med [geo-replikering](active-geo-replication-configure-portal.md) aktiverat uppgraderar du dess sekundära databaser till önskad tjänstnivå och beräkningsstorlek innan du uppgraderar den primära databasen (allmänna riktlinjer för bästa prestanda). När du uppgraderar till en annan utgåva är det ett krav att den sekundära databasen uppgraderas först.
- När du nedgraderar en databas med [geo-replikering](active-geo-replication-configure-portal.md) aktiverat nedgraderar du dess primära databaser till önskad tjänstnivå och beräkningsstorlek innan du nedgraderar den sekundära databasen (allmänna riktlinjer för bästa prestanda). När du nedgraderar till en annan utgåva är det ett krav att den primära databasen nedgraderas först.
- Erbjudandena för återställningstjänsterna är olika för de olika tjänstnivåerna. Om du nedgraderar till **Basic-nivån finns** det en lägre kvarhållningsperiod för säkerhetskopior. Se [Azure SQL Database säkerhetskopieringar.](automated-backups-overview.md)
- De nya egenskaperna för databasen tillämpas inte förrän ändringarna har slutförts.
- När datakopiering krävs för att skala en databas (se Svarstid [)](#latency)vid ändring av tjänstnivån kan hög resursanvändning som är samtidig till skalningsåtgärden orsaka längre skalningstider. Med [Accelererad databasåterställning (ADR)](/sql/relational-databases/accelerated-database-recovery-concepts)är återställning av långvariga transaktioner inte en betydande källa till fördröjning, men hög samtidig resursanvändning kan lämna mindre resurser för beräkning, lagring och nätverksbandbredd för skalning, särskilt för mindre beräkningsstorlekar.

## <a name="billing"></a>Fakturering

Du debiteras för varje timme som en databas finns med den högsta tjänstnivån + beräkningsstorleken som tillämpades under den timmen, oavsett användning eller om databasen var aktiv i mindre än en timme. Om du till exempel skapar en enkel databas och tar bort den fem minuter senare återspeglar fakturan en avgift för en databastimme.

## <a name="change-storage-size"></a>Ändra lagringsstorlek

### <a name="vcore-based-purchasing-model"></a>Köpmodell baserad på virtuell kärna

- Lagring kan etableras upp till den maximala storleksgränsen för datalagring med hjälp av steg om 1 GB. Den minsta konfigurerbara datalagringen är 1 GB. Information om maxstorleksgränser för datalagring i varje tjänstmål finns i dokumentationssidor för resursgräns för resursgränser för enskilda databaser med köpmodellen [vCore](resource-limits-vcore-single-databases.md) och Resursgränser för enskilda databaser som använder [köpmodellen DTU.](resource-limits-dtu-single-databases.md)
- Datalagring för en enkel databas kan etableras genom att öka eller minska den maximala storleken med [hjälp av Azure Portal,](https://portal.azure.com) [Transact-SQL,](/sql/t-sql/statements/alter-database-transact-sql#examples-1) [PowerShell,](/powershell/module/az.sql/set-azsqldatabase) [Azure CLI](/cli/azure/sql/db#az_sql_db_update) [eller REST API](/rest/api/sql/databases/update). Om värdet för maximal storlek anges i byte måste det vara en multipel på 1 GB (1073741824 byte).
- Mängden data som kan lagras i datafilerna för en databas begränsas av den maximala storleken för konfigurerad datalagring. Utöver det lagringsutrymmet allokerar Azure SQL Database automatiskt 30 % mer lagringsutrymme som ska användas för transaktionsloggen.
- Azure SQL Database automatiskt 32 GB per vCore för `tempdb` databasen. `tempdb` finns på den lokala SSD-lagringen på alla tjänstnivåer.
- Priset för lagring för en enkel databas eller en elastisk pool är summan av lagringsbeloppen för datalagring och transaktionslogg multiplicerat med lagringsenhetspriset för tjänstnivån. Kostnaden för `tempdb` ingår i priset. Mer information om lagringspriset finns [i Azure SQL Database priser.](https://azure.microsoft.com/pricing/details/sql-database/)

> [!IMPORTANT]
> I vissa fall kan du behöva krympa en databas för att frigöra outnyttjat utrymme. Mer information finns i [Hantera filutrymme i Azure SQL Database](file-space-manage.md).

### <a name="dtu-based-purchasing-model"></a>Köpmodell baserad på DTU

- DTU-priset för en enkel databas inkluderar en viss mängd lagring utan extra kostnad. Extra lagringsutrymme utöver det inkluderade beloppet kan etableras för en extra kostnad upp till den maximala storleksgränsen i steg om 250 GB upp till 1 TB och sedan i steg om 256 GB över 1 TB. Information om inkluderade lagringsbelopp och maxstorleksgränser [finns i Enkel databas: Lagringsstorlekar och beräkningsstorlekar.](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes)
- Extra lagring för en enkel databas kan etableras genom att öka maxstorleken med hjälp av Azure Portal, [Transact-SQL,](/sql/t-sql/statements/alter-database-transact-sql#examples-1) [PowerShell,](/powershell/module/az.sql/set-azsqldatabase) [Azure CLI](/cli/azure/sql/db#az_sql_db_update) [eller REST API](/rest/api/sql/databases/update).
- Priset för extra lagring för en enkel databas är den extra lagringsmängden multiplicerat med det extra enhetspriset för lagring på tjänstnivån. Mer information om priset för extra lagringsutrymme finns i [Azure SQL Database priser.](https://azure.microsoft.com/pricing/details/sql-database/)

> [!IMPORTANT]
> I vissa fall kan du behöva krympa en databas för att frigöra outnyttjat utrymme. Mer information finns i [Hantera filutrymme i Azure SQL Database](file-space-manage.md).

### <a name="geo-replicated-database"></a>Geo-replikerad databas

Om du vill ändra databasstorleken för en replikerad sekundär databas ändrar du storleken på den primära databasen. Den här ändringen replikeras sedan och implementeras även på den sekundära databasen.

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>Begränsningar för P11 och P15 när maxstorleken är större än 1 TB

Mer än 1 TB lagringsutrymme på Premium-nivån är för närvarande tillgängligt i alla regioner utom: Kina, östra, Kina, norra, Tyskland, centrala och Tyskland, nordöstra. I dessa regioner är det maximala lagringsutrymmet på Premium-nivån begränsat till 1 TB. Följande överväganden och begränsningar gäller för P11- och P15-databaser med en maximal storlek som är större än 1 TB:

- Om den maximala storleken för en P11- eller P15-databas någonsin har angetts till ett värde som är större än 1 TB, kan den bara återställas eller kopieras till en P11- eller P15-databas.  Därefter kan databasen skalas om till en annan beräkningsstorlek förutsatt att mängden allokerat utrymme vid tidpunkten för omskalningsåtgärden inte överskrider maxgränsen för den nya beräkningsstorleken.
- För scenarier med aktiv geo-replikering:
  - Konfigurera en geo-replikeringsrelation: Om den primära databasen är P11 eller P15 måste den sekundära (dvs. ) också vara P11 eller P15. Lägre beräkningsstorlek avvisas som de andra eftersom de inte kan stödja mer än 1 TB.
  - Uppgradera den primära databasen i en geo-replikeringsrelation: Om du ändrar den maximala storleken till mer än 1 TB på en primär databas utlöses samma ändring på den sekundära databasen. Båda uppgraderingarna måste lyckas för att ändringen av den primära ska gälla. Regionbegränsningar för alternativet mer än 1 TB gäller. Om den sekundära finns i en region som inte stöder mer än 1 TB uppgraderas inte den primära.
- Användning av Import/Export-tjänsten för inläsning av P11/P15-databaser med mer än 1 TB stöds inte. Använd SqlPackage.exe för [att importera](database-import.md) [och exportera](database-export.md) data.

## <a name="next-steps"></a>Nästa steg

Information om övergripande resursbegränsningar [finns Azure SQL Database](resource-limits-vcore-single-databases.md) resursbegränsningar baserade på virtuella kärnor – enskilda databaser och [Azure SQL Database DTU-baserade resursbegränsningar – enskilda databaser.](resource-limits-dtu-single-databases.md)
