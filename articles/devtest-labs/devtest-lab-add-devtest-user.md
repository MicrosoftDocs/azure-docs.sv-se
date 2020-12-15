---
title: Lägg till ägare och användare i Azure DevTest Labs | Microsoft Docs
description: Lägg till ägare och användare i Azure DevTest Labs med antingen Azure Portal eller PowerShell
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2ca6a1ffa66ab294e34a1b4866953a393aba4d6d
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511985"
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Lägg till ägare och användare i Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

Åtkomst i Azure DevTest Labs styrs av [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md). Med hjälp av Azure RBAC kan du åtskilja uppgifter i ditt team i *roller* där du endast beviljas den mängd åtkomst som krävs för att användarna ska kunna utföra sina jobb. Tre av dessa Azure-roller är *ägare*, *DevTest Labs-användare* och *deltagare*. I den här artikeln får du lära dig vilka åtgärder som kan utföras i de tre viktigaste Azure-rollerna. Därifrån får du lära dig hur du lägger till användare i ett labb – både via portalen och via ett PowerShell-skript och hur du lägger till användare på prenumerations nivå.

## <a name="actions-that-can-be-performed-in-each-role"></a>Åtgärder som kan utföras i varje roll
Det finns tre huvud roller som du kan tilldela en användare:

* Ägare
* DevTest Labs-användare
* Deltagare

Följande tabell visar de åtgärder som kan utföras av användare i var och en av följande roller:

| **Åtgärder som användare med den här rollen kan utföra** | **DevTest Labs-användare** | **Ägare** | **Deltagare** |
| --- | --- | --- | --- |
| **Labb uppgifter** | | | |
| Lägga till användare i ett labb |Nej |Ja |Nej |
| Uppdatera kostnads inställningar |Nej |Ja |Ja |
| **Bas uppgifter för virtuella datorer** | | | |
| Lägga till och ta bort anpassade avbildningar |Nej |Ja |Ja |
| Lägga till, uppdatera och ta bort formler |Ja |Ja |Ja |
| Aktivera Marketplace-avbildningar |Nej |Ja |Ja |
| **VM-aktiviteter** | | | |
| Skapa VM:ar |Ja |Ja |Ja |
| Starta, stoppa och ta bort virtuella datorer |Endast virtuella datorer som har skapats av användaren |Ja |Ja |
| Uppdatera VM-principer |Nej |Ja |Ja |
| Lägga till/ta bort data diskar till och från virtuella datorer |Endast virtuella datorer som har skapats av användaren |Ja |Ja |
| **Artefakt uppgifter** | | | |
| Lägga till och ta bort artefakt databaser |Nej |Ja |Ja |
| Tillämpa artefakter |Ja |Ja |Ja |

> [!NOTE]
> När en användare skapar en virtuell dator tilldelas den användaren **automatiskt rollen som den skapade** virtuella datorn.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>Lägg till en ägare eller användare på labb nivån
Ägare och användare kan läggas till på labb nivån via Azure Portal. En användare kan vara en extern användare med en giltig [Microsoft-konto (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account).
Följande steg vägleder dig genom processen att lägga till en ägare eller användare till ett labb i Azure DevTest Labs:

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **alla tjänster** och välj sedan **DevTest Labs** i listan.
3. I listan med labb väljer du önskat labb.
4. På labb bladet väljer du **konfiguration och principer**. 
5. På sidan **konfiguration och principer** väljer du **åtkomst kontroll (IAM)** på menyn till vänster. 
6. Välj **Lägg till roll tilldelning** i verktygsfältet för att lägga till en användare till en roll.
1. I fönstret **Lägg till behörigheter** utför du följande åtgärder: 
    1. Välj en roll (till exempel: DevTest Labs User). De avsnitts [åtgärder som kan utföras i varje roll](#actions-that-can-be-performed-in-each-role) visar de olika åtgärder som kan utföras av användare i rollerna ägare, DevTest användare och deltagare.
    2. Välj den användare som ska läggas till i rollen. 
    3. Välj **Spara**. 
11. När du återgår till bladet **användare** har användaren lagts till.  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Lägga till en extern användare i ett labb med hjälp av PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Förutom att lägga till användare i Azure Portal kan du lägga till en extern användare i labbet med hjälp av ett PowerShell-skript. I följande exempel ändrar du parameter värden under **värdena för att ändra** kommentaren.
Du kan hämta `subscriptionId` värdena, `labResourceGroup` och `labName` från labb bladet i Azure Portal.

> [!NOTE]
> Exempel skriptet förutsätter att den angivna användaren har lagts till som gäst i Active Directory och kommer att Miss läge om detta inte är fallet. Om du vill lägga till en användare som inte finns i Active Directory till ett labb använder du Azure Portal för att tilldela användaren till en roll som illustreras i avsnittet, [Lägg till en ägare eller användare på labb nivån](#add-an-owner-or-user-at-the-lab-level).   
> 
> 

```azurepowershell
# Add an external user in DevTest Labs user role to a lab
# Ensure that guest users can be added to the Azure Active directory:
# https://azure.microsoft.com/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

# Values to change
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource name here>"
$labName = "<Enter lab name here>"
$userDisplayName = "<Enter user's display name here>"

# Log into your Azure account
Connect-AzAccount

# Select the Azure subscription that contains the lab. 
# This step is optional if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Retrieve the user object
$adObject = Get-AzADUser -SearchString $userDisplayName

# Create the role assignment. 
$labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
New-AzRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId
```

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Lägg till en ägare eller användare på prenumerations nivå
Azure-behörigheter sprids från det överordnade omfånget till det underordnade omfånget i Azure. Ägare av en Azure-prenumeration som innehåller labb är därför automatiskt ägare av dessa labb. De äger också de virtuella datorerna och andra resurser som skapats av Labbets användare och tjänsten Azure DevTest Labs. 

Du kan lägga till ytterligare ägare till ett labb via Labbets blad i [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040). Den tillagda ägarens omfång för administration är dock mer snävare än prenumerationens ägares omfång. Till exempel har de tillagda ägarna inte fullständig åtkomst till några av resurserna som skapas i prenumerationen av DevTest Labs-tjänsten. 

Följ dessa steg om du vill lägga till en ägare till en Azure-prenumeration:

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **alla tjänster** och välj sedan **prenumerationer** i listan.
3. Välj önskad prenumeration.
4. Välj **åtkomst** ikon. 
   
    ![Åtkomst till användare](./media/devtest-lab-add-devtest-user/access-users.png)
5. Välj **Lägg till** på bladet **användare** .
   
    ![Lägg till användare](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. På bladet **Välj en roll** väljer du **ägare**.
7. På bladet **Lägg till användare** anger du e-postadressen eller namnet på den användare som du vill lägga till som ägare. Om det inte går att hitta användaren får du ett fel meddelande som förklarar problemet. Om användaren hittas visas den användaren under text rutan **användare** .
8. Välj det hittade användar namnet.
9. Välj **Välj**.
10. Välj **OK** för att stänga bladet **Lägg till åtkomst** .
11. När du återgår till bladet **användare** har användaren lagts till som ägare. Den här användaren är nu ägare till alla labb som skapats under den här prenumerationen och kan därmed utföra ägar uppgifter. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
