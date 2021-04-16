---
title: Snabbstart för att skapa ett Azure Recovery Services-valv med Azure Resource Manager mall.
description: I den här snabbstarten får du lära dig hur du skapar ett Azure Recovery Services-valv med en Azure Resource Manager mall (ARM-mall).
ms.date: 04/29/2020
ms.topic: quickstart
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 11a88b5485ad970802a65af31daccdb30a1c86df
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533256"
---
# <a name="quickstart-create-a-recovery-services-vault-using-an-arm-template"></a>Snabbstart: Skapa ett Recovery Services-valv med hjälp av en ARM-mall

Den här snabbstarten beskriver hur du ställer in ett Recovery Services-valv med hjälp Azure Resource Manager en mall (ARM-mall). Tjänsten [Azure Site Recovery](site-recovery-overview.md) bidrar till din BCDR-strategi för affärskontinui och haveriberedskap så att dina affärsprogram förblir online under planerade och oplanerade avbrott. Site Recovery hanterar haveriberedskap för lokala datorer och virtuella Azure-datorer (VM), inklusive replikering, redundans och återställning.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en aktiv Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-recovery-services-vault-create/).

:::code language="json" source="~/quickstart-templates/101-recovery-services-vault-create/azuredeploy.json":::

Två Azure-resurser definieras i mallen:

- [Microsoft.RecoveryServices-valv](/azure/templates/microsoft.recoveryservices/vaults): skapar valvet.
- [Microsoft.RecoveryServices/vaults/backupstorageconfig:](/rest/api/backup/backupresourcestorageconfigs)konfigurerar valvets redundansinställningar för säkerhetskopiering.

Mallen innehåller valfria parametrar för valvets konfiguration av säkerhetskopiering. Inställningarna för lagringsredundans är lokalt redundant lagring (LRS) eller geo-redundant lagring (GRS). Mer information finns i Ange [lagringsredundans.](../backup/backup-create-rs-vault.md#set-storage-redundancy)

Fler Azure Recovery Services-mallar finns i [Azure-snabbstartsmallar.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Recoveryservices&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>Distribuera mallen

Om du vill distribuera mallen måste **du ange** **prenumerations-,** resursgrupps- **och valvnamn.**

1. Om du vill logga in på Azure och öppna mallen väljer du **avbildningen Distribuera till Azure.**

   [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

1. Välj eller ange följande värden:

   :::image type="content" source="media/quickstart-create-vault-template/create-vault-template.png" alt-text="Mall för att skapa ett Recovery Services-valv.":::

   - **Prenumeration:** Välj din Azure-prenumeration.
   - **Resursgrupp:** Välj en befintlig grupp eller välj Skapa **ny för att** lägga till en grupp.
   - **Plats:** standardvärdet är resursgruppens plats och blir otillgänglig när en resursgrupp har valts.
   - **Valvnamn:** Ange ett namn för valvet.
   - **Ändra lagringstyp:** Standardvärdet är **false**. Välj **endast** sant om du behöver ändra lagringstypen för valvet.
   - **Valvlagringstyp:** **Standardvärdet är GloballyRedundant**. Om lagringstypen har angetts **till true** väljer **du LokaltRedundant**.
   - **Plats:** funktionen `[resourceGroup().location]` använder som standard resursgruppens plats. Om du vill ändra platsen anger du ett värde, till exempel **westus**.
   - Markera kryssrutan Jag **godkänner de villkor som anges ovan.**

1. Starta valvets distribution genom att välja **knappen** Köp. Efter en lyckad distribution visas ett meddelande.

   :::image type="content" source="media/quickstart-create-vault-template/deployment-success.png" alt-text="Valvdistributionen lyckades.":::

## <a name="validate-the-deployment"></a>Verifiera distributionen

Bekräfta att valvet har skapats genom att använda Azure CLI eller Azure PowerShell.

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

Om du planerar att använda de nya resurserna krävs ingen åtgärd. Annars kan du ta bort resursgruppen och valvet som skapades i den här snabbstarten. Om du vill ta bort resursgruppen och dess resurser använder du Azure CLI eller Azure PowerShell.

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

I den här snabbstarten skapade du ett Recovery Services-valv. Om du vill veta mer om haveriberedskap fortsätter du till nästa snabbstartsartikel.

> [!div class="nextstepaction"]
> [Konfigurera haveriberedskap](azure-to-azure-quickstart.md)
