---
title: Hantera variabler i Azure Automation
description: Den här artikeln beskriver hur du arbetar med variabler i runbooks och DSC-konfigurationer.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/28/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9f1ace00356583dbb6102317e3d157fb58682710
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832587"
---
# <a name="manage-variables-in-azure-automation"></a>Hantera variabler i Azure Automation

Variabeltillgångar är värden som är tillgängliga för alla runbooks och DSC-konfigurationer i ditt Automation-konto. Du kan hantera dem från Azure Portal, från PowerShell, i en runbook eller i en DSC-konfiguration.

Automation-variabler är användbara i följande scenarier:

- Dela ett värde mellan flera runbooks eller DSC-konfigurationer.

- Dela ett värde mellan flera jobb från samma runbook eller DSC-konfiguration.

- Hantera ett värde som används av runbooks eller DSC-konfigurationer från portalen eller från PowerShell-kommandoraden. Ett exempel är en uppsättning vanliga konfigurationsobjekt, till exempel en specifik lista över VM-namn, en specifik resursgrupp, ett AD-domännamn med mera.  

Azure Automation bevarar variabler och gör dem tillgängliga även om en runbook eller DSC-konfiguration misslyckas. Med det här beteendet kan en runbook eller DSC-konfiguration ange ett värde som sedan används av en annan runbook, eller av samma runbook- eller DSC-konfiguration nästa gång den körs.

