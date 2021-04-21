---
title: Serverlös beräkningsnivå
description: Den här artikeln beskriver den nya serverlösa beräkningsnivån och jämför den med den befintliga etablerade beräkningsnivån för Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: test sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 4/16/2021
ms.openlocfilehash: d5b0c8e60632be5e058900680dc376b7f0761150
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781585"
---
# <a name="azure-sql-database-serverless"></a>Azure SQL Database serverlös
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Serverlös är en beräkningsnivå för enskilda databaser i Azure SQL Database som automatiskt skalar beräkningen utifrån arbetsbelastningsbehovet. Kunden faktureras för den mängd databearbetning som används per sekund. På den serverlösa beräkningsnivån pausas databaser automatiskt under inaktiva perioder och då faktureras endast lagring. Databaserna startas igen automatiskt när aktiviteten återupptas.

## <a name="serverless-compute-tier"></a>Serverlös beräkningsnivå

Den serverlösa beräkningsnivån för enskilda databaser i Azure SQL Database parameteriseras med ett intervall för automatisk skalning av beräkningar och en fördröjning vid automatisk paus. Konfigurationen av dessa parametrar formar databasens prestandaupplevelse och beräkningskostnad.

![serverlös fakturering](./media/serverless-tier-overview/serverless-billing.png)

### <a name="performance-configuration"></a>Prestandakonfiguration

- Minsta **virtuella kärnor och maximalt** antal virtuella kärnor **är** konfigurerbara parametrar som definierar intervallet för databasens tillgängliga beräkningskapacitet. Minnes- och I/O-gränser är proportionella mot det angivna vCore-intervallet.  
- Fördröjningen **för automatisk paus** är en konfigurerbar parameter som definierar hur lång tid databasen måste vara inaktiv innan den pausas automatiskt. Databasen återupptas automatiskt när nästa inloggning eller annan aktivitet inträffar.  Du kan också inaktivera automatisk pausning.

### <a name="cost"></a>Cost

- Kostnaden för en serverlös databas är sammanfattningen av beräkningskostnaden och lagringskostnaden.
- När beräkningsanvändningen är mellan den minsta och högsta gränsen som konfigurerats baseras beräkningskostnaden på vCore och använt minne.
- När beräkningsanvändningen understiger de min-gränser som konfigurerats baseras beräkningskostnaden på de minsta virtuella kärnorna och det minsta konfigurerade minnet.
- När databasen har pausats är beräkningskostnaden noll och endast lagringskostnaderna uppstår.
- Lagringskostnaden bestäms på samma sätt som på den etablerade beräkningsnivån.

