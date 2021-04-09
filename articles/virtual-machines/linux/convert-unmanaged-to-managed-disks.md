---
title: Konvertera en virtuell Linux-dator från ohanterade diskar till hanterade diskar
description: Så här konverterar du en virtuell Linux-dator från ohanterade diskar till hanterade diskar med hjälp av Azure CLI.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 12/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 1f3b62f8c05edffa1b55bf3d8cd24494b1c918bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558499"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Konvertera en virtuell Linux-dator från ohanterade diskar till hanterade diskar

Om du har befintliga virtuella Linux-datorer som använder ohanterade diskar kan du konvertera de virtuella datorerna så att de använder [Azure Managed disks](../managed-disks-overview.md). Den här processen konverterar både OS-disken och anslutna data diskar.

Den här artikeln visar hur du konverterar virtuella datorer med hjälp av Azure CLI. Om du behöver installera eller uppgradera den, se [Installera Azure CLI](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Innan du börjar
* Läs [vanliga frågor och svar om migrering till Managed disks](../faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* De ursprungliga virtuella hårddiskarna och lagringskontot som användes av den virtuella datorn före omvandlingen tas inte bort. De kan medföra ytterligare kostnader. För att undvika att debiteras för dem kan du ta bort de ursprungliga virtuella hårddiskblobbarna när du har kontrollerat att omvandlingen har slutförts. Om du behöver hitta de här frånkopplade diskarna för att ta bort dem läser du artikeln [hitta och ta bort frånkopplade Azure-hanterade och ohanterade diskar](find-unattached-disks.md).

## <a name="convert-single-instance-vms"></a>Konvertera virtuella datorer med en instans
Det här avsnittet beskriver hur du konverterar virtuella Azure-datorer med en instans från ohanterade diskar till hanterade diskar. (Om dina virtuella datorer finns i en tillgänglighets uppsättning, se nästa avsnitt.) Du kan använda den här processen för att konvertera de virtuella datorerna från Premium (SSD) ohanterade diskar till Premium Managed disks eller från standard diskar (HDD) ohanterade diskar till standard Managed disks.

1. Frigör den virtuella datorn genom att använda [AZ VM-frigörning](/cli/azure/vm). I följande exempel avallokeras den virtuella datorn `myVM` som heter i resurs gruppen med namnet `myResourceGroup` :

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Konvertera den virtuella datorn till Managed disks genom att använda [AZ VM Convert](/cli/azure/vm). Följande process konverterar den virtuella datorn med namnet `myVM` , inklusive OS-disken och alla data diskar:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Starta den virtuella datorn efter konverteringen till hanterade diskar genom att använda [AZ VM start](/cli/azure/vm). I följande exempel startas den virtuella datorn `myVM` som heter i resurs gruppen med namnet `myResourceGroup` .

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Konvertera virtuella datorer i en tillgänglighets uppsättning

Om de virtuella datorerna som du vill konvertera till hanterade diskar finns i en tillgänglighets uppsättning måste du först konvertera tillgänglighets uppsättningen till en hanterad tillgänglighets uppsättning.

Alla virtuella datorer i tillgänglighets uppsättningen måste frigöras innan du konverterar tillgänglighets uppsättningen. Planera att konvertera alla virtuella datorer till Managed disks efter att tillgänglighets uppsättningen har konverterats till en hanterad tillgänglighets uppsättning. Starta sedan alla virtuella datorer och fortsätt att fungera som vanligt.

1. Visa en lista med alla virtuella datorer i en tillgänglighets uppsättning med hjälp av [AZ VM Availability-set list](/cli/azure/vm/availability-set). I följande exempel visas alla virtuella datorer i tillgänglighets uppsättningen som heter `myAvailabilitySet` i resurs gruppen med namnet `myResourceGroup` :

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Frigör alla virtuella datorer genom att använda [AZ VM-frigörning](/cli/azure/vm). I följande exempel avallokeras den virtuella datorn `myVM` som heter i resurs gruppen med namnet `myResourceGroup` :

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Konvertera tillgänglighets uppsättningen genom att använda [AZ VM Availability-set Convert](/cli/azure/vm/availability-set). I följande exempel konverteras tillgänglighets uppsättningen som heter `myAvailabilitySet` i resurs gruppen med namnet `myResourceGroup` :

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Konvertera alla virtuella datorer till Managed disks genom att använda [AZ VM Convert](/cli/azure/vm). Följande process konverterar den virtuella datorn med namnet `myVM` , inklusive OS-disken och alla data diskar:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Starta alla virtuella datorer efter konverteringen till hanterade diskar genom att använda [AZ VM start](/cli/azure/vm). I följande exempel startas den virtuella datorn `myVM` som heter i resurs gruppen med namnet `myResourceGroup` :

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-using-the-azure-portal"></a>Konvertera med Azure Portal

Du kan också konvertera ohanterade diskar till hanterade diskar med hjälp av Azure Portal.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj den virtuella datorn i listan över virtuella datorer i portalen.
3. I bladet för den virtuella datorn väljer du **diskar** på menyn.
4. Överst på bladet **diskar** väljer **du migrera till Managed disks**.
5. Om den virtuella datorn finns i en tillgänglighets uppsättning visas en varning på bladet **migrera till hanterade diskar** som du behöver för att konvertera tillgänglighets uppsättningen först. Varningen bör ha en länk som du kan klicka på för att konvertera tillgänglighets uppsättningen. När tillgänglighets uppsättningen har konverterats eller om den virtuella datorn inte finns i en tillgänglighets uppsättning, klickar du på **migrera** för att starta processen med att migrera diskarna till hanterade diskar.

Den virtuella datorn kommer att stoppas och startas om när migreringen är klar.

## <a name="next-steps"></a>Nästa steg

Mer information om lagrings alternativ finns i [Översikt över Azure Managed disks](../managed-disks-overview.md).
