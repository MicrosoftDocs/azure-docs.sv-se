---
title: Automatisk skalning av compute-noder i en Azure Batch-pool
description: Aktivera automatisk skalning i en molnpool för att dynamiskt justera antalet beräkningsnoder i poolen.
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: H1Hack27Feb2017, fasttrack-edit, devx-track-csharp
ms.openlocfilehash: f70c29f0e8a313233991c7363dc4b8a41a1b1cd5
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389374"
---
# <a name="create-an-automatic-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Skapa en automatisk formel för att skala beräkningsnoder i en Batch-pool

Azure Batch automatiskt skala pooler baserat på parametrar som du definierar, vilket sparar tid och pengar. Med automatisk skalning lägger Batch dynamiskt till noder i en pool när aktivitetskraven ökar och tar bort beräkningsnoder när aktivitetskraven minskar.

Om du vill aktivera automatisk skalning på en pool med beräkningsnoder associerar du poolen med en *autoskalningsformel* som du definierar. Batch-tjänsten använder autoskalningsformeln för att avgöra hur många noder som behövs för att köra din arbetsbelastning. Dessa noder kan vara dedikerade noder [eller noder med låg prioritet.](batch-low-pri-vms.md) Batch granskar sedan regelbundet tjänstmåttdata och använder dem för att justera antalet noder i poolen baserat på din formel och med ett intervall som du definierar.

Du kan aktivera automatisk skalning när du skapar en pool eller tillämpa den på en befintlig pool. Med Batch kan du utvärdera dina formler innan du tilldelar dem till pooler och övervaka statusen för automatiska skalningskörningar. När du har konfigurerat en pool med automatisk skalning kan du göra ändringar i formeln senare.

> [!IMPORTANT]
> När du skapar ett Batch-konto kan du ange [poolallokeringsläget](accounts.md), som avgör om pooler allokeras i en Batch-tjänstprenumeration (standard) eller i din användarprenumeration. Om du har skapat Batch-kontot med standardkonfigurationen för Batch-tjänsten är ditt konto begränsat till ett maximalt antal kärnor som kan användas för bearbetning. Batch-tjänsten skalar endast beräkningsnoder upp till den kärngränsen. Därför kan Batch-tjänsten inte nå det målantal beräkningsnoder som anges av en autoskalningsformel. Se [Kvoter och begränsningar för Azure Batch för](batch-quota-limit.md) information om hur du visar och ökar dina kontokvoter.
>
>Om du har skapat ditt konto med användarprenumerationsläge delar ditt konto i kärnkvoten för prenumerationen. Mer information finns i [Virtual Machines limits](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits) (Gränser för virtuella datorer) i [Azure subscription and service limits, quotas, and constraints](../azure-resource-manager/management/azure-subscription-service-limits.md) (Prenumerations- och tjänstgränser, kvoter och begränsningar i Azure).

## <a name="autoscale-formulas"></a>Formler för autoskalning

