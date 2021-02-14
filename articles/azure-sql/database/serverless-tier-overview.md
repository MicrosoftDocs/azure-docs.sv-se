---
title: Serverlös beräkningsnivå
description: Den här artikeln beskriver den nya server lös beräknings nivån och jämför den med den befintliga allokerade beräknings nivån för Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: test sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 12/8/2020
ms.openlocfilehash: 1b8be7fc6295c6332d26718b5752d2fd8f2a6f73
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393249"
---
# <a name="azure-sql-database-serverless"></a>Azure SQL Database utan Server
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Server lös är en beräknings nivå för enskilda databaser i Azure SQL Database som automatiskt skalar beräkning baserat på arbets belastnings behov och räkningar för mängden data bearbetning som används per sekund. Server lös beräknings nivån pausar också automatiskt databaser under inaktiva perioder när endast lagring faktureras och återupptar automatiskt databaser när aktiviteten returnerar.

## <a name="serverless-compute-tier"></a>Serverlös beräkningsnivå

Den server lösa beräknings nivån för enskilda databaser i Azure SQL Database är parametriserad av ett intervall för automatisk skalning av beräkning och en fördröjning för automatisk paus. Konfigurationen av dessa parametrar formar databasens prestanda upplevelse och beräknings kostnader.

![utan server fakturering](./media/serverless-tier-overview/serverless-billing.png)

### <a name="performance-configuration"></a>Prestanda konfiguration

- **Lägsta virtuella kärnor** och **maximal virtuella kärnor** är konfigurerbara parametrar som definierar intervallet för beräknings kapacitet som är tillgängliga för databasen. Minnes-och IO-gränser är proportionella till det vCore-intervall som angetts.  
- **Fördröjningen för automatisk paus** är en konfigurerbar parameter som definierar den tids period som databasen måste vara inaktiv innan den pausas automatiskt. Databasen återupptas automatiskt när nästa inloggning eller annan aktivitet sker.  Du kan också inaktivera automatisk upppausning.

### <a name="cost"></a>Cost

- Kostnaden för en server lös databas är summan av beräknings kostnaden och lagrings kostnaden.
- När beräknings användningen är mellan den minsta och högsta gränsen som kon figurer ATS baseras beräknings kostnaden på vCore och använt minne.
- När beräknings användningen är under de lägsta gränser som har kon figurer ATS, baseras beräknings kostnaden på den minsta virtuella kärnor och minimalt minne som har kon figurer ATS.
- När databasen har pausats är beräknings kostnaden noll och endast lagrings kostnader uppkommer.
- Lagrings kostnaden fastställs på samma sätt som i den allokerade beräknings nivån.

