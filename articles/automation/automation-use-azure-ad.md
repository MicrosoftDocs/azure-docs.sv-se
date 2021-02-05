---
title: Använda Azure AD i Azure Automation för autentisering till Azure
description: Den här artikeln beskriver hur du använder Azure AD inom Azure Automation som Provider för autentisering till Azure.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 336c0387ac9febcc517c2ce358d0b04c80d10678
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576811"
---
# <a name="use-azure-ad-to-authenticate-to-azure"></a>Använda Azure AD för att autentisera mot Azure

Tjänsten [Azure Active Directory (AD)](../active-directory/fundamentals/active-directory-whatis.md) möjliggör ett antal administrativa uppgifter, till exempel användar hantering, domän hantering och konfiguration av enkel inloggning. Den här artikeln beskriver hur du använder Azure AD inom Azure Automation som Provider för autentisering till Azure. 

## <a name="install-azure-ad-modules"></a>Installera Azure AD-moduler

Du kan aktivera Azure AD genom följande PowerShell-moduler:

* Azure Active Directory PowerShell för Graph (AzureRM-och AZ-moduler). Azure Automation levereras med AzureRM-modulen och den senaste uppgraderingen, AZ-modulen. Funktionerna omfattar icke-interaktiv autentisering till Azure med autentisering baserad på OrgId-autentisering (Azure AD User). Se [Azure AD-2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76).

* Microsoft Azure Active Directory för Windows PowerShell (MSOnline-modul). Den här modulen möjliggör interaktioner med Microsoft Online, inklusive Microsoft 365.

>[!NOTE]
>PowerShell-kärnan stöder inte modulen MSOnline. Om du vill använda cmdlets för moduler måste du köra dem från Windows PowerShell. Du uppmanas att använda den nyare Azure Active Directory PowerShell för graf-moduler i stället för MSOnline-modulen. 

### <a name="preinstallation"></a>Preinstallation

Innan du installerar Azure AD-modulerna på datorn:

* Avinstallera alla tidigare versioner av AzureRM/AZ-modulen och MSOnline-modulen. 

* Avinstallera Microsoft Online Services Sign-In-assistenten för att säkerställa att de nya PowerShell-modulerna fungerar korrekt.  

### <a name="install-the-azurerm-and-az-modules"></a>Installera AzureRM-och AZ-modulerna

>[!NOTE]
>Om du vill arbeta med dessa moduler måste du använda PowerShell version 5,1 eller senare med en 64-bitars version av Windows. 

1. Installera Windows Management Framework (WMF) 5,1. Se [Installera och konfigurera WMF 5,1](/powershell/scripting/wmf/setup/install-configure).

2. Installera AzureRM och/eller AZ med hjälp av instruktionerna i [installera Azure PowerShell på Windows med PowerShellGet](/powershell/azure/azurerm/install-azurerm-ps).

### <a name="install-the-msonline-module"></a>Installera MSOnline-modulen

>[!NOTE]
>Om du vill installera MSOnline-modulen måste du vara medlem i en administratörs roll. Se [om administratörs roller](/microsoft-365/admin/add-users/about-admin-roles).

1. Se till att funktionen Microsoft .NET Framework 3.5. x är aktive rad på datorn. Det är troligt att datorn har en senare version installerad, men bakåtkompatibilitet med äldre versioner av .NET Framework kan aktive ras eller inaktive ras. 