Mer information om kostnader finns i [Fakturering.](serverless-tier-overview.md#billing)

## <a name="scenarios"></a>Scenarier

Nivån med serverlös databehandling är pris/prestanda-optimerad för enstaka databaser med oförutsägbart användningsmönster som inte störs av viss fördröjning i databehandlingen efter inaktiva perioder. Nivån med etablerad databehandling är å andra sidan pris/prestanda-optimerad för enstaka eller flera databaser i elastiska pooler med högre genomsnittlig användning där det inte får förekomma en sådan fördröjning.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scenarier som passar bra för serverlös beräkning

- Enskilda databaser med tillfälliga, oförutsägbara användningsmönster översked med perioder av inaktivitet och lägre genomsnittlig beräkningsanvändning över tid.
- Enkla databaser på den etablerade beräkningsnivån som ofta skalas om och kunder som föredrar att delegera omskalning av beräkningar till tjänsten.
- Nya enskilda databaser utan användningshistorik där beräkningsändringen är svår eller inte går att uppskatta före distributionen i SQL Database.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scenarier som passar bra för etablerade beräkningar

- Enkla databaser med mer regelbundna, förutsägbara användningsmönster och högre genomsnittlig beräkningsanvändning över tid.
- Databaser som inte tolererar prestandabyten till följd av mer frekvent minnestrimning eller fördröjningar vid återupptagande från ett pausat tillstånd.
- Flera databaser med tillfälliga, oförutsägbara användningsmönster som kan konsolideras till elastiska pooler för bättre optimering av prisprestanda.

## <a name="comparison-with-provisioned-compute-tier"></a>Jämförelse med den etablerade beräkningsnivån

I följande tabell sammanfattas skillnaderna mellan den serverlösa beräkningsnivån och den etablerade beräkningsnivån:

| | **Serverlös databearbetning** | **Etablerad beräkning** |
|:---|:---|:---|
|**Mönster för databasanvändning**| Tillfällig, oförutsägbar användning med lägre genomsnittlig beräkningsanvändning över tid. | Mer vanliga användningsmönster med högre genomsnittlig beräkningsanvändning över tid eller flera databaser som använder elastiska pooler.|
| **Arbete med prestandahantering** |Lägre|Högre|
|**Beräkningsskalning**|Automatiskt|Manuell|
|**Svarstider för beräkning**|Lägre efter inaktiva perioder|Omedelbar|
|**Faktureringskornighet**|Per sekund|Per timme|

## <a name="purchasing-model-and-service-tier"></a>Köpmodell och tjänstnivå

SQL Database serverlös stöds för närvarande endast på Generell användning på generation 5-maskinvara i köpmodellen vCore.

## <a name="autoscaling"></a>Automatisk skalning

### <a name="scaling-responsiveness"></a>Skalning av svarstider

I allmänhet körs serverlösa databaser på en dator med tillräcklig kapacitet för att uppfylla resursbehovet utan avbrott för den mängd beräkning som begärs inom de gränser som anges av värdet för maximalt antal virtuella kärnor. Ibland inträffar belastningsutjämning automatiskt om datorn inte kan uppfylla resursbehovet inom några minuter. Om resursefterfrågan till exempel är 4 virtuella kärnor, men bara 2 virtuella kärnor är tillgängliga, kan det ta upp till några minuter att belastningsutjämna innan 4 virtuella kärnor har angetts. Databasen förblir online under belastningsutjämningen, förutom under en kort period i slutet av åtgärden när anslutningarna tas bort.

### <a name="memory-management"></a>Minneshantering

Minne för serverlösa databaser återtas oftare än för etablerade beräkningsdatabaser. Det här beteendet är viktigt för att kontrollera kostnaderna i serverlöst och kan påverka prestandan.

#### <a name="cache-reclamation"></a>Cache-återskapning

Till skillnad från etablerade beräkningsdatabaser återtas minne från SQL Cache från en serverlös databas när processoranvändningen eller den aktiva cacheanvändningen är låg.

- Aktiv cacheanvändning anses låg när den totala storleken på de senast använda cacheposterna understiger ett tröskelvärde under en viss tidsperiod.
- När cache-återaktivering utlöses minskas målcachestorleken stegvis till en bråkdel av den tidigare storleken och återtar endast om användningen förblir låg.
- När cache-återtagning sker är principen för att välja cacheposter som ska avlägsnas samma urvalsprincip som för etablerade beräkningsdatabaser när minnestrycket är högt.
- Cachestorleken minskas aldrig under den minsta minnesgränsen som definieras av minsta virtuella kärnor som kan konfigureras.

I både serverlösa och etablerade beräkningsdatabaser kan cacheposter avlägsnas om allt tillgängligt minne används.

Observera att när processoranvändningen är låg kan den aktiva cacheanvändningen vara hög beroende på användningsmönstret och förhindra minnesanrop.  Det kan också uppstå ytterligare fördröjningar när användaraktiviteten stoppas innan minnesrelamering sker på grund av periodiska bakgrundsprocesser som svarar på tidigare användaraktivitet.  Till exempel genererar borttagningsåtgärder och rensningsuppgifter för QDS spökposter som har markerats för borttagning, men som inte tas bort fysiskt förrän rensningsprocessen för spöken körs som kan omfatta läsning av datasidor i cacheminnet.

#### <a name="cache-hydration"></a>Cachefuktering

SQL-cachen växer när data hämtas från disken på samma sätt och med samma hastighet som för etablerade databaser. När databasen är upptagen tillåts cacheminnet att växa ohämmad upp till den maximala minnesgränsen.

## <a name="auto-pausing-and-auto-resuming"></a>Pausa automatiskt och återuppta automatiskt

### <a name="auto-pausing"></a>Pausa automatiskt

Automatisk pausning utlöses om alla följande villkor är sanna under varaktigheten för den automatiska pausfördröjningen:

- Nummersessioner = 0
- CPU = 0 för användararbetsbelastning som körs i användarpoolen

Ett alternativ finns för att inaktivera automatisk pausning om du vill.

Följande funktioner stöder inte automatisk pausning, men stöder automatisk skalning.  Om någon av följande funktioner används måste automatisk pausning inaktiveras och databasen förblir online oavsett varaktigheten för databasens inaktivitet:

- Geo-replikering (aktiv geo-replikering och automatiska redundansgrupper).
- Långsiktig kvarhållning av säkerhetskopior (LTR).
- Synkroniseringsdatabasen som används vid SQL-datasynkronisering.  Till skillnad från synkroniseringsdatabaser stöder hubb- och medlemsdatabaser automatisk pausning.
- DNS-alias
- Jobbdatabasen som används i elastiska jobb (förhandsversion).

Automatisk pausning förhindras tillfälligt under distributionen av vissa tjänstuppdateringar som kräver att databasen är online.  I sådana fall tillåts automatisk pausning igen när tjänstuppdateringen har slutförts.

### <a name="auto-resuming"></a>Återuppta automatiskt

Automatisk återupptagande utlöses om något av följande villkor är sant när som helst:

|Funktion|Utlösare för automatisk återupptagning|
|---|---|
|Autentisering och auktorisering|Inloggning|
|Hotidentifiering|Aktivera/inaktivera inställningar för hotidentifiering på databas- eller servernivå.<br>Ändra inställningar för hotidentifiering på databas- eller servernivå.|
|Dataidentifiering och -klassificering|Lägga till, ändra, ta bort eller visa känslighetsetiketter|
|Granskning|Visa granskningsposter.<br>Uppdatera eller visa granskningsprincip.|
|Datamaskning|Lägga till, ändra, ta bort eller visa datamaskeringsregler|
|Transparent datakryptering|Visa status eller status för transparent datakryptering|
|Sårbarhetsbedömning|Ad hoc-genomsökningar och regelbundna genomsökningar om det är aktiverat|
|Frågedatalager (prestanda)|Ändra eller visa inställningar för Query Store|
|Prestandarekommendationer|Visa eller tillämpa prestandarekommendationer|
|Automatisk justering|Tillämpning och verifiering av rekommendationer för automatisk justering, till exempel automatisk indexering|
|Databaskopiering|Skapa databasen som kopia.<br>Exportera till en BACPAC-fil.|
|SQL-datasynkronisering|Synkronisering mellan hubb- och medlemsdatabaser som körs enligt ett konfigurerbart schema eller utförs manuellt|
|Ändra vissa databasmetadata|Lägga till nya databastaggar.<br>Ändra maximalt antal virtuella kärnor, minsta antal virtuella kärnor eller automatisk fördröjning.|
|SQL Server Management Studio (SSMS)|Om du använder tidigare SSMS-versioner än 18.1 och öppnar ett nytt frågefönster för en databas på servern återupptas alla automatiskt pausade databaser på samma server. Det här beteendet inträffar inte om du använder SSMS version 18.1 eller senare.|

Övervakning, hantering eller andra lösningar som utför någon av de åtgärder som anges ovan utlöser automatisk återupptagande.

Automatisk återupptagande utlöses också under distributionen av vissa tjänstuppdateringar som kräver att databasen är online.

### <a name="connectivity"></a>Anslutning

Om en serverlös databas har pausats återupptar den första inloggningen databasen och returnerar ett fel som anger att databasen inte är tillgänglig med felkoden 40613. När databasen har återupptagits måste inloggningen försöka upprättas igen. Databasklienter med logik för anslutningsförsök bör inte behöva ändras.

### <a name="latency"></a>Svarstid

Svarstiden för att återuppta automatiskt och automatiskt pausa en serverlös databas är vanligtvis i ordning 1 minut för att återuppta automatiskt och 1–10 minuter att pausa automatiskt.

### <a name="customer-managed-transparent-data-encryption-byok"></a>Kund hanterad transparent datakryptering (BYOK)

Om användningen av kund hanterad [transparent datakryptering](transparent-data-encryption-byok-overview.md) (BYOK) och den serverlösa databasen pausas automatiskt när nyckeln tas bort eller återkallas, förblir databasen i automatiskt pausat tillstånd.  I det här fallet blir databasen otillgänglig inom cirka 10 minuter efter att databasen har återupptagits.  När databasen blir otillgänglig är återställningsprocessen densamma som för etablerade beräkningsdatabaser.  Om den serverlösa databasen är online när nyckelborttagning eller återkallelse inträffar blir databasen också otillgänglig inom cirka 10 minuter på samma sätt som med etablerade beräkningsdatabaser.

## <a name="onboarding-into-serverless-compute-tier"></a>Registrering på serverlös beräkningsnivå

Att skapa en ny databas eller flytta en befintlig databas till en serverlös beräkningsnivå följer samma mönster som när du skapar en ny databas på den etablerade beräkningsnivån och omfattar följande två steg.

1. Ange tjänstmålet. Tjänstmålet fastställer tjänstnivå, maskinvarugenerering och maximalt antal virtuella kärnor. Information om alternativ för tjänstmål finns [i serverlösa resursgränser](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)


2. Du kan också ange den minsta virtuella kärnorna och fördröjningen för att automatiskt lägga till dem för att ändra standardvärdena. I följande tabell visas tillgängliga värden för dessa parametrar.

   |Parameter|Värdeval|Standardvärde|
   |---|---|---|---|
   |Minsta virtuella kärnor|Beror på maximalt antal konfigurerade virtuella kärnor – se [resursbegränsningar.](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)|0,5 virtuella kärnor|
   |Tidsfördröjning automatiskt|Minimum: 60 minuter (1 timme)<br>Max: 1 0080 minuter (7 dagar)<br>Steg: 10 minuter<br>Inaktivera autopause: -1|60 minuter|


### <a name="create-a-new-database-in-the-serverless-compute-tier"></a>Skapa en ny databas på den serverlösa beräkningsnivån

I följande exempel skapas en ny databas på den serverlösa beräkningsnivån.

#### <a name="use-the-azure-portal"></a>Använda Azure-portalen

Se [Snabbstart: Skapa en enkel databas i Azure SQL Database med hjälp av Azure Portal](single-database-create-quickstart.md).


#### <a name="use-powershell"></a>Använd PowerShell

```powershell
New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -ComputeModel Serverless -Edition GeneralPurpose -ComputeGeneration Gen5 `
  -MinVcore 0.5 -MaxVcore 2 -AutoPauseDelayInMinutes 720
```
#### <a name="use-the-azure-cli"></a>Använda Azure CLI

```azurecli
az sql db create -g $resourceGroupName -s $serverName -n $databaseName `
  -e GeneralPurpose -f Gen5 --min-capacity 0.5 -c 2 --compute-model Serverless --auto-pause-delay 720
```


#### <a name="use-transact-sql-t-sql"></a>Använda Transact-SQL (T-SQL)

När du använder T-SQL tillämpas standardvärden för minsta virtuella kärnor och fördröjning för automatiskpausning.

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

Mer information finns i [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true).  

### <a name="move-a-database-from-the-provisioned-compute-tier-into-the-serverless-compute-tier"></a>Flytta en databas från den etablerade beräkningsnivån till den serverlösa beräkningsnivån

I följande exempel flyttas en databas från den etablerade beräkningsnivån till den serverlösa beräkningsnivån.

#### <a name="use-powershell"></a>Använd PowerShell


```powershell
Set-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -Edition GeneralPurpose -ComputeModel Serverless -ComputeGeneration Gen5 `
  -MinVcore 1 -MaxVcore 4 -AutoPauseDelayInMinutes 1440
```

#### <a name="use-the-azure-cli"></a>Använda Azure CLI

```azurecli
az sql db update -g $resourceGroupName -s $serverName -n $databaseName `
  --edition GeneralPurpose --min-capacity 1 --capacity 4 --family Gen5 --compute-model Serverless --auto-pause-delay 1440
```


#### <a name="use-transact-sql-t-sql"></a>Använda Transact-SQL (T-SQL)

När du använder T-SQL tillämpas standardvärden för minsta virtuella kärnor och fördröjning för automatisk paus.

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

Mer information finns i [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true).

### <a name="move-a-database-from-the-serverless-compute-tier-into-the-provisioned-compute-tier"></a>Flytta en databas från den serverlösa beräkningsnivån till den etablerade beräkningsnivån

En serverlös databas kan flyttas till en etablerad beräkningsnivå på samma sätt som du flyttar en etablerad beräkningsdatabas till en serverlös beräkningsnivå.

## <a name="modifying-serverless-configuration"></a>Ändra serverlös konfiguration

### <a name="use-powershell"></a>Använd PowerShell

Ändring av högsta eller lägsta virtuella kärnor och automatisk fördröjning utförs med hjälp av kommandot [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) i PowerShell med argumenten `MaxVcore` , och `MinVcore` `AutoPauseDelayInMinutes` .

### <a name="use-the-azure-cli"></a>Använda Azure CLI

Du ändrar det högsta eller lägsta antalet virtuella kärnor och fördröjningen för automatisk uppdatering med hjälp av [kommandot az sql db update](/cli/azure/sql/db#az_sql_db_update) i Azure CLI med argumenten , och `capacity` `min-capacity` `auto-pause-delay` .


## <a name="monitoring"></a>Övervakning

### <a name="resources-used-and-billed"></a>Resurser som används och faktureras

Resurserna i en serverlös databas kapslas in av appaket, SQL-instans och entiteter för användarresurspooler.

#### <a name="app-package"></a>Appaket

App-paketet är den yttre gränsen för resurshantering för en databas, oavsett om databasen är på en serverlös eller etablerad beräkningsnivå. Appaketet innehåller SQL-instansen och externa tjänster som fulltextsökning som tillsammans omfattar alla användar- och systemresurser som används av en databas i SQL Database. SQL-instansen är i allmänhet den övergripande resursanvändningen i hela appaketet.

#### <a name="user-resource-pool"></a>Användarresurspool

Användarresurspoolen är den innersta gränsen för resurshantering för en databas, oavsett om databasen är på en serverlös eller etablerad beräkningsnivå. Användarresurspoolen omfattar CPU och I/O för användararbetsbelastning som genereras av DDL-frågor som CREATE- och ALTER- och DML-frågor som SELECT, INSERT, UPDATE och DELETE. Dessa frågor representerar vanligtvis den mest betydande andelen användning i appaketet.

### <a name="metrics"></a>Mått

Mått för att övervaka resursanvändningen för appaketet och användarpoolen för en serverlös databas visas i följande tabell:

|Entitet|Metric|Beskrivning|Enheter|
|---|---|---|---|
|Appaket|app_cpu_percent|Procentandel virtuella kärnor som används av appen i förhållande till maximalt antal virtuella kärnor som tillåts för appen.|Procent|
|Appaket|app_cpu_billed|Mängden beräkning som debiteras för appen under rapporteringsperioden. Det belopp som betalas under den här perioden är produkten av det här måttet och enhetspriset för virtuella kärnor. <br><br>Värdena för det här måttet bestäms genom att den maximala processoranvändningen och det minne som används varje sekund aggregeras över tid. Om den mängd som används är mindre än den minsta mängd som har etablerats enligt de minsta virtuella kärnorna och minimiminnet debiteras den minsta mängd som etableras.För att jämföra CPU med minne i faktureringssyfte normaliseras minnet till enheter med virtuella kärnor genom att mängden minne i GB omskalas med 3 GB per vCore.|vCore sekunder|
|Appaket|app_memory_percent|Procentandel minne som används av appen i förhållande till maximalt minne som tillåts för appen.|Procent|
|Användarpool|cpu_percent|Procentandel virtuella kärnor som används av användarens arbetsbelastning i förhållande till maximalt antal virtuella kärnor som tillåts för användararbetsbelastning.|Procent|
|Användarpool|data_IO_percent|Procentandel data-IOPS som används av användarens arbetsbelastning i förhållande till högsta tillåtna data-IOPS för användararbetsbelastning.|Procent|
|Användarpool|log_IO_percent|Procentandel av logg-MB/s som används av användarens arbetsbelastning i förhållande till maximalt antal logg-MB/s som tillåts för användarens arbetsbelastning.|Procent|
|Användarpool|workers_percent|Procentandel arbetare som används av användararbetsbelastningen i förhållande till maximalt antal arbetare som tillåts för användararbetsbelastning.|Procent|
|Användarpool|sessions_percent|Procentandel sessioner som används av användarens arbetsbelastning i förhållande till maximalt antal sessioner som tillåts för användararbetsbelastning.|Procent|

### <a name="pause-and-resume-status"></a>Pausa och återuppta status

I Azure Portal visas databasstatusen i översiktsfönstret på den server som visar de databaser som den innehåller. Databasstatusen visas också i översiktsfönstret för databasen.

Använd följande kommandon för att fråga efter pausa och återuppta status för en databas:

#### <a name="use-powershell"></a>Använd PowerShell

```powershell
Get-AzSqlDatabase -ResourceGroupName $resourcegroupname -ServerName $servername -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

#### <a name="use-the-azure-cli"></a>Använda Azure CLI

```azurecli
az sql db show --name $databasename --resource-group $resourcegroupname --server $servername --query 'status' -o json
```


## <a name="resource-limits"></a>Resursgränser

Information om resursbegränsningar finns [i serverlös beräkningsnivå.](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)

## <a name="billing"></a>Fakturering

Mängden beräkning som faktureras är den maximala processoranvändningen och det minne som används varje sekund. Om mängden processoranvändning och använt minne är mindre än den minsta mängd som har etablerats för var och en debiteras det etablerade beloppet. För att jämföra CPU med minne i faktureringssyfte normaliseras minnet till enheter med virtuella kärnor genom att mängden minne i GB omskalas med 3 GB per vCore.

- **Debiterad resurs:** CPU och minne
- **Fakturerat belopp:** enhetspris för vCore * max (min vCores, vCores used, min memory GB * 1/3, memory GB used * 1/3) 
- **Faktureringsfrekvens:** Per sekund

Enhetspriset för virtuella kärnor är kostnaden per vCore per sekund. Se sidan [Azure SQL Database för specifika](https://azure.microsoft.com/pricing/details/sql-database/single/) enhetspriser i en viss region.

Den fakturerade beräkningsmängden exponeras med följande mått:

- **Mått:** app_cpu_billed (vCore sekunder)
- **Definition:** max (min vCores, vCores used, min memory GB * 1/3, memory GB used * 1/3)
- **Rapporteringsfrekvens:** Per minut

Den här kvantiteten beräknas varje sekund och aggregeras över 1 minut.

### <a name="minimum-compute-bill"></a>Minsta beräkningsfaktura

Om en serverlös databas pausas är beräkningsfakturan noll.  Om en serverlös databas inte har pausats är den minsta beräkningsfakturan inte mindre än mängden virtuella kärnor baserat på max (min virtuella kärnor, min minne GB * 1/3).

Exempel:

- Anta att en serverlös databas inte har pausats och konfigurerats med maximalt 8 virtuella kärnor och 1 min vCore motsvarande 3,0 GB minne.  Den lägsta beräkningsfakturan baseras sedan på max (1 vCore, 3,0 GB * 1 vCore/3 GB) = 1 vCore.
- Anta att en serverlös databas inte har pausats och konfigurerats med 4 virtuella kärnor och 0,5 min virtuella kärnor som motsvarar 2,1 GB minne.  Den lägsta beräkningsfakturan baseras sedan på max (0,5 virtuella kärnor, 2,1 GB * 1 vCore/3 GB) = 0,7 virtuella kärnor.

[Priskalkylatorn Azure SQL Database](https://azure.microsoft.com/pricing/calculator/?service=sql-database) för serverlös kan användas för att fastställa det minsta minne som kan konfigureras baserat på antalet konfigurerade högsta och minsta virtuella kärnor.  Om de minsta virtuella kärnorna som konfigurerats är större än 0,5 virtuella kärnor är den minsta beräkningsfakturan oberoende av det minsta antalet konfigurerade virtuella kärnor och baseras endast på det antal virtuella kärnor som konfigurerats.

### <a name="example-scenario"></a>Exempelscenario

Överväg en serverlös databas som konfigurerats med 1 min vCore och 4 max virtuella kärnor.  Detta motsvarar cirka 3 GB minne och 12 GB max minne.  Anta att fördröjningen för automatisk paus är inställd på 6 timmar och att databasarbetsbelastningen är aktiv under de första 2 timmarna av en 24-timmarsperiod och annars inaktiv.    

I det här fallet debiteras databasen för beräkning och lagring under de första 8 timmarna.  Även om databasen är inaktiv från och med den andra timmen debiteras den fortfarande för beräkning under de efterföljande 6 timmarna baserat på den minsta beräkning som etablerats när databasen är online.  Endast lagring faktureras under återstoden av 24-timmarsperioden medan databasen pausas.

Mer exakt beräknas beräkningsfakturan i det här exemplet så här:

|Tidsintervall|Virtuella kärnor används varje sekund|GB används varje sekund|Fakturerad beräkningsdimension|vCore-sekunder faktureras över tidsintervall|
|---|---|---|---|---|
|0:00-1:00|4|9|Virtuella kärnor som används|4 virtuella kärnor * 3 600 sekunder = 1 4400 virtuella kärnor sekunder|
|1:00-2:00|1|12|Minnesanvändning|12 GB * 1/3 * 3 600 sekunder = 1 4400 virtuella kärnor sekunder|
|2:00-8:00|0|0|Minsta etablerade minne|3 GB * 1/3 * 2 1600 sekunder = 2 1 600 vCore sekunder|
|8:00-24:00|0|0|Ingen beräkning faktureras när den har pausats|0 virtuella kärnor sekunder|
|Totalt antal sekunder för virtuella kärnor som fakturerats under 24 timmar||||5 0400 virtuella kärnor sekunder|

Anta att beräkningsenhetspriset är 0,000145 USD/vCore/sekund.  Den beräkning som debiteras för den här 24-timmarsperioden är sedan produkten av beräkningsenhetens pris och antalet sekunder för virtuella kärnor fakturerade: 0,000145 USD/vCore/sekund * 5 0400 virtuella kärnor sekunder ~ 7,31 USD

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Azure Hybrid-förmån och reserverad kapacitet

Azure Hybrid-förmån (AHB) och rabatter för reserverad kapacitet gäller inte för den serverlösa beräkningsnivån.

## <a name="available-regions"></a>Tillgängliga regioner

Den serverlösa beräkningsnivån är tillgänglig över hela världen förutom följande regioner: Kina, östra, Kina, norra, Tyskland, centrala, Tyskland, nordöstra och US Gov centrala (Iowa).

## <a name="next-steps"></a>Nästa steg

- Kom igång genom att gå [till Snabbstart: Skapa en enkel databas i Azure SQL Database med hjälp av Azure Portal](single-database-create-quickstart.md).
- Information om resursbegränsningar finns [i Resursgränser för serverlös beräkningsnivå.](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)