Azure Automation lagrar varje krypterad variabel på ett säkert sätt. När du skapar en variabel kan du ange dess kryptering och lagring genom Azure Automation som en säker tillgång. När du har skapat variabeln kan du inte ändra dess krypteringsstatus utan att återskapa variabeln. Om du har Automation-kontovariabler som lagrar känsliga data som inte redan är krypterade måste du ta bort dem och återskapa dem som krypterade variabler. En Azure Security Center rekommendation är att kryptera alla Azure Automation variabler enligt beskrivningen i [Automation-kontovariabler ska krypteras](../../security-center/recommendations-reference.md#recs-compute). Om du har okrypterade variabler som du vill [](../../security-center/exempt-resource.md) undanta från den här säkerhetsrekommendationen kan du se Undanta en resurs från rekommendationer och säkerhetspoäng för att skapa en undantagsregel.

>[!NOTE]
>Säkra tillgångar i Azure Automation omfattar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa tillgångar krypteras och lagras i Azure Automation med en unik nyckel som genereras för varje Automation-konto. Azure Automation lagrar nyckeln i den system-hanterade Key Vault. Innan du lagrar en säker tillgång läser Automation in nyckeln från Key Vault och använder den sedan för att kryptera tillgången.

## <a name="variable-types"></a>Variabeltyper

När du skapar en variabel med Azure Portal måste du ange en datatyp från listrutan så att portalen kan visa lämplig kontroll för att ange variabelvärdet. Följande är variabeltyper som är tillgängliga i Azure Automation:

* Sträng
* Integer
* DateTime
* Boolesk
* Null

Variabeln är inte begränsad till den angivna datatypen. Du måste ange variabeln med Windows PowerShell om du vill ange ett värde av en annan typ. Om du anger `Not defined` anges värdet för variabeln till Null. Du måste ange värdet med cmdleten [Set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable) eller den interna `Set-AutomationVariable` cmdleten. Du använder i dina runbooks som är avsedda att köras i `Set-AutomationVariable` Sandbox-miljön i Azure eller på en Windows-Hybrid Runbook Worker.

Du kan inte använda Azure Portal för att skapa eller ändra värdet för en komplex variabeltyp. Du kan dock ange ett värde av valfri typ med hjälp av Windows PowerShell. Komplexa typer hämtas som en [Newtonsoft.Jspå. Linq.JProperty för](https://www.newtonsoft.com/json/help/html/N_Newtonsoft_Json_Linq.htm) en komplex objekttyp i stället för en PSObject-typ [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Du kan lagra flera värden till en enskild variabel genom att skapa en matris eller hash-tabell och spara den i variabeln.

>[!NOTE]
>Namnvariabler för virtuella datorer kan vara högst 80 tecken. Resursgruppvariabler kan vara högst 90 tecken. Se [Namngivningsregler och begränsningar för Azure-resurser.](../../azure-resource-manager/management/resource-name-rules.md)

## <a name="powershell-cmdlets-to-access-variables"></a>PowerShell-cmdlets för att få åtkomst till variabler

Cmdletarna i följande tabell skapar och hanterar Automation-variabler med PowerShell. De levereras som en del av [Az-modulerna](modules.md#az-modules).

| Cmdlet | Beskrivning |
|:---|:---|
|[Get-AzAutomationVariable](/powershell/module/az.automation/get-azautomationvariable) | Hämtar värdet för en befintlig variabel. Om värdet är en enkel typ hämtas samma typ. Om det är en komplex typ `PSCustomObject` hämtas en typ. <sup>1</sup>|
|[New-AzAutomationVariable](/powershell/module/az.automation/new-azautomationvariable) | Skapar en ny variabel och anger dess värde.|
|[Remove-AzAutomationVariable](/powershell/module/az.automation/remove-azautomationvariable)| Tar bort en befintlig variabel.|
|[Set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable)| Ställer in värdet för en befintlig variabel. |

<sup>1</sup> Du kan inte använda den här cmdleten för att hämta värdet för en krypterad variabel. Det enda sättet att göra detta är genom att använda den interna `Get-AutomationVariable` cmdleten i en runbook eller DSC-konfiguration. Om du till exempel vill se värdet för en krypterad variabel kan du skapa en runbook för att hämta variabeln och sedan skriva den till utdataströmmen:

```powershell
$encryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $encryptvar"
```

## <a name="internal-cmdlets-to-access-variables"></a>Interna cmdlets för att komma åt variabler

De interna cmdletarna i följande tabell används för att komma åt variabler i dina runbooks och DSC-konfigurationer. Dessa cmdlets har den globala modulen `Orchestrator.AssetManagement.Cmdlets` . Mer information finns i [Interna cmdlets](modules.md#internal-cmdlets).

| Intern cmdlet | Description |
|:---|:---|
|`Get-AutomationVariable`|Hämtar värdet för en befintlig variabel.|
|`Set-AutomationVariable`|Ställer in värdet för en befintlig variabel.|

> [!NOTE]
> Undvik att använda variabler i `Name` parametern för `Get-AutomationVariable` cmdleten i en Runbook eller DSC-konfiguration. Användning av en variabel kan komplicera identifieringen av beroenden mellan runbooks och Automation-variabler vid designtiden.

## <a name="python-functions-to-access-variables"></a>Python-funktioner för att komma åt variabler

Funktionerna i följande tabell används för att komma åt variabler i en Python 2- och 3-runbook. Python 3-runbooks är för närvarande i förhandsversion.

|Python Functions|Description|
|:---|:---|
|`automationassets.get_automation_variable`|Hämtar värdet för en befintlig variabel. |
|`automationassets.set_automation_variable`|Ställer in värdet för en befintlig variabel. |

> [!NOTE]
> Du måste importera modulen `automationassets` överst i din Python-runbook för att få åtkomst till tillgångsfunktionerna.

## <a name="create-and-get-a-variable"></a>Skapa och hämta en variabel

>[!NOTE]
>Om du vill ta bort krypteringen för en variabel måste du ta bort variabeln och återskapa den som okrypterad.

### <a name="create-and-get-a-variable-using-the-azure-portal"></a>Skapa och hämta en variabel med hjälp av Azure Portal

1. Från ditt Automation-konto väljer du Variabler i det **vänstra fönstret** under **Delade resurser.**
2. På sidan **Variabler väljer** du Lägg till **en variabel**.
3. Slutför alternativen på sidan **Ny variabel och** välj sedan Skapa för **att** spara den nya variabeln.

> [!NOTE]
> När du har sparat en krypterad variabel kan den inte visas i portalen. Den kan bara uppdateras.

### <a name="create-and-get-a-variable-in-windows-powershell"></a>Skapa och hämta en variabel i Windows PowerShell

Din runbook eller DSC-konfiguration använder `New-AzAutomationVariable` cmdleten för att skapa en ny variabel och ange dess ursprungliga värde. Om variabeln är krypterad ska anropet använda `Encrypted` parametern . Skriptet kan hämta värdet för variabeln med hjälp av `Get-AzAutomationVariable` .

>[!NOTE]
>Ett PowerShell-skript kan inte hämta ett krypterat värde. Det enda sättet att göra detta är att använda den interna `Get-AutomationVariable` cmdleten.

I följande exempel visas hur du skapar en strängvariabel och sedan returnerar dess värde.

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

I följande exempel visas hur du skapar en variabel med en komplex typ och sedan hämtar dess egenskaper. I det här fallet används ett virtuellt [datorobjekt från Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) för att ange en delmängd av dess egenskaper.

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

## <a name="textual-runbook-examples"></a>Exempel på runbook-text

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

I följande exempel visas hur du anger och hämtar en variabel i en textbaserad runbook. Det här exemplet förutsätter att du skapar heltalsvariabler med namnet **numberOfIterations** och **numberOfRunnings** och en strängvariabel med namnet **sampleMessage**.

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

Följande exempel visar hur du hämtar en variabel, anger en variabel och hanterar ett undantag för en icke-existerande variabel i en Python 2-runbook.

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

I följande exempel visas hur du hämtar en variabel, anger en variabel och hanterar ett undantag för en icke-existerande variabel i en Python 3-runbook (förhandsversion).

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

## <a name="graphical-runbook-examples"></a>Grafiska runbook-exempel

I en grafisk runbook kan du lägga till aktiviteter för de interna cmdletarna **Get-AutomationVariable** **eller Set-AutomationVariable.** Högerklicka bara på varje variabel i fönstret Bibliotek i den grafiska redigeraren och välj den aktivitet som du vill använda.

![Lägg till variabel på arbetsytan](../media/variables/runbook-variable-add-canvas.png)

Följande bild visar exempelaktiviteter för att uppdatera en variabel med ett enkelt värde i en grafisk runbook. I det här exemplet hämtar aktiviteten för `Get-AzVM` en enskild virtuell Azure-dator och sparar datornamnet i en befintlig Automation-strängvariabel. Det spelar ingen roll om länken [är en pipeline eller sekvens](../automation-graphical-authoring-intro.md#use-links-for-workflow) eftersom koden bara förväntar sig ett enda objekt i utdata.

![Ange enkel variabel](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Nästa steg

- Mer information om de cmdlets som används för att komma åt variabler finns [i Hantera moduler i Azure Automation](modules.md).

- Allmän information om runbooks finns i [Runbook-körning i Azure Automation](../automation-runbook-execution.md).

- Mer information om DSC-konfigurationer finns [i Azure Automation State Configuration översikt](../automation-dsc-overview.md).