2. Installera 64-bitars versionen av [Microsoft Online Services-inloggnings assistenten](https://www.microsoft.com/Download/details.aspx?id=28177).

3. Kör Windows PowerShell som administratör för att skapa en upphöjd kommando tolk för Windows PowerShell.

4. Distribuera Azure Active Directory från [MSOnline 1,0](http://www.powershellgallery.com/packages/MSOnline/1.0).

5. Om du uppmanas att installera NuGet-providern skriver du Y och trycker på RETUR.

6. Om du uppmanas att installera modulen från [PSGallery](https://www.powershellgallery.com/), Skriv Y och tryck på RETUR.

### <a name="install-support-for-pscredential"></a>Installera stöd för PSCredential

Azure Automation använder klassen [PSCredential](/dotnet/api/system.management.automation.pscredential) för att representera en referens till gång. Skripten hämtar `PSCredential` objekt med hjälp av `Get-AutomationPSCredential` cmdleten. Mer information finns i avsnittet [om inloggnings till gångar i Azure Automation](shared-resources/credentials.md).

## <a name="assign-a-subscription-administrator"></a>Tilldela en prenumerationsadministratör

Du måste tilldela en administratör för Azure-prenumerationen. Den här personen har rollen ägare för prenumerations omfånget. Se [rollbaserad åtkomst kontroll i Azure Automation](automation-role-based-access-control.md). 

## <a name="change-the-azure-ad-users-password"></a>Ändra Azure AD-användarens lösen ord

Ändra Azure AD-användarens lösen ord:

1. Logga ut från Azure.

2. Be administratören logga in på Azure som den Azure AD-användare som precis har skapat, med hjälp av det fullständiga användar namnet (inklusive domänen) och ett tillfälligt lösen ord. 

3. Be administratören att ändra lösen ordet när du uppmanas till det.

## <a name="configure-azure-automation-to-manage-the-azure-subscription"></a>Konfigurera Azure Automation för att hantera Azure-prenumerationen

För att Azure Automation ska kunna kommunicera med Azure AD måste du hämta de autentiseringsuppgifter som är associerade med Azure-anslutningen till Azure AD. Exempel på dessa autentiseringsuppgifter är klient-ID, prenumerations-ID och liknande. Mer information om anslutningen mellan Azure och Azure AD finns i [Anslut din organisation till Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="create-a-credential-asset"></a>Skapa en inloggnings till gång

Med Azures autentiseringsuppgifter för Azure AD är det dags att skapa en Azure Automation Credential-till gång för att lagra autentiseringsuppgifter för Azure AD på ett säkert sätt så att Runbooks och aktuella tillstånds konfigurations skript (DSC) kan komma åt dem. Du kan göra detta med hjälp av antingen Azure Portal-eller PowerShell-cmdletar.

### <a name="create-the-credential-asset-in-azure-portal"></a>Skapa inloggnings till gången i Azure Portal

Du kan använda Azure Portal för att skapa inloggnings till gången. Utför den här åtgärden från ditt Automation-konto med hjälp av **autentiseringsuppgifter** under **delade resurser**. Se [inloggnings till gångar i Azure Automation](shared-resources/credentials.md).

### <a name="create-the-credential-asset-with-windows-powershell"></a>Skapa inloggnings till gången med Windows PowerShell

För att förbereda en ny inloggnings till gång i Windows PowerShell skapar skriptet först ett `PSCredential` objekt med det tilldelade användar namnet och lösen ordet. Skriptet använder sedan det här objektet för att skapa till gången via ett anrop till cmdleten [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential) . Alternativt kan skriptet anropa cmdleten [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) för att uppmana användaren att ange ett namn och lösen ord. Se [inloggnings till gångar i Azure Automation](shared-resources/credentials.md). 

## <a name="manage-azure-resources-from-an-azure-automation-runbook"></a>Hantera Azure-resurser från en Azure Automation Runbook

Du kan hantera Azure-resurser från Azure Automation-runbooks med hjälp av inloggnings till gången. Nedan visas ett exempel på en PowerShell-Runbook som samlar in den inloggnings till gång som ska användas för att stoppa och starta virtuella datorer i en Azure-prenumeration. Den här runbooken använder först `Get-AutomationPSCredential` för att hämta de autentiseringsuppgifter som ska användas för att autentisera till Azure. Sedan anropas cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) för att ansluta till Azure med hjälp av autentiseringsuppgiften. Skriptet använder cmdleten [Select-AzureSubscription](/powershell/module/servicemanagement/azure.service/select-azuresubscription) för att välja vilken prenumeration som ska användas. 

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

* Information om användning av autentiseringsuppgifter finns [i Hantera autentiseringsuppgifter i Azure Automation](shared-resources/credentials.md).
* Information om moduler finns [i hantera moduler i Azure Automation](shared-resources/modules.md).
* Om du behöver starta en Runbook kan du läsa [starta en Runbook i Azure Automation](start-runbooks.md).
* Information om PowerShell finns i [PowerShell-dokument](/powershell/scripting/overview).
