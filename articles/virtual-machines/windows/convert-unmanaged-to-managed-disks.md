---
title: Konvertera en virtuell Windows-dator från ohanterade till hanterade diskar
description: Konvertera en virtuell Windows-dator från ohanterade diskar till hanterade diskar med hjälp av PowerShell i distributions modellen för Resource Manager
author: roygara
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.date: 07/12/2018
ms.author: rogarana
ms.openlocfilehash: 3f586c8907ded618b4cc4aaaadf3c87471cda0b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550730"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Konvertera en virtuell Windows-dator från ohanterade till hanterade diskar

Om du har befintliga virtuella Windows-datorer som använder ohanterade diskar kan du konvertera de virtuella datorerna så att de använder hanterade diskar via [Azure Managed disks](../managed-disks-overview.md) -tjänsten. Den här processen konverterar både OS-disken och anslutna data diskar.

 

## <a name="before-you-begin"></a>Innan du börjar


* Granska [plan för migrering till Managed disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Läs [vanliga frågor och svar om migrering till Managed disks](../faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* De ursprungliga virtuella hårddiskarna och lagringskontot som användes av den virtuella datorn före omvandlingen tas inte bort. De kan medföra ytterligare kostnader. För att undvika att debiteras för dem kan du ta bort de ursprungliga virtuella hårddiskblobbarna när du har kontrollerat att omvandlingen har slutförts. Om du behöver hitta de här frånkopplade diskarna för att ta bort dem läser du artikeln [hitta och ta bort frånkopplade Azure-hanterade och ohanterade diskar](find-unattached-disks.md).


## <a name="convert-single-instance-vms"></a>Konvertera virtuella datorer med en instans
Det här avsnittet beskriver hur du konverterar virtuella Azure-datorer med en instans från ohanterade diskar till hanterade diskar. (Om dina virtuella datorer finns i en tillgänglighets uppsättning, se nästa avsnitt.) 

1. Frigör den virtuella datorn med hjälp av cmdleten [Stop-AzVM](/powershell/module/az.compute/stop-azvm) . I följande exempel avallokeras den virtuella datorn `myVM` som heter i resurs gruppen med namnet `myResourceGroup` : 

   ```azurepowershell-interactive
   $rgName = "myResourceGroup"
   $vmName = "myVM"
   Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
   ```

2. Konvertera den virtuella datorn till Managed disks med hjälp av cmdleten [ConvertTo-AzVMManagedDisk](/powershell/module/az.compute/convertto-azvmmanageddisk) . Följande process konverterar den tidigare virtuella datorn, inklusive OS-disken och eventuella data diskar, och startar den virtuella datorn:

   ```azurepowershell-interactive
   ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
   ```



## <a name="convert-vms-in-an-availability-set"></a>Konvertera virtuella datorer i en tillgänglighets uppsättning

Om de virtuella datorerna som du vill konvertera till hanterade diskar finns i en tillgänglighets uppsättning måste du först konvertera tillgänglighets uppsättningen till en hanterad tillgänglighets uppsättning.

1. Konvertera tillgänglighets uppsättningen med hjälp av cmdleten [Update-AzAvailabilitySet](/powershell/module/az.compute/update-azavailabilityset) . I följande exempel uppdateras tillgänglighets uppsättningen `myAvailabilitySet` som heter i resurs gruppen med namnet `myResourceGroup` :

   ```azurepowershell-interactive
   $rgName = 'myResourceGroup'
   $avSetName = 'myAvailabilitySet'

   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
   ```

   Om den region där din tillgänglighets uppsättning finns bara har två hanterade fel domäner men antalet ohanterade fel domäner är 3, visar det här kommandot ett fel som liknar "det angivna antalet fel domäner 3 måste ligga inom intervallet 1 till 2." Lös felet genom att uppdatera fel domänen till 2 och uppdatera `Sku` till `Aligned` enligt följande:

   ```azurepowershell-interactive
   $avSet.PlatformFaultDomainCount = 2
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
   ```

2. Frigör och konvertera de virtuella datorerna i tillgänglighets uppsättningen. Följande skript frigör varje virtuell dator med hjälp av cmdleten [Stop-AzVM](/powershell/module/az.compute/stop-azvm) , konverterar den med hjälp av [ConvertTo-AzVMManagedDisk](/powershell/module/az.compute/convertto-azvmmanageddisk)och startar om den automatiskt enligt konverterings processen:

   ```azurepowershell-interactive
   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

   foreach($vmInfo in $avSet.VirtualMachinesReferences)
   {
     $vm = Get-AzVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
   }
   ```


## <a name="troubleshooting"></a>Felsökning

Om det uppstår ett fel under konverteringen, eller om en virtuell dator är i ett felaktigt tillstånd på grund av problem i en tidigare konvertering, kör du `ConvertTo-AzVMManagedDisk` cmdleten igen. Ett enkelt återförsök blockerar ofta situationen.
Innan du konverterar kontrollerar du att alla VM-tillägg är i läget etablering lyckades eller att konverteringen Miss lyckas med felkoden 409.

## <a name="convert-using-the-azure-portal"></a>Konvertera med Azure Portal

Du kan också konvertera ohanterade diskar till hanterade diskar med hjälp av Azure Portal.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj den virtuella datorn i listan över virtuella datorer i portalen.
3. I bladet för den virtuella datorn väljer du **diskar** på menyn.
4. Överst på bladet **diskar** väljer **du migrera till Managed disks**.
5. Om den virtuella datorn finns i en tillgänglighets uppsättning visas en varning på bladet **migrera till hanterade diskar** som du behöver för att konvertera tillgänglighets uppsättningen först. Varningen bör ha en länk som du kan klicka på för att konvertera tillgänglighets uppsättningen. När tillgänglighets uppsättningen har konverterats eller om den virtuella datorn inte finns i en tillgänglighets uppsättning, klickar du på **migrera** för att starta processen med att migrera diskarna till hanterade diskar.

Den virtuella datorn kommer att stoppas och startas om när migreringen är klar.

## <a name="next-steps"></a>Nästa steg

[Konvertera standard Managed disks till Premium](convert-disk-storage.md)

Ta en skrivskyddad kopia av en virtuell dator med hjälp av [ögonblicks bilder](snapshot-copy-managed-disk.md).