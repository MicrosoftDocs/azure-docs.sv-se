---
title: Redigera grafiska runbooks i Azure Automation
description: Den här artikeln beskriver hur du skapar en grafisk Runbook utan att arbeta med kod.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: bbac794263fec176e03c7148d860c479a2ed9d39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102501236"
---
# <a name="author-graphical-runbooks-in-azure-automation"></a>Redigera grafiska runbooks i Azure Automation

Alla Runbooks i Azure Automation är Windows PowerShell-arbetsflöden. Grafiska runbooks och grafiska PowerShell-arbetsflöden i Runbook genererar PowerShell-kod som automatiserings arbets tagarna kör men som du inte kan visa eller ändra. Du kan konvertera en grafisk Runbook till en grafisk Runbook för PowerShell-arbetsflöde och vice versa. Du kan dock inte konvertera dessa Runbooks till en text-Runbook. Dessutom kan inte Automation Graphic-redigeraren importera en text Runbook.

Med grafisk redigering kan du skapa Runbooks för Azure Automation utan att den underliggande Windows PowerShell-eller PowerShell-arbetsflödes koden är komplex. Du kan lägga till aktiviteter till arbets ytan från ett bibliotek med cmdlets och Runbooks, länka dem tillsammans och konfigurera dem för att skapa ett arbets flöde. Om du har arbetat med System Center Orchestrator eller Service Management Automation (SMA) bör grafisk redigering se bekant. Den här artikeln innehåller en introduktion till de koncept du behöver för att komma igång med att skapa en grafisk Runbook.

## <a name="overview-of-graphical-editor"></a>Översikt över grafiskt redigerings program

Du kan öppna den grafiska redigeraren i Azure Portal genom att skapa eller redigera en grafisk Runbook.

