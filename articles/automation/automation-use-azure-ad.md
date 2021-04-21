---
title: Använda Azure AD i Azure Automation för autentisering till Azure
description: Den här artikeln beskriver hur du använder Azure AD Azure Automation som provider för autentisering till Azure.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 34033589a297b1a3a2abb97d346f1da478f950e6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830294"
---
# <a name="use-azure-ad-to-authenticate-to-azure"></a>Använda Azure AD för att autentisera mot Azure

Tjänsten [Azure Active Directory (AD)](../active-directory/fundamentals/active-directory-whatis.md) möjliggör ett antal administrativa uppgifter, till exempel användarhantering, domänhantering och konfiguration av enkel inloggning. I den här artikeln beskrivs hur du använder Azure AD Azure Automation som provider för autentisering till Azure. 

## <a name="install-azure-ad-modules"></a>Installera Azure AD-moduler

Du kan aktivera Azure AD via följande PowerShell-moduler:

* Azure Active Directory PowerShell för Graph (AzureRM- och Az-moduler). Azure Automation levereras med AzureRM-modulen och dess senaste uppgradering, Az-modulen. Funktionerna omfattar icke-interaktiv autentisering till Azure med autentiseringsuppgifter baserade på Azure AD-användare (OrgId). Se [Azure AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76).

* Microsoft Azure Active Directory för Windows PowerShell (MSOnline-modul). Den här modulen möjliggör interaktioner med Microsoft Online, inklusive Microsoft 365.

>[!NOTE]
>PowerShell Core stöder inte MSOnline-modulen. Om du vill använda modul-cmdletarna måste du köra dem från Windows PowerShell. Vi uppmanar dig att använda den nyare Azure Active Directory PowerShell för Graph-moduler i stället för MSOnline-modulen. 

### <a name="preinstallation"></a>Preinstallation

Innan du installerar Azure AD-modulerna på datorn:

* Avinstallera alla tidigare versioner av AzureRM/Az-modulen och MSOnline-modulen. 

* Avinstallera Microsoft Online Services Sign-In assistenten för att säkerställa att de nya PowerShell-modulerna fungerar korrekt.  

### <a name="install-the-azurerm-and-az-modules"></a>Installera Modulerna AzureRM och Az

>[!NOTE]
>Om du vill arbeta med dessa moduler måste du använda PowerShell version 5.1 eller senare med en 64-bitars version av Windows. 

1. Installera Windows Management Framework (WMF) 5.1. Se [Installera och konfigurera WMF 5.1.](/powershell/scripting/wmf/setup/install-configure)

2. Installera AzureRM och/eller Az med hjälp av [anvisningarna i Installera Azure PowerShell i Windows med PowerShellGet.](/powershell/azure/azurerm/install-azurerm-ps)

### <a name="install-the-msonline-module"></a>Installera MSOnline-modulen

>[!NOTE]
>Om du vill installera MSOnline-modulen måste du vara medlem i en administratörsroll. Se [Om administratörsroller.](/microsoft-365/admin/add-users/about-admin-roles)

1. Kontrollera att Microsoft .NET Framework 3.5.x-funktionen är aktiverad på datorn. Det är troligt att datorn har en nyare version installerad, men bakåtkompatibilitet med äldre versioner av .NET Framework kan aktiveras eller inaktiveras. 

