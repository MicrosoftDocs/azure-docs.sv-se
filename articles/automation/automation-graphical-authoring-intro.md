---
title: Skapa grafiska runbooks i Azure Automation
description: Den här artikeln beskriver hur du skapar en grafisk runbook utan att arbeta med kod.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 88492d914b710c7a738dd6d7f501e22d490065b6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833822"
---
# <a name="author-graphical-runbooks-in-azure-automation"></a>Skapa grafiska runbooks i Azure Automation

Alla runbooks i Azure Automation är Windows PowerShell arbetsflöden. Grafiska runbooks och grafiska PowerShell Workflow-runbooks genererar PowerShell-kod som Automation-arbetarna kör men som du inte kan visa eller ändra. Du kan konvertera en grafisk runbook till en grafisk PowerShell Workflow-runbook och vice versa. Du kan dock inte konvertera dessa runbooks till en textbaserad runbook. Dessutom kan den grafiska Automation-redigeraren inte importera en text-runbook.

Med grafisk redigering kan du skapa runbooks för Azure Automation utan komplexiteten i den underliggande Windows PowerShell- eller PowerShell-arbetsflödeskoden. Du kan lägga till aktiviteter på arbetsytan från ett bibliotek med cmdlets och runbooks, länka dem tillsammans och konfigurera dem så att de bildar ett arbetsflöde. Om du har arbetat med System Center Orchestrator eller Service Management Automation (SMA) bör grafisk redigering se bekant ut. Den här artikeln innehåller en introduktion till de begrepp du behöver för att komma igång med att skapa en grafisk runbook.

## <a name="overview-of-graphical-editor"></a>Översikt över grafisk redigerare

Du kan öppna den grafiska redigeraren i Azure Portal genom att skapa eller redigera en grafisk runbook.

