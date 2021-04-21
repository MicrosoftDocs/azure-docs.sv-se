---
title: Hantera kontots åtkomstnycklar
titleSuffix: Azure Storage
description: Lär dig hur du visar, hanterar och roterar åtkomstnycklarna för ditt lagringskonto.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/24/2020
ms.author: tamram
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 82d272f22295a5b68d1e8de3fb5a70c45d4c14a3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791219"
---
# <a name="manage-storage-account-access-keys"></a>Hantera åtkomstnycklar för lagringskonto

När du skapar ett lagringskonto genererar Azure två 512-bitars åtkomstnycklar för lagringskontot. Dessa nycklar kan användas för att ge åtkomst till data i ditt lagringskonto via auktorisering med delad nyckel.

Microsoft rekommenderar att du använder Azure Key Vault för att hantera dina åtkomstnycklar och att du regelbundet roterar och återskapar dina nycklar. Med Azure Key Vault det enkelt att rotera dina nycklar utan avbrott i dina program. Du kan också rotera dina nycklar manuellt.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-account-access-keys"></a>Visa åtkomstnycklar för konto

Du kan visa och kopiera åtkomstnycklarna för ditt konto med Azure Portal, PowerShell eller Azure CLI. I Azure Portal även en anslutningssträng för ditt lagringskonto som du kan kopiera.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Visa och kopiera lagringskontots åtkomstnycklar eller anslutningssträng från Azure Portal:

