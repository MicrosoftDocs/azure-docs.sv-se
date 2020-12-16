---
title: Tilldela en hanterad identitets åtkomst till en resurs med hjälp av PowerShell – Azure AD
description: Stegvisa instruktioner för att tilldela en hanterad identitet på en resurs, åtkomst till en annan resurs med hjälp av PowerShell.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf1a7a608df7a2b752d9a6bed52a4024fd776c5f
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592508"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Tilldela en hanterad identitets åtkomst till en resurs med hjälp av PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

När du har konfigurerat en Azure-resurs med en hanterad identitet kan du ge den hanterade identiteten åtkomst till en annan resurs, precis som alla säkerhets objekt. Det här exemplet visar hur du ger åtkomst till ett Azure Storage-konto med hjälp av PowerShell i en virtuell Azure-dators hanterade identitet.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [avsnittet Översikt](overview.md). **Se till att granska [skillnaden mellan en tilldelad och användardefinierad hanterad identitet](overview.md#managed-identity-types)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Om du vill köra exempel skripten har du två alternativ:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md)som du kan öppna med knappen **prova** på det övre högra hörnet av kodblock.
    - Kör skript lokalt genom att installera den senaste versionen av [Azure PowerShell](/powershell/azure/install-az-ps)och logga sedan in på Azure med hjälp av `Connect-AzAccount` . 

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Använd Azure RBAC för att tilldela en hanterad identitets åtkomst till en annan resurs

1. Aktivera hanterad identitet på en Azure-resurs, [till exempel en virtuell Azure-dator](qs-configure-powershell-windows-vm.md).

1. I det här exemplet ger vi en Azure VM-åtkomst till ett lagrings konto. Först använder vi [Get-AzVM](/powershell/module/az.compute/get-azvm) för att hämta tjänstens huvud namn för den virtuella datorn med namnet `myVM` , som skapades när vi aktiverade den hanterade identiteten. Använd sedan [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) för att ge VM- **läsaren** åtkomst till ett lagrings konto med namnet `myStorageAcct` :

    ```azurepowershell-interactive
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Nästa steg

- [Översikt över hanterade identiteter för Azure-resurser](overview.md)
- Om du vill aktivera hanterad identitet på en virtuell Azure-dator, se [Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med PowerShell](qs-configure-powershell-windows-vm.md).
