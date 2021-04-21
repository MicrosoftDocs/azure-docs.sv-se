---
title: Tilldela en hanterad identitet åtkomst till en resurs med hjälp av Azure CLI – Azure AD
description: Stegvisa instruktioner för att tilldela en hanterad identitet på en resurs, åtkomst till en annan resurs med hjälp av Azure CLI.
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
ms.date: 01/29/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1e1fa22cc36df00b098274002b6bd444be4140ff
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783295"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Tilldela en hanterad identitet åtkomst till en resurs med hjälp av Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

När du har konfigurerat en Azure-resurs med en hanterad identitet kan du ge den hanterade identiteten åtkomst till en annan resurs, precis som alla säkerhetsobjekt. Det här exemplet visar hur du ger en hanterad identitetsåtkomst för en virtuell Azure-dator eller VM-skalningsuppsättning till ett Azure Storage-konto med hjälp av Azure CLI.

Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="prerequisites"></a>Förutsättningar

- Om du inte är bekant med hanterade identiteter för Azure-resurser kan du se [Vad är hanterade identiteter för Azure-resurser?](overview.md). Mer information om system-tilldelade och användar tilldelade hanterade identitetstyper finns i [Hanterade identitetstyper.](overview.md#managed-identity-types)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Använda Azure RBAC för att tilldela en hanterad identitet åtkomst till en annan resurs

När du har aktiverat hanterad identitet på en Azure-resurs, till exempel en virtuell [Azure-dator eller](qs-configure-cli-windows-vm.md) [en VM-skalningsuppsättning i Azure:](qs-configure-cli-windows-vmss.md) 

1. I det här exemplet ger vi en virtuell Azure-dator åtkomst till ett lagringskonto. Först använder vi [az resource list för](/cli/azure/resource/#az_resource_list) att hämta tjänstens huvudnamn för den virtuella datorn med namnet myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   För en VM-skalningsuppsättning i Azure är kommandot detsamma förutom här, du får tjänstens huvudnamn för VM-skalningsuppsättningen med namnet "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

1. När du har ID:t för tjänstens huvudnamn använder du [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) för att ge den virtuella datorn eller VM-skalningsuppsättningen "Läsar"-åtkomst till ett lagringskonto med namnet "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Nästa steg

- [Översikt över hanterade identiteter för Azure-resurser](overview.md)
- Information om hur du aktiverar hanterade identiteter på en virtuell Azure-dator finns i Konfigurera hanterade identiteter för [Azure-resurser på en virtuell Azure-dator med hjälp av Azure CLI.](qs-configure-cli-windows-vm.md)
- Information om hur du aktiverar hanterade identiteter på en VM-skalningsuppsättning i Azure finns i Konfigurera hanterade identiteter för Azure-resurser på en [VM-skalningsuppsättning med Azure CLI.](qs-configure-cli-windows-vmss.md)