2. Installera 64-bitarsversionen av [inloggningsassistenten för Microsoft Online Services.](https://www.microsoft.com/Download/details.aspx?id=28177)

3. Kör Windows PowerShell administratör för att skapa en upphöjd Windows PowerShell kommandotolk.

4. Distribuera Azure Active Directory från [MSOnline 1.0](http://www.powershellgallery.com/packages/MSOnline/1.0).

5. Om du uppmanas att installera NuGet-providern skriver du Y och trycker på RETUR.

6. Om du uppmanas att installera modulen från [PSGallery](https://www.powershellgallery.com/)skriver du Y och trycker på RETUR.

### <a name="install-support-for-pscredential"></a>Installera stöd för PSCredential

Azure Automation använder [PSCredential-klassen](/dotnet/api/system.management.automation.pscredential) för att representera en autentiseringstillgång. Skripten hämtar `PSCredential` objekt med hjälp av `Get-AutomationPSCredential` cmdleten . Mer information finns i [Autentiseringstillgångar i Azure Automation](shared-resources/credentials.md).

## <a name="assign-a-subscription-administrator"></a>Tilldela en prenumerationsadministratör

Du måste tilldela en administratör för Azure-prenumerationen. Den här personen har rollen Ägare för prenumerationsomfånget. Se [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md). 

## <a name="change-the-azure-ad-users-password"></a>Ändra Azure AD-användarens lösenord

Så här ändrar du Azure AD-användarens lösenord:

1. Logga ut från Azure.

2. Låt administratören logga in på Azure som Azure AD-användaren precis har skapat, med det fullständiga användarnamnet (inklusive domänen) och ett tillfälligt lösenord. 

3. Be administratören att ändra lösenordet när du uppmanas att göra det.

## <a name="configure-azure-automation-to-manage-the-azure-subscription"></a>Konfigurera Azure Automation för att hantera Azure-prenumerationen

För Azure Automation kommunicera med Azure AD måste du hämta autentiseringsuppgifterna som är associerade med Azure-anslutningen till Azure AD. Exempel på dessa autentiseringsuppgifter är klientorganisations-ID, prenumerations-ID och liknande. Mer information om anslutningen mellan Azure och Azure AD finns i [Ansluta din organisation till Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="create-a-credential-asset"></a>Skapa en autentiseringstillgång

När Azure-autentiseringsuppgifterna för Azure AD är tillgängliga är det dags att skapa en Azure Automation-autentiseringstillgång för att lagra Azure AD-autentiseringsuppgifterna på ett säkert sätt så att runbooks och Desire State Configuration-skript (DSC) kan komma åt dem. Du kan göra detta med antingen Azure Portal eller PowerShell-cmdlets.

### <a name="create-the-credential-asset-in-azure-portal"></a>Skapa autentiseringstillgången i Azure Portal

Du kan använda Azure Portal för att skapa autentiseringstillgången. Gör den här åtgärden från ditt Automation-konto med **hjälp av autentiseringsuppgifter** under **Delade resurser.** Se [Autentiseringstillgångar i Azure Automation](shared-resources/credentials.md).

### <a name="create-the-credential-asset-with-windows-powershell"></a>Skapa autentiseringstillgången med Windows PowerShell

För att förbereda en ny autentiseringstillgång i Windows PowerShell skapar skriptet först ett -objekt med det `PSCredential` tilldelade användarnamnet och lösenordet. Skriptet använder sedan det här objektet för att skapa tillgången via ett anrop till cmdleten [New-AzureAutomationCredential.](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential) Skriptet kan också anropa [cmdleten Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) för att uppmana användaren att ange ett namn och lösenord. Se [Autentiseringstillgångar i Azure Automation](shared-resources/credentials.md). 

## <a name="manage-azure-resources-from-an-azure-automation-runbook"></a>Hantera Azure-resurser från en Azure Automation runbook

Du kan hantera Azure-resurser från Azure Automation runbooks med hjälp av autentiseringstillgången. Nedan visas ett exempel på en PowerShell-runbook som samlar in den autentiseringstillgång som ska användas för att stoppa och starta virtuella datorer i en Azure-prenumeration. Den här runbooken använder `Get-AutomationPSCredential` först för att hämta de autentiseringsuppgifter som ska användas för att autentisera till Azure. Den anropar sedan [cmdleten Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) för att ansluta till Azure med autentiseringssuppgifter. Skriptet använder [cmdleten Select-AzureSubscription](/powershell/module/servicemanagement/azure.service/select-azuresubscription) för att välja den prenumeration som ska fungera med. 

```azurepowershell
Workflow Stop-Start-AzureVM 
{ 
    Param 
    (    
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureSubscriptionId, 
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureVMList="All", 
        [Parameter(Mandatory=$true)][ValidateSet("Start","Stop")] 
        [String] 
        $Action 
    ) 
     
    $credential = Get-AutomationPSCredential -Name 'AzureCredential' 
    Connect-AzAccount -Credential $credential 
    Select-AzureSubscription -SubscriptionId $AzureSubscriptionId 
 
    if($AzureVMList -ne "All") 
    { 
        $AzureVMs = $AzureVMList.Split(",") 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
    } 
    else 
    { 
        $AzureVMs = (Get-AzVM).Name 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
 
    } 
 
    foreach($AzureVM in $AzureVMsToHandle) 
    { 
        if(!(Get-AzVM | ? {$_.Name -eq $AzureVM})) 
        { 
            throw " AzureVM : [$AzureVM] - Does not exist! - Check your inputs " 
        } 
    } 
 
    if($Action -eq "Stop") 
    { 
        Write-Output "Stopping VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Stop-AzVM -Force 
        } 
    } 
    else 
    { 
        Write-Output "Starting VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Start-AzVM 
        } 
    } 
}
```  

## <a name="next-steps"></a>Nästa steg

* Mer information om användning av autentiseringsuppgifter finns i [Hantera autentiseringsuppgifter i Azure Automation](shared-resources/credentials.md).
* Information om moduler finns i [Hantera moduler i Azure Automation](shared-resources/modules.md).
* Om du behöver starta en runbook kan du se [Starta en runbook i Azure Automation](start-runbooks.md).
* Mer information om PowerShell finns i [PowerShell Docs](/powershell/scripting/overview).
