---
title: Automatisk skalning av compute-noder i en Azure Batch-pool
description: Aktivera automatisk skalning i en molnbaserad pool för att dynamiskt justera antalet datornoder i poolen.
ms.topic: how-to
ms.date: 11/23/2020
ms.custom: H1Hack27Feb2017, fasttrack-edit, devx-track-csharp
ms.openlocfilehash: 06f717e7c3ab8285b494f89c39838af6b0d96c8f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381434"
---
# <a name="create-an-automatic-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Skapa en automatisk formel för skalning av Compute-noder i en batch-pool

Azure Batch kan automatiskt skala pooler baserat på parametrar som du definierar, vilket sparar tid och pengar. Med automatisk skalning lägger batch dynamiskt till noder i en pool när aktivitets behoven ökar, och tar bort datornoder när aktivitets kraven minskar.

Om du vill aktivera automatisk skalning på en pool av datornoder associerar du poolen med en formel för automatisk *skalning* som du definierar. Batch-tjänsten använder den automatiska skalnings formeln för att avgöra hur många noder som behövs för att köra arbets belastningen. Dessa noder kan vara dedikerade noder eller [noder med låg prioritet](batch-low-pri-vms.md). Batch granskar sedan regelbundet tjänst mått data och använder den för att justera antalet noder i poolen baserat på din formel och med ett intervall som du definierar.

Du kan aktivera automatisk skalning när du skapar en pool eller tillämpa den på en befintlig pool. Med batch kan du utvärdera dina formler innan du tilldelar dem till pooler och övervaka statusen för automatiska skalnings körningar. När du har konfigurerat en pool med automatisk skalning kan du göra ändringar i formeln senare.

> [!IMPORTANT]
> När du skapar ett batch-konto kan du ange [poolens fördelnings läge](accounts.md), som avgör om pooler allokeras i en batch-tjänst-prenumeration (standard) eller i din användar prenumeration. Om du har skapat batch-kontot med standard konfigurationen för batch-tjänsten är ditt konto begränsat till ett maximalt antal kärnor som kan användas för bearbetning. Batch-tjänsten skalar bara Compute-noderna upp till den kärn gränsen. Därför kanske batch-tjänsten inte når mål antalet Compute-noder som anges av en autoskalning-formel. Information om hur du visar och ökar dina konto kvoter finns i [kvoter och begränsningar för Azure Batch tjänsten](batch-quota-limit.md) .
>
>Om du har skapat ditt konto med användar prenumerations läge delas ditt konto i den grundläggande kvoten för prenumerationen. Mer information finns i [Virtual Machines limits](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits) (Gränser för virtuella datorer) i [Azure subscription and service limits, quotas, and constraints](../azure-resource-manager/management/azure-subscription-service-limits.md) (Prenumerations- och tjänstgränser, kvoter och begränsningar i Azure).

## <a name="autoscale-formulas"></a>Autoskala formler

En autoskalning-formel är ett sträng värde som du definierar som innehåller en eller flera instruktioner. Den automatiska skalnings formeln tilldelas till en Pools [autoScaleFormula](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) -element (batch rest) eller [CloudPool. autoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) -egenskapen (batch .net). Batch-tjänsten använder din formel för att fastställa mål antalet för datornoderna i poolen för nästa bearbetnings intervall. Formel strängen får inte överskrida 8 KB, kan innehålla upp till 100 instruktioner som skiljs åt av semikolon, och kan innehålla rad brytningar och kommentarer.

Du kan tänka på automatiska skalnings formler som ett språk för autoskalning av batch. Formel uttryck är kostnads fria uttryck som kan innehålla både tjänstedefinierade variabler (definieras av batch-tjänsten) och användardefinierade variabler. Formler kan utföra olika åtgärder på dessa värden genom att använda inbyggda typer, operatorer och funktioner. En instruktion kan till exempel ha följande format:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Formler innehåller vanligt vis flera instruktioner som utför åtgärder på värden som hämtas i tidigare-uttryck. Till exempel får vi först ett värde för `variable1` och skickar det sedan till en funktion för att fylla `variable2` :

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Inkludera dessa instruktioner i den automatiska skalnings formeln för att nå ett mål antal datornoder. Dedikerade noder och noder med låg prioritet har sina egna mål inställningar. En autoskalning-formel kan innehålla ett målvärde för dedikerade noder, ett målvärde för noder med låg prioritet eller båda.

