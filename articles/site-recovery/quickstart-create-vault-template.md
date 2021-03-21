---
title: Snabb start för att skapa ett Azure Recovery Services-valv med en Azure Resource Manager-mall.
description: I den här snabb starten får du lära dig hur du skapar ett Azure Recovery Services-valv med en Azure Resource Manager-mall (ARM-mall).
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/29/2020
ms.openlocfilehash: 62bfe71c397367282c696eecdf55ab9c900c69a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92745886"
---
# <a name="quickstart-create-a-recovery-services-vault-using-an-arm-template"></a>Snabb start: skapa ett Recovery Services valv med en ARM-mall

I den här snabb starten beskrivs hur du konfigurerar ett Recovery Services valv med hjälp av en Azure Resource Manager-mall (ARM-mall). [Azure Site Recoverys](site-recovery-overview.md) tjänsten bidrar till din strategi för affärs kontinuitet och haveri beredskap (BCDR) så att dina affärs program hålls online under planerade och oplanerade drifts avbrott. Site Recovery hanterar haveri beredskap för lokala datorer och virtuella Azure-datorer (VM), inklusive replikering, redundans och återställning.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en aktiv Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-recovery-services-vault-create/).

:::code language="json" source="~/quickstart-templates/101-recovery-services-vault-create/azuredeploy.json":::

Två Azure-resurser definieras i mallen:

- [Microsoft. RecoveryServices-valv](/azure/templates/microsoft.recoveryservices/vaults): skapar valvet.
- [Microsoft. RecoveryServices/valv/backupstorageconfig](/rest/api/backup/backupresourcestorageconfigs): konfigurerar valvets inställningar för redundans för säkerhets kopiering.

Mallen innehåller valfria parametrar för valvets säkerhets kopierings konfiguration. Inställningarna för redundans är lokalt redundant lagring (LRS) eller Geo-redundant lagring (GRS). Mer information finns i [set Storage redundans](../backup/backup-create-rs-vault.md#set-storage-redundancy).

Mer information om Azure Recovery Services-mallar finns i [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Recoveryservices&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuera mallen

För att distribuera mallen krävs **prenumeration**, **resurs grupp** och **valv namn** .

1. Om du vill logga in på Azure och öppna mallen väljer du avbildningen **distribuera till Azure** .

   [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

1. Välj eller ange följande värden:

   :::image type="content" source="media/quickstart-create-vault-template/create-vault-template.png" alt-text="Mall för att skapa ett Recovery Services-valv.":::

   - **Prenumeration**: Välj din Azure-prenumeration.
   - **Resurs grupp**: Välj en befintlig grupp eller Välj **Skapa ny** för att lägga till en grupp.
   - **Plats**: standardvärdet för resurs gruppens plats och blir otillgänglig när en resurs grupp har valts.
   - **Valv namn**: Ange ett namn för valvet.
   - **Ändra lagrings typ**: standardvärdet är **false**. Välj endast **True** om du behöver ändra valvets lagrings typ.
   - **Valv lagrings typ**: standardvärdet är **GloballyRedundant**. Om lagrings typen har angetts till **True** väljer du **LocallyRedundant**.
   - **Plats**: funktionen `[resourceGroup().location]` används som standard för resurs gruppens plats. Ange ett värde som **väst** för att ändra plats.
   - Markera kryss rutan **Jag godkänner villkoren som anges ovan**.

1. Starta valv distributionen genom att välja knappen **köp** . När distributionen har slutförts visas ett meddelande.

   :::image type="content" source="media/quickstart-create-vault-template/deployment-success.png" alt-text="Valv distributionen lyckades.":::

## <a name="validate-the-deployment"></a>Verifiera distributionen

För att bekräfta att valvet har skapats använder du Azure CLI eller Azure PowerShell.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the vault name:" &&
read vaultName &&
az backup vault show --name $vaultName --resource-group $resourceGroupName &&
az backup vault backup-properties show --name $vaultName --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
$vaultName = Read-Host -Prompt "Enter the vault name"
$vaultBackupConfig = Get-AzRecoveryServicesVault -Name $vaultName
Get-AzRecoveryServicesVault -ResourceGroupName $resouceGroupName -Name $vaultName
Get-AzRecoveryServicesBackupProperty -Vault $vaultBackupConfig
Write-Host "Press [ENTER] to continue..."
```

---

Följande utdata är ett utdrag ur valvets information:

# <a name="cli"></a>[CLI](#tab/CLI)

```Output
"id": "/subscriptions/<Subscription Id>/resourceGroups/myResourceGroup
         /providers/Microsoft.RecoveryServices/vaults/myVault"
"location": "eastus"
"name": "myVault"
"resourceGroup": "myResourceGroup"

"storageModelType": "GeoRedundant"
"storageType": "GeoRedundant"
"type": "Microsoft.RecoveryServices/vaults/backupstorageconfig"
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : myVault
Type              : Microsoft.RecoveryServices/vaults
Location          : eastus
ResourceGroupName : myResourceGroup
SubscriptionId    : <Subscription Id>

BackupStorageRedundancy
-----------------------
GeoRedundant
```

---

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att använda de nya resurserna behövs ingen åtgärd. Annars kan du ta bort resurs gruppen och valvet som skapades i den här snabb starten. Om du vill ta bort resurs gruppen och dess resurser använder du Azure CLI eller Azure PowerShell.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resouceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ett Recovery Services-valv. Om du vill veta mer om haveri beredskap fortsätter du till nästa snabb starts artikel.

> [!div class="nextstepaction"]
> [Konfigurera haveriberedskap](azure-to-azure-quickstart.md)
