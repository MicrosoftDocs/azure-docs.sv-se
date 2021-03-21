---
title: Omfångs synkronisering med PowerShell för Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du använder Azure AD PowerShell för att konfigurera omfångst synkronisering från Azure AD till en Azure Active Directory Domain Services hanterad domän
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: justinha
ms.openlocfilehash: f877a631fd3c89d74b9e3b47cf205bbcf173ebc0
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102453418"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-azure-ad-powershell"></a>Konfigurera omfångs synkronisering från Azure AD till Azure Active Directory Domain Services med hjälp av Azure AD PowerShell

Azure Active Directory Domain Services (Azure AD DS) för att tillhandahålla Authentication Services synkroniserar användare och grupper från Azure AD. I en hybrid miljö kan användare och grupper från en lokal Active Directory Domain Services-miljö (AD DS) först synkroniseras till Azure AD med hjälp av Azure AD Connect och sedan synkroniseras till Azure AD DS.

Som standard synkroniseras alla användare och grupper från en Azure AD-katalog till en hanterad Azure AD DS-domän. Om du har speciella behov kan du istället välja att bara synkronisera en definierad uppsättning användare.

Den här artikeln visar hur du skapar en hanterad domän som använder omfångs synkronisering och sedan ändrar eller inaktiverar uppsättningen med begränsade användare med hjälp av Azure AD PowerShell. Du kan också [utföra dessa steg med hjälp av Azure Portal][scoped-sync].

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra den här artikeln behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
    * Om det behövs, slutför du själv studie kursen för att [skapa och konfigurera en Azure Active Directory Domain Services hanterad domän][tutorial-create-instance].
* Du behöver *Global administratörs* behörighet i Azure AD-klienten för att ändra synkroniserings omfånget för Azure AD DS.

## <a name="scoped-synchronization-overview"></a>Översikt över omfånget synkronisering

Som standard synkroniseras alla användare och grupper från en Azure AD-katalog till en hanterad domän. Om bara några få användare behöver åtkomst till den hanterade domänen kan du bara synkronisera dessa användar konton. Den här omfånget är gruppbaserad. När du konfigurerar gruppbaserad omsynkronisering synkroniseras bara de användar konton som tillhör de grupper som du anger till den hanterade domänen. Kapslade grupper är inte synkroniserade, bara de enskilda grupper som du väljer.

Du kan ändra omfånget för synkronisering före eller efter att du har skapat den hanterade domänen. Omfånget för synkronisering definieras av ett huvud namn för tjänsten med program identifieraren 2565bd9d-DA50-47d4-8b85-4c97f669dc36. Du kan förhindra omfattnings förlust genom att inte ta bort eller ändra tjänstens huvud namn. Om det tas bort av misstag går det inte att återställa omfånget för synkronisering. 

Tänk på följande varningar om du ändrar omfånget för synkronisering:

- En fullständig synkronisering sker.
- Objekt som inte längre behövs i den hanterade domänen tas bort. Nya objekt skapas i den hanterade domänen.

Läs mer om synkroniseringsprocessen i [förstå synkronisering i Azure AD Domain Services][concepts-sync].

## <a name="powershell-script-for-scoped-synchronization"></a>PowerShell-skript för omfångst synkronisering

Om du vill konfigurera omfångs synkronisering med PowerShell måste du först spara följande skript till en fil med namnet `Select-GroupsToSync.ps1` .

Det här skriptet konfigurerar Azure AD DS för att synkronisera valda grupper från Azure AD. Alla användar konton som ingår i de angivna grupperna synkroniseras med den hanterade domänen.

Det här skriptet används i de ytterligare stegen i den här artikeln.

```powershell
param (
    [Parameter(Position = 0)]
    [String[]]$groupsToAdd
)

Connect-AzureAD
$sp = Get-AzureADServicePrincipal -Filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
$role = $sp.AppRoles | where-object -FilterScript {$_.DisplayName -eq "User"}

Write-Output "`n****************************************************************************"

