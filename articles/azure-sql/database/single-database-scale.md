---
title: Skala enkla databasresurser
description: Den här artikeln beskriver hur du skalar beräknings-och lagrings resurserna som är tillgängliga för en enskild databas i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1, references_regions
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 02/22/2021
ms.openlocfilehash: 5852899175f9cc9f2725b875c6e1ce9fd682768d
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625279"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Skala enkla databasresurser i Azure SQL Database

Den här artikeln beskriver hur du skalar beräknings-och lagrings resurserna som är tillgängliga för en Azure SQL Database i den allokerade beräknings nivån. Alternativt tillhandahåller Server lös beräknings [nivån](serverless-tier-overview.md) beräkning av autoskalning och räkningar per sekund för beräkning som används.

När du först har valt antalet virtuella kärnor eller DTU: er kan du skala upp eller ned en enkel databas dynamiskt baserat på den faktiska upplevelsen med hjälp av [Azure Portal](single-database-manage.md#the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update)eller [REST API](/rest/api/sql/databases/update).

Följande video visar dynamiskt ändring av tjänst nivå och beräknings storlek för att öka tillgänglig DTU: er för en enskild databas.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva krympa en databas för att frigöra utrymme som inte används. Mer information finns i [Hantera fil utrymme i Azure SQL Database](file-space-manage.md).

## <a name="impact"></a>Påverkan

Att ändra tjänst nivå eller beräknings storlek i huvudsak inbegriper tjänsten som utför följande steg:

1. Skapa en ny beräknings instans för databasen. 

    En ny beräknings instans skapas med den begärda tjänst nivån och beräknings storleken. För vissa kombinationer av tjänst nivå och beräknings storlek måste en replik av databasen skapas i den nya beräknings instansen, vilket inbegriper kopiering av data och kan starkt påverka den totala svars tiden. Oavsett, är databasen online under det här steget, och anslutningar fortsätter att dirigeras till databasen i den ursprungliga beräknings instansen.

2. Växla mellan routning av anslutningar till en ny beräknings instans.

    Befintliga anslutningar till databasen i den ursprungliga beräknings instansen tas bort. Alla nya anslutningar upprättas till databasen i den nya beräknings instansen. För vissa kombinationer av tjänst nivå och beräknings storlek, kopplas databasfilerna bort och återkopplas under växeln.  Oavsett vilket kan växeln resultera i ett kort avbrott när databasen inte är tillgänglig generellt i mindre än 30 sekunder och ofta i några sekunder. Om det finns tids krävande transaktioner som körs när anslutningar släpps, kan varaktigheten för det här steget ta längre tid för att återställa avbrutna transaktioner. [Accelererad databas återställning](../accelerated-database-recovery.md) kan minska påverkan från att avbryta tids krävande transaktioner.

> [!IMPORTANT]
> Inga data förloras under något steg i arbets flödet. Kontrol lera att du har implementerat vissa [omprövnings logik](troubleshoot-common-connectivity-issues.md) i de program och komponenter som använder Azure SQL Database medan tjänst nivån ändras.

## <a name="latency"></a>Svarstid

Beräknad svars tid för att ändra tjänst nivån, skala beräknings storleken för en enskild databas eller elastisk pool, flytta en databas i/från en elastisk pool eller flytta en databas mellan elastiska pooler är parameterstyrda enligt följande:

|Tjänstenivå|Enkel databas,</br>Standard (S0-S1)|Basic elastisk pool,</br>Standard (S2-S12) </br>Generell användning enskild databas eller elastisk pool|Premium-eller Affärskritisk enkel databas eller elastisk pool|Hyperskala
|:---|:---|:---|:---|:---|
|**Enkel databas, </br> Standard (S0-S1)**|&bull;&nbsp;Tidssvars tid för konstant som är oberoende av använt utrymme</br>&bull;&nbsp;Normalt mindre än 5 minuter|&bull;En &nbsp; latens som är proportionell till databas utrymmet som används på grund av data kopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB använt utrymme|&bull;En &nbsp; latens som är proportionell till databas utrymmet som används på grund av data kopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB använt utrymme|&bull;En &nbsp; latens som är proportionell till databas utrymmet som används på grund av data kopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB använt utrymme|
|**Basic elastisk pool, </br> Standard (S2-S12), </br> generell användning enskild databas eller elastisk pool**|&bull;En &nbsp; latens som är proportionell till databas utrymmet som används på grund av data kopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB använt utrymme|&bull;&nbsp;För enskilda databaser, konstant svars tid oberoende av använt utrymme</br>&bull;&nbsp;Normalt är mindre än 5 minuter för enskilda databaser</br>&bull;&nbsp;För elastiska pooler, i proportion till antalet databaser|&bull;En &nbsp; latens som är proportionell till databas utrymmet som används på grund av data kopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB använt utrymme|&bull;En &nbsp; latens som är proportionell till databas utrymmet som används på grund av data kopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB använt utrymme|
|**Premium-eller Affärskritisk enkel databas eller elastisk pool**|&bull;En &nbsp; latens som är proportionell till databas utrymmet som används på grund av data kopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB använt utrymme|&bull;En &nbsp; latens som är proportionell till databas utrymmet som används på grund av data kopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB använt utrymme|&bull;En &nbsp; latens som är proportionell till databas utrymmet som används på grund av data kopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB använt utrymme|&bull;En &nbsp; latens som är proportionell till databas utrymmet som används på grund av data kopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB använt utrymme|
|**Hyperskala**|Saknas|Saknas|Saknas|&bull;&nbsp;Tidssvars tid för konstant som är oberoende av använt utrymme</br>&bull;&nbsp;Normalt, mindre än 2 minuter|

> [!NOTE]
> Dessutom, för standard (S2-S12) och Generell användning databaser, kommer svars tiden för att flytta en databas in i och ut ur en elastisk pool eller mellan elastiska pooler att vara proportionerlig till databasens storlek om databasen använder sig av tjänsten Premium File Share ([PFS](../../storage/files/storage-files-introduction.md)).
>
> Du kan ta reda på om en databas använder PFS-lagring genom att köra följande fråga i databasens kontext. Om värdet i kolumnen flervärdesattribut är `PremiumFileStorage` eller `PremiumFileStorage-ZRS` , använder databasen PFS-lagring.

[!NOTE]
 Zonens redundanta egenskap förblir samma som standard när du skalar från Affärskritisk till Generell användning-nivån. Svars tiden för den här nedgraderingen när zon redundans är aktive rad samt svars tid för växling till zon-redundans för Generell användning nivån är proportionell till databasens storlek.

```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!TIP]
> Information om hur du övervakar pågående åtgärder finns i: [Hantera åtgärder med hjälp av SQL REST API](/rest/api/sql/operations/list), [Hantera åtgärder med CLI](/cli/azure/sql/db/op), [övervaka åtgärder med hjälp av T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) och dessa två PowerShell-kommandon: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) och [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

## <a name="cancelling-changes"></a>Ändringarna avbryts

En ändring eller skalnings åtgärd för en tjänst nivå kan avbrytas.

### <a name="the-azure-portal"></a>Azure Portal

I bladet databas översikt navigerar du till **meddelanden** och klickar på panelen som visar att det finns en pågående åtgärd:

![Pågående åtgärd](./media/single-database-scale/ongoing-operations.png)

Klicka sedan på knappen **Avbryt den här åtgärden**.

![Avbryt pågående åtgärd](./media/single-database-scale/cancel-ongoing-operation.png)

### <a name="powershell"></a>PowerShell

Från en PowerShell-kommandotolk ställer du in `$resourceGroupName` , `$serverName` och och `$databaseName` kör sedan följande kommando:

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

- Om du uppgraderar till en högre tjänst nivå eller beräknings storlek ökar inte databasens maximala storlek om du inte uttryckligen anger en större storlek (MaxSize).
- För att nedgradera en databas måste det databas utrymme som används vara mindre än den största tillåtna storleken för mål tjänst nivån och beräknings storleken.
- Vid nedgradering från **Premium** till **standard** -nivån gäller en extra lagrings kostnad om både (1) databasens Max storlek stöds i mål beräknings storleken och (2) Max storleken överskrider den mängd lagrings storlek som används för mål beräknings storleken. Om t. ex. en P1-databas med en Max storlek på 500 GB är downsized till S3, gäller en extra lagrings kostnad eftersom S3 har stöd för en Max storlek på 1 TB och den inkluderade lagrings mängden är bara 250 GB. Det extra lagrings utrymmet är 500 GB – 250 GB = 250 GB. Prissättning av extra lagrings utrymme finns [Azure SQL Database prissättning](https://azure.microsoft.com/pricing/details/sql-database/). Om den faktiska mängden utrymme som används är mindre än den mängd lagrings mängd som används, kan den här extra kostnaden undvikas genom att max storleken för databasen minskas till den inkluderade mängden.
- När du uppgraderar en databas med [geo-replikering](active-geo-replication-configure-portal.md) aktive rad uppgraderar du dess sekundära databaser till önskad tjänste nivå och beräknings storlek innan du uppgraderar den primära databasen (allmän vägledning för bästa prestanda). När du uppgraderar till en annan utgåva är det ett krav att den sekundära databasen uppgraderas först.
- Vid nedgradering av en databas med [geo-replikering](active-geo-replication-configure-portal.md) aktiverat, nedgradera dess primära databaser till önskad tjänste nivå och beräknings storlek innan du degraderar den sekundära databasen (allmän vägledning för bästa prestanda). Vid nedgradering till en annan utgåva är det ett krav att den primära databasen nedgraderas först.
- Erbjudandena för återställningstjänsterna är olika för de olika tjänstnivåerna. Om du nedgraderar nivån **Basic** finns det en lägre kvarhållningsperiod för säkerhets kopior. Se [Azure SQL Database säkerhets kopieringar](automated-backups-overview.md).
- De nya egenskaperna för databasen tillämpas inte förrän ändringarna har slutförts.
- När data kopiering krävs för att skala en databas (se [svars tid](#latency)) när du ändrar tjänst nivån kan hög resursutnyttjande som är samtidig till skalnings åtgärden orsaka längre skalnings tider. Med [accelererad databas återställning (ADR)](/sql/relational-databases/accelerated-database-recovery-concepts)är återställningen av tids krävande transaktioner inte en betydande fördröjning, men hög samtidiga resursanvändning kan medföra mindre beräknings-, lagrings-och nätverks bandbredds resurser för skalning, särskilt för mindre beräknings storlekar.

## <a name="billing"></a>Fakturering

Du debiteras för varje timme som det finns en databas med den högsta tjänst nivån och den beräknings storlek som tillämpas under den timmen, oavsett användning eller om databasen var aktiv i mindre än en timme. Om du till exempel skapar en enskild databas och tar bort den fem minuter senare, motsvarar din faktura en avgift för en databas timme.

## <a name="change-storage-size"></a>Ändra lagrings storlek

### <a name="vcore-based-purchasing-model"></a>Köpmodell baserad på virtuell kärna

- Lagrings utrymmet kan tillhandahållas till max storleks gränsen för data lagring med steg om 1 GB. Det minsta konfigurerbara data lagrings utrymmet är 1 GB. Se dokumentations sidor för resurs gränser för [enskilda databaser](resource-limits-vcore-single-databases.md) och [elastiska pooler](resource-limits-vcore-elastic-pools.md) för data lagring Max storleks gränser i varje tjänst mål.
- Data lagring för en enskild databas kan tillhandahållas genom att öka eller minska dess Max storlek med hjälp av [Azure Portal](https://portal.azure.com), [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update)eller [REST API](/rest/api/sql/databases/update). Om värdet för maximal storlek anges i byte, måste det vara en multipel av 1 GB (1073741824 byte).
- Mängden data som kan lagras i datafilerna för en databas begränsas av den konfigurerade maximala storleken för data lagring. Förutom lagringen allokerar Azure SQL Database automatiskt 30% mer lagrings utrymme som ska användas för transaktions loggen.
- Azure SQL Database allokerar automatiskt 32 GB per vCore för `tempdb` databasen. `tempdb` finns på den lokala SSD-lagringen på alla tjänst nivåer.
- Priset för lagring för en enskild databas eller en elastisk pool är summan av data lagrings-och transaktions logg lagrings belopp multiplicerat med tjänst nivåns lagrings enhets pris. Kostnaden för `tempdb` ingår i priset. Mer information om lagrings priset finns [Azure SQL Database prissättning](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva krympa en databas för att frigöra utrymme som inte används. Mer information finns i [Hantera fil utrymme i Azure SQL Database](file-space-manage.md).

### <a name="dtu-based-purchasing-model"></a>DTU-baserad inköps modell

- DTU-priset för en enskild databas omfattar en viss mängd lagring utan extra kostnad. Extra lagring utöver den mängd som ingår kan tillhandahållas för ytterligare kostnad upp till den maximala storleks gränsen i steg om 250 GB upp till 1 TB och sedan i steg om 256 GB utöver 1 TB. För inkluderade lagrings mängder och Max storleks gränser, se [enskild databas: lagrings storlekar och beräknings storlekar](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Extra lagrings utrymme för en enskild databas kan tillhandahållas genom att öka Max storleken med Azure Portal, [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#examples-1), [POWERSHELL](/powershell/module/az.sql/set-azsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update)eller [REST API](/rest/api/sql/databases/update).
- Priset för extra lagring för en enskild databas är det extra lagrings beloppet multiplicerat med det extra lagrings enhets priset för tjänst nivån. Mer information om priset för extra lagring finns [Azure SQL Database prissättning](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva krympa en databas för att frigöra utrymme som inte används. Mer information finns i [Hantera fil utrymme i Azure SQL Database](file-space-manage.md).

### <a name="geo-replicated-database"></a>Geo-replikerad databas

Ändra storleken på den primära databasen om du vill ändra databas storleken på en replikerad sekundär databas. Den här ändringen replikeras sedan och implementeras även i den sekundära databasen.

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>P11-och p15-begränsningar när Max storleken är större än 1 TB

Mer än 1 TB lagrings utrymme på Premium-nivån är för närvarande tillgängligt i alla regioner utom: Kina, östra, Kina, norra, Tyskland, centrala och Tyskland nordöstra. I dessa regioner är det maximala lagringsutrymmet på Premium-nivån begränsat till 1 TB. Följande överväganden och begränsningar gäller för p11-och p15-databaser med en maximal storlek som är större än 1 TB:

- Om den maximala storleken för en p11-eller p15-databas någonsin har angetts till ett värde som är större än 1 TB, kan den bara återställas eller kopieras till en p11-eller p15-databas.  Därefter kan databasen skalas om till en annan beräknings storlek förutsatt att mängden utrymme som allokerats vid tidpunkten för skalnings åtgärden inte överskrider max storleks gränsen för den nya beräknings storleken.
- För aktiva scenarier för geo-replikering:
  - Konfigurera en relation för geo-replikering: om den primära databasen är p11 eller p15 måste de sekundära (får) också vara p11 eller p15. Lägre beräknings storlek avvisas som sekundära eftersom de inte har stöd för mer än 1 TB.
  - Uppgradera den primära databasen i en geo-replikeringsrelation-relation: om du ändrar den maximala storleken till mer än 1 TB på en primär databas utlöses samma ändring på den sekundära databasen. Båda uppgraderingarna måste lyckas för att ändringen på den primära ska börja gälla. Region begränsningar för mer än 1 TB-alternativet gäller. Om den sekundära finns i en region som inte har stöd för mer än 1 TB, uppgraderas inte den primära.
- Det går inte att använda import/export-tjänsten för att läsa in p11/p15-databaser med mer än 1 TB. Använd SqlPackage.exe för att [Importera](database-import.md) och [Exportera](database-export.md) data.

## <a name="next-steps"></a>Nästa steg

För övergripande resurs gränser, se [Azure SQL Database vCore resurs gränser – enkla databaser](resource-limits-vcore-single-databases.md) och [Azure SQL Database DTU-baserade resurs gränser – enskilda databaser](resource-limits-dtu-single-databases.md).
