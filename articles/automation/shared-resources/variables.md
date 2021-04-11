---
title: Hantera variabler i Azure Automation
description: Den här artikeln beskriver hur du arbetar med variabler i Runbooks och DSC-konfigurationer.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/28/2021
ms.topic: conceptual
ms.openlocfilehash: 74b808b941c00c9c47fbff31223274318ebeb2a0
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169393"
---
# <a name="manage-variables-in-azure-automation"></a>Hantera variabler i Azure Automation

Variabel till gångar är värden som är tillgängliga för alla Runbooks och DSC-konfigurationer i ditt Automation-konto. Du kan hantera dem från Azure Portal, från PowerShell, i en Runbook eller i en DSC-konfiguration.

Automation-variabler är användbara i följande scenarier:

- Dela ett värde mellan flera Runbooks och DSC-konfigurationer.

- Dela ett värde mellan flera jobb från samma Runbook-eller DSC-konfiguration.

- Hantera ett värde som används av Runbooks eller DSC-konfigurationer från portalen eller från PowerShell-kommandoraden. Ett exempel är en uppsättning vanliga konfigurations objekt, till exempel en lista med namn på virtuella datorer, en särskild resurs grupp, ett AD-domännamn och mer.  

Azure Automation behåller variablerna och gör dem tillgängliga även om en Runbook-eller DSC-konfiguration Miss lyckas. Med det här beteendet kan en Runbook-eller DSC-konfiguration ange ett värde som sedan används av en annan Runbook eller av samma Runbook-eller DSC-konfiguration nästa gången den körs.