Write-Output "Total group-assignments need to be added: $($groupsToAdd.Count)"
$newGroupIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($groupName in $groupsToAdd)
{
    try
    {
        $group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"
        $newGroupIds.Add($group.ObjectId)

        Write-Output "Group-Name: $groupName, Id: $($group.ObjectId)"
    }
    catch
    {
        Write-Error "Failed to find group: $groupName. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

$currentAssignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId
Write-Output "Total current group-assignments: $($currentAssignments.Count), SP-ObjectId: $($sp.ObjectId)"

$currAssignedObjectIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($assignment in $currentAssignments)
{
    Write-Output "Assignment-ObjectId: $($assignment.PrincipalId)"

    if ($newGroupIds.Contains($assignment.PrincipalId) -eq $false)
    {
        Write-Output "This assignment is not needed anymore. Removing it! Assignment-ObjectId: $($assignment.PrincipalId)"
        Remove-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -AppRoleAssignmentId $assignment.ObjectId
    }
    else
    {
        $currAssignedObjectIds.Add($assignment.PrincipalId)
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

foreach ($id in $newGroupIds)
{
    try
    {
        if ($currAssignedObjectIds.Contains($id) -eq $false)
        {
            Write-Output "Adding new group-assignment. Role-Id: $($role.Id), Group-Object-Id: $id, ResourceId: $($sp.ObjectId)"
            New-AzureADGroupAppRoleAssignment -Id $role.Id -ObjectId $id -PrincipalId $id -ResourceId $sp.ObjectId
        }
        else
        {
            Write-Output "Group-ObjectId: $id is already assigned."
        }
    }
    catch
    {
        Write-Error "Exception occurred assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```

## <a name="enable-scoped-synchronization"></a>Aktivera omfångs synkronisering

Utför följande steg för att aktivera gruppbaserad omsynkronisering för en hanterad domän:

1. Först anger du *"filteredSync" = "Enabled"* på Azure AD DS-resursen och uppdaterar sedan den hanterade domänen.

    När du uppmanas anger du autentiseringsuppgifterna för en *Global administratör* för att logga in på din Azure AD-klient med hjälp av cmdleten [Connect-AzureAD][Connect-AzureAD] :

    ```powershell
    # Connect to your Azure AD tenant
    Connect-AzureAD

    # Retrieve the Azure AD DS resource.
    $DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

    # Enable group-based scoped synchronization.
    $enableScopedSync = @{"filteredSync" = "Enabled"}

    # Update the Azure AD DS resource
    Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $enableScopedSync
    ```

1. Ange nu listan över grupper vars användare ska synkroniseras till den hanterade domänen.

    Kör `Select-GroupsToSync.ps1` skriptet och ange listan över grupper som ska synkroniseras. I följande exempel är grupperna som ska synkroniseras *GroupName1* och *GroupName2*.

    > [!WARNING]
    > Du måste inkludera *Administratörs gruppen för AAD-domänkontrollanten* i listan över grupper för omfångs synkronisering. Om du inte tar med den här gruppen går det inte att använda den hanterade domänen.

    ```powershell
    .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
    ```

Om du ändrar omfånget för synkroniseringen synkroniserar den hanterade domänen om alla data. Objekt som inte längre behövs i den hanterade domänen tas bort och omsynkroniseringen kan ta lång tid att slutföra.

## <a name="modify-scoped-synchronization"></a>Ändra omfångs synkronisering

Om du vill ändra listan över grupper vars användare ska synkroniseras med den hanterade domänen kör du `Select-GroupsToSync.ps1` skript och anger den nya listan över grupper som ska synkroniseras.

I följande exempel innehåller grupperna för synkronisering inte längre *GroupName2* och innehåller nu *GroupName3*.

> [!WARNING]
> Du måste inkludera *Administratörs gruppen för AAD-domänkontrollanten* i listan över grupper för omfångs synkronisering. Om du inte tar med den här gruppen går det inte att använda den hanterade domänen.

När du uppmanas anger du autentiseringsuppgifterna för en *Global administratör* för att logga in på din Azure AD-klient med hjälp av cmdleten [Connect-AzureAD][Connect-AzureAD] :

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

Om du ändrar omfånget för synkroniseringen synkroniserar den hanterade domänen om alla data. Objekt som inte längre behövs i den hanterade domänen tas bort och omsynkroniseringen kan ta lång tid att slutföra.

## <a name="disable-scoped-synchronization"></a>Inaktivera omfångs synkronisering

Om du vill inaktivera gruppbaserad omsynkronisering för en hanterad domän anger du *"filteredSync" = "inaktive rad"* på Azure AD DS-resursen och uppdaterar sedan den hanterade domänen. När det är klart är alla användare och grupper inställda för synkronisering från Azure AD.

När du uppmanas anger du autentiseringsuppgifterna för en *Global administratör* för att logga in på din Azure AD-klient med hjälp av cmdleten [Connect-AzureAD][Connect-AzureAD] :

```powershell
# Connect to your Azure AD tenant
Connect-AzureAD

# Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

# Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

# Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

Om du ändrar omfånget för synkroniseringen synkroniserar den hanterade domänen om alla data. Objekt som inte längre behövs i den hanterade domänen tas bort och omsynkroniseringen kan ta lång tid att slutföra.

## <a name="next-steps"></a>Nästa steg

Läs mer om synkroniseringsprocessen i [förstå synkronisering i Azure AD Domain Services](synchronization.md).

<!-- INTERNAL LINKS -->
[scoped-sync]: scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md

<!-- EXTERNAL LINKS -->
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