En autoskalningsformel är ett strängvärde som du definierar och som innehåller en eller flera -instruktioner. Autoskalningsformeln tilldelas till en [pools autoScaleFormula-element](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (Batch REST) eller [egenskapen CloudPool.AutoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) (Batch .NET). Batch-tjänsten använder din formel för att fastställa antalet beräkningsnoder i poolen för nästa bearbetningsintervall. Formelsträngen får inte överskrida 8 kB, kan innehålla upp till 100 instruktioner som avgränsas med semikolon och kan innehålla radbrytningar och kommentarer.

Du kan tänka på formler för automatisk skalning som ett "språk" för autoskalning i Batch. Formeluttryck är friformsuttryck som kan innehålla både tjänstdefinierade variabler (definieras av Batch-tjänsten) och användardefinierade variabler. Formler kan utföra olika åtgärder på dessa värden med hjälp av inbyggda typer, operatorer och funktioner. En -instruktion kan till exempel ha följande format:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Formler innehåller vanligtvis flera instruktioner som utför åtgärder på värden som hämtas i tidigare instruktioner. Först hämtar vi till exempel ett värde för `variable1` och skickar det sedan till en funktion för att fylla i `variable2` :

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Inkludera dessa instruktioner i din autoskalningsformel för att komma fram till ett målantal beräkningsnoder. Dedikerade noder och noder med låg prioritet har sina egna målinställningar. En autoskalningsformel kan innehålla ett målvärde för dedikerade noder, ett målvärde för noder med låg prioritet eller både och.

Målantalet noder kan vara högre, lägre eller samma som det aktuella antalet noder av den typen i poolen. Batch utvärderar en pools autoskalningsformel med ett [specifikt intervall för automatisk skalning.](#automatic-scaling-interval) Batch justerar målnumret för varje typ av nod i poolen till det tal som autoskalningsformeln anger vid tidpunkten för utvärderingen.

### <a name="sample-autoscale-formulas"></a>Exempel på formler för autoskalning

Nedan visas exempel på två formler för autoskalning, som kan justeras för att fungera i de flesta scenarier. Variablerna `startingNumberOfVMs` och `maxNumberofVMs` i exempelformeln kan justeras efter dina behov.

#### <a name="pending-tasks"></a>Väntande uppgifter

Med den här autoskalningsformeln skapas poolen först med en enda virtuell dator. Måttet `$PendingTasks` definierar antalet aktiviteter som körs eller köas. Formeln hittar det genomsnittliga antalet väntande aktiviteter under de senaste 180 sekunderna och anger `$TargetDedicatedNodes` variabeln i enlighet med detta. Formeln säkerställer att målantalet dedikerade noder aldrig överstiger 25 virtuella datorer. När nya uppgifter skickas växer poolen automatiskt. När uppgifterna slutförs blir virtuella datorer kostnadsfria och formeln för automatisk skalning krymper poolen.

Den här formeln skalar dedikerade noder, men kan ändras för att även gälla skalningsnoder med låg prioritet.

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
$NodeDeallocationOption = taskcompletion;
```

#### <a name="preempted-nodes"></a>Avinstallerade noder

I det här exemplet skapas en pool som börjar med 25 noder med låg prioritet. Varje gång en nod med låg prioritet preempteras ersätts den med en dedikerad nod. Precis som i det första exemplet förhindrar `maxNumberofVMs` variabeln att poolen överstiger 25 virtuella datorer. Det här exemplet är användbart för att dra nytta av virtuella datorer med låg prioritet samtidigt som du ser till att endast ett fast antal avbrott inträffar under poolens livslängd.

```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
$NodeDeallocationOption = taskcompletion;
```

Du lär dig mer om hur [du skapar formler för autoskalning](#write-an-autoscale-formula) och ser ytterligare exempel på [autoskalningsformel senare](#example-autoscale-formulas) i det här avsnittet.

## <a name="variables"></a>Variabler

Du kan använda både **tjänstdefinierade och** **användardefinierade variabler** i formlerna för autoskalning.

De tjänstdefinierade variablerna är inbyggda i Batch-tjänsten. Vissa tjänstdefinierade variabler är skrivskyddade och vissa är skrivskyddade.

Användardefinierade variabler är variabler som du definierar. I exempelformeln som visas `$TargetDedicatedNodes` ovan `$PendingTasks` är och tjänstdefinierade variabler, medan `startingNumberOfVMs` och är `maxNumberofVMs` användardefinierade variabler.

> [!NOTE]
> Tjänstdefinierade variabler föregås alltid av ett dollartecken ($). För användardefinierade variabler är dollartecknet valfritt.

Följande tabeller visar de skrivskyddade variabler som definieras av Batch-tjänsten.

### <a name="read-write-service-defined-variables"></a>Skrivskyddade tjänstdefinierade variabler

Du kan hämta och ange värden för dessa tjänstdefinierade variabler för att hantera antalet beräkningsnoder i en pool.

| Variabel | Beskrivning |
| --- | --- |
| $TargetDedicatedNodes |Målantalet dedikerade beräkningsnoder för poolen. Detta anges som ett mål eftersom en pool kanske inte alltid uppnår önskat antal noder. Om till exempel antalet dedikerade noder ändras av en autoskalningsutvärdering innan poolen har nått det första målet, kanske inte poolen når målet. <br /><br /> En pool i ett konto som skapats i Batch-tjänstläge kanske inte uppnår sitt mål om målet överskrider en Batch-kontonod eller kärnkvot. En pool i ett konto som skapats i användarprenumerationsläge kanske inte uppnår sitt mål om målet överskrider den delade kärnkvoten för prenumerationen.|
| $TargetLowPriorityNodes |Målantalet beräkningsnoder med låg prioritet för poolen. Detta anges som ett mål eftersom en pool kanske inte alltid uppnår önskat antal noder. Om till exempel målantalet noder med låg prioritet ändras av en autoskalningsutvärdering innan poolen har nått det första målet, kanske poolen inte når målet. En pool kanske inte heller uppnår sitt mål om målet överskrider en Batch-kontonod eller kärnkvot. <br /><br /> Mer information om beräkningsnoder med låg prioritet finns [i Använda lågprioriterade virtuella datorer med Batch](batch-low-pri-vms.md). |
| $NodeDeallocationOption |Den åtgärd som inträffar när beräkningsnoder tas bort från en pool. Möjliga värden:<ul><li>**kör på följande** sätt: Standardvärdet. Avslutar uppgifter omedelbart och placerar dem i jobbkön igen så att de schemalades om. Den här åtgärden säkerställer att målantalet noder nås så snabbt som möjligt. Det kan dock vara mindre effektivt eftersom alla aktiviteter som körs avbryts och sedan måste startas om helt. <li>**terminate**: Avslutar aktiviteter omedelbart och tar bort dem från jobbkön.<li>**taskcompletion:** Väntar på att pågående aktiviteter ska slutföras och tar sedan bort noden från poolen. Använd det här alternativet för att undvika att uppgifter avbryts och tas i kontakt med andra och slösar bort allt arbete som aktiviteten har gjort.<li>**retaineddata:** Väntar på att alla lokala uppgiftshållna data på noden ska rensas innan noden tas bort från poolen.</ul> |

> [!NOTE]
> Variabeln `$TargetDedicatedNodes` kan också anges med hjälp av aliaset `$TargetDedicated` . På samma sätt kan `$TargetLowPriorityNodes` variabeln anges med hjälp av aliaset `$TargetLowPriority` . Om både den fullständigt namngivna variabeln och dess alias anges av formeln har värdet som tilldelats den fullständigt namngivna variabeln företräde.

### <a name="read-only-service-defined-variables"></a>Skrivskyddade tjänstdefinierade variabler

Du kan hämta värdet för dessa tjänstdefinierade variabler för att göra justeringar som baseras på mått från Batch-tjänsten.

> [!IMPORTANT]
> Jobbutgåvar ingår för närvarande inte i variabler som tillhandahåller uppgiftsantal, till exempel $ActiveTasks och $PendingTasks. Beroende på din autoskalningsformel kan detta resultera i att noder tas bort utan att några noder är tillgängliga för att köra jobbutgåsaktiviteter.

| Variabel | Beskrivning |
| --- | --- |
| $CPUPercent |Den genomsnittliga procentandelen cpu-användning. |
| $WallClockSeconds |Antalet förbrukade sekunder. |
| $MemoryBytes |Det genomsnittliga antalet megabyte som används. |
| $DiskBytes |Det genomsnittliga antalet gigabyte som används på de lokala diskarna. |
| $DiskReadBytes |Antalet lästa byte. |
| $DiskWriteBytes |Antalet skrivna byte. |
| $DiskReadOps |Antalet utförda läsdiskåtgärder. |
| $DiskWriteOps |Antalet utförda skrivdiskåtgärder. |
| $NetworkInBytes |Antalet inkommande byte. |
| $NetworkOutBytes |Antalet utgående byte. |
| $SampleNodeCount |Antalet beräkningsnoder. |
| $ActiveTasks |Antalet uppgifter som är redo att köras men som ännu inte körs. Detta omfattar alla uppgifter som är i aktivt tillstånd och vars beroenden har uppfyllts. Alla aktiviteter som är i aktivt tillstånd men vars beroenden inte har uppfyllts undantas från $ActiveTasks antal. För en aktivitet med flera instanser $ActiveTasks du det antal instanser som angetts för uppgiften.|
| $RunningTasks |Antalet aktiviteter i körningstillstånd. |
| $PendingTasks |Summan av $ActiveTasks och $RunningTasks. |
| $SucceededTasks |Antalet uppgifter som har slutförts. |
| $FailedTasks |Antalet aktiviteter som misslyckades. |
| $TaskSlotsPerNode |Antalet uppgiftsplatser som kan användas för att köra samtidiga aktiviteter på en enda beräkningsnod i poolen. |
| $CurrentDedicatedNodes |Det aktuella antalet dedikerade beräkningsnoder. |
| $CurrentLowPriorityNodes |Det aktuella antalet beräkningsnoder med låg prioritet, inklusive eventuella noder som har avfigerats. |
| $PreemptedNodeCount | Antalet noder i poolen som är i ett avfröt tillstånd. |

> [!TIP]
> Dessa skrivskyddade tjänstdefinierade variabler är objekt som *tillhandahåller olika* metoder för att komma åt data som är associerade med var och en. Mer information finns i Hämta [exempeldata senare](#obtain-sample-data) i den här artikeln.

> [!NOTE]
> Använd vid skalning baserat på antalet aktiviteter som körs vid en tidpunkt och vid skalning baserat på antalet aktiviteter som ska `$RunningTasks` `$ActiveTasks` köras i kö.

## <a name="types"></a>Typer

Formler för autoskalning stöder följande typer:

- double
- doubleVec
- doubleVecList
- sträng
- tidsstämpel – en sammansatt struktur som innehåller följande medlemmar:
  - år
  - månad (1–12)
  - dag (1–31)
  - weekday (i formatet tal, till exempel 1 för måndag)
  - hour (i 24-timmarsformat, till exempel 13 betyder 1 PM)
  - minut (00–59)
  - sekund (00–59)
- timeinterval
  - TimeInterval_Zero
  - TimeInterval_100ns
  - TimeInterval_Microsecond
  - TimeInterval_Millisecond
  - TimeInterval_Second
  - TimeInterval_Minute
  - TimeInterval_Hour
  - TimeInterval_Day
  - TimeInterval_Week
  - TimeInterval_Year

## <a name="operations"></a>Operations

De här åtgärderna tillåts för de typer som visas i föregående avsnitt.

| Åtgärd | Operatorer som stöds | Resultattyp |
| --- | --- | --- |
| *double-operatorn* double |+, -, *, / |double |
| *double-operatorn* timeinterval |* |timeinterval |
| doubleVec *operator* double |+, -, *, / |doubleVec |
| doubleVec *operator* doubleVec |+, -, *, / |doubleVec |
| timeinterval *operator* double |*, / |timeinterval |
| timeinterval *operator* timeinterval |+, - |timeinterval |
| tidsstämpel för tidsintervaloperator  |+ |timestamp |
| *tidsstämpeloperatorn* timeinterval |+ |timestamp |
| *tidsstämpeloperatorns* tidsstämpel |- |timeinterval |
| *operator double* |-, ! |double |
| *operator* timeinterval |- |timeinterval |
| *double-operator* double |<, <=, ==, >=, >, != |double |
| *strängoperatorsträng* |<, <=, ==, >=, >, != |double |
| *tidsstämpeloperatorns* tidsstämpel |<, <=, ==, >=, >, != |double |
| timeinterval *operator* timeinterval |<, <=, ==, >=, >, != |double |
| *double-operator* double |&&, &#124;&#124; |double |

När du testar en double med en ternary-operator ( `double ? statement1 : statement2` ) är noll **true** och noll är **falskt**.

## <a name="functions"></a>Functions

Du kan använda dessa fördefinierade **funktioner när** du definierar en autoskalningsformel.

| Funktion | Returtyp | Beskrivning |
| --- | --- | --- |
| avg(doubleVecList) |double |Returnerar medelvärdet för alla värden i doubleVecList. |
| len(doubleVecList) |double |Returnerar längden på vektorn som skapas från doubleVecList. |
| lg(double) |double |Returnerar loggbasen 2 för dubbeln. |
| lg(doubleVecList) |doubleVec |Returnerar komponentvis loggbas 2 av doubleVecList. En vec(double) måste uttryckligen skickas för parametern . Annars antas den dubbla lg(double)-versionen. |
| ln(double) |double |Returnerar den naturliga loggen för double. |
| ln(doubleVecList) |doubleVec |Returnerar den naturliga loggen för double. |
| log(double) |double |Returnerar loggbasen 10 av double. |
| log(doubleVecList) |doubleVec |Returnerar den komponentvisa loggbasen 10 för doubleVecList. En vec(double) måste uttryckligen skickas för den enkla dubbelparametern. Annars antas den dubbla loggversionen (dubbel). |
| max(doubleVecList) |double |Returnerar det maximala värdet i doubleVecList. |
| min(doubleVecList) |double |Returnerar det minsta värdet i doubleVecList. |
| norm(doubleVecList) |double |Returnerar två normen för vektorn som skapas från doubleVecList. |
| percentile(doubleVec v, double p) |double |Returnerar percentilelementet i vektorn v. |
| rand() |double |Returnerar ett slumpmässigt värde mellan 0,0 och 1,0. |
| range(doubleVecList) |double |Returnerar skillnaden mellan min- och maxvärdena i doubleVecList. |
| std(doubleVecList) |double |Returnerar exempelstandardavvikelsen för värdena i doubleVecList. |
| stop() | |Stoppar utvärderingen av autoskalningsuttrycket. |
| sum(doubleVecList) |double |Returnerar summan av alla komponenter i doubleVecList. |
| time(string dateTime="") |timestamp |Returnerar tidsstämpeln för den aktuella tiden om inga parametrar skickas, eller tidsstämpeln för dateTime-strängen om detta skickas. DateTime-format som stöds är W3C-DTF och RFC 1123. |
| val(doubleVec v, double i) |double |Returnerar värdet för elementet som finns på plats i i i vector v, med startindexet noll. |

Några av funktionerna som beskrivs i föregående tabell kan ta en lista som argument. Den kommaavgränsade listan är valfri kombination av *double* och *doubleVec*. Exempel:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

*DoubleVecList-värdet* konverteras till en *doubleVec före* utvärderingen. Om till exempel , `v = [1,2,3]` motsvarar `avg(v)` anropet att anropa `avg(1,2,3)` . Anrop `avg(v, 7)` motsvarar att anropa `avg(1,2,3,7)` .

## <a name="metrics"></a>Mått

Du kan använda både resurs- och uppgiftsmått när du definierar en formel. Du justerar antalet dedikerade noder i poolen baserat på de måttdata som du hämtar och utvärderar. Mer information om varje mått finns i [avsnittet Variabler](#variables) ovan.

<table>
  <tr>
    <th>Metric</th>
    <th>Beskrivning</th>
  </tr>
  <tr>
    <td><b>Resurs</b></td>
    <td><p>Resursmått baseras på processorn, bandbredden, minnesanvändningen för beräkningsnoder och antalet noder.</p>
        <p> Dessa tjänstdefinierade variabler är användbara för att göra justeringar baserat på antalet noder:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$PreemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Dessa tjänstdefinierade variabler är användbara för att göra justeringar baserat på nodresursanvändning:</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Uppgift</b></td>
    <td><p>Uppgiftsmått baseras på status för aktiviteter, till exempel Aktiv, Väntar och Slutförd. Följande tjänstdefinierade variabler är användbara för att göra justeringar av poolstorleken baserat på uppgiftsmått:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="obtain-sample-data"></a>Hämta exempeldata

Kärnåtgärden i en autoskalningsformel är att hämta uppgifts- och resursmåttdata (exempel) och sedan justera poolstorleken baserat på dessa data. Därför är det viktigt att ha en tydlig förståelse för hur formler för autoskalning interagerar med exempel.

### <a name="methods"></a>Metoder

Formler för autoskalning fungerar på exempel på måttdata som tillhandahålls av Batch-tjänsten. En formel växer eller krymper poolstorleken baserat på de värden som den får. Tjänstdefinierade variabler är objekt som tillhandahåller metoder för att komma åt data som är associerade med objektet. Följande uttryck visar till exempel en begäran om att hämta de senaste fem minuterna av CPU-användning:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

Följande metoder kan användas för att hämta exempeldata om tjänstdefinierade variabler.

| Metod | Beskrivning |
| --- | --- |
| GetSample() |Metoden `GetSample()` returnerar en vektor med dataexempel.<br/><br/>Ett exempel är måttdata på 30 sekunder. Med andra ord hämtas urval var 30:e sekund. Men som anges nedan finns det en fördröjning mellan när ett exempel samlas in och när det är tillgängligt för en formel. Därför kan inte alla exempel för en viss tidsperiod vara tillgängliga för utvärdering med en formel.<ul><li>`doubleVec GetSample(double count)`: Anger antalet exempel som ska hämtas från de senaste exemplen som samlats in. `GetSample(1)` returnerar det senaste tillgängliga exemplet. För mått som bör dock inte användas eftersom det är omöjligt `$CPUPercent` `GetSample(1)` att veta *när* exemplet samlades in. Det kan vara nyligen, eller, på grund av systemproblem, det kan vara mycket äldre. I sådana fall är det bättre att använda ett tidsintervall som visas nedan.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`: Anger en tidsram för insamling av exempeldata. Om du vill kan du också ange procentandelen exempel som måste vara tillgängliga inom den begärda tidsramen. Skulle till exempel `$CPUPercent.GetSample(TimeInterval_Minute * 10)` returnera 20 exempel om alla exempel för de senaste 10 minuterna finns i `CPUPercent` historiken. Om den sista minuten av historiken inte var tillgänglig skulle endast 18 exempel returneras. I det här `$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` fallet skulle misslyckas eftersom endast 90 procent av exemplen är tillgängliga, men skulle `$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` lyckas.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`: Anger en tidsram för att samla in data, med både en starttid och en sluttid. Som nämnts ovan finns det en fördröjning mellan när ett exempel samlas in och när det blir tillgängligt för en formel. Tänk på den här fördröjningen när du använder `GetSample` metoden . Se `GetSamplePercent` nedan. |
| GetSamplePeriod() |Returnerar perioden med exempel som har tagits i en historisk urvalsdatauppsättning. |
| Count() |Returnerar det totala antalet exempel i måtthistoriken. |
| HistoryBeginTime() |Returnerar tidsstämpeln för det äldsta tillgängliga dataprovet för måttet. |
| GetSamplePercent() |Returnerar procentandelen exempel som är tillgängliga för ett visst tidsintervall. Till exempel `doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`. Eftersom metoden `GetSample` misslyckas om procentandelen exempel som returneras är mindre än det angivna `samplePercent` kan du använda metoden för att kontrollera `GetSamplePercent` först. Sedan kan du utföra en alternativ åtgärd om det inte finns tillräckligt med exempel, utan att stoppa den automatiska skalningsutvärderingen. |

### <a name="samples"></a>Exempel

Batch-tjänsten tar regelbundet exempel på uppgifts- och resursmått och gör dem tillgängliga för dina formler för autoskalning. Dessa exempel registreras var 30:e sekund av Batch-tjänsten. Det finns dock vanligtvis en fördröjning mellan när exemplen registrerades och när de görs tillgängliga för (och kan läsas av) dina autoskalningsformlar. Dessutom kan exempel inte registreras för ett visst intervall på grund av faktorer som nätverk eller andra infrastrukturproblem.

### <a name="sample-percentage"></a>Samplingsprocent

När skickas till metoden eller metoden anropas avser procent en jämförelse mellan det totala möjliga antalet exempel som registreras av Batch-tjänsten och antalet exempel som är tillgängliga för din `samplePercent` `GetSample()` `GetSamplePercent()` autoskalningsformel. 

Låt oss titta på ett tidsspann på 10 minuter som exempel. Eftersom urvalen registreras var 30:e sekund inom det 10 minuter långa tidsspann, skulle det maximala totala antalet urval som registrerats av Batch vara 20 urval (2 per minut). Men på grund av den inbyggda svarstiden för rapporteringsmekanismen och andra problem i Azure kan det bara finnas 15 exempel som är tillgängliga för din autoskalningsformel för läsning. För den perioden på 10 minuter kan därför bara 75 % av det totala antalet registrerade urval vara tillgängliga för din formel.

### <a name="getsample-and-sample-ranges"></a>GetSample() och exempelintervall

Formlerna för autoskalning växer och krymper dina pooler genom att lägga till eller ta bort noder. Eftersom noder kostar pengar måste du se till att dina formler använder en intelligent analysmetod som baseras på tillräckligt med data. Vi rekommenderar att du använder en trendanalys i dina formler. Den här typen växer och krymper dina pooler baserat på ett antal insamlade exempel.

Det gör du genom att `GetSample(interval look-back start, interval look-back end)` använda för att returnera en vektor med prover:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

När ovanstående rad utvärderas av Batch returneras ett urval som en vektor med värden. Exempel:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

När du har samlat in vektorn för urvalen kan du sedan använda funktioner som , och för att härleda meningsfulla `min()` värden från det insamlade `max()` `avg()` intervallet.

För ytterligare säkerhet kan du tvinga en formelutvärdering att misslyckas om mindre än en viss samplingsprocent är tillgänglig under en viss tidsperiod. När du tvingar en formelutvärdering att misslyckas instruerar du Batch att upphöra med ytterligare utvärdering av formeln om den angivna procentandelen exempel inte är tillgänglig. I det här fallet görs ingen ändring av poolstorleken. Om du vill ange en procentandel exempel som krävs för att utvärderingen ska lyckas anger du det som den tredje parametern till `GetSample()` . Här anges ett krav på 75 procent av exemplen:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Eftersom det kan uppstå en fördröjning i exemplets tillgänglighet bör du alltid ange ett intervall med en look-back-starttid som är äldre än en minut. Det tar ungefär en minut för exemplen att spridas genom systemet, så exempel i intervallet `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` kanske inte är tillgängliga. Återigen kan du använda procentparametern för `GetSample()` för att tvinga fram ett visst urvalsprocentkrav.

> [!IMPORTANT]
> Vi rekommenderar starkt att du **undviker att bara *förlita dig* `GetSample(1)` på i autoskalningsformeln**. Det beror på att i princip säger till Batch-tjänsten "Ge mig det sista exemplet du `GetSample(1)` har, oavsett hur länge sedan du hämtade det". Eftersom det bara är ett enda exempel, och det kan vara ett äldre exempel, kanske det inte är representativt för den större bilden av den senaste uppgiften eller resurstillståndet. Om du använder kontrollerar du att den är en del av en större -instruktion och inte den `GetSample(1)` enda datapunkt som formeln förlitar sig på.

## <a name="write-an-autoscale-formula"></a>Skriva en autoskalningsformel

Du skapar en autoskalningsformel genom att bilda instruktioner som använder ovanstående komponenter och sedan kombinera dessa -instruktioner i en fullständig formel. I det här avsnittet skapar vi ett exempel på en autoskalningsformel som kan utföra verkliga skalningsbeslut och göra justeringar.

Först ska vi definiera kraven för vår nya autoskalningsformel. Formeln ska:

- Öka antalet dedikerade beräkningsnoder i en pool om CPU-användningen är hög.
- Minska antalet dedikerade beräkningsnoder i en pool när processoranvändningen är låg.
- Begränsa alltid det maximala antalet dedikerade noder till 400.
- Ta inte bort noder som kör aktiviteter när du minskar antalet noder. Om det behövs väntar du tills aktiviteterna har slutförts innan du tar bort noderna.

Den första instruktionen i vår formel ökar antalet noder vid hög CPU-användning. Vi definierar en -instruktion som fyller i en användardefinierad variabel ( ) med ett värde som är 110 procent av det aktuella målantalet dedikerade noder, men bara om den minsta genomsnittliga CPU-användningen under de senaste 10 minuterna var över `$totalDedicatedNodes` 70 procent. Annars används värdet för det aktuella antalet dedikerade noder.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

För att minska antalet dedikerade noder vid låg CPU-användning anger nästa instruktion i vår formel samma variabel till 90 procent av det aktuella målantalet dedikerade noder, om den genomsnittliga CPU-användningen under de senaste 60 minuterna `$totalDedicatedNodes` var under 20 procent. Annars används det aktuella värdet för som `$totalDedicatedNodes` vi fyllt i i instruktionen ovan.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Nu begränsar vi antalet dedikerade beräkningsnoder till högst 400.

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Slutligen ser vi till att noderna inte tas bort förrän deras uppgifter har slutförts.

```
$NodeDeallocationOption = taskcompletion;
```

Här är den fullständiga formeln:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
$NodeDeallocationOption = taskcompletion;
```

> [!NOTE]
> Om du väljer att inkludera både kommentarer och radbrytningar i formelsträngar. Tänk också på att saknade semikolon kan resultera i utvärderingsfel.

## <a name="automatic-scaling-interval"></a>Intervall för automatisk skalning

Som standard justerar Batch-tjänsten en pools storlek enligt dess autoskalningsformel var 15:e minut. Det här intervallet kan konfigureras med hjälp av följande poolegenskaper:

- [CloudPool.AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (Batch .NET)
- [autoScaleEvaluationInterval](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (REST API)

Det minsta intervallet är fem minuter och det högsta är 168 timmar. Om ett intervall utanför det här intervallet anges returnerar Batch-tjänsten felet Bad Request (400).

> [!NOTE]
> Autoskalning är för närvarande inte avsett att svara på ändringar på mindre än en minut, utan är i stället avsett att justera storleken på poolen gradvis när du kör en arbetsbelastning.

## <a name="create-an-autoscale-enabled-pool-with-batch-sdks"></a>Skapa en autoskalningsaktiverad pool med Batch-SDK:er

Automatisk skalning av pooler kan konfigureras med någon av [Batch-SDK:erna](batch-apis-tools.md#azure-accounts-for-batch-development), [Batch REST API](/rest/api/batchservice/) [Batch PowerShell-cmdletarna](batch-powershell-cmdlets-get-started.md)och [Batch CLI.](batch-cli-get-started.md) I det här avsnittet kan du se exempel för både .NET och Python.

### <a name="net"></a>.NET

Följ dessa steg om du vill skapa en pool med automatisk skalning aktiverat i .NET:

1. Skapa poolen med [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
1. Ange egenskapen [CloudPool.AutoScaleEnabled](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) till `true` .
1. Ange egenskapen [CloudPool.AutoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) med autoskalningsformeln.
1. (Valfritt) Ange egenskapen [CloudPool.AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (standardvärdet är 15 minuter).
1. Genomför poolen med [CloudPool.Commit eller](/dotnet/api/microsoft.azure.batch.cloudpool.commit) [CommitAsync](/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

I följande exempel skapas en autoskalningsaktiverad pool i .NET. Poolens autoskalningsformel anger antalet dedikerade noder till 5 på måndagar och till 1 varannan dag i veckan. Intervallet [för automatisk skalning](#automatic-scaling-interval) är inställt på 30 minuter. I den här och de andra C#-kodfragmenten i den här artikeln är en korrekt `myBatchClient` initierad instans av [klassen BatchClient.](/dotnet/api/microsoft.azure.batch.batchclient)

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "standard_d1_v2",
                    VirtualMachineConfiguration: new VirtualMachineConfiguration(
                        imageReference: new ImageReference(
                                            publisher: "MicrosoftWindowsServer",
                                            offer: "WindowsServer",
                                            sku: "2019-datacenter-core",
                                            version: "latest"),
                        nodeAgentSkuId: "batch.node.windows amd64");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> När du skapar en autoskalningsaktiverad pool ska du inte ange parametern _targetDedicatedNodes_ eller _targetLowPriorityNodes_ för anropet **till CreatePool**. Ange i stället **egenskaperna AutoScaleEnabled** **och AutoScaleFormula** i poolen. Värdena för dessa egenskaper avgör målnumret för varje typ av nod.
>
> Om du vill ändra storlek på en automatiskt skalningsaktiverad pool manuellt (till exempel med [BatchClient.PoolOperations.ResizePoolAsync)](/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync)måste du först inaktivera automatisk skalning på poolen och sedan ändra storlek på den.

### <a name="python"></a>Python

Så här skapar du en pool med automatisk skalning med Python SDK:

1. Skapa en pool och ange dess konfiguration.
1. Lägg till poolen i tjänstklienten.
1. Aktivera autoskalning i poolen med en formel som du skriver.

I följande exempel visas de här stegen.

```python
# Create a pool; specify configuration
new_pool = batch.models.PoolAddParameter(
    id="autoscale-enabled-pool",
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
          publisher="Canonical",
          offer="UbuntuServer",
          sku="18.04-LTS",
          version="latest"
            ),
        node_agent_sku_id="batch.node.ubuntu 18.04"),
    vm_size="STANDARD_D1_v2",
    target_dedicated_nodes=0,
    target_low_priority_nodes=0
)
batch_service_client.pool.add(new_pool) # Add the pool to the service client

formula = """$curTime = time();
             $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
             $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
             $isWorkingWeekdayHour = $workHours && $isWeekday;
             $TargetDedicated = $isWorkingWeekdayHour ? 20:10;""";

# Enable autoscale; specify the formula
response = batch_service_client.pool.enable_auto_scale(pool_id, auto_scale_formula=formula,
                                            auto_scale_evaluation_interval=datetime.timedelta(minutes=10),
                                            pool_enable_auto_scale_options=None,
                                            custom_headers=None, raw=False)
```

> [!TIP]
> Fler exempel på hur du använder Python SDK finns på [lagringsplatsen för Batch Python-snabbstart](https://github.com/Azure-Samples/batch-python-quickstart) på GitHub.

## <a name="enable-autoscaling-on-an-existing-pool"></a>Aktivera automatisk skalning i en befintlig pool

Varje Batch SDK är ett sätt att aktivera automatisk skalning. Exempel:

- [BatchClient.PoolOperations.EnableAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync) (Batch .NET)
- [Aktivera automatisk skalning på en pool](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (REST API)

Tänk på följande när du aktiverar automatisk skalning i en befintlig pool:

- Om autoskalning för närvarande är inaktiverat i poolen måste du ange en giltig autoskalningsformel när du utfärdar begäran. Om du vill kan du ange ett intervall för automatisk skalning. Om du inte anger något intervall används standardvärdet 15 minuter.
- Om automatisk skalning för närvarande är aktiverat i poolen kan du ange en ny formel, ett nytt intervall eller båda. Du måste ange minst en av dessa egenskaper.
  - Om du anger ett nytt intervall för automatisk skalning stoppas det befintliga schemat och ett nytt schema startas. Starttiden för det nya schemat är den tid då begäran om att aktivera autoskalning utfärdades.
  - Om du utelämnar antingen autoskalningsformeln eller intervallet fortsätter Batch-tjänsten att använda det aktuella värdet för inställningen.

> [!NOTE]
> Om du har angett värden för *parametrarna targetDedicatedNodes* eller *targetLowPriorityNodes* för **metoden CreatePool** när du skapade poolen i .NET, eller för jämförbara parametrar på ett annat språk, ignoreras dessa värden när autoskalningsformeln utvärderas.

Det här C#-exemplet använder [Batch .NET-biblioteket](/dotnet/api/microsoft.azure.batch) för att aktivera automatisk skalning i en befintlig pool.

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Uppdatera en autoskalningsformel

Om du vill uppdatera formeln i en befintlig autoskalningsaktiverad pool anropar du åtgärden för att aktivera autoskalning igen med den nya formeln. Om autoskalning till exempel redan är aktiverat när följande .NET-kod körs ersätts dess autoskalningsformel `myexistingpool` med innehållet i `myNewFormula` .

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Uppdatera autoskalningsintervallet

Om du vill uppdatera utvärderingsintervallet för automatisk skalning för en befintlig automatiskt skalningsaktiverad pool anropar du åtgärden för att aktivera autoskalning igen med det nya intervallet. Om du till exempel vill ange utvärderingsintervallet för automatisk skalning till 60 minuter för en pool som redan är autoskalningsaktiverad i .NET:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Utvärdera en autoskalningsformel

Du kan utvärdera en formel innan du tillämpar den på en pool. På så sätt kan du testa formelns resultat innan du lägger det i produktion.

Innan du kan utvärdera en autoskalningsformel måste du först aktivera autoskalning i poolen med en giltig formel, till exempel enradsformeln `$TargetDedicatedNodes = 0` . Använd sedan något av följande för att utvärdera formeln som du vill testa:

- [BatchClient.PoolOperations.EvaluateAutoScale](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) eller [EvaluateAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Dessa Batch .NET-metoder kräver ID:t för en befintlig pool och en sträng som innehåller autoskalningsformeln som ska utvärderas.

- [Utvärdera en formel för automatisk skalning](/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    I den REST API begäran anger du pool-ID:t i URI:n och autoskalningsformeln i *autoScaleFormula-elementet* i begärandetexten. Svaret på åtgärden innehåller eventuell felinformation som kan vara relaterad till formeln.

Det [här Batch .NET-exemplet](/dotnet/api/microsoft.azure.batch) utvärderar en autoskalningsformel. Om poolen inte redan använder automatisk skalning aktiverar vi den först.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = await batchClient.PoolOperations.GetPoolAsync("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on a non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    await pool.EnableAutoScaleAsync(
        autoscaleFormula: "$TargetDedicatedNodes = $CurrentDedicatedNodes;",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScaleAsync calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    await pool.RefreshAsync();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this code does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        await batchClient.PoolOperations.EvaluateAutoScaleAsync(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        await batchClient.PoolOperations.EnableAutoScaleAsync(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

En lyckad utvärdering av formeln som visas i det här kodfragmentet ger resultat som liknar följande:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Hämta information om autoskalningskörningar

För att säkerställa att formeln fungerar som förväntat rekommenderar vi att du regelbundet kontrollerar resultatet av de autoskalningskörningar som Batch utför i poolen. Det gör du genom att hämta (eller uppdatera) en referens till poolen och sedan undersöka egenskaperna för den senaste autoskalningskörningen.

I Batch .NET har [egenskapen CloudPool.AutoScaleRun](/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) flera egenskaper som innehåller information om den senaste automatiska skalningskörningen som utförs på poolen:

- [AutoScaleRun.Timestamp](/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
- [AutoScaleRun.Results](/dotnet/api/microsoft.azure.batch.autoscalerun.results)
- [AutoScaleRun.Error](/dotnet/api/microsoft.azure.batch.autoscalerun.error)

I REST API returnerar Hämta information om en [poolbegäran](/rest/api/batchservice/get-information-about-a-pool) information om poolen, som innehåller den senaste informationen om automatisk skalningskörning i [egenskapen autoScaleRun.](/rest/api/batchservice/get-information-about-a-pool)

I följande C#-exempel används Batch .NET-biblioteket för att skriva ut information om den senaste autoskalningskörningen i poolen _myPool_.

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Exempel på utdata från föregående exempel:

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicatedNodes=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="get-autoscale-run-history-using-pool-autoscale-events"></a>Hämta körningshistorik för autoskalning med hjälp av autoskalningshändelser för pooler
Du kan också kontrollera historiken för automatisk skalning genom att fråga [PoolAutoScaleEvent](batch-pool-autoscale-event.md). Den här händelsen genereras av Batch-tjänsten för att registrera varje förekomst av utvärdering och körning av autoskalningsformel, vilket kan vara användbart för att felsöka potentiella problem.

Exempelhändelse för PoolAutoScaleEvent:
```json
{
    "id": "poolId",
    "timestamp": "2020-09-21T23:41:36.750Z",
    "formula": "...",
    "results": "$TargetDedicatedNodes=10;$NodeDeallocationOption=requeue;$curTime=2016-10-14T18:36:43.282Z;$isWeekday=1;$isWorkingWeekdayHour=0;$workHours=0",
    "error": {
        "code": "",
        "message": "",
        "values": []
    }
}
```

## <a name="example-autoscale-formulas"></a>Exempel på formler för autoskalning

Nu ska vi titta på några formler som visar olika sätt att justera mängden beräkningsresurser i en pool.

### <a name="example-1-time-based-adjustment"></a>Exempel 1: Tidsbaserad justering

Anta att du vill justera poolstorleken baserat på veckodag och tid på dagen. Det här exemplet visar hur du ökar eller minskar antalet noder i poolen i enlighet med detta.

Formeln hämtar först den aktuella tiden. Om det är en veckodag (1–5) och inom arbetstid (08.00 till 18.00) anges målpoolens storlek till 20 noder. Annars är det inställt på 10 noder.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
$NodeDeallocationOption = taskcompletion;
```

`$curTime` kan justeras för att återspegla din lokala tidszon genom att lägga `time()` till i produkten av och `TimeZoneInterval_Hour` UTC-förskjutningen. Använd till exempel `$curTime = time() + (-6 * TimeInterval_Hour);` för Mountain Daylight Time (MDT). Tänk på att förskjutningen måste justeras i början och slutet av sommartid (om tillämpligt).

### <a name="example-2-task-based-adjustment"></a>Exempel 2: Uppgiftsbaserad justering

I det här C#-exemplet justeras poolstorleken baserat på antalet aktiviteter i kön. Vi har inkluderat både kommentarer och radbrytningar i formelsträngarna.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $PendingTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$PendingTasks.GetSample(1)) : max( $PendingTasks.GetSample(1), avg($PendingTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - let running tasks finish before removing a node
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Exempel 3: Redovisning av parallella uppgifter

Det här C#-exemplet justerar poolstorleken baserat på antalet aktiviteter. Den här formeln tar också hänsyn till [det TaskSlotsPerNode-värde](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) som har angetts för poolen. Den här metoden är användbar i situationer [där parallell uppgiftskörning](batch-parallel-node-tasks.md) har aktiverats i din pool.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks
// (the TaskSlotsPerNode property on this pool is set to 4, adjust
// this number for your use case)
$cores = $TargetDedicatedNodes * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicatedNodes + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicatedNodes = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Exempel 4: Ange en ursprunglig poolstorlek

Det här exemplet visar ett C#-exempel med en autoskalningsformel som anger poolstorleken till ett angivet antal noder under en inledande tidsperiod. Därefter justeras poolstorleken baserat på antalet aktiva aktiviteter som körs.

Mer specifikt gör den här formeln följande:

- Anger den ursprungliga poolstorleken till fyra noder.
- Justerar inte poolstorleken inom de första 10 minuterna av poolens livscykel.
- Efter 10 minuter hämtar det maximala värdet för antalet aktiva och aktiva aktiviteter under de senaste 60 minuterna.
  - Om båda värdena är 0 (vilket anger att inga aktiviteter har körts eller varit aktiva under de senaste 60 minuterna) anges poolstorleken till 0.
  - Om något av dessa värden är större än noll görs ingen ändring.

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicatedNodes = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicatedNodes = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicatedNodes) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>Nästa steg

- Lär dig hur [du kör flera aktiviteter samtidigt på beräkningsnoderna i din pool](batch-parallel-node-tasks.md). Tillsammans med autoskalning kan detta minska jobbvaraktigheten för vissa arbetsbelastningar, vilket sparar pengar.
- Lär dig hur [du frågar Azure Batch tjänsten effektivt](batch-efficient-list-queries.md) för ytterligare effektivitet.