Azure Automation lagrar varje krypterad variabel på ett säkert sätt. När du skapar en variabel kan du ange dess kryptering och lagring genom att Azure Automation som en säker till gång. När du har skapat variabeln kan du inte ändra dess krypterings status utan att skapa variabeln på nytt. Om du har variabler för Automation-konto som lagrar känsliga data som inte redan är krypterade måste du ta bort dem och återskapa dem som krypterade variabler. En Azure Security Center rekommendation är att kryptera alla Azure Automation variabler enligt beskrivningen i [Automation-kontots variabler bör vara krypterade](../../security-center/recommendations-reference.md#recs-compute). Om du har okrypterade variabler som du inte vill ska uteslutas från den här säkerhets rekommendationen läser du [undanta en resurs från rekommendationer och säkra Poäng](../../security-center/exempt-resource.md) för att skapa en undantags regel.

>[!NOTE]
>Säkra till gångar i Azure Automation inkluderar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa till gångar krypteras och lagras i Azure Automation att använda en unik nyckel som genereras för varje Automation-konto. Azure Automation lagrar nyckeln i systemhanterade Key Vault. Innan du lagrar en säker till gång läser Automation in nyckeln från Key Vault och använder den för att kryptera till gången.

## <a name="variable-types"></a>Variabel typer

När du skapar en variabel med Azure Portal måste du ange en datatyp i list rutan så att portalen kan visa lämplig kontroll för att ange variabelvärdet. Följande är variabel typer som är tillgängliga i Azure Automation:

* Sträng
* Integer
* DateTime
* Boolesk
* Null

Variabeln är inte begränsad till den angivna data typen. Du måste ange variabeln med hjälp av Windows PowerShell om du vill ange ett värde av en annan typ. Om du anger `Not defined` anges värdet för variabeln till null. Du måste ange värdet med cmdleten [set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable) eller den interna `Set-AutomationVariable` cmdleten. Du använder `Set-AutomationVariable` i dina runbooks som är avsedda att köras i Azure sandbox-miljön eller på en Windows hybrid Runbook Worker.

Du kan inte använda Azure Portal för att skapa eller ändra värdet för en komplex variabel typ. Du kan dock ange ett värde av vilken typ som helst med hjälp av Windows PowerShell. Komplexa typer hämtas som en [Newtonsoft.Jspå. LINQ. JProperty](https://www.newtonsoft.com/json/help/html/N_Newtonsoft_Json_Linq.htm) för en komplex objekt typ i stället för en PSObject-typ [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Du kan lagra flera värden i en enskild variabel genom att skapa en matris eller hash och spara den i variabeln.

>[!NOTE]
>Variabler för virtuella dator namn får bestå av högst 80 tecken. Variabler för resurs grupper får bestå av högst 90 tecken. Se [namngivnings regler och begränsningar för Azure-resurser](../../azure-resource-manager/management/resource-name-rules.md).

## <a name="powershell-cmdlets-to-access-variables"></a>PowerShell-cmdletar för att få åtkomst till variabler

Cmdletarna i följande tabell skapar och hanterar Automation-variabler med PowerShell. De levereras som en del av [AZ-modulerna](modules.md#az-modules).

| Cmdlet | Beskrivning |
|:---|:---|
|[Get-AzAutomationVariable](/powershell/module/az.automation/get-azautomationvariable) | Hämtar värdet för en befintlig variabel. Om värdet är en enkel typ hämtas samma typ. Om det är en komplex typ hämtas en `PSCustomObject` typ. <sup>1</sup>|
|[New-AzAutomationVariable](/powershell/module/az.automation/new-azautomationvariable) | Skapar en ny variabel och anger dess värde.|
|[Remove-AzAutomationVariable](/powershell/module/az.automation/remove-azautomationvariable)| Tar bort en befintlig variabel.|
|[Set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable)| Ställer in värdet för en befintlig variabel. |

<sup>1</sup> du kan inte använda denna cmdlet för att hämta värdet för en krypterad variabel. Det enda sättet att göra detta är genom att använda den interna `Get-AutomationVariable` cmdleten i en Runbook-eller DSC-konfiguration. Om du till exempel vill visa värdet för en krypterad variabel kan du skapa en Runbook för att hämta variabeln och sedan skriva den till utdataströmmen:

```powershell
$encryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $encryptvar"
```

## <a name="internal-cmdlets-to-access-variables"></a>Interna cmdletar för att få åtkomst till variabler

De interna cmdletarna i följande tabell används för att få åtkomst till variabler i dina runbooks och DSC-konfigurationer. Dessa cmdletar levereras med den globala modulen `Orchestrator.AssetManagement.Cmdlets` . Mer information finns i [interna cmdletar](modules.md#internal-cmdlets).

| Intern cmdlet | Beskrivning |
|:---|:---|
|`Get-AutomationVariable`|Hämtar värdet för en befintlig variabel.|
|`Set-AutomationVariable`|Ställer in värdet för en befintlig variabel.|

> [!NOTE]
> Undvik att använda variabler i `Name` cmdlet-parametern `Get-AutomationVariable` i en Runbook-eller DSC-konfiguration. Användningen av en variabel kan komplicera identifieringen av beroenden mellan Runbooks och automation-variabler i design läge.

## <a name="python-functions-to-access-variables"></a>Python-funktioner för att få åtkomst till variabler

Funktionerna i följande tabell används för att få åtkomst till variabler i en python 2-eller 3-Runbook. Python 3-Runbooks är för närvarande en för hands version.

|Python-funktioner|Beskrivning|
|:---|:---|
|`automationassets.get_automation_variable`|Hämtar värdet för en befintlig variabel. |
|`automationassets.set_automation_variable`|Ställer in värdet för en befintlig variabel. |

> [!NOTE]
> Du måste importera `automationassets` modulen överst i python-runbooken för att få åtkomst till till gångs funktionerna.

## <a name="create-and-get-a-variable"></a>Skapa och hämta en variabel

>[!NOTE]
>Om du vill ta bort krypteringen för en variabel måste du ta bort variabeln och återskapa den som okrypterad.

### <a name="create-and-get-a-variable-using-the-azure-portal"></a>Skapa och hämta en variabel med hjälp av Azure Portal

1. Från ditt Automation-konto väljer du **variabler** under **delade resurser** i den vänstra rutan.
2. På sidan **variabler** väljer du **Lägg till en variabel**.
3. Slutför alternativen på sidan **ny variabel** och välj sedan **skapa** för att spara den nya variabeln.

> [!NOTE]
> När du har sparat en krypterad variabel kan den inte visas i portalen. Den kan bara uppdateras.

### <a name="create-and-get-a-variable-in-windows-powershell"></a>Skapa och hämta en variabel i Windows PowerShell

Din Runbook-eller DSC-konfiguration använder `New-AzAutomationVariable` cmdleten för att skapa en ny variabel och anger dess start värde. Om variabeln är krypterad ska anropet använda- `Encrypted` parametern. Skriptet kan hämta värdet för variabeln med hjälp av `Get-AzAutomationVariable` .

>[!NOTE]
>Ett PowerShell-skript kan inte hämta ett krypterat värde. Det enda sättet att göra detta är att använda den interna `Get-AutomationVariable` cmdleten.

I följande exempel visas hur du skapar en sträng variabel och sedan returnerar dess värde.

```powershell
$rgName = "ResourceGroup01"
$accountName = "MyAutomationAccount"
$variableValue = "My String"

New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
-AutomationAccountName "MyAutomationAccount" -Name 'MyStringVariable' `
-Encrypted $false -Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
-AutomationAccountName "MyAutomationAccount" -Name 'MyStringVariable').Value
```

I följande exempel visas hur du skapar en variabel med en komplex typ och sedan hämtar dess egenskaper. I det här fallet används ett virtuellt dator objekt från [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) för att ange en delmängd av dess egenskaper.

```powershell
$rgName = "ResourceGroup01"
$accountName = "MyAutomationAccount"

$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" -Name "VM01" | Select Name, Location, Extensions
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" -AutomationAccountName "MyAutomationAccount" -Name "MyComplexVariable" -Encrypted $false -Value $vm

$vmValue = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
-AutomationAccountName "MyAutomationAccount" -Name "MyComplexVariable"

$vmName = $vmValue.Value.Name
$vmTags = $vmValue.Value.Tags
```

## <a name="textual-runbook-examples"></a>Text Runbook-exempel

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

I följande exempel visas hur du ställer in och hämtar en variabel i en text-Runbook. Det här exemplet förutsätter att du skapar heltals variabler med namnet **numberOfIterations** och **numberOfRunnings** och en String-variabel med namnet **sampleMessage**.

```powershell
$rgName = "ResourceGroup01"
$accountName = "MyAutomationAccount"

$numberOfIterations = Get-AutomationVariable -Name "numberOfIterations"
$numberOfRunnings = Get-AutomationVariable -Name "numberOfRunnings"
$sampleMessage = Get-AutomationVariable -Name "sampleMessage"

Write-Output "Runbook has been run $numberOfRunnings times."

for ($i = 1; $i -le $numberOfIterations; $i++) {
    Write-Output "$i`: $sampleMessage"
}
Set-AutomationVariable -Name numberOfRunnings -Value ($numberOfRunnings += 1)
```

# <a name="python-2"></a>[Python 2](#tab/python2)

Följande exempel visar hur du hämtar en variabel, ställer in en variabel och hanterar ett undantag för en icke-existerande variabel i en python 2-Runbook.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print "variable not found"
```

# <a name="python-3"></a>[Python 3](#tab/python3)

Följande exempel visar hur du hämtar en variabel, ställer in en variabel och hanterar ett undantag för en icke-existerande variabel i en python 3-Runbook (för hands version).

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print ("variable not found")
```

---

## <a name="graphical-runbook-examples"></a>Grafiska Runbook-exempel

I en grafisk Runbook kan du lägga till aktiviteter för de interna cmdletarna **Get-AutomationVariable** eller **set-AutomationVariable**. Högerklicka bara på varje variabel i fönstret Bibliotek i den grafiska redigeraren och välj den aktivitet som du vill använda.

![Lägg till variabel på arbets ytan](../media/variables/runbook-variable-add-canvas.png)

Följande bild visar exempel aktiviteter för att uppdatera en variabel med ett enkelt värde i en grafisk Runbook. I det här exemplet har aktiviteten för att `Get-AzVM` Hämta en enda virtuell Azure-dator och sparar dator namnet i en befintlig Automation-sträng-variabel. Det spelar ingen roll om [länken är en pipeline eller sekvens](../automation-graphical-authoring-intro.md#use-links-for-workflow) eftersom koden endast förväntar sig ett enskilt objekt i utdata.

![Ange enkel variabel](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Nästa steg

- Mer information om de cmdletar som används för att få åtkomst till variabler finns [i hantera moduler i Azure Automation](modules.md).

- Allmän information om Runbooks finns [i Runbook-körning i Azure Automation](../automation-runbook-execution.md).

- Mer information om DSC-konfigurationer finns i [Översikt över Azure Automation tillstånds konfiguration](../automation-dsc-overview.md).
