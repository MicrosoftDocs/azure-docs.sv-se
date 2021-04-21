---
title: Tilldela en hanterad identitet åtkomst till en resurs med hjälp av PowerShell – Azure AD
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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fc5df641f27f8d604f7b5647736128856a3ecd51
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764377"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Tilldela en hanterad identitet åtkomst till en resurs med hjälp av PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

När du har konfigurerat en Azure-resurs med en hanterad identitet kan du ge den hanterade identiteten åtkomst till en annan resurs, precis som alla säkerhetsobjekt. Det här exemplet visar hur du ger en virtuell Azure-dators hanterade identitet åtkomst till ett Azure Storage-konto med hjälp av PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

- Om du inte är bekant med hanterade identiteter för Azure-resurser kan du läsa [översiktsavsnittet](overview.md). **Se till att granska skillnaden [mellan en system-tilldelad och användar tilldelad hanterad identitet.](overview.md#managed-identity-types)**
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Om du vill köra exempelskripten har du två alternativ:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md), som du kan öppna med hjälp av **knappen Prova** i det övre högra hörnet av kodblock.
    - Kör skript lokalt genom att installera den senaste versionen av [Azure PowerShell](/powershell/azure/install-az-ps)och logga sedan in på Azure med `Connect-AzAccount` . 

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Använda Azure RBAC för att tilldela en hanterad identitet åtkomst till en annan resurs

1. Aktivera hanterad identitet på en Azure-resurs, [till exempel en virtuell Azure-dator.](qs-configure-powershell-windows-vm.md)

1. I det här exemplet ger vi en virtuell Azure-dator åtkomst till ett lagringskonto. Först använder vi [Get-AzVM för att](/powershell/module/az.compute/get-azvm) hämta tjänstens huvudnamn för den virtuella datorn med namnet , som `myVM` skapades när vi aktiverade hanterad identitet. Använd sedan [New-AzRoleAssignment för att](/powershell/module/Az.Resources/New-AzRoleAssignment) ge VM-läsaren **åtkomst** till ett lagringskonto med namnet `myStorageAcct` :

    ```azurepowershell-interactive
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Nästa steg

- [Översikt över hanterad identitet för Azure-resurser](overview.md)
- Information om hur du aktiverar hanterade identiteter på en virtuell Azure-dator finns i Konfigurera hanterade [identiteter för Azure-resurser på en virtuell Azure-dator med Hjälp av PowerShell.](qs-configure-powershell-windows-vm.md)