![Grafisk arbets yta](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

I följande avsnitt beskrivs kontrollerna i den grafiska redigeraren.

### <a name="canvas-control"></a>Arbets ytans kontroll

Med kontrollen arbets yta kan du utforma din Runbook. Du kan lägga till aktiviteter från noderna i biblioteks kontrollen i Runbook-flödet och koppla dem till länkar för att definiera Runbook-logik. Längst ned på arbets ytan finns det kontroller som gör att du kan zooma in och ut.

### <a name="library-control"></a>Biblioteks kontroll

Med biblioteks kontrollen kan du välja vilka [aktiviteter](#use-activities) som ska läggas till i din Runbook. Du lägger till dem på arbets ytan, där du kan ansluta dem till andra aktiviteter. Biblioteks kontrollen inkluderar de avsnitt som definieras i följande tabell.

| Avsnitt | Beskrivning |
|:--- |:--- |
| Cmdletar |Alla cmdletar som kan användas i din Runbook. Cmdletar är ordnade efter modul. Alla moduler som du har installerat i ditt Automation-konto är tillgängliga. |
| Runbooks |Runbooks i ditt Automation-konto. Du kan lägga till dessa Runbooks i arbets ytan som ska användas som underordnade Runbooks. Endast Runbooks av samma kärn typ som den Runbook som redige ras visas. För grafiska runbooks visas endast PowerShell-baserade Runbooks. För grafiska PowerShell Workflow-Runbooks visas bara PowerShell Workflow-baserade Runbooks. |
| Tillgångar |[Automation-till gångar](/previous-versions/azure/dn939988(v=azure.100)) i ditt Automation-konto som du kan använda i din Runbook. Om du lägger till en till gång i en Runbook läggs en arbets flödes aktivitet som hämtar den valda till gången. Om det gäller variabla till gångar kan du välja om du vill lägga till en aktivitet för att hämta variabeln eller ange variabeln. |
| Runbook-kontroll |Styr aktiviteter som kan användas i din aktuella Runbook. En Knut punkts aktivitet tar flera indata och väntar tills alla har slutförts innan arbets flödet fortsätter. En kod aktivitet kör en eller flera rader med PowerShell-eller PowerShell-arbetsflöde, beroende på vilken grafisk Runbook-typ som används. Du kan använda den här aktiviteten för anpassad kod eller för funktioner som är svåra att uppnå med andra aktiviteter. |

### <a name="configuration-control"></a>Konfigurations kontroll

Med konfigurations kontrollen kan du ange information om ett objekt som är markerat på arbets ytan. Vilka egenskaper som är tillgängliga i den här kontrollen beror på vilken typ av objekt som valts. När du väljer ett alternativ i konfigurations kontrollen öppnar det ytterligare blad för att få mer information.

### <a name="test-control"></a>Test kontroll

Test kontrollen visas inte när den grafiska redigeraren startas första gången. Den öppnas när du interaktivt testar en grafisk Runbook.

## <a name="use-activities"></a>Använd aktiviteter

Aktiviteter är Bygg stenarna i en Runbook. En aktivitet kan vara en PowerShell-cmdlet, en underordnad Runbook eller ett arbets flöde. Du kan lägga till en aktivitet i runbooken genom att högerklicka på den i biblioteks kontrollen och välja **Lägg till på arbets ytan**. Du kan sedan klicka och dra aktiviteten för att placera den var som helst på arbets ytan som du vill. Platsen för aktiviteten på arbets ytan påverkar inte Runbook-åtgärden. Du kan utforma din Runbook på ett sätt som passar bäst för att visualisera åtgärden.

![Lägg till på arbets ytan](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

Välj en aktivitet på arbets ytan för att konfigurera dess egenskaper och parametrar på bladet konfiguration. Du kan ändra aktivitets etiketten till ett namn som du hittar beskrivande. Runbooken kör fortfarande den ursprungliga cmdleten. Du ändrar bara visnings namnet som används i den grafiska redigeraren. Observera att etiketten måste vara unik inom runbooken.

### <a name="parameter-sets"></a>Parameter uppsättningar

En parameter uppsättning definierar de obligatoriska och valfria parametrarna som accepterar värden för en viss cmdlet. Alla cmdlets har minst en parameter uppsättning och några har flera uppsättningar. Om en cmdlet har flera parameter uppsättningar måste du välja den som ska användas innan du kan konfigurera parametrar. Du kan ändra den parameter uppsättning som används av en aktivitet genom att välja **parameter uppsättning** och välja en annan uppsättning. I det här fallet går alla parameter värden som du redan har konfigurerat förlorade.

I följande exempel har cmdleten [Get-AzVM](/powershell/module/az.compute/get-azvm) tre parameter uppsättningar. Exemplet använder en uppsättning som kallas **ListVirtualMachineInResourceGroupParamSet**, med en enda valfri parameter, för att returnera alla virtuella datorer i en resurs grupp. I exemplet används också **GetVirtualMachineInResourceGroupParamSet** -parametern för att ange den virtuella dator som ska returneras. Den här uppsättningen har två obligatoriska parametrar och en valfri parameter.

![Parameter uppsättning](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>Parametervärden

När du anger ett värde för en parameter väljer du en data källa för att avgöra hur värdet anges. De data källor som är tillgängliga för en viss parameter beror på giltiga värden för den parametern. Null är till exempel inte ett tillgängligt alternativ för en parameter som inte tillåter Null-värden.

| Datakälla | Beskrivning |
|:--- |:--- |
| Konstant värde |Ange ett värde för parametern. Den här data källan är bara tillgänglig för följande data typer: Int32, Int64, String, Boolean, DateTime, switch. |
| Aktivitetens utdata |Använd utdata från en aktivitet som föregår den aktuella aktiviteten i arbets flödet. Alla giltiga aktiviteter visas. Använd bara den aktivitet som genererar utdata för parametervärdet. Om aktiviteten matar ut ett objekt med flera egenskaper kan du ange namnet på en speciell egenskap när du har valt aktiviteten. |
| Inmatade Runbook |Välj en inmatad Runbook som inmatad för aktivitets parametern. |
| Variabel till gång |Välj en Automation-variabel som inmatade. |
| Referens till gång |Välj en Automation-autentiseringsuppgift som indatamängd. |
| Certifikat till gång |Välj ett Automation-certifikat som inmatade. |
| Anslutnings till gång |Välj en Automation-anslutning som inmatade. |
| PowerShell-uttryck |Ange ett enkelt [PowerShell-uttryck](#work-with-powershell-expressions). Uttrycket utvärderas innan aktiviteten och resultatet används för parametervärdet. Du kan använda variabler för att referera till utdata för en aktivitet eller en inmatnings parameter för Runbook. |
| Inte konfigurerat |Rensa alla värden som tidigare har kon figurer ATS. |

#### <a name="optional-additional-parameters"></a>Valfria ytterligare parametrar

Alla cmdlets har möjlighet att ange ytterligare parametrar. Detta är PowerShell-vanliga parametrar eller andra anpassade parametrar. Den grafiska redigeraren visar en text ruta där du kan ange parametrar med PowerShell-syntax. Om du till exempel vill använda den `Verbose` gemensamma parametern anger du `-Verbose:$True` .

### <a name="retry-activity"></a>Försök igen-aktivitet

Funktionen försök igen för en aktivitet gör att den kan köras flera gånger tills ett visst villkor uppfylls, ungefär som en slinga. Du kan använda den här funktionen för aktiviteter som ska köras flera gånger, är fel känsliga, kan behöva fler än ett försök för att lyckas eller testa utdata för aktiviteten för giltiga data.

När du aktiverar återförsök för en aktivitet kan du ange en fördröjning och ett villkor. Fördröjningen är den tid (mätt i sekunder eller minuter) som Runbook väntar innan aktiviteten körs igen. Om du inte anger en fördröjning körs aktiviteten igen omedelbart efter att den har slutförts.

:::image type="content" source="media/automation-graphical-authoring-intro/retry-delay.png" alt-text="Skärm bild av inställningarna för aktivera funktions försök.":::

Villkoret för återförsök är ett PowerShell-uttryck som utvärderas efter varje tid som aktiviteten körs. Om uttrycket matchar True körs aktiviteten igen. Om uttrycket matchar falskt körs inte aktiviteten igen och runbooken flyttas vidare till nästa aktivitet.

:::image type="content" source="media/automation-graphical-authoring-intro/retry-condition.png" alt-text="Skärm bild som visar återförsök tills det här villkoret är sant och exempel på PowerShell-uttryck som kan användas i villkoret för återförsök.":::

Villkoret för återförsök kan använda en variabel med namnet `RetryData` som ger till gång till information om aktiviteternas återförsök. Den här variabeln har egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|:--- |:--- |
| `NumberOfAttempts` |Antal gånger som aktiviteten har körts. |
| `Output` |Utdata från den senaste körningen av aktiviteten. |
| `TotalDuration` |Förfluten tid sedan aktiviteten startades första gången. |
| `StartedAt` |Tid (i UTC-format) när aktiviteten startades första gången. |

Följande är exempel på villkor för aktivitets återförsök.

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

När du har konfigurerat ett villkor för återförsök för en aktivitet innehåller aktiviteten två visuella tips som påminner dig om detta. En visas i aktiviteten och den andra visas när du granskar aktivitetens konfiguration.

![Visuella indikatorer för aktivitets försök](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Skript kontroll för arbets flöde

En skript kontroll för arbets flöde är en särskild aktivitet som accepterar PowerShell-eller PowerShell Workflow-skript, beroende på vilken typ av grafisk Runbook som ska redige ras. Den här kontrollen innehåller funktioner som kanske inte är tillgängliga på annat sätt. Den kan inte ta emot parametrar, men den kan använda variabler för aktivitets utdata och indataparametrar för Runbook. Alla utdata för aktiviteten läggs till i data bussen. Ett undantag är utdata utan utgående länk, vilket innebär att utdata läggs till i Runbook-resultatet.

Till exempel utför följande kod datum beräkningar med en angiven Runbook-variabel med namnet `NumberOfDays` . Därefter skickas ett beräknat DateTime-värde som utdata som ska användas av efterföljande aktiviteter i runbooken.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="use-links-for-workflow"></a>Använd Länkar för arbets flöde

En länk i en grafisk Runbook ansluter två aktiviteter. Den visas på arbets ytan som en pil som pekar på käll aktiviteten till mål aktiviteten. Aktiviteterna körs i pilens riktning med mål aktiviteten som startar när käll aktiviteten har slutförts.

### <a name="create-a-link"></a>Skapa en länk

Du kan skapa en länk mellan två aktiviteter genom att välja käll aktivitet och klicka på cirkeln längst ned i formen. Dra pilen till mål aktiviteten och släpp.

![Skapa en länk](media/automation-graphical-authoring-intro/create-link-options.png)

Välj länken för att konfigurera dess egenskaper på bladet konfiguration. Egenskaperna inkluderar länk typen, som beskrivs i följande tabell.

| Länktyp | Beskrivning |
|:--- |:--- |
| Pipeline |Mål aktiviteten körs en gång för varje objekts utdata från käll aktiviteten. Mål aktiviteten körs inte om käll aktiviteten resulterar i inga utdata. Utdata från käll aktiviteten är tillgängligt som ett objekt. |
| Sequence |Mål aktiviteten körs bara en gång när den tar emot utdata från käll aktiviteten. Utdata från käll aktiviteten är tillgängligt som en matris med objekt. |

### <a name="start-runbook-activity"></a>Starta Runbook-aktivitet

En grafisk Runbook börjar med aktiviteter som inte har någon inkommande länk. Det finns ofta bara en aktivitet som fungerar som start aktivitet för runbooken. Om flera aktiviteter inte har en inkommande länk börjar runbooken genom att köra dem parallellt. Det följer länkarna för att köra andra aktiviteter som var och en är klar.

### <a name="specify-link-conditions"></a>Ange länk villkor

När du anger ett villkor för en länk körs mål aktiviteten bara om villkoret matchar sant. Du använder vanligt vis en `ActivityOutput` variabel i ett villkor för att hämta utdata från käll aktiviteten.

För en pipeline-länk måste du ange ett villkor för ett enskilt objekt. Runbooken utvärderar villkoret för varje objekts utdata av käll aktiviteten. Sedan körs mål aktiviteten för varje objekt som uppfyller villkoret. Med en käll aktivitet av `Get-AzVM` kan du till exempel använda följande syntax för en villkorlig pipeline-länk för att bara hämta virtuella datorer i resurs gruppen med namnet Grupp1.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

För en Sequence-länk utvärderar runbooken bara villkoret en gång, eftersom en enda matris som innehåller alla objekt från käll aktiviteten returneras. Därför kan Runbook inte använda en Sequence-länk för filtrering, som den kan med en pipeline-länk. I sekvens-länken kan du enkelt avgöra om nästa aktivitet körs eller inte.

Ta till exempel med följande aktivitets uppsättning i vår **Starta VM** -Runbook:

![Villkorlig länk med sekvenser](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

I Runbook används tre olika sekvenser som verifierar värdena för indataparametrarna `VMName` och `ResourceGroupName` för att fastställa vilken åtgärd som ska vidtas. Möjliga åtgärder är starta en enskild virtuell dator, starta alla virtuella datorer i resurs gruppen eller starta alla virtuella datorer i en prenumeration. För sekvens-länken mellan `Connect to Azure` och `Get single VM` , här är villkors logiken:

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

När du använder en villkorlig länk filtreras data som är tillgängliga från käll aktiviteten till andra aktiviteter i grenen av villkoret. Om en aktivitet är källa till flera länkar beror de data som är tillgängliga för aktiviteter i varje gren på villkoret i länken som ansluter till grenen.

`Start-AzVM`Aktiviteten i runbooken nedan startar till exempel alla virtuella datorer. Det har två villkorliga länkar. Den första villkorliga länken använder uttrycket `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` för att filtrera om `Start-AzVM` aktiviteten har slutförts. Den andra villkorliga länken använder uttrycket `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` för att filtrera om `Start-AzVm` aktiviteten inte kan starta den virtuella datorn.

![Exempel på villkorlig länk](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Alla aktiviteter som följer den första länken och som använder aktivitets utdata från `Get-AzureVM` hämtar bara de virtuella datorer som startades vid tidpunkten för `Get-AzureVM` körningen. Alla aktiviteter som följer den andra länken får bara de virtuella datorer som stoppades vid `Get-AzureVM` körningen. Alla aktiviteter som följer den tredje länken hämtar alla virtuella datorer oavsett deras körnings tillstånd.

### <a name="use-junctions"></a>Använd Knut punkter

En Knut punkt är en särskild aktivitet som väntar tills alla inkommande grenar har slutförts. Detta gör att runbooken kan köra flera aktiviteter parallellt och se till att alla har slutförts innan du går vidare.

En Knut punkt kan ha ett obegränsat antal inkommande länkar, men endast en av dessa länkar kan vara en pipeline. Antalet länkar för inkommande sekvenser är inte begränsat. Du kan skapa en Knut punkt med flera inkommande pipeline-länkar och spara runbooken, men den kommer inte att fungera när den körs.

Exemplet nedan är en del av en Runbook som startar en uppsättning virtuella datorer samtidigt som de uppdateringar som tillämpas på dessa datorer hämtas samtidigt. Den använder en Knut punkt för att säkerställa att båda processerna slutförs innan runbooken fortsätter.

![Knutpunkt](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="work-with-cycles"></a>Arbeta med cykler

En cykel bildas när en mål aktivitet länkar tillbaka till dess käll aktivitet eller till en annan aktivitet som till sist länkar tillbaka till källan. Grafisk redigering stöder för närvarande inte cykler. Om din Runbook har en cykel sparas den korrekt men får ett fel meddelande när den körs.

![Cykler](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="share-data-between-activities"></a>Dela data mellan aktiviteter

Alla data som en aktivitet matar ut med en utgående länk skrivs till data bussen för runbooken. Alla aktiviteter i runbooken kan använda data på data bussen för att fylla i parameter värden eller ta med i skript koden. En aktivitet kan komma åt utdata från en tidigare aktivitet i arbets flödet.

Hur data skrivs till data bussen beror på typen av länk för aktiviteten. För en pipeline-länk, matas data ut som flera objekt. För en Sequence-länk matas data ut som en matris. Om det bara finns ett värde, matas det ut som en matris med ett enda element.

Din Runbook har två sätt att komma åt data på data bussen: 
* Använd en data källa för utdata från en aktivitet.
* Använd en data källa för PowerShell-uttryck.

Den första mekanismen använder en data källa för aktivitets utdata för att fylla i en parameter för en annan aktivitet. Om utdata är ett objekt kan runbooken ange en enskild egenskap.

![aktivitetens utdata](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Den andra data åtkomst metoden hämtar utdata för en aktivitet i en data källa för PowerShell-uttryck eller en arbets flödes skript aktivitet med en `ActivityOutput` variabel med hjälp av syntaxen som visas nedan. Om utdata är ett objekt kan din Runbook ange en enda egenskap.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="use-checkpoints"></a>Använda kontroll punkter

Du kan ställa in [kontroll punkter](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) i ett grafiskt PowerShell Workflow-Runbook genom att välja **kontroll punkts-Runbook** i valfri aktivitet. Detta gör att en kontroll punkt anges när aktiviteten har körts.

![Checkpoint](media/automation-graphical-authoring-intro/set-checkpoint.png)

Kontroll punkter är bara aktiverade i grafiska PowerShell Workflow-Runbooks och är inte tillgängliga i grafiska runbooks. Om Runbook använder Azure-cmdlets bör den följa alla kontroll punkts aktiviteter med en `Connect-AzAccount` aktivitet. Åtgärden Anslut används om Runbook-flödet har pausats och måste startas om från den här kontroll punkten på en annan arbetare.

## <a name="handle-runbook-input"></a>Hantera indatakälla för Runbook

En Runbook kräver indata från en användare som startar runbooken via Azure Portal eller från en annan Runbook, om den aktuella används som underordnad. För en Runbook som skapar en virtuell dator kan användaren till exempel behöva ange sådan information som namnet på den virtuella datorn och andra egenskaper varje gången Runbook startar.

Runbooken accepterar ininformation genom att definiera en eller flera indataparametrar. Användaren anger värden för dessa parametrar varje gång som Runbook startar. När användaren startar en Runbook med hjälp av Azure Portal, uppmanas användaren att ange värden för varje indataparameter som stöds av runbooken.

När du redigerar din Runbook kan du komma åt dess indataparametrar genom att klicka på **indata och utdata** i Runbook-verktygsfältet. Då öppnas kontroll av indata och utdata där du kan redigera en befintlig indataparameter eller skapa en ny genom att klicka på **Lägg till indata**.

![Lägg till indata](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Varje indataparameter definieras av egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|:--- |:--- |
| Name | Krävs. Parameterns namn. Namnet måste vara unikt inom runbooken. Det måste börja med en bokstav och får bara innehålla bokstäver, siffror och under streck. Namnet får inte innehålla blank steg. |
| Beskrivning |Valfritt. Beskrivning av syftet med indataparametern. |
| Typ | Valfritt. Datatyp förväntas för parametervärdet. Azure Portal ger en lämplig kontroll för data typen för varje parameter när du uppmanas att ange indata. Parameter typer som stöds är sträng, Int32, Int64, decimal, Boolean, DateTime och Object. Om du inte väljer någon datatyp används strängen som standard.|
| Obligatorisk | Valfritt. Inställning som anger om ett värde måste anges för parametern. Om du väljer `yes` måste du ange ett värde när Runbook startas. Om du väljer `no` krävs inget värde när runbooken startas och ett standardvärde kan användas. Det går inte att starta runbooken om du inte anger något värde för varje obligatorisk parameter som inte har något definierat standardvärde. |
| Standardvärde | Valfritt. Det värde som används för en parameter om det inte skickas i när runbooken startas. Om du vill ange ett standardvärde väljer du `Custom` . Välj `None` om du inte vill ange något standardvärde. |

## <a name="handle-runbook-output"></a>Hantera Runbook-utdata

Grafisk redigering sparar data som skapats av en aktivitet som inte har någon utgående länk till Runbook- [utdata](./automation-runbook-output-and-messages.md). Utdata sparas med Runbook-jobbet och är tillgängligt för en överordnad runbook när runbooken används som underordnad.

## <a name="work-with-powershell-expressions"></a>Arbeta med PowerShell-uttryck

En av fördelarna med grafisk redigering är att du kan skapa en Runbook med minimal kunskap om PowerShell. För närvarande behöver du veta lite av PowerShell för att fylla i vissa [parameter värden](#use-activities) och för att ange [länk villkor](#use-links-for-workflow). Det här avsnittet ger en snabb introduktion till PowerShell-uttryck. Fullständig information om PowerShell finns i [skript med Windows PowerShell](/powershell/scripting/overview).

### <a name="use-a-powershell-expression-as-a-data-source"></a>Använda ett PowerShell-uttryck som en data Källa

Du kan använda ett PowerShell-uttryck som en data källa för att fylla värdet för en [aktivitets parameter](#use-activities) med resultatet av PowerShell-koden. Uttrycket kan vara en enda rad med kod som utför en enkel funktion eller flera rader som utför en viss komplex logik. Utdata från ett kommando som inte är tilldelade till en variabel matas ut till parametervärdet.

Följande kommando matar till exempel in det aktuella datumet.

```powershell-interactive
Get-Date
```

Nästa kodfragment skapar en sträng från aktuellt datum och tilldelar den till en variabel. Koden skickar innehållet i variabeln till utdata.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

Följande kommandon utvärderar aktuellt datum och returnerar en sträng som anger om den aktuella dagen är en helg eller en veckodag.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="use-activity-output"></a>Använd aktivitets utdata

Använd variabeln med följande syntax om du vill använda utdata från en tidigare aktivitet i din Runbook `ActivityOutput` .

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Du kan till exempel ha en aktivitet med en egenskap som kräver namnet på en virtuell dator. I det här fallet kan din Runbook använda följande uttryck.

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

Om egenskapen kräver ett virtuellt dator objekt i stället för ett namn, returnerar runbooken hela objektet med hjälp av följande syntax.

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

Runbooken kan använda utdata från en aktivitet i ett mer komplext uttryck, till exempel följande. Det här uttrycket sammanfogar text till namnet på den virtuella datorn.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="compare-values"></a>Jämför värden

Använd [jämförelse operatorer](/powershell/module/microsoft.powershell.core/about/about_comparison_operators) för att jämföra värden eller bestämma om ett värde matchar ett angivet mönster. En jämförelse returnerar värdet true eller false.

Följande villkor avgör till exempel om den virtuella datorn från en aktivitet som heter `Get-AzureVM` är stoppad.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

Följande villkor avgör om samma virtuella dator är i något annat tillstånd än stoppad.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

Du kan koppla flera villkor i din Runbook med hjälp av en [logisk operator](/powershell/module/microsoft.powershell.core/about/about_logical_operators), till exempel `-and` eller `-or` . Följande villkor kontrollerar till exempel om den virtuella datorn i föregående exempel är i tillståndet Stoppad eller stoppad.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="use-hashtables"></a>Använd hash

[Hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) är namn/värde-par som är användbara för att returnera en uppsättning värden. Du kan också se en hash-form som kallas för en ord lista. Egenskaper för vissa aktiviteter förväntar sig en hash i stället för ett enkelt värde.

Skapa en hash-tabellen med hjälp av följande syntax. Det kan innehålla valfritt antal poster, men de definieras av ett namn och ett värde.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Till exempel skapar följande uttryck en hash-post som ska användas som data källa för en aktivitets parameter som förväntar sig en hash med värden för en Internets ökning.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

I följande exempel används utdata från en aktivitet `Get Twitter Connection` som kallas för att fylla i en hash-tabellen.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticate-to-azure-resources"></a>Autentisera till Azure-resurser

Runbooks i Azure Automation som hanterar Azure-resurser kräver autentisering till Azure. [Kör som-kontot](./automation-security-overview.md), som även kallas för tjänstens huvud namn, är standard mekanismen som en Automation-Runbook använder för att få åtkomst till Azure Resource Manager resurser i din prenumeration. Du kan lägga till den här funktionen i en grafisk Runbook genom att lägga till `AzureRunAsConnection` anslutnings till gången, som använder PowerShell-cmdleten [Get-AutomationConnection](/system-center/sma/manage-global-assets) till arbets ytan. Du kan också lägga till cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Det här scenariot illustreras i följande exempel.

![Aktiviteter för kör som-autentisering](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

`Get Run As Connection`Aktiviteten, eller `Get-AutomationConnection` , har kon figurer ATS med en data källa med konstant värde med namnet `AzureRunAsConnection` .

![Konfiguration av kör som-anslutning](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

Nästa aktivitet, `Connect-AzAccount` , lägger till det autentiserade kör som-kontot för användning i runbooken.

![Connect-AzAccount parameter uppsättning](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>För PowerShell-Runbooks `Add-AzAccount` och `Add-AzureRMAccount` är alias för `Connect-AzAccount` . Observera att dessa alias inte är tillgängliga för dina grafiska runbooks. En grafisk Runbook kan bara använda `Connect-AzAccount` sig själv.

För parameter fälten **APPLICATIONID**, **CERTIFICATETHUMBPRINT** och **TENANTID** anger du namnet på egenskapen för fält Sök vägen, eftersom aktiviteten matar ut ett objekt med flera egenskaper. Annars Miss lyckas den vid försök att autentisera när runbooken körs. Det här är vad du behöver för att autentisera din Runbook med kör som-kontot.

Vissa prenumeranter skapar ett Automation-konto med hjälp av ett [Azure AD-användarkonto](./shared-resources/credentials.md) för att hantera den klassiska Azure-distributionen eller för Azure Resource Manager resurser. För att upprätthålla bakåtkompatibilitet för de här prenumeranterna är autentiseringen som används i din Runbook en `Add-AzureAccount` cmdlet med en [referens till gång](./shared-resources/credentials.md). Till gången representerar en Active Directory användare med åtkomst till Azure-kontot.

Du kan aktivera den här funktionen för din grafiska Runbook genom att lägga till en inloggnings till gång till arbets ytan, följt av en `Add-AzureAccount` aktivitet som använder den som referens till gång. Se följande exempel.

![Autentiserings aktiviteter](media/automation-graphical-authoring-intro/authentication-activities.png)

Runbooken måste autentisera vid start och efter varje kontroll punkt. Därför måste du använda en `Add-AzureAccount` aktivitet efter en `Checkpoint-Workflow` aktivitet. Du behöver inte använda ytterligare en aktivitet för autentiseringsuppgifter.

![Aktivitetens utdata](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="export-a-graphical-runbook"></a>Exportera en grafisk Runbook

Du kan bara exportera den publicerade versionen av en grafisk Runbook. Om Runbook ännu inte har publicerats är knappen **Exportera** inaktive rad. När du klickar på knappen **Exportera** laddas Runbook ned till den lokala datorn. Namnet på filen matchar namnet på runbooken med tillägget **. graphrunbook** .

## <a name="import-a-graphical-runbook"></a>Importera en grafisk Runbook

Du kan importera en grafisk eller grafisk PowerShell-Runbook-fil genom att välja alternativet **Importera** när du lägger till en Runbook. När du väljer den fil som ska importeras, kan du behålla samma namn eller ange en ny. I fältet **typ** av Runbook visas typen av runbook när den har bedömt den valda filen. Om du försöker välja en annan typ som inte är korrekt, visar den grafiska redigeraren ett meddelande om att det finns potentiella konflikter och att det kan finnas syntaxfel under konverteringen.

![Importera Runbook](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="test-a-graphical-runbook"></a>Testa en grafisk Runbook

Varje grafisk Runbook i Azure Automation har en utkast version och en publicerad version. Du kan bara köra den publicerade versionen, men du kan bara redigera utkast versionen. Den Publicerade versionen påverkas inte av ändringar i Utkastet. När utkast versionen är klar att användas, publicerar du den och skriver över den aktuella publicerade versionen med ditt utkast.

Du kan testa utkast versionen av en Runbook i Azure Portal när du lämnar den publicerade versionen oförändrad. Du kan också testa en ny Runbook innan den har publicerats så att du kan kontrol lera att runbooken fungerar som den ska innan eventuella versions ersättningar. Testning av en Runbook kör utkast versionen och säkerställer att alla åtgärder som den utför slutförs. Ingen jobb historik skapas, men i fönstret testutdata visas utdata.

Öppna test kontrollen för din grafiska Runbook genom att öppna Runbook för redigering och klicka sedan på **test fönster**. Test kontrollen efterfrågar indataparametrar och du kan starta runbooken genom att klicka på **Start**.

## <a name="publish-a-graphical-runbook"></a>Publicera en grafisk Runbook

Publicera en grafisk Runbook genom att öppna Runbook för redigering och klicka sedan på **publicera**. Möjliga statusar för runbooken är:

* Ny--Runbook har ännu inte publicerats. 
* Publicerat – Runbook har publicerats.
* I Edit--Runbook har redigerats efter att den publicerats, och utkast och publicerade versioner är olika.

![Runbook-status](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Du kan välja att återgå till den publicerade versionen av en Runbook. Den här åtgärden genererar ändringar som gjorts sedan Runbook senast publicerades. Den ersätter utkast versionen av Runbook med den publicerade versionen.

## <a name="next-steps"></a>Nästa steg

* Information om hur du kommer igång med grafiska runbooks finns i [Självstudier: skapa en grafisk Runbook](learn/automation-tutorial-runbook-graphical.md).
* Om du vill veta mer om Runbook-typer och deras fördelar och begränsningar, se [Azure Automation Runbook-typer](automation-runbook-types.md).
* Information om hur du autentiserar med hjälp av Automation kör som-kontot finns i [Kör som-konto](automation-security-overview.md#run-as-account).
* En PowerShell-cmdlet-referens finns i [AZ. Automation](/powershell/module/az.automation/#automation).
