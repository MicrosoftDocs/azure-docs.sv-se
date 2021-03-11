---
title: Använd ett Azure Resource Manager lås till ett lagrings konto
titleSuffix: Azure Storage
description: Lär dig hur du använder ett Azure Resource Manager lås till ett lagrings konto.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 9d80c0b8d4d913322c47d1ad278d6dbc033d2409
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102620152"
---
# <a name="apply-an-azure-resource-manager-lock-to-a-storage-account"></a>Använd ett Azure Resource Manager lås till ett lagrings konto

Microsoft rekommenderar att du låser alla dina lagrings konton med ett Azure Resource Manager lås för att förhindra oavsiktlig eller skadlig borttagning av lagrings kontot. Det finns två typer av Azure Resource Manager resurs Lås:

- Ett **CannotDelete** -Lås hindrar användare från att ta bort ett lagrings konto, men tillåter läsning och ändring av konfigurationen.
- Ett **skrivskyddat** lås förhindrar användare från att ta bort ett lagrings konto eller ändra dess konfiguration, men tillåter att du läser konfigurationen.

Mer information om Azure Resource Manager lås finns i [låsa resurser för att förhindra ändringar](../../azure-resource-manager/management/lock-resources.md).

> [!IMPORTANT]
> Att låsa ett lagrings konto skyddar inte data i det kontot från att uppdateras eller tas bort.

## <a name="configure-an-azure-resource-manager-lock"></a>Konfigurera ett Azure Resource Manager lås

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du vill konfigurera ett lagrings konto med Azure Portal:

1. Navigera till ditt lagringskonto i Azure-portalen.
1. Under avsnittet **Inställningar** väljer du **Lås**.
1. Välj **Lägg till**.
1. Ange ett namn för resurs låset och ange typen av lås. Lägg till en anteckning om låset om du vill.

    :::image type="content" source="media/lock-account-resource/lock-storage-account.png" alt-text="Skärm bild som visar hur du låser ett lagrings konto med ett CannotDelete-lås":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Kontrol lera först att du har installerat [PowerShell-modulen AZ](https://www.powershellgallery.com/packages/Az)för att konfigurera ett lås på ett lagrings konto med PowerShell. Anropa sedan kommandot [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) och ange den typ av lås som du vill skapa, som du ser i följande exempel:

```azurepowershell
New-AzResourceLock -LockLevel CanNotDelete `
    -LockName <lock> `
    -ResourceName <storage-account> `
    -ResourceType Microsoft.Storage/storageAccounts `
    -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill konfigurera ett lås på ett lagrings konto med Azure CLI anropar du kommandot [AZ lock Create](/cli/azure/lock#az_lock_create) och anger den typ av lås som du vill skapa, som du ser i följande exempel:

```azurecli
az lock create \
    --name <lock> \
    --resource-group <resource-group> \
    --resource <storage-account> \
    --lock-type CanNotDelete \
    --resource-type Microsoft.Storage/storageAccounts
```

---

## <a name="authorizing-data-operations-when-a-readonly-lock-is-in-effect"></a>Auktorisera data åtgärder när ett skrivskyddat lås gäller

När ett **skrivskyddat** lås används för ett lagrings konto blockeras Åtgärden [lista nycklar](/rest/api/storagerp/storageaccounts/listkeys) för det lagrings kontot. Åtgärden **lista nycklar** är en https post-åtgärd och alla post åtgärder förhindras när ett **skrivskyddat** lås har kon figurer ATS för kontot. Åtgärden **lista nycklar** returnerar kontots åtkomst nycklar, som sedan kan användas för att läsa och skriva till alla data i lagrings kontot.

Om en klient har till gång till kontots åtkomst nycklar vid den tidpunkt då låset tillämpas på lagrings kontot, kan klienten fortsätta att använda nycklar för att få åtkomst till data. Klienter som inte har åtkomst till nycklarna måste dock använda Azure Active Directory (Azure AD) autentiseringsuppgifter för att få åtkomst till BLOB-eller Queue-data i lagrings kontot.

Användare av Azure Portal kan påverkas när ett **skrivskyddat** lås används, om de tidigare har använt BLOB-eller Queue-data i portalen med konto åtkomst nycklar. När låset har tillämpats måste Portal användarna använda Azure AD-autentiseringsuppgifter för att få åtkomst till BLOB-eller Queue-data i portalen. För att göra detta måste en användare ha minst två RBAC-roller tilldelade: rollen Azure Resource Manager läsaren minst och en av Azure Storage data åtkomst roller. Mer information finns i någon av följande artiklar:

- [Välj hur du vill ge åtkomst till BLOB-data i Azure Portal](../blobs/authorize-data-operations-portal.md)
- [Välj hur du vill ge åtkomst till köade data i Azure Portal](../queues/authorize-data-operations-portal.md)

Data i Azure Files eller Table service kan bli otillgängliga för klienter som tidigare har åtkomst till den med konto nycklarna. Det bästa är om du måste använda ett **skrivskyddat** lås till ett lagrings konto och sedan flytta Azure Files och table service arbets belastningar till ett lagrings konto som inte är låst med ett **skrivskyddat** lås.

## <a name="next-steps"></a>Nästa steg

[Lås resurser för att förhindra ändringar](../../azure-resource-manager/management/lock-resources.md)
