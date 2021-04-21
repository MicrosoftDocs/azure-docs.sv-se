---
title: hantera autentiseringsuppgifter i Azure Automation
description: Den här artikeln beskriver hur du skapar autentiseringstillgångar och använder dem i en runbook eller DSC-konfiguration.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6220a44e952aa4d9856ac5fc2077d254103d4a2c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834290"
---
# <a name="manage-credentials-in-azure-automation"></a>hantera autentiseringsuppgifter i Azure Automation

En Automation-autentiseringstillgång innehåller ett objekt som innehåller säkerhetsautentiseringsuppgifter, till exempel ett användarnamn och ett lösenord. Runbooks och DSC-konfigurationer använder cmdlets som accepterar ett [PSCredential-objekt](/dotnet/api/system.management.automation.pscredential) för autentisering. De kan också extrahera användarnamnet och lösenordet för objektet för att tillhandahålla `PSCredential` till vissa program eller tjänster som kräver autentisering.

>[!NOTE]
>Säkra tillgångar i Azure Automation omfattar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa tillgångar krypteras och lagras i Azure Automation med en unik nyckel som genereras för varje Automation-konto. Azure Automation nyckeln i den system-hanterade Key Vault. Innan du lagrar en säker tillgång läser Automation in nyckeln från Key Vault och använder den sedan för att kryptera tillgången. 

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="powershell-cmdlets-used-to-access-credentials"></a>PowerShell-cmdlets som används för att komma åt autentiseringsuppgifter

Cmdletarna i följande tabell skapar och hanterar autentiseringsuppgifter för Automation med PowerShell. De levereras som en del av [Az-modulerna](modules.md#az-modules).

| Cmdlet | Beskrivning |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential) |Hämtar ett [CredentialInfo-objekt](/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo) som innehåller metadata om autentiseringsobjektet. Cmdleten hämtar inte själva `PSCredential` objektet.  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential) |Skapar en ny Automation-autentiseringsidentifiering. |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential) |Tar bort autentiseringsuppgifter för Automation. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential) |Anger egenskaperna för en befintlig Automation-autentiseringsidentifiering. |

## <a name="other-cmdlets-used-to-access-credentials"></a>Andra cmdlets som används för att komma åt autentiseringsuppgifter

Cmdletarna i följande tabell används för att komma åt autentiseringsuppgifter i dina runbooks och DSC-konfigurationer. 

