---
title: Flytta virtuella Azure-datorer till en ny prenumeration eller resurs grupp
description: Använd Azure Resource Manager för att flytta virtuella datorer till en ny resurs grupp eller prenumeration.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: ad7023f309f1ca948711eaa9bdf3867d2ef7a6f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100104917"
---
# <a name="move-guidance-for-virtual-machines"></a>Flytta vägledning för virtuella datorer

I den här artikeln beskrivs scenarier som inte stöds för närvarande och stegen för att flytta virtuella datorer med säkerhets kopiering.

## <a name="scenarios-not-supported"></a>Scenarier som inte stöds

Följande scenarier stöds inte än:

* Virtual Machine Scale Sets med standard-SKU Load Balancer eller offentlig IP-adress för standard-SKU: n kan inte flyttas.
* Det går inte att flytta virtuella datorer i ett befintligt virtuellt nätverk till en ny prenumeration när du inte flyttar alla resurser i det virtuella nätverket.
* Virtuella datorer som skapats från Marketplace-resurser med anslutna planer kan inte flyttas mellan prenumerationer. En möjlig lösning finns i [virtuella datorer med Marketplace-planer](#virtual-machines-with-marketplace-plans).
* Det går inte att flytta virtuella datorer med låg prioritet och den virtuella datorns skalnings uppsättningar med låg prioritet flyttas över resurs grupper eller prenumerationer.
* Det går inte att flytta virtuella datorer i en tillgänglighets uppsättning individuellt.

## <a name="azure-disk-encryption"></a>Azure Disk Encryption

Du kan inte flytta en virtuell dator som är integrerad med ett nyckel valv för att implementera [Azure Disk Encryption för virtuella Linux-datorer](../../../virtual-machines/linux/disk-encryption-overview.md) eller [Azure Disk Encryption för virtuella Windows-datorer](../../../virtual-machines/windows/disk-encryption-overview.md). Om du vill flytta den virtuella datorn måste du inaktivera kryptering.

```azurecli-interactive
az vm encryption disable --resource-group demoRG --name myVm1
```

```azurepowershell-interactive
Disable-AzVMDiskEncryption -ResourceGroupName demoRG -VMName myVm1
```

## <a name="virtual-machines-with-marketplace-plans"></a>Virtuella datorer med Marketplace-planer

Virtuella datorer som skapats från Marketplace-resurser med anslutna planer kan inte flyttas mellan prenumerationer. För att undvika den här begränsningen kan du avetablera den virtuella datorn i den aktuella prenumerationen och distribuera den igen i den nya prenumerationen. Följande steg hjälper dig att återskapa den virtuella datorn i den nya prenumerationen. Men de kanske inte fungerar för alla scenarier. Om planen inte längre är tillgänglig i Marketplace fungerar inte de här stegen.

1. Kopiera information om planen.

1. Klona OS-disken till mål prenumerationen eller flytta den ursprungliga disken när du har tagit bort den virtuella datorn från käll prenumerationen.

1. Godkänn villkoren för marknads platsen för din plan i mål prenumerationen. Du kan acceptera villkoren genom att köra följande PowerShell-kommando:

   ```azurepowershell
   Get-AzMarketplaceTerms -Publisher {publisher} -Product {product/offer} -Name {name/SKU} | Set-AzMarketplaceTerms -Accept
   ```

   Du kan också skapa en ny instans av en virtuell dator med planen via portalen. Du kan ta bort den virtuella datorn när du har accepterat villkoren i den nya prenumerationen.

1. I mål prenumerationen återskapar du den virtuella datorn från den klonade OS-disken med PowerShell, CLI eller en Azure Resource Manager mall. Ta med Marketplace-planen som är kopplad till disken. Informationen om planen bör matcha den plan som du har köpt i den nya prenumerationen.

## <a name="virtual-machines-with-azure-backup"></a>Virtuella datorer med Azure Backup

Om du vill flytta virtuella datorer som kon figurer ATS med Azure Backup måste du ta bort återställnings punkterna från valvet.

Om [mjuk borttagning](../../../backup/soft-delete-virtual-machines.md) är aktiverat för den virtuella datorn kan du inte flytta den virtuella datorn medan återställnings punkterna behålls. [Inaktivera mjuk borttagning](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) eller vänta 14 dagar efter att återställnings punkterna har tagits bort.

### <a name="portal"></a>Portal

1. Stoppa säkerhets kopieringen tillfälligt och behåll säkerhets kopierings data.
2. Gör så här om du vill flytta virtuella datorer som kon figurer ATS med Azure Backup:

   1. Hitta platsen för den virtuella datorn.
   2. Hitta en resurs grupp med följande namn mönster: `AzureBackupRG_<VM location>_1` . Namnet har till exempel formatet *AzureBackupRG_westus2_1*.
   3. I Azure Portal markerar du **Visa dolda typer**.
   4. Hitta resursen med typen **Microsoft. Compute/restorePointCollections** som har namngivnings mönstret `AzureBackup_<VM name>_###########` .
   5. Ta bort den här resursen. Den här åtgärden tar bara bort direkta återställnings punkter, inte säkerhetskopierade data i valvet.
   6. När borttagnings åtgärden har slutförts kan du flytta den virtuella datorn.

3. Flytta den virtuella datorn till mål resurs gruppen.
4. Återuppta säkerhets kopieringen.

### <a name="powershell"></a>PowerShell

1. Hitta platsen för den virtuella datorn.

1. Hitta en resurs grupp med namngivnings mönstret – `AzureBackupRG_<VM location>_1` . Namnet kan till exempel vara `AzureBackupRG_westus2_1` .

1. Om du bara vill flytta en virtuell dator hämtar du återställnings punkt samlingen för den virtuella datorn.

   ```azurepowershell-interactive
   $restorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -name AzureBackup_<VM name>* -ResourceType Microsoft.Compute/restorePointCollections
   ```

   Ta bort den här resursen. Den här åtgärden tar bara bort direkta återställnings punkter, inte säkerhetskopierade data i valvet.

   ```azurepowershell-interactive
   Remove-AzResource -ResourceId $restorePointCollection.ResourceId -Force
   ```

1. Om du flyttar alla virtuella datorer med Back UPS på den här platsen hämtar du återställnings punkt samlingarna för de virtuella datorerna.

   ```azurepowershell-interactive
   $restorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -ResourceType Microsoft.Compute/restorePointCollections
   ```

   Ta bort varje resurs. Den här åtgärden tar bara bort direkta återställnings punkter, inte säkerhetskopierade data i valvet.

   ```azurepowershell-interactive
   foreach ($restorePoint in $restorePointCollection)
   {
     Remove-AzResource -ResourceId $restorePoint.ResourceId -Force
   }
   ```

### <a name="azure-cli"></a>Azure CLI

1. Hitta platsen för den virtuella datorn.

1. Hitta en resurs grupp med namngivnings mönstret – `AzureBackupRG_<VM location>_1` . Namnet kan till exempel vara `AzureBackupRG_westus2_1` .

1. Om du bara vill flytta en virtuell dator hämtar du återställnings punkt samlingen för den virtuella datorn.

   ```azurecli-interactive
   RESTOREPOINTCOL=$(az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections --query "[?starts_with(name, 'AzureBackup_<VM name>')].id" --output tsv)
   ```

   Ta bort den här resursen. Den här åtgärden tar bara bort direkta återställnings punkter, inte säkerhetskopierade data i valvet.

   ```azurecli-interactive
   az resource delete --ids $RESTOREPOINTCOL
   ```

1. Om du flyttar alla virtuella datorer med Back UPS på den här platsen hämtar du återställnings punkt samlingarna för de virtuella datorerna.

   ```azurecli-interactive
   RESTOREPOINTCOL=$(az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections)
   ```

   Ta bort varje resurs. Den här åtgärden tar bara bort direkta återställnings punkter, inte säkerhetskopierade data i valvet.

   ```azurecli-interactive
   az resource delete --ids $RESTOREPOINTCOL
   ```

## <a name="next-steps"></a>Nästa steg

* För kommandon för att flytta resurser, se [Flytta resurser till ny resurs grupp eller prenumeration](../move-resource-group-and-subscription.md).

* Information om hur du flyttar Recovery Service-valv för säkerhets kopiering finns i [Recovery Services begränsningar](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).