Mer kostnads information finns i [fakturering](serverless-tier-overview.md#billing).

## <a name="scenarios"></a>Scenarier

Nivån med serverlös databehandling är pris/prestanda-optimerad för enstaka databaser med oförutsägbart användningsmönster som inte störs av viss fördröjning i databehandlingen efter inaktiva perioder. Nivån med etablerad databehandling är å andra sidan pris/prestanda-optimerad för enstaka eller flera databaser i elastiska pooler med högre genomsnittlig användning där det inte får förekomma en sådan fördröjning.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scenarier som passar bra för Server lös data behandling

- Enkla databaser med intermittent, oförutsägbara användnings mönster som blandats med perioder av inaktivitet och lägre genomsnittlig data bearbetning över tid.
- Enskilda databaser i den allokerade beräknings nivån som ofta skalas om och kunder som föredrar att delegera beräkning av skalbarhet till tjänsten.
- Nya enkla databaser utan användnings historik där beräknings storlek är svårt eller inte går att uppskatta innan distributionen i SQL Database.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scenarier som är väl lämpade för allokerad beräkning

- Enkla databaser med mer vanliga, förutsägbara användnings mönster och större genomsnittlig beräknings användning över tid.
- Databaser som inte kan tolerera prestanda kompromisser som orsakas av mer frekvent minnes trimning eller fördröjningar i att återupptas från pausat tillstånd.
- Flera databaser med intermittent, oförutsägbara användnings mönster som kan konsol IDE ras i elastiska pooler för bättre pris-och prestanda optimering.

## <a name="comparison-with-provisioned-compute-tier"></a>Jämförelse med allokerad beräknings nivå

I följande tabell sammanfattas skillnader mellan server lös beräknings nivå och den allokerade beräknings nivån:

| | **Serverlös databearbetning** | **Allokerad beräkning** |
|:---|:---|:---|
|**Användnings mönster för databas**| Intermittent, oförutsägbar användning med lägre genomsnittlig beräknings användning över tid. | Vanliga användnings mönster med högre genomsnittlig beräknings användning över tid, eller flera databaser med elastiska pooler.|
| **Prestanda hanterings ansträngning** |Lägre|Högre|
|**Beräknings skalning**|Automatiskt|Manuell|
|**Beräknings svars tid**|Lägre efter inaktiva perioder|Direkt|
|**Fakturerings precision**|Per sekund|Per timme|

## <a name="purchasing-model-and-service-tier"></a>Inköps modell och tjänst nivå

SQL Database Server lös stöds för närvarande bara på Generell användning nivå på generation 5-maskin vara i vCore inköps modell.

## <a name="autoscaling"></a>Automatisk skalning

### <a name="scaling-responsiveness"></a>Skalnings svars tid

I allmänhet körs serverbaserade databaser på en dator som har tillräckligt med kapacitet för att uppfylla resurs behovet utan avbrott för en mängd data som begärs inom gränser som anges av Max värdet för virtuella kärnor. Ibland sker belastnings utjämning automatiskt om datorn inte kan uppfylla resurs behovet inom några minuter. Om till exempel resurs behovet är 4 virtuella kärnor, men bara 2 virtuella kärnor är tillgängliga, kan det ta upp till några minuter att belastningsutjämna innan 4 virtuella kärnor tillhandahålls. Databasen är online under belastnings utjämning, förutom en kort period i slutet av åtgärden när anslutningarna bryts.

### <a name="memory-management"></a>Minnes hantering

Minne för serverbaserade databaser frigörs oftare än för etablerade beräknings databaser. Det här beteendet är viktigt för att kontrol lera kostnader i Server lös och kan påverka prestandan.

#### <a name="cache-reclamation"></a>Cache regenering

Till skillnad från etablerade data bearbetnings databaser frigörs minne från SQL-cachen från en server lös databas när CPU eller aktiv cache-användning är låg.

- Användningen av aktiva cacheminnen anses låg när den totala storleken på de senast använda cacheposter unders tiger ett tröskelvärde under en viss tids period.
- När cache regenering utlöses, minskas storleken på målets cachestorlek stegvis till en bråkdel av den tidigare storleken och återställningen fortsätter bara om användningen är låg.
- När cache regenering sker är principen för att välja cacheposter att ta bort samma princip som för etablerade beräknings databaser när minnes trycket är hög.
- Cachestorleken minskas aldrig under den minsta minnes gränsen som definieras av min virtuella kärnor, som kan konfigureras.

I både server lösa och etablerade beräknings databaser kan cacheposter avlägsnas om allt tillgängligt minne används.

Observera att användningen av aktiva cacheminnen kan vara hög beroende på användnings mönstret och förhindra minnes regenerering när processor användningen är låg.  Det kan också uppstå ytterligare fördröjningar efter att en användar aktivitet stoppas innan återtagning av minne sker på grund av regelbundna bakgrunds processer som svarar på tidigare användar aktivitet.  Ta bort åtgärder och QDs servernamn rensnings uppgifter genererar Ghost-poster som marker ATS för borttagning, men tas inte bort fysiskt förrän rensnings processen för dubbel rensning körs som kan innebära att läsning av data sidor cachelagras.

#### <a name="cache-hydration"></a>Cachelagra hydrering

SQL-cachen växer när data hämtas från disk på samma sätt och med samma hastighet som för etablerade databaser. När databasen är upptagen får cachen öka obegränsad till högsta minnes gräns.

## <a name="auto-pause-and-auto-resume"></a>Automatisk paus och automatisk återgång

### <a name="auto-pause"></a>Pausa automatiskt

Automatisk paus utlöses om samtliga följande villkor är uppfyllda under tiden för fördröjningen för automatisk paus:

- Antal sessioner = 0
- CPU = 0 för användar arbets belastning som körs i anslutningspoolen

Det finns ett alternativ för att inaktivera automatisk paus vid behov.

Följande funktioner stöder inte automatisk pausning, men stöder automatisk skalning.  Om någon av följande funktioner används, ska automatisk pausning vara inaktive rad och databasen förblir online oavsett hur lång tid det tar för inaktivitet i databasen:

- Geo-replikering (aktiv geo-replikering och grupper för automatisk redundans).
- Långsiktig kvarhållning av säkerhets kopior (brv).
- Den synkroniserade databasen som används i SQL Data Sync.  Till skillnad från Sync-databaser, nav och medlems databaser har stöd för automatisk paus.
- DNS-alias
- Jobb databasen som används i elastiska jobb (för hands version).

Automatisk Pausering förhindras tillfälligt under distributionen av vissa tjänste uppdateringar som kräver att databasen är online.  I sådana fall blir automatisk paus automatiskt tillåten när uppdateringen är klar.

### <a name="auto-resuming"></a>Automatisk återställning

Automatisk aktivering utlöses om något av följande villkor är uppfyllt när som helst:

|Funktion|Automatiskt återuppta utlösare|
|---|---|
|Autentisering och auktorisering|Inloggning|
|Hotidentifiering|Aktivera/inaktivera inställningar för hot identifiering på databas-eller server nivå.<br>Ändra inställningarna för hot identifiering på databas-eller server nivå.|
|Dataidentifiering och -klassificering|Lägga till, ändra, ta bort eller Visa känslighets etiketter|
|Granskning|Visa gransknings poster.<br>Uppdaterar eller visar gransknings principen.|
|Datamaskning|Lägga till, ändra, ta bort eller Visa regler för data maskering|
|Transparent datakryptering|Visa status eller status för transparent data kryptering|
|Sårbarhetsbedömning|Ad hoc-sökningar och regelbundna sökningar om det är aktiverat|
|Fråga (prestanda) data lager|Ändra eller Visa inställningar för frågearkivet|
|Prestandarekommendationer|Visa eller tillämpa prestanda rekommendationer|
|Automatisk justering|Program och verifiering av rekommendationer för automatisk justering, till exempel automatisk indexering|
|Databas kopiering|Skapa databas som kopia.<br>Exportera till en BACPAC-fil.|
|SQL Data Sync|Synkronisering mellan hubb och medlems databaser som körs enligt ett konfigurerbart schema eller som utförs manuellt|
|Ändra vissa metadata för databasen|Lägger till nya Database-taggar.<br>Ändra Max virtuella kärnor, min virtuella kärnor eller AutoPause fördröjning.|
|SQL Server Management Studio (SSMS)|Om du använder SSMS-versioner som är äldre än 18,1 och öppnar ett nytt frågefönster för en databas på servern återupptas alla automatiskt pausade databaser på samma server. Det här problemet uppstår inte om du använder SSMS version 18,1 eller senare.|

Övervakning, hantering eller andra lösningar som utför någon av de åtgärder som anges ovan utlöser automatiskt återupptagning.

Automatisk återupptagning utlöses även under distributionen av vissa tjänst uppdateringar som kräver att databasen är online.

### <a name="connectivity"></a>Anslutning

Om en server lös databas har pausats kommer den första inloggningen att återuppta databasen och returnera ett fel som anger att databasen inte är tillgänglig med felkoden 40613. När databasen har återupptagits måste inloggningen göras om för att upprätta anslutningen. Databas klienter med logik för anslutnings försök ska inte behöva ändras.

### <a name="latency"></a>Svarstid

Svars tiden för att automatiskt återuppta och automatiskt pausa en databas utan server är vanligt vis 1 minut att automatiskt återuppta och 1-10 minuter för automatisk pausning.

### <a name="customer-managed-transparent-data-encryption-byok"></a>Kund hanterad transparent data kryptering (BYOK)

Om du använder [kundhanterad transparent data kryptering](transparent-data-encryption-byok-overview.md) (BYOK) och den server lösa databasen automatiskt pausas när nyckel borttagningen eller återkallning sker, så förblir databasen i läget för automatisk paus.  I det här fallet blir databasen otillgänglig inom cirka 10 minuter efter att databasen har återupptagits.  När databasen blir otillgänglig är återställnings processen densamma som för etablerade beräknings databaser.  Om databasen utan server är online när nyckel borttagningen eller återkallning sker, blir databasen också otillgänglig inom cirka 10 minuter på samma sätt som med etablerade beräknings databaser.

## <a name="onboarding-into-serverless-compute-tier"></a>Onboarding in in på Server lös beräknings nivå

Att skapa en ny databas eller flytta en befintlig databas till en server lös beräknings nivå följer samma mönster som att skapa en ny databas i en allokerad beräknings nivå och omfattar följande två steg.

1. Ange tjänst målet. Tjänst målet föreskriver tjänst nivån, maskin varu genereringen och Max virtuella kärnor. Information om tjänst mål finns i [begränsningar för Server lös resurs](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)


2. Alternativt kan du ange den minsta virtuella kärnor och den automatiskt paus fördröjningen för att ändra standardvärdena. I följande tabell visas de tillgängliga värdena för dessa parametrar.

   |Parameter|Värde alternativ|Standardvärde|
   |---|---|---|---|
   |Minsta virtuella kärnor|Är beroende av Max virtuella kärnor konfigurerade – se [resurs gränser](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).|0,5 virtuella kärnor|
   |Pausa fördröjning|Minst: 60 minuter (1 timme)<br>Max: 10080 minuter (7 dagar)<br>Steg: 10 minuter<br>Inaktivera autopausen:-1|60 minuter|


### <a name="create-a-new-database-in-the-serverless-compute-tier"></a>Skapa en ny databas i Server lös beräknings nivån

I följande exempel skapas en ny databas i Server lös beräknings nivån.

#### <a name="use-the-azure-portal"></a>Använda Azure-portalen

Se [snabb start: skapa en enda databas i Azure SQL Database med hjälp av Azure Portal](single-database-create-quickstart.md).


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


#### <a name="use-transact-sql-t-sql"></a>Använd Transact-SQL (T-SQL)

När du använder T-SQL tillämpas standardvärden för den minsta virtuella kärnor och den automatiskt pausade fördröjningen.

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

Mer information finns i [skapa databas](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true).  

### <a name="move-a-database-from-the-provisioned-compute-tier-into-the-serverless-compute-tier"></a>Flytta en databas från den allokerade beräknings nivån till Server lös beräknings nivån

Följande exempel flyttar en databas från den allokerade beräknings nivån till Server lös beräknings nivån.

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


#### <a name="use-transact-sql-t-sql"></a>Använd Transact-SQL (T-SQL)

När du använder T-SQL används standardvärden för den minsta virtuella kärnor och fördröjningen för automatisk paus.

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

Mer information finns i [Alter Database](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true).

### <a name="move-a-database-from-the-serverless-compute-tier-into-the-provisioned-compute-tier"></a>Flytta en databas från Server lös beräknings nivån till den allokerade beräknings nivån

En server lös databas kan flyttas till en allokerad beräknings nivå på samma sätt som en allokerad beräknings databas flyttas till en server lös beräknings nivå.

## <a name="modifying-serverless-configuration"></a>Ändra konfiguration utan Server

### <a name="use-powershell"></a>Använd PowerShell

Att ändra den maximala eller lägsta virtuella kärnor och den autopausade fördröjningen utförs med hjälp av kommandot [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) i PowerShell med `MaxVcore` `MinVcore` `AutoPauseDelayInMinutes` argumenten, och.

### <a name="use-the-azure-cli"></a>Använda Azure CLI

Att ändra den maximala eller lägsta virtuella kärnor och den autopausade fördröjningen utförs med kommandot [AZ SQL DB Update](/cli/azure/sql/db#az-sql-db-update) i Azure CLI med `capacity` `min-capacity` `auto-pause-delay` argumenten, och.


## <a name="monitoring"></a>Övervakning

### <a name="resources-used-and-billed"></a>Använda och fakturerade resurser

Resurserna för en server lös databas kapslas av appaket, SQL-instans och entiteter för resurspooler för användare.

#### <a name="app-package"></a>Appaket

Appaketet är den yttre mest resurs hanterings gränserna för en databas, oavsett om databasen finns i en server lös eller allokerad beräknings nivå. Appaketet innehåller SQL-instansen och externa tjänster som tillsammans omfattar alla användar-och system resurser som används av en databas i SQL Database. Exempel på externa tjänster är R och full texts ökning. SQL-instansen dominerar vanligt vis den övergripande resursutnyttjande över Appaketet.

#### <a name="user-resource-pool"></a>Resurspool för användare

Resurspoolen är den inre resurs hanterings gränserna för en databas, oavsett om databasen finns i en server lös eller allokerad beräknings nivå. Användarens resurspool omfångerar CPU och IO för användar arbets belastningar som skapats av DDL-frågor som skapa och ändra och DML-frågor, till exempel SELECT, INSERT, UPDATE och DELETE. Dessa frågor representerar vanligt vis den mest betydande delen av användningen i Appaketet.

### <a name="metrics"></a>Mått

Mät värden för att övervaka resursanvändningen för Appaketet och poolen för en server lös databas visas i följande tabell:

|Entitet|Metric|Beskrivning|Enheter|
|---|---|---|---|
|Appaket|app_cpu_percent|Procent andelen av virtuella kärnor som används av appen i förhållande till högsta tillåtna virtuella kärnor för appen.|Procent|
|Appaket|app_cpu_billed|Mängden data som debiteras för appen under rapporterings perioden. Det belopp som betalas under perioden är produkten av det här måttet och vCore enhets pris. <br><br>Värdena för det här måttet bestäms genom agg regering över tid för maximalt CPU-använt och minne som används varje sekund. Om det använda beloppet är mindre än det lägsta belopp som har angetts som den lägsta virtuella kärnor och minsta mängden minne, faktureras det lägsta mängd som har allokerats.För att kunna jämföra CPU med minne i fakturerings syfte normaliseras minnet till enheter av virtuella kärnor genom att skala om mängden minne i GB med 3 GB per vCore.|vCore sekunder|
|Appaket|app_memory_percent|Procent andelen minne som används av appen i förhållande till maximalt minne som tillåts för appen.|Procent|
|Adresspool|cpu_percent|Procent andelen av virtuella kärnor som används av användar arbets belastning i förhållande till högsta tillåtna virtuella kärnor för användar arbets belastning.|Procent|
|Adresspool|data_IO_percent|Procent andel data IOPS som används av användar arbets belastning i förhållande till maximal data-IOPS som tillåts för användar arbets belastning.|Procent|
|Adresspool|log_IO_percent|Procent andel logg MB/s som används av användar arbets belastning i förhållande till maximalt antal loggar i MB/s som tillåts för användar arbets belastning.|Procent|
|Adresspool|workers_percent|Procent andel arbetare som används av användar arbets belastning i förhållande till max arbetare som tillåts för användar arbets belastning.|Procent|
|Adresspool|sessions_percent|Procent andel sessioner som används av användar arbets belastning i förhållande till högsta antal sessioner som tillåts för användar arbets belastning.|Procent|

### <a name="pause-and-resume-status"></a>Pausa och återuppta status

I Azure Portal visas databasens status i fönstret Översikt på servern som innehåller en lista över de databaser som den innehåller. Databasens status visas också i fönstret Översikt för-databasen.

Använd följande kommandon för att fråga efter paus och återuppta status för en databas:

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

För resurs gränser, se [Server lös beräknings nivå](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).

## <a name="billing"></a>Fakturering

Den mängd data som faktureras är det högsta antal CPU-som används och minne som används varje sekund. Om mängden använt CPU och använt minne är mindre än den lägsta mängd som har skapats för varje, faktureras det etablerade beloppet. För att kunna jämföra CPU med minne i fakturerings syfte normaliseras minnet till enheter av virtuella kärnor genom att skala om mängden minne i GB med 3 GB per vCore.

- **Resurs fakturerad**: CPU och minne
- **Fakturerat belopp**: vCore enhets pris * Max (min virtuella kärnor, virtuella kärnor som används, minimalt minne GB * 1/3, använt minnes utrymme * 1/3) 
- **Fakturerings frekvens**: per sekund

Priset för vCore-enheten är kostnaden per vCore per sekund. Mer information finns på [sidan med Azure SQL Database priser](https://azure.microsoft.com/pricing/details/sql-database/single/) för vissa enhets priser i en specifik region.

Den totala mängden data som faktureras exponeras enligt följande mått:

- **Mått**: App_cpu_billed (vCore sekunder)
- **Definition**: Max (min-virtuella kärnor, virtuella kärnor som används, minimalt minne gb * 1/3, använt minnes utrymme * 1/3)
- **Rapport frekvens**: per minut

Den här kvantiteten beräknas varje sekund och sammanställs över 1 minut.

### <a name="minimum-compute-bill"></a>Minsta beräknings faktura

Om en server lös databas har pausats är beräknings fakturan noll.  Om en server lös databas inte är pausad, är den minsta beräknings fakturan inte mindre än mängden virtuella kärnor baserat på max (min virtuella kärnor, minsta minne GB * 1/3).

Exempel:

- Anta att en databas utan server inte har pausats och kon figurer ATS med 8 Max virtuella kärnor och 1 min vCore som motsvarar 3,0 GB för minsta minne.  Sedan baseras den minsta beräknings fakturan på max (1 vCore, 3,0 GB * 1 vCore/3 GB) = 1 vCore.
- Anta att en databas utan server inte har pausats och kon figurer ATS med 4 Max virtuella kärnor och 0,5 min virtuella kärnor som motsvarar 2,1 GB för minsta minne.  Sedan baseras den minsta beräknings fakturan på max (0,5 virtuella kärnor, 2,1 GB * 1 vCore/3 GB) = 0,7 virtuella kärnor.

[Azure SQL Database pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/?service=sql-database) för Server lös kan användas för att fastställa det minsta minnes konfigurerbara värdet baserat på antalet max-och min virtuella kärnor som kon figurer ATS.  Om den minsta virtuella kärnor som kon figurer ATS är större än 0,5 virtuella kärnor, är den minsta beräknings fakturan oberoende av det minsta minne som har kon figurer ATS och enbart baserat på antalet virtuella kärnor som kon figurer ATS.

### <a name="example-scenario"></a>Exempelscenario

Överväg en server lös databas som kon figurer ATS med 1 min vCore och 4 Max virtuella kärnor.  Detta motsvarar cirka 3 GB min minne och maximalt 12 GB minne.  Antag att fördröjningen för automatisk paus är inställd på 6 timmar och databasens arbets belastning är aktiv under de första två timmarna av en 24-timmarsperiod och annars inaktiv.    

I det här fallet faktureras databasen för beräkning och lagring under de första 8 timmarna.  Även om databasen är inaktiv från och med den andra timmen, faktureras den fortfarande för beräkning under de följande 6 timmarna baserat på den lägsta beräkning som etablerades när databasen är online.  Endast lagring faktureras under resten av den 24-timmarsperiod medan databasen pausas.

Mer exakt, beräknings fakturan i det här exemplet beräknas enligt följande:

|Tidsintervall|Virtuella kärnor som används varje sekund|GB som används varje sekund|Beräknings dimension faktureras|vCore sekunder debiteras över tidsintervallet|
|---|---|---|---|---|
|0:00-1:00|4|9|Virtuella kärnor som används|4 virtuella kärnor * 3600 sekunder = 14400 vCore sekunder|
|1:00-2:00|1|12|Minnesanvändning|12 GB * 1/3 * 3600 sekunder = 14400 vCore sekunder|
|2:00-8:00|0|0|Minsta allokerade minne|3 GB * 1/3 * 21600 sekunder = 21600 vCore sekunder|
|8:00-24:00|0|0|Ingen beräkning har pausats|0 vCore sekunder|
|Totalt antal vCore sekunder debiteras över 24 timmar||||50400 vCore sekunder|

Anta att priset för beräknings enheten är $0.000145/vCore/Second.  Sedan debiteras beräkningen för den här 24-timmarsperiod produkten av priset för beräknings enheten och vCore sekunder fakturerat: $0.000145/vCore/Second * 50400 vCore sekunder ~ $7,31

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Azure Hybrid-förmån och reserverad kapacitet

Azure Hybrid-förmån (AHB) och reserverade kapacitets rabatter gäller inte för Server lös beräknings nivån.

## <a name="available-regions"></a>Tillgängliga regioner

Server lös beräknings nivån är tillgänglig över hela världen utom följande regioner: Kina, östra, Kina, norra, Tyskland, centrala, Tyskland nordöstra och US Gov Central (Iowa).

## <a name="next-steps"></a>Nästa steg

- Information om hur du kommer igång finns i [snabb start: skapa en enda databas i Azure SQL Database med hjälp av Azure Portal](single-database-create-quickstart.md).
- För resurs gränser, se [resurs gränser för Server lös beräknings nivå](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).