| Cmdlet | Beskrivning |
|:--- |:--- |
| `Get-AutomationPSCredential` |Hämtar ett `PSCredential` -objekt som ska användas i en runbook eller DSC-konfiguration. Oftast bör du använda den här [interna cmdleten](modules.md#internal-cmdlets) i stället för cmdleten eftersom den senare endast `Get-AzAutomationCredential` hämtar autentiseringsinformation. Den här informationen är normalt inte användbar för att skicka till en annan cmdlet. |
| [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) |Hämtar autentiseringsuppgifter med en uppmaning om användarnamn och lösenord. Den här cmdleten är en del av standardmodulen Microsoft.PowerShell.Security. Se [Standardmoduler](modules.md#default-modules).|
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential) | Skapar en autentiseringstillgång. Den här cmdleten är en del av Azure-standardmodulen. Se [Standardmoduler](modules.md#default-modules).|

Om du `PSCredential` vill hämta objekt i koden måste du importera `Orchestrator.AssetManagement.Cmdlets` modulen. Mer information finns i [Hantera moduler i Azure Automation](modules.md).

```powershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Du bör undvika att använda variabler i `Name` parametern för `Get-AutomationPSCredential` . Användningen av dem kan göra det komplicerat att identifiera beroenden mellan runbooks eller DSC-konfigurationer och autentiseringstillgångar vid designtiden.

## <a name="python-functions-that-access-credentials"></a>Python-funktioner som har åtkomst till autentiseringsuppgifter

Funktionen i följande tabell används för att komma åt autentiseringsuppgifter i en Python 2- och 3-runbook. Python 3-runbooks är för närvarande i förhandsversion.

| Funktion | Beskrivning |
|:---|:---|
| `automationassets.get_automation_credential` | Hämtar information om en autentiseringstillgång. |

> [!NOTE]
> Importera modulen `automationassets` överst i din Python-runbook för att få åtkomst till tillgångsfunktionerna.

## <a name="create-a-new-credential-asset"></a>Skapa en ny autentiseringstillgång

Du kan skapa en ny autentiseringstillgång med hjälp av Azure Portal eller Windows PowerShell.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Skapa en ny autentiseringstillgång med Azure Portal

1. Välj Autentiseringsuppgifter under Delade resurser i det vänstra fönstret **från** **ditt Automation-konto.**
2. På sidan **Autentiseringsuppgifter** väljer du **Lägg till en autentiseringsuppgifterna**.
3. I fönstret Nya autentiseringsuppgifter anger du ett lämpligt namn på autentiseringsnamnet enligt dina namngivningsstandarder.
4. Ange ditt åtkomst-ID **i fältet** Användarnamn.
5. För båda lösenordsfälten anger du din hemliga åtkomstnyckel.

    ![Skapa nya autentiseringsuppgifter](../media/credentials/credential-create.png)

6. Om rutan multifaktorautentisering är markerad avmarkerar du den.
7. Klicka **på Skapa** för att spara den nya autentiseringstillgången.

> [!NOTE]
> Azure Automation stöder inte användarkonton som använder multifaktorautentisering.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Skapa en ny autentiseringstillgång med Windows PowerShell

I följande exempel visas hur du skapar en ny Automation-autentiseringstillgång. Ett `PSCredential` -objekt skapas först med namnet och lösenordet och används sedan för att skapa autentiseringstillgången. I stället kan du använda `Get-Credential` cmdleten för att uppmana användaren att ange ett namn och lösenord.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="get-a-credential-asset"></a>Hämta en autentiseringstillgång

En runbook eller DSC-konfiguration hämtar en autentiseringstillgång med den interna `Get-AutomationPSCredential` cmdleten. Den här cmdleten `PSCredential` hämtar ett -objekt som du kan använda med en cmdlet som kräver autentiseringsuppgifter. Du kan också hämta egenskaperna för autentiseringsobjektet för att använda individuellt. Objektet har egenskaper för användarnamnet och det säkra lösenordet. 

> [!NOTE]
> `Get-AzAutomationCredential`Cmdleten hämtar inte ett `PSCredential` objekt som kan användas för autentisering. Den innehåller endast information om autentiseringsinformationen. Om du behöver använda autentiseringsuppgifter i en runbook måste du hämta den som ett objekt `PSCredential` med hjälp av `Get-AutomationPSCredential` .

Du kan också använda [metoden GetNetworkCredential](/dotnet/api/system.management.automation.pscredential.getnetworkcredential) för att hämta ett [NetworkCredential-objekt](/dotnet/api/system.net.networkcredential) som representerar en oskyddad version av lösenordet.

### <a name="textual-runbook-example"></a>Exempel på textbaserad runbook

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

I följande exempel visas hur du använder powershell-autentiseringsuppgifter i en runbook. Den hämtar autentiseringssuppgifter och tilldelar dess användarnamn och lösenord till variabler.

```powershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Du kan också använda autentiseringsuppgifter för att autentisera till Azure med [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). I de flesta fall bör du använda ett [Kör som-konto](../automation-security-overview.md#run-as-accounts) och hämta anslutningen med [Get-AzAutomationConnection](../automation-connections.md).

```powershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$securePassword)

Connect-AzAccount -Credential $myPsCred
```

# <a name="python-2"></a>[Python 2](#tab/python2)

I följande exempel visas ett exempel på åtkomst till autentiseringsuppgifter i Python 2-runbooks.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

# <a name="python-3"></a>[Python 3](#tab/python3)

I följande exempel visas ett exempel på åtkomst till autentiseringsuppgifter i Python 3-runbooks (förhandsversion).

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print (cred["username"])
print (cred["password"])
```

---

### <a name="graphical-runbook-example"></a>Exempel på grafisk runbook

Du kan lägga till en aktivitet för den interna cmdleten i en grafisk runbook genom att högerklicka på autentiseringssuppgifter i fönstret Bibliotek i den grafiska redigeraren och välja Lägg till `Get-AutomationPSCredential` **på arbetsytan**.

![Lägg till cmdlet för autentiseringsuppgifter på arbetsytan](../media/credentials/credential-add-canvas.png)

Följande bild visar ett exempel på hur du använder autentiseringsuppgifter i en grafisk runbook. I det här fallet tillhandahåller autentiseringsautentisering för en runbook till Azure-resurser, enligt beskrivningen i Använda Azure AD i Azure Automation för [att autentisera till Azure](../automation-use-azure-ad.md). Den första aktiviteten hämtar de autentiseringsuppgifter som har åtkomst till Azure-prenumerationen. Kontoanslutningsaktiviteten använder sedan dessa autentiseringsuppgifter för att tillhandahålla autentisering för alla aktiviteter som kommer efter den. En [pipelinelänk](../automation-graphical-authoring-intro.md#use-links-for-workflow) används här eftersom `Get-AutomationPSCredential` förväntar sig ett enda objekt.  

![Exempel på arbetsflöde för autentiseringsuppgifter med pipelinelänk](../media/credentials/get-credential.png)

## <a name="use-credentials-in-a-dsc-configuration"></a>Använda autentiseringsuppgifter i en DSC-konfiguration

DSC-konfigurationer i Azure Automation fungerar med autentiseringstillgångar med , men de kan också skicka `Get-AutomationPSCredential` autentiseringstillgångar via parametrar. Mer information finns i [Kompilera konfigurationer i Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="next-steps"></a>Nästa steg

* Mer information om de cmdlets som används för att komma åt certifikat finns [i Hantera moduler i Azure Automation](modules.md).
* Allmän information om runbooks finns i [Runbook-körning i Azure Automation](../automation-runbook-execution.md).
* Mer information om DSC-konfigurationer finns [i Azure Automation State Configuration översikt](../automation-dsc-overview.md).
