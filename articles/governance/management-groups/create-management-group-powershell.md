---
title: 'Snabbstart: Skapa en hanteringsgrupp med Azure PowerShell'
description: I den här snabbstarten använder du Azure PowerShell för att skapa en hanteringsgrupp för att organisera dina resurser i en resurshierarki.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 0291bb2bfb439ad09531066f6bad4e20a3f4c6bd
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535973"
---
# <a name="quickstart-create-a-management-group-with-azure-powershell"></a>Snabbstart: Skapa en hanteringsgrupp med Azure PowerShell

Hanteringsgrupper är containrar som hjälper dig att hantera åtkomst, policyer och efterlevnad i flera prenumerationer. Skapa dessa containrar för att skapa en effektiv hierarki som kan användas [med Azure Policy](../policy/overview.md) och [Rollbaserade åtkomstkontroller i Azure.](../../role-based-access-control/overview.md) Mer information om hanteringsgrupper finns i [Organisera dina resurser med Azure-hanteringsgrupper.](overview.md)

Den första hanteringsgruppen som skapats i katalogen kan ta upp till 15 minuter att slutföra. Det finns processer som körs första gången för att konfigurera tjänsten för hanteringsgrupper i Azure för din katalog. Du får ett meddelande när processen är klar. Mer information finns i inledande [konfiguration av hanteringsgrupper.](./overview.md#initial-setup-of-management-groups)

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

- Innan du börjar bör du kontrollera att den senaste versionen av Azure PowerShell har installerats. Detaljerad information finns i [Installera Azure PowerShell-modulen](/powershell/azure/install-az-ps).

- Alla Azure AD-användare i klientorganisationen kan skapa en hanteringsgrupp utan den skrivbehörighet för hanteringsgruppen som tilldelats användaren om [hierarkiskydd](./how-to/protect-resource-hierarchy.md#setting---require-authorization) inte är aktiverat. Den här nya hanteringsgruppen blir underordnad rothanteringsgruppen eller standardhanteringsgruppen och skaparen får rolltilldelningen "Ägare". [](./how-to/protect-resource-hierarchy.md#setting---default-management-group) Med hanteringsgrupptjänsten kan du göra det så att rolltilldelningar inte behövs på rotnivå. Inga användare har åtkomst till rothanteringsgruppen när den skapas. För att undvika att hitta de globala Azure AD-administratörerna för att börja använda hanteringsgrupper kan vi skapa de första hanteringsgrupperna på rotnivå.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-azure-powershell"></a>Skapa i Azure PowerShell

För PowerShell använder du cmdleten [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) för att skapa en ny hanteringsgrupp. I det här exemplet är **hanteringsgruppen GroupName** _Contoso_.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName är** en unik identifierare som skapas. Det här ID:t används av andra kommandon för att referera till den här gruppen och det kan inte ändras senare.

Om du vill att hanteringsgruppen ska visa ett annat namn i Azure Portal lägger du till **parametern DisplayName.** Om du till exempel vill skapa en hanteringsgrupp med GroupName för Contoso och visningsnamnet "Contoso Group" använder du följande cmdlet:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

I föregående exempel skapas den nya hanteringsgruppen under rothanteringsgruppen. Om du vill ange en annan hanteringsgrupp som överordnad använder du **parametern ParentId.**

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort hanteringsgruppen som skapades ovan använder du cmdleten [Remove-AzManagementGroup:](/powershell/module/az.resources/remove-azmanagementgroup)

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en hanteringsgrupp för att organisera resurshierarkin. Hanteringsgruppen kan innehålla prenumerationer eller andra hanteringsgrupper.

Om du vill veta mer om hanteringsgrupper och hur du hanterar din resurshierarki fortsätter du till:

> [!div class="nextstepaction"]
> [Hantera dina resurser med hanteringsgrupper](./manage.md)