![Grafisk arbetsyta](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

I följande avsnitt beskrivs kontrollerna i den grafiska redigeraren.

### <a name="canvas-control"></a>Arbetsytekontroll

Med kontrollen Arbetsyta kan du utforma din runbook. Du kan lägga till aktiviteter från noderna i bibliotekskontrollen till runbooken och ansluta dem med länkar för att definiera runbook-logik. Längst ned på arbetsytan finns det kontroller som gör att du kan zooma in och ut.

### <a name="library-control"></a>Bibliotekskontroll

Med bibliotekskontrollen kan du välja aktiviteter [att](#use-activities) lägga till i din runbook. Du lägger till dem på arbetsytan, där du kan ansluta dem till andra aktiviteter. Bibliotekskontrollen innehåller avsnitten som definieras i följande tabell.

| Avsnitt | Description |
|:--- |:--- |
| Cmdletar |Alla cmdlets som kan användas i din runbook. Cmdlets ordnas efter modul. Alla moduler som du har installerat i ditt Automation-konto är tillgängliga. |
| Runbooks |Runbooks i ditt Automation-konto. Du kan lägga till dessa runbooks på arbetsytan som ska användas som underordnade runbooks. Endast runbooks av samma kärntyp som den runbook som redigeras visas. För grafiska runbooks visas endast PowerShell-baserade runbooks. För grafiska PowerShell Workflow-runbooks visas endast PowerShell Workflow-baserade runbooks. |
| Tillgångar |[Automation-tillgångarna](/previous-versions/azure/dn939988(v=azure.100)) i ditt Automation-konto som du kan använda i din runbook. Om du lägger till en tillgång i en runbook läggs en arbetsflödesaktivitet till som hämtar den valda tillgången. När det gäller variabeltillgångar kan du välja om du vill lägga till en aktivitet för att hämta variabeln eller ange variabeln. |
| Runbook-kontroll |Kontrollera aktiviteter som kan användas i din aktuella runbook. En punktsaktivitet tar flera indata och väntar tills alla har slutförts innan arbetsflödet fortsätter. En kodaktivitet kör en eller flera rader med PowerShell- eller PowerShell-arbetsflödeskod, beroende på den grafiska runbooktypen. Du kan använda den här aktiviteten för anpassad kod eller för funktioner som är svåra att uppnå med andra aktiviteter. |

### <a name="configuration-control"></a>Konfigurationskontroll

Med konfigurationskontrollen kan du ange information om ett objekt som har valts på arbetsytan. Vilka egenskaper som är tillgängliga i den här kontrollen beror på vilken typ av objekt som valts. När du väljer ett alternativ i konfigurationskontrollen öppnas ytterligare blad med mer information.

### <a name="test-control"></a>Testkontroll

Testkontrollen visas inte första gången den grafiska redigeraren startas. Den öppnas när du testar en grafisk runbook interaktivt.

## <a name="use-activities"></a>Använda aktiviteter

Aktiviteter är byggstenarna i en runbook. En aktivitet kan vara en PowerShell-cmdlet, en underordnad runbook eller ett arbetsflöde. Du kan lägga till en aktivitet i runbooken genom att högerklicka på den i bibliotekskontrollen och välja Lägg **till på arbetsytan.** Du kan sedan klicka och dra aktiviteten för att placera den var som helst på den arbetsyta som du vill. Platsen för aktiviteten på arbetsytan påverkar inte runbookens drift. Du kan placera ut din runbook på det sätt som passar bäst för att visualisera dess drift.

![Lägga till på arbetsytan](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

Välj en aktivitet på arbetsytan för att konfigurera dess egenskaper och parametrar på bladet Konfiguration. Du kan ändra etiketten för aktiviteten till ett namn som du hittar beskrivande. Runbooken kör fortfarande den ursprungliga cmdleten. Du ändrar helt enkelt visningsnamnet som den grafiska redigeraren använder. Observera att etiketten måste vara unik i runbooken.

### <a name="parameter-sets"></a>Parameteruppsättningar

En parameteruppsättning definierar obligatoriska och valfria parametrar som accepterar värden för en viss cmdlet. Alla cmdlets har minst en parameteruppsättning och vissa har flera uppsättningar. Om en cmdlet har flera parameteruppsättningar måste du välja den som ska användas innan du kan konfigurera parametrar. Du kan ändra parameteruppsättningen som används av en aktivitet genom att välja **Parameteruppsättning och** välja en annan uppsättning. I det här fallet går alla parametervärden som du redan har konfigurerat förlorade.

I följande exempel har [cmdleten Get-AzVM](/powershell/module/az.compute/get-azvm) tre parameteruppsättningar. I exemplet används en uppsättning med namnet **ListVirtualMachineInResourceGroupParamSet**, med en enda valfri parameter för att returnera alla virtuella datorer i en resursgrupp. I exemplet används också **parameteruppsättningen GetVirtualMachineInResourceGroupParamSet** för att ange att den virtuella datorn ska returneras. Den här uppsättningen har två obligatoriska parametrar och en valfri parameter.

![Parameteruppsättning](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>Parametervärden

När du anger ett värde för en parameter väljer du en datakälla för att avgöra hur värdet anges. Vilka datakällor som är tillgängliga för en viss parameter beror på de giltiga värdena för den parametern. Null är till exempel inte ett tillgängligt alternativ för en parameter som inte tillåter null-värden.

| Datakälla | Description |
|:--- |:--- |
| Konstant värde |Ange ett värde för parametern . Den här datakällan är endast tillgänglig för följande datatyper: Int32, Int64, String, Boolean, DateTime, Switch. |
| Aktivitetsutdata |Använd utdata från en aktivitet som föregår den aktuella aktiviteten i arbetsflödet. Alla giltiga aktiviteter visas. För parametervärdet använder du bara aktiviteten som producerar utdata. Om aktiviteten matar ut ett objekt med flera egenskaper kan du ange namnet på en specifik egenskap när du har valt aktiviteten. |
| Runbook-indata |Välj en Runbook-indata som indata för aktivitetsparametern. |
| Variabeltillgång |Välj en Automation-variabel som indata. |
| Autentiseringstillgång |Välj en Automation-autentiseringsidentifiering som indata. |
| Certifikattillgång |Välj ett Automation-certifikat som indata. |
| Anslutningstillgång |Välj en Automation-anslutning som indata. |
| PowerShell-uttryck |Ange ett enkelt [PowerShell-uttryck.](#work-with-powershell-expressions) Uttrycket utvärderas före aktiviteten och resultatet används för parametervärdet. Du kan använda variabler för att referera till utdata för en aktivitet eller en runbook-indataparameter. |
| Inte konfigurerat |Rensa alla värden som tidigare har konfigurerats. |

#### <a name="optional-additional-parameters"></a>Valfria ytterligare parametrar

Alla cmdlets har möjlighet att ange ytterligare parametrar. Det här är PowerShell-vanliga parametrar eller andra anpassade parametrar. Den grafiska redigeraren visar en textruta där du kan ange parametrar med hjälp av PowerShell-syntax. Om du till exempel vill använda `Verbose` den gemensamma parametern bör du ange `-Verbose:$True` .

### <a name="retry-activity"></a>Återförsöksaktivitet

Återförsöksfunktioner för en aktivitet gör att den kan köras flera gånger tills ett visst villkor uppfylls, ungefär som en loop. Du kan använda den här funktionen för aktiviteter som ska köras flera gånger, är felbenägna, kan behöva mer än ett försök att lyckas eller testa utdatainformationen för aktiviteten för giltiga data.

När du aktiverar återförsök för en aktivitet kan du ange en fördröjning och ett villkor. Fördröjningen är den tid (mätt i sekunder eller minuter) som runbooken väntar innan den kör aktiviteten igen. Om du inte anger någon fördröjning körs aktiviteten igen omedelbart efter att den har slutförts.

:::image type="content" source="media/automation-graphical-authoring-intro/retry-delay.png" alt-text="Skärmbild av funktionsinställningarna Aktivera återförsök.":::

Återförsöksvillkoret är ett PowerShell-uttryck som utvärderas efter varje gång aktiviteten körs. Om uttrycket matchas till True körs aktiviteten igen. Om uttrycket matchas till False körs inte aktiviteten igen och runbooken går vidare till nästa aktivitet.

:::image type="content" source="media/automation-graphical-authoring-intro/retry-condition.png" alt-text="Skärmbild som visar fältet Försök igen tills villkoret är sant och exempel på PowerShell-uttryck som kan användas i återförsöksvillkoret.":::

Villkoret för återförsök kan använda en variabel med `RetryData` namnet som ger åtkomst till information om aktivitetsförsök. Den här variabeln har egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|:--- |:--- |
| `NumberOfAttempts` |Antal gånger som aktiviteten har körts. |
| `Output` |Utdata från den senaste körningen av aktiviteten. |
| `TotalDuration` |Förfluten tid sedan aktiviteten startades första gången. |
| `StartedAt` |Tid (i UTC-format) när aktiviteten startades för första gången. |

Följande är exempel på villkor för återförsök av aktiviteter.

```powershell-interactive
# Run the activity exactly 10 times.
$RetryData.NumberOfAttempts -ge 10
```

```powershell-interactive
# Run the activity repeatedly until it produces any output.
$RetryData.Output.Count -ge 1
```

```powershell-interactive
# Run the activity repeatedly until 2 minutes has elapsed.
$RetryData.TotalDuration.TotalMinutes -ge 2
```

När du har konfigurerat ett återförsöksvillkor för en aktivitet innehåller aktiviteten två visuella tips som påminner dig. Den ena visas i aktiviteten och den andra visas när du granskar konfigurationen av aktiviteten.

![Visuella indikatorer för återförsök av aktivitet](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Kontroll av arbetsflödesskript

En arbetsflödesskriptkontroll är en särskild aktivitet som accepterar PowerShell- eller PowerShell Workflow-skript, beroende på vilken typ av grafisk runbook som redigeras. Den här kontrollen innehåller funktioner som kanske inte är tillgängliga på annat sätt. Den kan inte acceptera parametrar, men den kan använda variabler för aktivitetsutdata och runbook-indataparametrar. Alla utdata från aktiviteten läggs till i databussen. Ett undantag är utdata utan utgående länk, vilket innebär att utdata läggs till i runbookens utdata.

Följande kod utför till exempel datumberäkningar med hjälp av en runbook-indatavariabel med namnet `NumberOfDays` . Den skickar sedan ett beräknat DateTime-värde som utdata som ska användas av efterföljande aktiviteter i runbooken.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="use-links-for-workflow"></a>Använda länkar för arbetsflöde

En länk i en grafisk runbook ansluter två aktiviteter. Den visas på arbetsytan som en pil som pekar från källaktiviteten till målaktiviteten. Aktiviteterna körs i pilens riktning med målaktiviteten som startar när källaktiviteten har slutförts.

### <a name="create-a-link"></a>Skapa en länk

Du kan skapa en länk mellan två aktiviteter genom att välja källaktiviteten och klicka på cirkeln längst ned i formen. Dra pilen till målaktiviteten och släpp.

![Skapa en länk](media/automation-graphical-authoring-intro/create-link-options.png)

Välj länken för att konfigurera dess egenskaper på bladet Konfiguration. Egenskaperna innehåller länktypen, som beskrivs i följande tabell.

| Länktyp | Description |
|:--- |:--- |
| Pipeline |Målaktiviteten körs en gång för varje objekts utdata från källaktiviteten. Målaktiviteten körs inte om källaktiviteten resulterar i inga utdata. Utdata från källaktiviteten är tillgängliga som ett -objekt. |
| Sequence |Målaktiviteten körs bara en gång när den tar emot utdata från källaktiviteten. Utdata från källaktiviteten är tillgängliga som en matris med objekt. |

### <a name="start-runbook-activity"></a>Starta runbook-aktivitet

En grafisk runbook börjar med alla aktiviteter som inte har en inkommande länk. Det finns ofta bara en aktivitet som fungerar som startaktivitet för runbooken. Om flera aktiviteter inte har en inkommande länk börjar runbooken med att köra dem parallellt. Den följer länkarna för att köra andra aktiviteter när var och en slutförs.

### <a name="specify-link-conditions"></a>Ange länkvillkor

När du anger ett villkor för en länk körs målaktiviteten endast om villkoret matchas till Sant. Du använder vanligtvis en `ActivityOutput` variabel i ett villkor för att hämta utdata från källaktiviteten.

För en pipelinelänk måste du ange ett villkor för ett enskilt objekt. Runbook utvärderar villkoret för varje objekts utdata efter källaktiviteten. Den kör sedan målaktiviteten för varje objekt som uppfyller villkoret. Med källaktiviteten kan du till exempel använda följande syntax för en villkorlig pipelinelänk för att endast hämta virtuella datorer i `Get-AzVM` resursgruppen med namnet Group1.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

För en sekvenslänk utvärderar runbooken bara villkoret en gång, eftersom en enda matris som innehåller alla objekt från källaktiviteten returneras. På grund av detta kan runbooken inte använda en sekvenslänk för filtrering, som den kan göra med en pipelinelänk. Sekvenslänken kan helt enkelt avgöra om nästa aktivitet körs eller inte.

Ta till exempel följande uppsättning aktiviteter  i vår Start VM-runbook:

![Villkorsstyrd länk med sekvenser](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Runbooken använder tre olika sekvenslänkar som verifierar värdena för indataparametrarna `VMName` och för att fastställa vilken åtgärd som ska `ResourceGroupName` vidtas. Möjliga åtgärder är att starta en enskild virtuell dator, starta alla virtuella datorer i resursgruppen eller starta alla virtuella datorer i en prenumeration. För sekvenslänken mellan `Connect to Azure` och `Get single VM` är här villkorslogiken:

```powershell-interactive
<#
Both VMName and ResourceGroupName runbook input parameters have values
#>
(
(($VMName -ne $null) -and ($VMName.Length -gt 0))
) -and (
(($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
)
```

När du använder en villkorsstyrd länk filtreras data som är tillgängliga från källaktiviteten till andra aktiviteter i den grenen av villkoret. Om en aktivitet är källan till flera länkar beror de data som är tillgängliga för aktiviteter i varje gren på villkoret i länken som ansluter till den grenen.

Aktiviteten i `Start-AzVM` runbooken nedan startar till exempel alla virtuella datorer. Den har två villkorliga länkar. Den första villkorliga länken använder uttrycket `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` för att filtrera om aktiviteten `Start-AzVM` slutförs korrekt. Den andra villkorliga länken använder uttrycket `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` för att filtrera om aktiviteten inte kan starta den virtuella `Start-AzVm` datorn.

![Exempel på villkorsstyrd länk](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Alla aktiviteter som följer den första länken och använder aktivitetsutdata från hämtar endast de virtuella datorer som `Get-AzureVM` startades när `Get-AzureVM` körningen gjordes. Alla aktiviteter som följer den andra länken hämtar bara de virtuella datorer som stoppades vid den tidpunkt `Get-AzureVM` då körningen gjordes. Alla aktiviteter som följer den tredje länken hämtar alla virtuella datorer oavsett deras körningstillstånd.

### <a name="use-junctions"></a>Använda knutpunkter

En knutpunkt är en särskild aktivitet som väntar tills alla inkommande grenar har slutförts. Detta gör att runbooken kan köra flera aktiviteter parallellt och se till att alla har slutförts innan du fortsätter.

En knutpunkt kan ha ett obegränsat antal inkommande länkar, men endast en av dessa länkar kan vara en pipeline. Antalet inkommande sekvenslänkar är inte begränsat. Du kan skapa knutpunkten med flera inkommande pipelinelänkar och spara runbooken, men den misslyckas när den körs.

Exemplet nedan är en del av en runbook som startar en uppsättning virtuella datorer samtidigt som korrigeringar laddas ned som ska tillämpas på dessa datorer. Den använder en knutpunkt för att säkerställa att båda processerna slutförs innan runbooken fortsätter.

![Knutpunkt](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="work-with-cycles"></a>Arbeta med cykler

En cykel skapas när en målaktivitet länkar tillbaka till sin källaktivitet eller till en annan aktivitet som så småningom länkar tillbaka till dess källa. Grafisk redigering stöder för närvarande inte cykler. Om din runbook har en cykel sparas den korrekt men får ett fel när den körs.

![Cykler](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="share-data-between-activities"></a>Dela data mellan aktiviteter

Alla data som en aktivitet matar ut med en utgående länk skrivs till databusen för runbooken. Alla aktiviteter i runbooken kan använda data på databussen för att fylla i parametervärden eller inkludera dem i skriptkoden. En aktivitet kan komma åt utdata från alla tidigare aktiviteter i arbetsflödet.

Hur data skrivs till databussen beror på typen av länk för aktiviteten. För en pipelinelänk matas data ut som flera objekt. För en sekvenslänk matas data ut som en matris. Om det bara finns ett värde matas det ut som en matris med ett element.

Din runbook har två sätt att komma åt data i databussen: 
* Använd en aktivitetsutdatakälla.
* Använd en datakälla för PowerShell-uttryck.

Den första mekanismen använder en aktivitetsutdatakälla för att fylla i en parameter för en annan aktivitet. Om utdata är ett objekt kan runbooken ange en enskild egenskap.

![aktivitetsutdata](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Den andra dataåtkomstmekanismen hämtar utdata från en aktivitet i en datakälla för PowerShell-uttryck eller en arbetsflödesskriptaktivitet med en variabel med hjälp av `ActivityOutput` syntaxen som visas nedan. Om utdata är ett objekt kan din runbook ange en enda egenskap.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="use-checkpoints"></a>Använda kontrollpunkter

Du kan ange [kontrollpunkter i en](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) grafisk PowerShell Workflow-runbook genom att välja **Kontrollpunkts-runbook** för valfri aktivitet. Detta gör att en kontrollpunkt anges när aktiviteten körs.

![Checkpoint](media/automation-graphical-authoring-intro/set-checkpoint.png)

Kontrollpunkter aktiveras endast i grafiska PowerShell Workflow-runbooks och är inte tillgängliga i grafiska runbooks. Om runbooken använder Azure-cmdlets bör den följa alla kontrollpunktsaktiviteter med en `Connect-AzAccount` aktivitet. Anslutningsåtgärden används om runbooken pausas och måste startas om från den här kontrollpunkten på en annan arbetsroll.

## <a name="handle-runbook-input"></a>Hantera runbook-indata

En runbook kräver indata antingen från en användare som startar runbooken via Azure Portal eller från en annan runbook, om den aktuella används som underordnad. För en runbook som skapar en virtuell dator kan användaren till exempel behöva ange information som namnet på den virtuella datorn och andra egenskaper varje gång runbooken startar.

Runbooken accepterar indata genom att definiera en eller flera indataparametrar. Användaren anger värden för dessa parametrar varje gång runbooken startar. När användaren startar runbooken med hjälp av Azure Portal uppmanas användaren att ange värden för varje indataparameter som stöds av runbooken.

När du skapar din runbook kan du komma åt dess indataparametrar genom att klicka på **Indata och utdata** i Runbook-verktygsfältet. Då öppnas kontrollen Indata och Utdata där du kan redigera en befintlig indataparameter eller skapa en ny genom att klicka på Lägg **till indata.**

![Lägg till indata](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Varje indataparameter definieras av egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|:--- |:--- |
| Name | Krävs. Namnet på parametern. Namnet måste vara unikt i runbooken. Den måste börja med en bokstav och får bara innehålla bokstäver, siffror och understreck. Namnet får inte innehålla blanksteg. |
| Description |Valfritt. Beskrivning av syftet med indataparametern. |
| Typ | Valfritt. Datatypen förväntas för parametervärdet. Den Azure Portal ger en lämplig kontroll för datatypen för varje parameter när du frågar efter indata. Parametertyper som stöds är String, Int32, Int64, Decimal, Boolean, DateTime och Object. Om en datatyp inte är markerad används sträng som standard.|
| Obligatorisk | Valfritt. Inställning som anger om ett värde måste anges för parametern . Om du väljer `yes` måste ett värde anges när runbooken startas. Om du `no` väljer krävs inget värde när runbooken startas och ett standardvärde kan användas. Runbooken kan inte starta om du inte anger ett värde för varje obligatorisk parameter som inte har ett definierat standardvärde. |
| Standardvärde | Valfritt. Värdet som används för en parameter om den inte skickas när runbooken startas. Om du vill ange ett standardvärde väljer du `Custom` . Välj `None` om du inte vill ange något standardvärde. |

## <a name="handle-runbook-output"></a>Hantera runbook-utdata

Grafisk redigering sparar data som skapats av en aktivitet som inte har en utgående länk till [runbookens utdata.](./automation-runbook-output-and-messages.md) Utdata sparas med runbook-jobbet och är tillgängliga för en överordnad runbook när runbooken används som underordnad.

## <a name="work-with-powershell-expressions"></a>Arbeta med PowerShell-uttryck

En av fördelarna med grafisk redigering är att du kan skapa en runbook med minimal kunskap om PowerShell. För närvarande behöver du dock känna till lite PowerShell för att fylla i vissa [parametervärden](#use-activities) och för att ange [länkvillkor.](#use-links-for-workflow) Det här avsnittet innehåller en snabb introduktion till PowerShell-uttryck. Fullständig information om PowerShell finns i [Skript med Windows PowerShell](/powershell/scripting/overview).

### <a name="use-a-powershell-expression-as-a-data-source"></a>Använda ett PowerShell-uttryck som datakälla

Du kan använda ett PowerShell-uttryck som datakälla för att fylla i värdet för en [aktivitetsparameter](#use-activities) med resultatet av PowerShell-koden. Uttrycket kan vara en enda kodrad som utför en enkel funktion eller flera rader som utför komplicerad logik. Utdata från ett kommando som inte har tilldelats till en variabel är utdata till parametervärdet.

Följande kommando visar till exempel det aktuella datumet.

```powershell-interactive
Get-Date
```

Nästa kodfragment skapar en sträng från det aktuella datumet och tilldelar den till en variabel. Koden skickar innehållet i variabeln till utdata.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

Följande kommandon utvärderar det aktuella datumet och returnerar en sträng som anger om den aktuella dagen är en helg eller en veckodag.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="use-activity-output"></a>Använda aktivitetsutdata

Om du vill använda utdata från en tidigare aktivitet i din runbook `ActivityOutput` använder du variabeln med följande syntax.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Du kan till exempel ha en aktivitet med en egenskap som kräver namnet på en virtuell dator. I det här fallet kan din runbook använda följande uttryck.

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

Om egenskapen kräver det virtuella datorobjektet i stället för bara ett namn returnerar runbooken hela objektet med hjälp av följande syntax.

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

Runbooken kan använda utdata från en aktivitet i ett mer komplext uttryck, till exempel följande. Det här uttrycket sammanfogar text till namnet på den virtuella datorn.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="compare-values"></a>Jämföra värden

Använd [jämförelseoperatorer](/powershell/module/microsoft.powershell.core/about/about_comparison_operators) för att jämföra värden eller avgöra om ett värde matchar ett angivet mönster. En jämförelse returnerar värdet True eller False.

Följande villkor avgör till exempel om den virtuella datorn från en aktivitet med namnet för närvarande `Get-AzureVM` är stoppad.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState -eq "Stopped"
```

Följande villkor avgör om samma virtuella dator är i något annat tillstånd än stoppat.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState -ne "Stopped"
```

Du kan ansluta till flera villkor i din runbook med hjälp av [en logisk operator,](/powershell/module/microsoft.powershell.core/about/about_logical_operators)till exempel `-and` eller `-or` . Följande villkor kontrollerar till exempel om den virtuella datorn i föregående exempel har statusen Stoppad eller Stoppad.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState -eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState -eq "Stopping")
```

### <a name="use-hashtables"></a>Använda hashtabeller

[Hash-tabeller](/powershell/module/microsoft.powershell.core/about/about_hash_tables) är namn/värde-par som är användbara för att returnera en uppsättning värden. Du kan också se en hash-tabell som kallas en ordlista. Egenskaper för vissa aktiviteter förväntar sig en hash-tabell i stället för ett enkelt värde.

Skapa en hash-tabell med följande syntax. Den kan innehålla val annat antal poster, men var och en definieras av ett namn och värde.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Följande uttryck skapar till exempel en hash-tabell som ska användas som datakälla för en aktivitetsparameter som förväntar sig en hash-tabell med värden för en Internetsökning.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

I följande exempel används utdata från en aktivitet med namnet `Get Twitter Connection` för att fylla i en hash-tabell.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticate-to-azure-resources"></a>Autentisera till Azure-resurser

Runbooks i Azure Automation som hanterar Azure-resurser kräver autentisering till Azure. Kör [som-kontot](./automation-security-overview.md), som även kallas tjänstens huvudnamn, är standardmekanismen som en Automation-runbook använder för att komma åt Azure Resource Manager resurser i din prenumeration. Du kan lägga till den här funktionen i en grafisk runbook genom att lägga till anslutningstillgången, som använder `AzureRunAsConnection` [powershell-cmdleten Get-AutomationConnection,](/system-center/sma/manage-global-assets) på arbetsytan. Du kan också lägga till [cmdleten Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Det här scenariot illustreras i följande exempel.

![Kör som autentiseringsaktiviteter](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

Aktiviteten, `Get Run As Connection` eller `Get-AutomationConnection` , konfigureras med en konstant värdedatakälla med namnet `AzureRunAsConnection` .

![Kör som-anslutningskonfiguration](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

Nästa aktivitet, `Connect-AzAccount` , lägger till det autentiserade Kör som-kontot för användning i runbooken.

![Connect-AzAccount parameteruppsättning](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>För PowerShell-runbooks `Add-AzAccount` och är alias för `Add-AzureRMAccount` `Connect-AzAccount` . Observera att dessa alias inte är tillgängliga för dina grafiska runbooks. En grafisk runbook kan bara använda `Connect-AzAccount` sig själv.

För parameterfälten **APPLICATIONID,** **CERTIFICATETHUMBPRINT** och **TENANTID** anger du namnet på egenskapen för fältsökvägen, eftersom aktiviteten matar ut ett objekt med flera egenskaper. Annars misslyckas runbooken när den körs vid försök att autentisera. Det här är vad du minst behöver för att autentisera din Runbook med Kör som-kontot.

Vissa prenumeranter skapar ett Automation-konto med ett [Azure AD-användarkonto](./shared-resources/credentials.md) för att hantera klassisk Azure-distribution eller för Azure Resource Manager resurser. För att upprätthålla bakåtkompatibilitet för dessa prenumeranter är autentiseringsmekanismen som ska användas i din runbook `Add-AzureAccount` cmdleten med [en autentiseringstillgång](./shared-resources/credentials.md). Tillgången representerar en Active Directory-användare med åtkomst till Azure-kontot.

Du kan aktivera den här funktionen för din grafiska runbook genom att lägga till en autentiseringstillgång på arbetsytan, följt av en aktivitet som använder `Add-AzureAccount` autentiseringstillgången för sina indata. Se följande exempel.

![Autentiseringsaktiviteter](media/automation-graphical-authoring-intro/authentication-activities.png)

Runbooken måste autentiseras vid början och efter varje kontrollpunkt. Därför måste du använda en `Add-AzureAccount` aktivitet efter en `Checkpoint-Workflow` aktivitet. Du behöver inte använda någon ytterligare autentiseringsaktivitet.

![Aktivitetsutdata](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="export-a-graphical-runbook"></a>Exportera en grafisk runbook

Du kan bara exportera den publicerade versionen av en grafisk runbook. Om runbooken ännu inte har publicerats **inaktiveras** knappen Exportera. När du klickar på **knappen** Exportera laddas runbooken ned till din lokala dator. Namnet på filen matchar namnet på runbooken med tillägget **.graphrunbook.**

## <a name="import-a-graphical-runbook"></a>Importera en grafisk runbook

Du kan importera en grafisk eller grafisk Runbook-fil för PowerShell Workflow genom att välja **alternativet Importera när** du lägger till en runbook. När du väljer den fil som ska importeras kan du behålla samma namn eller ange ett nytt. Fältet **Typ av runbook** visar typen av runbook när den har utvärderat den valda filen. Om du försöker välja en annan typ som inte är korrekt visar den grafiska redigeraren ett meddelande som anger att det finns potentiella konflikter och det kan finnas syntaxfel under konverteringen.

![Importera runbook](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="test-a-graphical-runbook"></a>Testa en grafisk runbook

Varje grafisk runbook i Azure Automation har en utkastversion och en publicerad version. Du kan bara köra den publicerade versionen, medan du bara kan redigera utkastversionen. Den Publicerade versionen påverkas inte av ändringar i Utkastet. När utkastversionen är klar att användas publicerar du den, vilket skriver över den aktuella publicerade versionen med utkastversionen.

Du kan testa utkastversionen av en runbook i Azure Portal utan att den publicerade versionen ändras. Du kan också testa en ny runbook innan den har publicerats så att du kan kontrollera att runbooken fungerar korrekt innan du ersätter versionen. Testning av en runbook kör utkastversionen och säkerställer att alla åtgärder som den utför slutförs. Ingen jobbhistorik skapas, men fönstret Testutdata visar utdata.

Öppna testkontrollen för din grafiska runbook genom att öppna runbooken för redigering och sedan klicka på **Testfönster.** Testkontrollen frågar efter indataparametrar och du kan starta runbooken genom att klicka på **Starta**.

## <a name="publish-a-graphical-runbook"></a>Publicera en grafisk runbook

Publicera en grafisk runbook genom att öppna runbooken för redigering och sedan klicka på **Publicera.** Möjliga statusar för runbooken är:

* Ny – runbooken har inte publicerats ännu. 
* Publicerad – runbooken har publicerats.
* I redigering – runbooken har redigerats efter att den har publicerats och utkast- och publicerade versioner skiljer sig åt.

![Runbook-status](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Du har möjlighet att återgå till den publicerade versionen av en runbook. Den här åtgärden kastar bort alla ändringar som gjorts sedan runbooken senast publicerades. Den ersätter Utkast-versionen av runbooken med den publicerade versionen.

## <a name="next-steps"></a>Nästa steg

* Information om hur du kommer igång med grafiska runbooks finns [i Självstudie: Skapa en grafisk runbook.](learn/automation-tutorial-runbook-graphical.md)
* Mer information om runbook-typer och deras fördelar och begränsningar finns i [Azure Automation runbook-typer.](automation-runbook-types.md)
* Information om hur du autentiserar med hjälp av Kör som-kontot för Automation finns i [Kör som-konto.](automation-security-overview.md#run-as-account)
* En PowerShell-cmdlet-referens finns i [Az.Automation](/powershell/module/az.automation/#automation).
