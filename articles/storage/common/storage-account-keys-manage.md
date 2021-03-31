---
title: Hantera kontots åtkomstnycklar
titleSuffix: Azure Storage
description: Lär dig hur du visar, hanterar och roterar åtkomst nycklar för lagrings kontot.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/24/2020
ms.author: tamram
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: e5ea94fea00771b64634d6c28a7879fabb195f09
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89069667"
---
# <a name="manage-storage-account-access-keys"></a>Hantera åtkomst nycklar för lagrings konton

När du skapar ett lagrings konto genererar Azure åtkomst nycklar för 2 512-bitars lagrings konto. Dessa nycklar kan användas för att ge åtkomst till data i ditt lagrings konto via autentisering med delad nyckel.

Microsoft rekommenderar att du använder Azure Key Vault för att hantera dina åtkomst nycklar och att du regelbundet roterar och återskapar dina nycklar. Med hjälp av Azure Key Vault är det enkelt att rotera dina nycklar utan avbrott i dina program. Du kan också rotera dina nycklar manuellt.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-account-access-keys"></a>Visa konto åtkomst nycklar

Du kan visa och kopiera åtkomst nycklar för ditt konto med Azure Portal, PowerShell eller Azure CLI. Azure Portal innehåller också en anslutnings sträng för ditt lagrings konto som du kan kopiera.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Visa och kopiera åtkomst nycklar för lagrings kontot eller anslutnings strängen från Azure Portal:

1. Navigera till ditt lagrings konto i [Azure Portal](https://portal.azure.com).
1. Gå till **Inställningar** och välj **Åtkomstnycklar**. Åtkomstnycklarna för kontot visas, samt den fullständiga anslutningssträngen för varje nyckel.
1. Leta upp **nyckelvärdet** under **KEY1** och klicka på knappen **Kopiera** för att kopiera konto nyckeln.
1. Alternativt kan du kopiera hela anslutnings strängen. Sök efter värdet för **Anslutningssträng** under **key1** och kopiera anslutningssträngen genom att klicka på **Kopiera**.

    :::image type="content" source="media/storage-account-keys-manage/portal-connection-string.png" alt-text="Skärm bild som visar hur du visar åtkomst nycklar i Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Anropa kommandot [Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey) för att hämta åtkomst nycklar för ditt konto med PowerShell.

I följande exempel hämtas den första nyckeln. Om du vill hämta den andra nyckeln använder du `Value[1]` i stället för `Value[0]` . Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName <resource-group> `
    -Name <storage-account>).Value[0]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill visa en lista med åtkomst nycklar för ditt konto med Azure CLI anropar du kommandot [AZ Storage konto Keys List](/cli/azure/storage/account/keys#az-storage-account-keys-list) , som du ser i följande exempel. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden. 

```azurecli-interactive
az storage account keys list \
  --resource-group <resource-group> \
  --account-name <storage-account>
```

---

Du kan använda någon av de två nycklarna för att komma åt Azure Storage, men i allmänhet är det en bra idé att använda den första nyckeln och reservera användningen av den andra nyckeln för när du roterar nycklar.

Om du vill visa eller läsa ett kontos åtkomst nycklar måste användaren antingen vara tjänst administratör eller ha tilldelats en Azure-roll som innehåller **Microsoft. Storage/storageAccounts/listnycklar/Action**. Vissa inbyggda Azure-roller som innehåller den här åtgärden är roll rollerna **ägare**, **deltagare** och **lagrings konto nyckel operatörer** . Mer information om tjänst administratörs rollen finns i [klassiska prenumerationer på administratörs roller, Azure-roller och Azure AD-roller](../../role-based-access-control/rbac-and-directory-admin-roles.md). Detaljerad information om inbyggda roller för Azure Storage finns i avsnittet **Storage** i [inbyggda Azure-roller för Azure RBAC](../../role-based-access-control/built-in-roles.md#storage).

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Använd Azure Key Vault för att hantera dina åtkomst nycklar

Microsoft rekommenderar att du använder Azure Key Vault för att hantera och rotera dina åtkomst nycklar. Ditt program kan komma åt dina nycklar i Key Vault på ett säkert sätt, så att du kan undvika att lagra dem med program koden. Mer information om hur du använder Key Vault för nyckel hantering finns i följande artiklar:

- [Hantera lagrings konto nycklar med Azure Key Vault och PowerShell](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [Hantera lagrings konto nycklar med Azure Key Vault och Azure CLI](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Rotera åtkomst nycklar manuellt

Microsoft rekommenderar att du roterar dina åtkomst nycklar regelbundet för att skydda ditt lagrings konto. Använd om möjligt Azure Key Vault för att hantera dina åtkomst nycklar. Om du inte använder Key Vault måste du rotera dina nycklar manuellt.

Två åtkomst nycklar tilldelas så att du kan rotera dina nycklar. Att ha två nycklar säkerställer att ditt program behåller åtkomsten till Azure Storage under hela processen.

> [!WARNING]
> Återskapande av åtkomst nycklar kan påverka alla program eller Azure-tjänster som är beroende av lagrings konto nyckeln. Alla klienter som använder konto nyckeln för att komma åt lagrings kontot måste uppdateras för att använda den nya nyckeln, inklusive Media Services, moln, skriv bords-och mobil program och grafiska användar gränssnitts program för Azure Storage, till exempel [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Så här roterar du åtkomst nycklar för lagrings kontot i Azure Portal:

1. Uppdatera anslutnings strängarna i program koden så att de refererar till den sekundära åtkomst nyckeln för lagrings kontot.
1. Navigera till ditt lagrings konto i [Azure Portal](https://portal.azure.com).
1. Gå till **Inställningar** och välj **Åtkomstnycklar**.
1. Om du vill återskapa den primära åtkomst nyckeln för ditt lagrings konto väljer du knappen **skapa** igen bredvid den primära åtkomst nyckeln.
1. Uppdatera anslutningssträngarna i koden så att de refererar till den nya primärnyckeln.
1. Återskapa den sekundära åtkomstnyckeln på samma sätt.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Så här roterar du dina åtkomst nycklar för lagrings konton med PowerShell:

1. Uppdatera anslutnings strängarna i program koden så att de refererar till den sekundära åtkomst nyckeln för lagrings kontot.
1. Anropa kommandot [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) för att återskapa den primära åtkomst nyckeln, som du ser i följande exempel:

    ```powershell
    New-AzStorageAccountKey -ResourceGroupName <resource-group> `
      -Name <storage-account> `
      -KeyName key1
    ```

1. Uppdatera anslutningssträngarna i koden så att de refererar till den nya primärnyckeln.
1. Återskapa den sekundära åtkomstnyckeln på samma sätt. Om du vill återskapa den sekundära nyckeln använder du `key2` som nyckel namn i stället för `key1` .

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Så här roterar du dina åtkomst nycklar för lagrings konton med Azure CLI:

1. Uppdatera anslutnings strängarna i program koden så att de refererar till den sekundära åtkomst nyckeln för lagrings kontot.
1. Anropa kommandot [AZ Storage Account Keys renew](/cli/azure/storage/account/keys#az-storage-account-keys-renew) för att återskapa den primära åtkomst nyckeln, som du ser i följande exempel:

    ```azurecli-interactive
    az storage account keys renew \
      --resource-group <resource-group> \
      --account-name <storage-account>
      --key primary
    ```

1. Uppdatera anslutningssträngarna i koden så att de refererar till den nya primärnyckeln.
1. Återskapa den sekundära åtkomstnyckeln på samma sätt. Om du vill återskapa den sekundära nyckeln använder du `key2` som nyckel namn i stället för `key1` .

---

> [!NOTE]
> Microsoft rekommenderar att du bara använder en av nycklarna i alla dina program på samma tidpunkt. Om du använder nyckel 1 på vissa platser och nyckel 2 i andra kommer du inte att kunna rotera dina nycklar utan att några program förlorar åtkomst.

Användaren måste antingen vara tjänst administratör eller ha tilldelats en Azure-roll som innehåller **Microsoft. Storage/storageAccounts/regeneratekey/åtgärd** för att kunna rotera ett kontos åtkomst nycklar. Vissa inbyggda Azure-roller som innehåller den här åtgärden är roll rollerna **ägare**, **deltagare** och **lagrings konto nyckel operatörer** . Mer information om tjänst administratörs rollen finns i [klassiska prenumerationer på administratörs roller, Azure-roller och Azure AD-roller](../../role-based-access-control/rbac-and-directory-admin-roles.md). Detaljerad information om inbyggda Azure-roller för Azure Storage finns i avsnittet **Storage** i [inbyggda Azure-roller för Azure RBAC](../../role-based-access-control/built-in-roles.md#storage).

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Storage-kontot](storage-account-overview.md)
- [Skapa ett lagringskonto](storage-account-create.md)
