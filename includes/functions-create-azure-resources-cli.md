---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 99ae35aca485ac928f7c5ef9f98295eed4bc1245
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500139"
---
## <a name="create-supporting-azure-resources-for-your-function"></a>Skapa stöd för Azure-resurser för din funktion

Innan du kan distribuera funktions koden till Azure måste du skapa tre resurser:

- En [resurs grupp](../articles/azure-resource-manager/management/overview.md), som är en logisk behållare för relaterade resurser.
- Ett [lagrings konto](../articles/storage/common/storage-account-create.md)som används för att underhålla tillstånd och annan information om dina funktioner.
- En Function-app som tillhandahåller miljön för att köra funktions koden. En Function-app mappar till ditt lokala Function-projekt och gör att du kan gruppera funktioner som en logisk enhet för enklare hantering, distribution och delning av resurser.

Använd följande kommandon för att skapa dessa objekt. Både Azure CLI och PowerShell stöds.

1. Om du inte redan har gjort det loggar du in på Azure:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Kommandot [AZ login](/cli/azure/reference-index#az_login) loggar in dig på ditt Azure-konto.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    Cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) loggar in dig på ditt Azure-konto.

    ---

1. Skapa en resurs grupp med namnet `AzureFunctionsQuickstart-rg` i `westeurope` regionen:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Kommandot [AZ Group Create](/cli/azure/group#az_group_create) skapar en resurs grupp. Du skapar vanligt vis resurs gruppen och resurserna i en region nära dig, med hjälp av en tillgänglig region som returneras från `az account list-locations` kommandot.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    Kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) skapar en resurs grupp. Du skapar vanligt vis resurs gruppen och resurserna i en region nära dig, med hjälp av en tillgänglig region som returneras från cmdleten [Get-AzLocation](/powershell/module/az.resources/get-azlocation) .

    ---

1. Skapa ett allmänt lagrings konto i din resurs grupp och region:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Kommandot [AZ Storage Account Create](/cli/azure/storage/account#az_storage_account_create) skapar lagrings kontot. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    Cmdlet: en [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) skapar lagrings kontot.

    ---

    I föregående exempel ersätter `<STORAGE_NAME>` du med ett namn som passar dig och är unikt i Azure Storage. Namn måste innehålla mellan 3 och 24 tecken och endast små bokstäver. `Standard_LRS` anger ett allmänt-syfte-konto, som [stöds av Functions](../articles/azure-functions/storage-considerations.md#storage-account-requirements).