Antalet noder kan vara högre, lägre eller samma som det aktuella antalet noder av den typen i poolen. Batch utvärderar en Pools automatiska skalnings formel vid ett angivet [intervall för automatisk skalning](#automatic-scaling-interval). Batch justerar mål numret för varje typ av nod i poolen till det tal som din autoskalning-formel anger vid tidpunkten för utvärderingen.

### <a name="sample-autoscale-formulas"></a>Exempel formler för autoskalning

Nedan visas exempel på två formler för autoskalning som kan justeras för att fungera i de flesta fall. Variablerna `startingNumberOfVMs` och `maxNumberofVMs` i exempel formlerna kan justeras efter dina behov.

#### <a name="pending-tasks"></a>Väntande uppgifter

Med den här automatiska skalnings formeln skapas poolen ursprungligen med en enda virtuell dator. `$PendingTasks`Måttet definierar antalet uppgifter som körs eller placeras i kö. Formeln hittar det genomsnittliga antalet väntande aktiviteter under de senaste 180 sekunderna och ställer in `$TargetDedicatedNodes` variabeln enligt detta. Formeln ser till att mål antalet för dedikerade noder aldrig överskrider 25 virtuella datorer. När nya uppgifter skickas växer automatiskt poolen. När uppgifter har slutförts blir de virtuella datorerna kostnads fria och den automatiska skalnings formeln minskar poolen.

Den här formeln skalar dedikerade noder, men du kan ändra dem så att de även kan användas för att skala låg prioritets noder.

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
$NodeDeallocationOption = taskcompletion;
```

#### <a name="preempted-nodes"></a>Misslyckade noder

I det här exemplet skapas en pool som börjar med 25 låg prioritets noder. Varje gång en nod med låg prioritet avbryts ersätts den med en dedikerad nod. Som i det första exemplet `maxNumberofVMs` förhindrar variabeln poolen att överskrida 25 virtuella datorer. Det här exemplet är användbart för att dra nytta av virtuella datorer med låg prioritet samtidigt som du även ser till att endast ett fast antal preemptions sker under poolens livstid.

```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
$NodeDeallocationOption = taskcompletion;
```

Du lär dig mer om [hur du skapar formler för autoskalning](#write-an-autoscale-formula) och se ytterligare exempel för att [skala formler](#example-autoscale-formulas) senare i det här avsnittet.

## <a name="variables"></a>Variabler

Du kan använda både **användardefinierade** och **användardefinierade** variabler i formler för autoskalning.

De användardefinierade variablerna är inbyggda i batch-tjänsten. Vissa användardefinierade variabler är skrivskyddade och vissa är skrivskyddade.

Användardefinierade variabler är variabler som du definierar. I exempel formeln som visas ovan `$TargetDedicatedNodes` och `$PendingTasks` är tjänstedefinierade variabler, medan `startingNumberOfVMs` och `maxNumberofVMs` är användardefinierade variabler.

> [!NOTE]
> Tjänstedefinierade variabler föregås alltid av ett dollar tecken ($). För användardefinierade variabler är dollar tecknet valfritt.

I följande tabeller visas Skriv-och skrivskyddade variabler som definieras av batch-tjänsten.

### <a name="read-write-service-defined-variables"></a>Läs-och skriv tjänst – definierade variabler

Du kan hämta och ange värdena för de här tjänstedefinierade variablerna för att hantera antalet datornoder i en pool.

| Variabel | Beskrivning |
| --- | --- |
| $TargetDedicatedNodes |Mål antalet dedikerade datornoder för poolen. Detta anges som ett mål eftersom en pool kanske inte alltid uppnår önskat antal noder. Om till exempel mål antalet dedikerade noder ändras med en utvärdering av autoskalning innan poolen har nått det första målet, kanske poolen inte når målet. <br /><br /> En pool i ett konto som har skapats i batch service mode kanske inte når målet om målet överskrider en nod för batch-konto eller kärn kvot. En pool i ett konto som skapats i användar prenumerations läge kanske inte når målet om målet överskrider den delade kärn kvoten för prenumerationen.|
| $TargetLowPriorityNodes |Mål antalet Compute-noder med låg prioritet för poolen. Detta anges som ett mål eftersom en pool kanske inte alltid uppnår önskat antal noder. Om till exempel mål antalet noder med låg prioritet ändras genom en utvärdering av autoskalning innan poolen har nått det första målet, kanske poolen inte når målet. En pool kan inte heller uppnå målet om målet överskrider en nod för batch-konto eller kärn kvot. <br /><br /> Mer information om Compute-noder med låg prioritet finns i [använda virtuella datorer med låg prioritet med batch](batch-low-pri-vms.md). |
| $NodeDeallocationOption |Den åtgärd som inträffar när Compute-noder tas bort från en pool. Möjliga värden:<ul><li>**köa** om: standardvärdet. Avslutar uppgifter direkt och placerar dem i jobbkön igen, så att de omplaneras. Den här åtgärden säkerställer att mål antalet noder uppnås så snabbt som möjligt. Det kan dock vara mindre effektivt eftersom alla pågående aktiviteter avbryts och måste startas om helt och hållet. <li>**Avsluta**: avslutar aktiviteter direkt och tar bort dem från jobbkön.<li>**taskcompletion**: väntar på att pågående aktiviteter ska slutföras och tar sedan bort noden från poolen. Använd det här alternativet för att undvika att aktiviteter avbryts och köas, vilket gör att det arbete som uppgiften har gjort avbryts.<li>**retaineddata**: väntar på att alla lokala uppgifter som kvarhålls på noden ska rensas innan noden tas bort från poolen.</ul> |

> [!NOTE]
> `$TargetDedicatedNodes`Variabeln kan också anges med aliaset `$TargetDedicated` . På samma sätt `$TargetLowPriorityNodes` kan variabeln anges med hjälp av alias `$TargetLowPriority` . Om både den fullständigt namngivna variabeln och dess alias anges av formeln, prioriteras värdet som är kopplat till den fullständigt namngivna variabeln.

### <a name="read-only-service-defined-variables"></a>Skrivskyddade tjänst-definierade variabler

Du kan hämta värdet för de här tjänstedefinierade variablerna för att göra justeringar som baseras på mått från batch-tjänsten.

> [!IMPORTANT]
> Jobb publicerings aktiviteter ingår för närvarande inte i variabler som tillhandahåller aktivitets antal, till exempel $ActiveTasks och $PendingTasks. Beroende på den automatiska skalnings formeln kan detta resultera i att noder tas bort utan att det finns några tillgängliga noder för att köra jobb publicerings aktiviteter.

| Variabel | Beskrivning |
| --- | --- |
| $CPUPercent |Genomsnittlig procent andel CPU-användning. |
| $WallClockSeconds |Antalet förbrukade sekunder. |
| $MemoryBytes |Det genomsnittliga antalet megabyte som används. |
| $DiskBytes |Det genomsnittliga antalet gigabyte som används på de lokala diskarna. |
| $DiskReadBytes |Antalet lästa byte. |
| $DiskWriteBytes |Antalet skrivna byte. |
| $DiskReadOps |Antal utförda Läs disk åtgärder. |
| $DiskWriteOps |Antalet utförda Skriv åtgärder. |
| $NetworkInBytes |Antalet inkommande byte. |
| $NetworkOutBytes |Antalet utgående byte. |
| $SampleNodeCount |Antalet Compute-noder. |
| $ActiveTasks |Antalet uppgifter som är redo att utföra, men som ännu inte körs. Detta omfattar alla uppgifter som är i det aktiva läget och vars beroenden har uppfyllts. Aktiviteter som är i det aktiva läget men vars beroenden inte har uppfyllts utesluts från $ActiveTasks antalet. För en aktivitet med flera instanser kommer $ActiveTasks att inkludera antalet instanser som har angetts för aktiviteten.|
| $RunningTasks |Antalet uppgifter i ett körnings tillstånd. |
| $PendingTasks |Summan av $ActiveTasks och $RunningTasks. |
| $SucceededTasks |Antalet uppgifter som har slutförts. |
| $FailedTasks |Antalet uppgifter som misslyckades. |
| $TaskSlotsPerNode |Antalet aktivitets platser som kan användas för att köra samtidiga aktiviteter på en enskild Compute-nod i poolen. |
| $CurrentDedicatedNodes |Aktuellt antal dedikerade datornoder. |
| $CurrentLowPriorityNodes |Det aktuella antalet Compute-noder med låg prioritet, inklusive eventuella noder som har blockerats. |
| $PreemptedNodeCount | Antalet noder i poolen som är i ett väntetillstånd-tillstånd. |

> [!TIP]
> Dessa skrivskyddade användardefinierade variabler är *objekt* som tillhandahåller olika metoder för att komma åt data som är kopplade till dem. Mer information finns i [Hämta exempel data](#obtain-sample-data) senare i den här artikeln.

> [!NOTE]
> Används `$RunningTasks` vid skalning baserat på antalet aktiviteter som körs vid en tidpunkt och `$ActiveTasks` vid skalning baserat på antalet aktiviteter som är köade till att köras.

## <a name="types"></a>Typer

Automatiska skalnings formler stöder följande typer:

- double
- doubleVec
- doubleVecList
- sträng
- Timestamp – en sammansatt struktur som innehåller följande medlemmar:
  - år
  - månad (1-12)
  - dag (1-31)
  - veckodag (i tal format, till exempel 1 för måndag)
  - timme (i 24-timmarsformat, till exempel 13 betyder 1 PM)
  - Minute (00-59)
  - sekund (00-59)
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

De här åtgärderna tillåts för de typer som anges i föregående avsnitt.

| Åtgärd | Operatorer som stöds | Resultat typ |
| --- | --- | --- |
| dubbel *operator* dubbel |+, -, *, / |double |
| TimeInterval med dubbla *operatorer* |* |timeinterval |
| doubleVec- *operator* dubbel |+, -, *, / |doubleVec |
| doubleVec- *operatör* doubleVec |+, -, *, / |doubleVec |
| TimeInterval- *operator* dubbel |*, / |timeinterval |
| TimeInterval- *operatör* TimeInterval |+, - |timeinterval |
| tidsstämpel för TimeInterval- *operator* |+ |timestamp |
| TimeInterval för timestamp- *operator* |+ |timestamp |
| Timestamp- *operator* |- |timeinterval |
| *operator* i dubbel |-, ! |double |
| *operator* TimeInterval |- |timeinterval |
| dubbel *operator* dubbel |<, <=, = =, >=, >,! = |double |
| sträng *operator* sträng |<, <=, = =, >=, >,! = |double |
| Timestamp- *operator* |<, <=, = =, >=, >,! = |double |
| TimeInterval- *operatör* TimeInterval |<, <=, = =, >=, >,! = |double |
| dubbel *operator* dubbel |&&  &#124;&#124; |double |

När du testar ett dubbelt värde med en ternär operator ( `double ? statement1 : statement2` ), är noll **Sant** och noll är **falskt**.

## <a name="functions"></a>Functions

Du kan använda dessa fördefinierade **funktioner** när du definierar en formel för autoskalning.

| Funktion | Returtyp | Beskrivning |
| --- | --- | --- |
| AVG (doubleVecList) |double |Returnerar det genomsnittliga värdet för alla värden i doubleVecList. |
| längd (doubleVecList) |double |Returnerar längden på den Vector som skapas från doubleVecList. |
| LG (Double) |double |Returnerar logg basen 2 för Double. |
| LG (doubleVecList) |doubleVec |Returnerar komponentens logg bas 2 i doubleVecList. En VEC (Double) måste uttryckligen skickas för parametern. Annars antas dubbelt LG-version (Double). |
| ln (dubbel) |double |Returnerar den naturliga loggen för Double. |
| ln (doubleVecList) |doubleVec |Returnerar den naturliga loggen för Double. |
| logg (dubbel) |double |Returnerar logg basen 10 för Double. |
| logg (doubleVecList) |doubleVec |Returnerar komponentens logg bas 10 i doubleVecList. En VEC (Double) måste uttryckligen skickas för enkel dubbel parameter. I annat fall antas dubbel-och dubbel logg version (Double). |
| Max (doubleVecList) |double |Returnerar det maximala värdet i doubleVecList. |
| min (doubleVecList) |double |Returnerar det minsta värdet i doubleVecList. |
| norm (doubleVecList) |double |Returnerar dubbel-normen för den Vector som skapas från doubleVecList. |
| percentil (doubleVec v, dubbel p) |double |Returnerar percentilvärdet i Vector v. |
| rand() |double |Returnerar ett slumpmässigt värde mellan 0,0 och 1,0. |
| intervall (doubleVecList) |double |Returnerar skillnaden mellan det minsta och högsta värdet i doubleVecList. |
| STD (doubleVecList) |double |Returnerar exempel standard avvikelsen för värdena i doubleVecList. |
| stoppa () | |Stoppar utvärderingen av uttrycket för automatisk skalning. |
| sum (doubleVecList) |double |Returnerar summan av alla komponenter i doubleVecList. |
| tid (String dateTime = "") |timestamp |Returnerar tidsstämpeln för den aktuella tiden om inga parametrar har skickats eller tidsstämpeln för datum/tid-strängen om den skickas. DateTime-format som stöds är W3C-DTF och RFC 1123. |
| val (doubleVec v, dubbel i) |double |Returnerar värdet för det element som finns på plats i i Vector v, med start indexet noll. |

Några av funktionerna som beskrivs i föregående tabell kan godkänna en lista som ett argument. Den kommaseparerade listan är en kombination av *dubbel* -och *doubleVec*. Exempel:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

*DoubleVecList* -värdet konverteras till en enda *doubleVec* före utvärdering. Om till exempel `v = [1,2,3]` `avg(v)` motsvarar anrop motsvarar anrop `avg(1,2,3)` . Anrop `avg(v, 7)` motsvarar anrop `avg(1,2,3,7)` .

## <a name="metrics"></a>Mått

Du kan använda både resurs-och aktivitets mått när du definierar en formel. Du justerar mål antalet dedikerade noder i poolen baserat på de mått data som du hämtar och utvärderar. Mer information om varje mått finns i avsnittet [variabler](#variables) ovan.

<table>
  <tr>
    <th>Metric</th>
    <th>Beskrivning</th>
  </tr>
  <tr>
    <td><b>Resurs</b></td>
    <td><p>Resurs mått baseras på processorn, bandbredden, minnes användningen för Compute-noder och antalet noder.</p>
        <p> Dessa användardefinierade variabler är användbara för att göra justeringar baserat på antalet noder:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$PreemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>De här tjänstedefinierade variablerna är användbara för att göra justeringar baserat på resursanvändningen i noden:</p>
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
    <td><p>Uppgifts måtten baseras på aktiviteternas status, till exempel aktiv, väntande och slutförd. Följande tjänstedefinierade variabler är användbara för att göra ändringar i pool storlek baserat på aktivitets mått:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="obtain-sample-data"></a>Hämta exempel data

Den grundläggande åtgärden för en autoskalning-formel är att hämta uppgifter om aktiviteter och resurser (exempel) och sedan ändra storlek på poolen baserat på dessa data. Därför är det viktigt att du får en tydlig förståelse för hur autoskalning formler interagerar med exempel.

### <a name="methods"></a>Metoder

Autoskalning av formler fungerar på prover av mått data från batch-tjänsten. En formel kommer att öka eller minska storleken på poolen baserat på de värden som den erhåller. Tjänstedefinierade variabler är objekt som tillhandahåller metoder för att komma åt data som är associerade med objektet. Följande uttryck visar till exempel en begäran om att hämta de senaste fem minuterna processor användningen:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

Följande metoder kan användas för att hämta exempel data om tjänstedefinierade variabler.

| Metod | Beskrivning |
| --- | --- |
| GetSample() |`GetSample()`Metoden returnerar en Vector med data exempel.<br/><br/>Ett exempel är 30 sekunders värd för Mät data. Med andra ord hämtas exempel var 30: e sekund. Men enligt vad som anges nedan uppstår en fördröjning mellan när ett exempel samlas in och när det är tillgängligt för en formel. Därför kan inte alla prover under en viss tids period vara tillgängliga för utvärdering av en formel.<ul><li>`doubleVec GetSample(double count)`: Anger antalet prover som ska hämtas från de senaste insamlade exemplen som samlats in. `GetSample(1)` Returnerar det senaste tillgängliga exemplet. För mått som till exempel bör `$CPUPercent` dock `GetSample(1)` inte användas, eftersom det är omöjligt att veta *när* exemplet samlades in. Det kan vara nyligen, eller på grund av system problem kan det vara mycket äldre. I sådana fall är det bättre att använda ett tidsintervall som visas nedan.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`: Anger en tidsram för insamling av exempel data. Om du vill kan du också ange procent andelen exempel som måste vara tillgängliga i den begärda tids ramen. Skulle till exempel `$CPUPercent.GetSample(TimeInterval_Minute * 10)` kunna returnera 20 exempel om alla exempel för de senaste 10 minuterna finns i `CPUPercent` historiken. Om den sista minuten i historiken inte var tillgänglig returneras bara 18 exempel. I detta fall `$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` Miss lyckas eftersom endast 90 procent av exemplen är tillgängliga, men `$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` skulle lyckas.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`: Anger en tidsram för insamling av data, med både en start tid och en slut tid. Som nämnts ovan uppstår en fördröjning mellan när ett exempel samlas in och när det blir tillgängligt för en formel. Ta hänsyn till den här fördröjningen när du använder- `GetSample` metoden. Se `GetSamplePercent` nedan. |
| GetSamplePeriod() |Returnerar den period med exempel som togs i en historisk exempel data uppsättning. |
| Count () |Returnerar det totala antalet exempel i mått historiken. |
| HistoryBeginTime() |Returnerar tidstämpeln för det äldsta tillgängliga data exemplet för måttet. |
| GetSamplePercent() |Returnerar procent andelen exempel som är tillgängliga under ett angivet tidsintervall. Till exempel `doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`. Eftersom `GetSample` metoden Miss lyckas om procent andelen av exempel som returneras är mindre än den `samplePercent` angivna kan du använda `GetSamplePercent` metoden för att kontrol lera först. Sedan kan du utföra en alternativ åtgärd om det inte finns tillräckligt många exempel, utan att stoppa den automatiska skalnings utvärderingen. |

### <a name="samples"></a>Exempel

Batch-tjänsten tar regelbundet prover av aktivitets-och resurs mått och gör dem tillgängliga för autoskalning av formler. De här exemplen registreras var 30: e sekund av batch-tjänsten. Det finns dock en fördröjning mellan när dessa exempel registrerades och när de görs tillgängliga för (och kan läsas av) formler för autoskalning. Dessutom kan exempel inte registreras för ett visst intervall på grund av faktorer som nätverks-eller andra infrastruktur problem.

### <a name="sample-percentage"></a>Samplings procent

När `samplePercent` skickas till `GetSample()` -metoden eller `GetSamplePercent()` metoden anropas, _procent_ refererar till en jämförelse mellan det totala antalet exempel som registreras av batch-tjänsten och antalet exempel som är tillgängliga för den automatiska skalnings formeln.

Nu ska vi titta på en 10-minuters TimeSpan som exempel. Eftersom exempel registreras var 30: e sekund inom 10 minuters TimeSpan, skulle det maximala antalet prover som registreras av batch bli 20 exempel (2 per minut). På grund av rapporterings mekanismen och andra problem i Azure kan det dock finnas 15 exempel som är tillgängliga för den automatiska skalnings formeln för läsning. Till exempel för den 10 minuters perioden kan bara 75% av det totala antalet inspelade prover vara tillgängliga för din formel.

### <a name="getsample-and-sample-ranges"></a>GetSample () och exempel intervall

De automatiska skalnings formlerna kommer att växa och minska dina pooler genom att lägga till eller ta bort noder. Eftersom noder kostar dig pengar bör du se till att dina formler använder en intelligent analys metod som baseras på tillräckligt med data. Vi rekommenderar att du använder en Trends typs analys i dina formler. Den här typen ökar och krymper dina pooler baserat på en mängd insamlade exempel.

För att göra det använder `GetSample(interval look-back start, interval look-back end)` du för att returnera en Vector med exempel:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

När raden ovan utvärderas av batch returneras ett intervall med exempel som en vektor med värden. Exempel:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

När du har samlat in vektorn av exempel kan du använda funktioner som `min()` , `max()` och `avg()` för att härleda meningsfulla värden från det insamlade intervallet.

För ytterligare säkerhet kan du tvinga en formel utvärdering att fungera om mindre än en viss samplings procent är tillgänglig under en viss tids period. När du tvingar fram en formel utvärdering som inte fungerar, instruerar du batch att upphöra med ytterligare utvärdering av formeln om den angivna procent andelen av exempel inte är tillgänglig. I detta fall görs ingen ändring av poolens storlek. Om du vill ange en nödvändig procent andel exempel för utvärderingen som ska lyckas anger du den som den tredje parametern till `GetSample()` . Här anges ett krav på 75 procent av proven:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Eftersom det kan uppstå en fördröjning i exempel tillgänglighet bör du alltid ange ett tidsintervall med en start tid som är äldre än en minut. Det tar ungefär en minut för exempel att spridas genom systemet, så exempel i intervallet `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` kanske inte är tillgängliga. Igen kan du använda parameter procent för `GetSample()` för att framtvinga ett visst exempel på procent krav.

> [!IMPORTANT]
> Vi rekommenderar starkt att du **inte *bara* förlitar dig på `GetSample(1)` i formler för autoskalning**. Detta beror på att i `GetSample(1)` stort sett är batch-tjänsten "ge mig det senaste exemplet, oavsett hur länge sedan du hämtade det." Eftersom det bara är ett enda exempel och det kan vara ett äldre exempel, kanske det inte är representativt för den större bilden av den senaste aktiviteten eller resursens tillstånd. Om du använder `GetSample(1)` ska du kontrol lera att den är en del av en större instruktion och inte den enda data punkt som formeln använder.

## <a name="write-an-autoscale-formula"></a>Skriv en formel för autoskalning

Du skapar en autoskalning-formel genom att formulera instruktioner som använder ovanstående komponenter och sedan kombinera dessa uttryck i en fullständig formel. I det här avsnittet skapar vi en exempel automatisk skalnings formel som kan utföra verkliga skalnings beslut och göra justeringar.

Först ska vi definiera kraven för den nya autoskalning-formeln. Formeln bör:

- Öka mål antalet dedikerade datornoder i en pool om CPU-användningen är hög.
- Minska mål antalet för dedikerade datornoder i en pool när CPU-användningen är låg.
- Begränsa alltid det maximala antalet dedikerade noder till 400.
- När du minskar antalet noder tar du inte bort noder som kör aktiviteterna. om det behövs väntar du tills aktiviteterna har avslut ATS innan du tar bort noder.

Den första instruktionen i vår formel kommer att öka antalet noder under hög CPU-användning. Vi definierar en instruktion som fyller en användardefinierad variabel ( `$totalDedicatedNodes` ) med ett värde som är 110 procent av det aktuella mål antalet dedikerade noder, men endast om den minsta genomsnittliga processor användningen under de senaste 10 minuterna var högre än 70 procent. Annars används värdet för det aktuella antalet dedikerade noder.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Om du vill minska antalet dedikerade noder under låg CPU-användning anger nästa instruktion i vår formel samma `$totalDedicatedNodes` variabel till 90 procent av det aktuella mål antalet dedikerade noder, om den genomsnittliga CPU-användningen under de senaste 60 minuterna var under 20 procent. Annars används det aktuella värdet för `$totalDedicatedNodes` som vi fyllde i i instruktionen ovan.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Nu begränsar vi mål antalet dedikerade datornoder till högst 400.

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Slutligen ser vi till att noderna inte tas bort förrän deras aktiviteter har avslut ATS.

```
$NodeDeallocationOption = taskcompletion;
```

Här är en fullständig formel:

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
> Om du väljer till kan du inkludera både kommentarer och rad brytningar i formel strängar. Tänk också på att saknade semikolon kan resultera i utvärderings fel.

## <a name="automatic-scaling-interval"></a>Intervall för automatisk skalning

Som standard justerar batch-tjänsten en Pools storlek enligt dess autoskalning-formel var 15: e minut. Intervallet kan konfigureras med följande egenskaper för poolen:

- [CloudPool. AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (batch .net)
- [autoScaleEvaluationInterval](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (REST API)

Det minsta intervallet är fem minuter och det maximala värdet är 168 timmar. Om ett intervall utanför det här intervallet anges returnerar batch-tjänsten ett fel av fel meddelandet (400).

> [!NOTE]
> Autoskalning är för närvarande inte avsett att svara på ändringar på mindre än en minut, men är i stället avsett att justera storleken på poolen gradvis när du kör en arbets belastning.

## <a name="create-an-autoscale-enabled-pool-with-batch-sdks"></a>Skapa en pool med autoskalning som är aktive rad med batch SDK: er

Automatisk skalning av pooler kan konfigureras med någon av [batch SDK: erna](batch-apis-tools.md#azure-accounts-for-batch-development), [batch-REST API](/rest/api/batchservice/) [batch PowerShell-cmdletar](batch-powershell-cmdlets-get-started.md)och [batch CLI](batch-cli-get-started.md). I det här avsnittet kan du se exempel för både .NET och python.

### <a name="net"></a>.NET

Följ dessa steg om du vill skapa en pool med autoskalning aktiverat i .NET:

1. Skapa poolen med [metoden batchclient. PoolOperations. CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
1. Ange egenskapen [CloudPool. AutoScaleEnabled](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) till `true` .
1. Ange egenskapen [CloudPool. AutoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) med formeln för autoskalning.
1. Valfritt Ange egenskapen [CloudPool. AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (Standardvärdet är 15 minuter).
1. Genomför poolen med [CloudPool. commit](/dotnet/api/microsoft.azure.batch.cloudpool.commit) eller [CommitAsync](/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

I följande exempel skapas en pool med autoskalning som är aktive rad i .NET. Poolens formel för autoskalning anger mål antalet dedikerade noder till 5 på måndagar, och till 1 på varannan dag i veckan. [Intervallet för automatisk skalning](#automatic-scaling-interval) är inställt på 30 minuter. I det här och de andra C#-kodfragmenten i den här artikeln `myBatchClient` är en korrekt initierad instans av klassen [metoden batchclient](/dotnet/api/microsoft.azure.batch.batchclient) .

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "standard_d1_v2",
                    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> När du skapar en pool med autoskalning aktive rad ska du inte ange parametern _targetDedicatedNodes_ eller parametern _targetLowPriorityNodes_ på anropet till **CreatePool**. Ange i stället egenskaperna **AutoScaleEnabled** och **AutoScaleFormula** i poolen. Värdena för dessa egenskaper bestämmer mål antalet för varje typ av nod.
>
> Om du vill ändra storlek på en autoskale-aktiverad pool manuellt (till exempel med [metoden batchclient. PoolOperations. ResizePoolAsync](/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync)), måste du först inaktivera automatisk skalning på poolen och sedan ändra storlek på den.

### <a name="python"></a>Python

Så här skapar du en pool med automatiska skalnings funktioner med python SDK:

1. Skapa en pool och ange dess konfiguration.
1. Lägg till poolen till tjänst klienten.
1. Aktivera autoskalning på poolen med en formel som du skriver.

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
> Fler exempel på hur du använder python SDK finns i [batch python-snabb starts databasen](https://github.com/Azure-Samples/batch-python-quickstart) på GitHub.

## <a name="enable-autoscaling-on-an-existing-pool"></a>Aktivera automatisk skalning i en befintlig pool

Varje batch-SDK är ett sätt att aktivera automatisk skalning. Exempel:

- [Metoden batchclient. PoolOperations. EnableAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync) (batch .net)
- [Aktivera automatisk skalning på en pool](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (REST API)

När du aktiverar automatisk skalning i en befintlig pool bör du tänka på följande:

- Om autoskalning är inaktiverat för poolen måste du ange en giltig autoskalning-formel när du utfärdar begäran. Du kan också ange ett intervall för automatisk skalning. Om du inte anger ett intervall används standardvärdet 15 minuter.
- Om autoskalning är aktiverat för poolen kan du ange en ny formel, ett nytt intervall eller båda. Du måste ange minst en av dessa egenskaper.
  - Om du anger ett nytt intervall för automatisk skalning stoppas det befintliga schemat och ett nytt schema startas. Det nya schemats start tid är den tid då begäran om automatisk skalning utfärdades.
  - Om du utelämnar formeln eller intervallet för autoskalning fortsätter batch-tjänsten att använda det aktuella värdet för den inställningen.

> [!NOTE]
> Om du har angett värden för parametrarna *targetDedicatedNodes* eller *TargetLowPriorityNodes* för metoden **CreatePool** när du skapade poolen i .net, eller för jämförbara parametrar på ett annat språk, ignoreras dessa värden när den automatiska skalnings formeln utvärderas.

I det här C#-exemplet används [batch .net](/dotnet/api/microsoft.azure.batch) -biblioteket för att aktivera automatisk skalning i en befintlig pool.

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Uppdatera en autoskalning-formel

Om du vill uppdatera formeln i en befintlig pool med autoskalning kan du anropa åtgärden för att aktivera automatisk skalning igen med den nya formeln. Om autoskalning till exempel redan är aktiverat `myexistingpool` när följande .NET-kod körs, ersätts den automatiska skalnings formeln med innehållet i `myNewFormula` .

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Uppdatera autoskalning-intervallet

Om du vill uppdatera utvärderings intervallet för automatisk skalning för en befintlig pool med autoskalning kan du anropa åtgärden för att aktivera automatisk skalning igen med det nya intervallet. Om du till exempel vill ställa in utvärderings intervallet för autoskalning på 60 minuter för en pool som redan är autoskalning-aktive rad i .NET:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Utvärdera en formel för autoskalning

Du kan utvärdera en formel innan du använder den i en pool. På så sätt kan du testa Formelns resultat innan du försätter dem i produktion.

Innan du kan utvärdera en autoskalning-formel måste du först aktivera autoskalning på poolen med en giltig formel, till exempel en formel med en rad `$TargetDedicatedNodes = 0` . Använd sedan något av följande för att utvärdera den formel som du vill testa:

- [Metoden batchclient. PoolOperations. EvaluateAutoScale](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) eller [EvaluateAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Dessa batch .NET-metoder kräver ID för en befintlig pool och en sträng som innehåller den automatiska skalnings formel som ska utvärderas.

- [Utvärdera en formel för automatisk skalning](/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    I den här REST API begäran anger du pool-ID i URI: n och den automatiska skalnings formeln i *autoScaleFormula* -elementet i begär ande texten. Åtgärdens svar innehåller eventuell fel information som kan vara relaterad till formeln.

Detta [batch .net](/dotnet/api/microsoft.azure.batch) -exempel utvärderar en autoskalning-formel. Om poolen inte redan använder autoskalning aktiverar du den först.

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

En lyckad utvärdering av formeln som visas i det här kodfragmentet ger resultat som liknar:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Hämta information om autoskalning-körningar

För att se till att din formel fungerar som förväntat, rekommenderar vi att du regelbundet kontrollerar resultatet av den automatiska skalnings körningen som batchen utför på din pool. Det gör du genom att hämta (eller uppdatera) en referens till poolen och sedan granska egenskaperna för den senaste autoskalning-körningen.

I batch .NET har egenskapen [CloudPool. AutoScaleRun](/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) flera egenskaper som innehåller information om den senaste automatiska skalnings körningen som utförs på poolen:

- [AutoScaleRun. timestamp](/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
- [AutoScaleRun. Results](/dotnet/api/microsoft.azure.batch.autoscalerun.results)
- [AutoScaleRun. error](/dotnet/api/microsoft.azure.batch.autoscalerun.error)

I REST API returnerar [information om en pool](/rest/api/batchservice/get-information-about-a-pool) -begäran information om poolen, som innehåller den senaste körnings informationen för automatisk skalning i egenskapen [autoScaleRun](/rest/api/batchservice/get-information-about-a-pool) .

I följande C#-exempel används batch .NET-biblioteket för att skriva ut information om den senaste automatiska skalnings körningen _i pool-poolen._

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

## <a name="get-autoscale-run-history-using-pool-autoscale-events"></a>Hämta körnings historik för autoskalning med automatiska skalnings händelser i pooler
Du kan också kontrol lera automatisk skalnings historik genom att skicka frågor till [PoolAutoScaleEvent](batch-pool-autoscale-event.md). Den här händelsen genereras av batch-tjänsten för att registrera varje förekomst av utvärderingen av automatiska skalnings formler och körning, vilket kan vara till hjälp för att felsöka eventuella problem.

Exempel händelse för PoolAutoScaleEvent:
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

## <a name="example-autoscale-formulas"></a>Exempel formler för autoskalning

Nu ska vi titta på några formler som visar olika sätt att justera mängden beräknings resurser i en pool.

### <a name="example-1-time-based-adjustment"></a>Exempel 1: tidsbaserad justering

Anta att du vill justera Poolens storlek baserat på veckodag och tid på dagen. Det här exemplet visar hur du ökar eller minskar antalet noder i poolen därefter.

Formeln hämtar först den aktuella tiden. Om det är en vardag (1-5) och inom arbets tid (8 till och med 6 PM), anges storleken på mål poolen till 20 noder. Annars är den inställd på 10 noder.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
$NodeDeallocationOption = taskcompletion;
```

`$curTime` kan justeras för att avspegla din lokala tidszon genom att lägga till `time()` produkten av `TimeZoneInterval_Hour` och din UTC-förskjutning. Används till exempel `$curTime = time() + (-6 * TimeInterval_Hour);` för Mountain, sommar tid (MDT). Tänk på att förskjutningen skulle behöva justeras i början och slutet av sommar tid (om tillämpligt).

### <a name="example-2-task-based-adjustment"></a>Exempel 2: uppgifts-baserad justering

I det här C#-exemplet justeras Poolens storlek baserat på antalet uppgifter i kön. Vi har inkluderat både kommentarer och rad brytningar i formel strängarna.

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

### <a name="example-3-accounting-for-parallel-tasks"></a>Exempel 3: redovisning för parallella uppgifter

Det här C#-exemplet justerar storleken på poolen baserat på antalet aktiviteter. Den här formeln tar också med i beräkningen [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) -värdet som har angetts för poolen. Den här metoden är användbar i situationer där [parallell körning](batch-parallel-node-tasks.md) har Aktiver ATS på din pool.

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

### <a name="example-4-setting-an-initial-pool-size"></a>Exempel 4: Ange en ursprunglig storlek på poolen

I det här exemplet visas ett C#-exempel med en autoskalning-formel som anger Poolens storlek till ett angivet antal noder under en inledande tids period. Därefter justeras Poolens storlek baserat på antalet aktiva och aktiva aktiviteter.

Mer specifikt gör den här formeln följande:

- Ställer in den ursprungliga Poolens storlek på fyra noder.
- Justerar inte Poolens storlek inom de första 10 minuterna i poolens livs cykel.
- Efter 10 minuter erhåller du Max värdet för antalet aktiva och aktiva uppgifter inom de senaste 60 minuterna.
  - Om båda värdena är 0 (vilket indikerar att inga aktiviteter kördes eller var aktiva under de senaste 60 minuterna), anges värdet 0 för Poolens storlek.
  - Om något värde är större än noll görs ingen ändring.

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

- Lär dig hur du [Kör flera uppgifter samtidigt på datornoderna i poolen](batch-parallel-node-tasks.md). Tillsammans med automatisk skalning kan detta hjälpa till att sänka jobb varaktigheten för vissa arbets belastningar, vilket sparar pengar.
- Lär dig hur du [frågar Azure Batch-tjänsten effektivt](batch-efficient-list-queries.md) för ytterligare effektivitet.