1. Gå till ditt lagringskonto i [Azure Portal](https://portal.azure.com).
1. Gå till **Inställningar** och välj **Åtkomstnycklar**. Åtkomstnycklarna för kontot visas, samt den fullständiga anslutningssträngen för varje nyckel.
1. Leta upp **nyckelvärdet** under **key1** och klicka på **knappen Kopiera** för att kopiera kontonyckeln.
1. Alternativt kan du kopiera hela anslutningssträngen. Sök efter värdet för **Anslutningssträng** under **key1** och kopiera anslutningssträngen genom att klicka på **Kopiera**.

    :::image type="content" source="media/storage-account-keys-manage/portal-connection-string.png" alt-text="Skärmbild som visar hur du visar åtkomstnycklar i Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill hämta åtkomstnycklarna för ditt konto med PowerShell anropar du kommandot [Get-AzStorageAccountKey.](/powershell/module/az.Storage/Get-azStorageAccountKey)

I följande exempel hämtas den första nyckeln. Om du vill hämta den andra nyckeln använder du `Value[1]` i stället för `Value[0]` . Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName <resource-group> `
    -Name <storage-account>).Value[0]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill visa åtkomstnycklarna för ditt konto med Azure CLI anropar du kommandot [az storage account keys list,](/cli/azure/storage/account/keys#az_storage_account_keys_list) som du ser i följande exempel. Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden. 

```azurecli-interactive
az storage account keys list \
  --resource-group <resource-group> \
  --account-name <storage-account>
```

---

Du kan använda någon av de två nycklarna för att komma åt Azure Storage, men i allmänhet är det en bra idé att använda den första nyckeln och reservera användningen av den andra nyckeln när du roterar nycklar.

Om du vill visa eller läsa ett kontos åtkomstnycklar måste användaren antingen vara tjänstadministratör eller ha tilldelats en Azure-roll som innehåller **Microsoft.Storage/storageAccounts/listkeys/action**. Vissa inbyggda Azure-roller som omfattar den här åtgärden är rollerna **Ägare,** **Deltagare** och **Lagringskontonyckeloperatör.** Mer information om tjänstadministratörsrollen finns i [Administratörsroller för klassiska prenumerationer, Azure-roller och Azure AD-roller.](../../role-based-access-control/rbac-and-directory-admin-roles.md) Detaljerad information om inbyggda roller för Azure Storage finns i avsnittet **Lagring** i [azure-inbyggda roller för Azure RBAC.](../../role-based-access-control/built-in-roles.md#storage)

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Använda Azure Key Vault för att hantera dina åtkomstnycklar

Microsoft rekommenderar att du använder Azure Key Vault för att hantera och rotera dina åtkomstnycklar. Programmet kan komma åt dina nycklar på ett säkert Key Vault, så att du kan undvika att lagra dem med din programkod. Mer information om hur Key Vault för nyckelhantering finns i följande artiklar:

- [Hantera lagringskontonycklar med Azure Key Vault och PowerShell](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [Hantera lagringskontonycklar med Azure Key Vault och Azure CLI](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Rotera åtkomstnycklar manuellt

Microsoft rekommenderar att du roterar dina åtkomstnycklar regelbundet för att skydda ditt lagringskonto. Om möjligt kan du använda Azure Key Vault för att hantera dina åtkomstnycklar. Om du inte använder Key Vault måste du rotera nycklarna manuellt.

Två åtkomstnycklar tilldelas så att du kan rotera dina nycklar. Med två nycklar säkerställer du att ditt program har Azure Storage åtkomst under hela processen.

> [!WARNING]
> Att återskapa dina åtkomstnycklar kan påverka alla program eller Azure-tjänster som är beroende av lagringskontonyckeln. Klienter som använder kontonyckeln för att få åtkomst till lagringskontot måste uppdateras så att de använder den nya nyckeln, inklusive medietjänster, moln-, skrivbords- och mobilprogram och grafiska användargränssnittsprogram för Azure Storage, till exempel [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Så här roterar du åtkomstnycklarna för lagringskontot i Azure Portal:

1. Uppdatera anslutningssträngarna i programkoden så att de refererar till lagringskontots sekundära åtkomstnyckel.
1. Gå till ditt lagringskonto i [Azure Portal](https://portal.azure.com).
1. Gå till **Inställningar** och välj **Åtkomstnycklar**.
1. Om du vill återskapa den primära åtkomstnyckeln för ditt lagringskonto väljer **du knappen Återskapa** bredvid den primära åtkomstnyckeln.
1. Uppdatera anslutningssträngarna i koden så att de refererar till den nya primärnyckeln.
1. Återskapa den sekundära åtkomstnyckeln på samma sätt.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Så här roterar du åtkomstnycklar för lagringskontot med PowerShell:

1. Uppdatera anslutningssträngarna i programkoden så att de refererar till lagringskontots sekundära åtkomstnyckel.
1. Anropa kommandot [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) för att återskapa den primära åtkomstnyckeln, som du ser i följande exempel:

    ```powershell
    New-AzStorageAccountKey -ResourceGroupName <resource-group> `
      -Name <storage-account> `
      -KeyName key1
    ```

1. Uppdatera anslutningssträngarna i koden så att de refererar till den nya primärnyckeln.
1. Återskapa den sekundära åtkomstnyckeln på samma sätt. Om du vill återskapa den sekundära nyckeln använder `key2` du som nyckelnamn i stället för `key1` .

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Så här roterar du åtkomstnycklar för lagringskontot med Azure CLI:

1. Uppdatera anslutningssträngarna i programkoden så att de refererar till lagringskontots sekundära åtkomstnyckel.
1. Anropa kommandot [az storage account keys renew](/cli/azure/storage/account/keys#az_storage_account_keys_renew) för att återskapa den primära åtkomstnyckeln, som du ser i följande exempel:

    ```azurecli-interactive
    az storage account keys renew \
      --resource-group <resource-group> \
      --account-name <storage-account>
      --key primary
    ```

1. Uppdatera anslutningssträngarna i koden så att de refererar till den nya primärnyckeln.
1. Återskapa den sekundära åtkomstnyckeln på samma sätt. Om du vill återskapa den sekundära nyckeln använder `key2` du som nyckelnamn i stället för `key1` .

---

> [!NOTE]
> Microsoft rekommenderar att du bara använder en av nycklarna i alla dina program på samma gång. Om du använder nyckel 1 på vissa platser och nyckel 2 i andra kommer du inte att kunna rotera dina nycklar utan att vissa program förlorar åtkomst.

Om du vill rotera ett kontos åtkomstnycklar måste användaren antingen vara tjänstadministratör eller tilldelas en Azure-roll som innehåller **Microsoft.Storage/storageAccounts/regeneratekey/action**. Vissa inbyggda Azure-roller som omfattar den här åtgärden är rollerna **Ägare,** **Deltagare** och **Tjänstroll för lagringskontonyckeloperatör.** Mer information om tjänstadministratörsrollen finns i [Administratörsroller för klassiska prenumerationer, Azure-roller och Azure AD-roller.](../../role-based-access-control/rbac-and-directory-admin-roles.md) Detaljerad information om inbyggda Azure-roller för Azure Storage finns i avsnittet **Lagring** i inbyggda [Azure-roller för Azure RBAC.](../../role-based-access-control/built-in-roles.md#storage)

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Storage-konto](storage-account-overview.md)
- [Skapa ett lagringskonto](storage-account-create.md)
