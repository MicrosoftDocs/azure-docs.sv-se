---
title: 'Förstå instans-ID: n för virtuella datorer i Azure VM Scale-uppsättning'
description: 'Förstå instans-ID: n för virtuella Azure-datorer med skalnings uppsättningar och de olika sätt som de är på.'
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 02/22/2018
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: a62c9bbde0726c8dec8fba1f69e221bd4e4b63bc
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102209857"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Förstå instans-ID: n för virtuella datorer i Azure VM Scale-uppsättning
I den här artikeln beskrivs instans-ID: n för skalnings uppsättningar och de olika sätt på ytan.

## <a name="scale-set-instance-ids"></a>Instans-ID för skalnings uppsättning

Varje virtuell dator i en skalnings uppsättning hämtar ett instans-ID som identifierar den unikt. Detta instans-ID används i skalnings uppsättnings-API: er för att utföra åtgärder på en angiven virtuell dator i skalnings uppsättningen. Du kan till exempel ange ett angivet instans-ID för att återställa avbildningen när du använder avbildnings-API: et:

REST API: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}/reimage?api-version={apiVersion}` (mer information finns i dokumentationen för [REST API](/rest/api/compute/virtualmachinescalesetvms/reimage))

PowerShell: `Set-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (mer information finns i PowerShell- [dokumentationen](/powershell/module/az.compute/set-azvmssvm))

CLI: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (mer information finns i CLI- [dokumentationen](/cli/azure/vmss)).

Du kan hämta listan med instans-ID: n genom att lista alla instanser i en skalnings uppsättning:

REST API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (mer information finns i dokumentationen för [REST API](/rest/api/compute/virtualmachinescalesetvms/list))

PowerShell: `Get-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (mer information finns i PowerShell- [dokumentationen](/powershell/module/az.compute/get-azvmssvm))

CLI: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (mer information finns i CLI- [dokumentationen](/cli/azure/vmss)).

Du kan också använda [Resources.Azure.com](https://resources.azure.com) eller [Azure SDK](https://azure.microsoft.com/downloads/) : er för att visa en lista över virtuella datorer i en skalnings uppsättning.

Den exakta presentationen av utdata beror på vilka alternativ du anger för kommandot, men här är några exempel på utdata från CLI:

```azurecli
az vmss show -g {resourceGroupName} -n {vmScaleSetName}
```

```output
[
  {
    "instanceId": "85",
    "latestModelApplied": true,
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Som du kan se är egenskapen "instanceId" bara ett decimal tal. Instans-ID: n kan återanvändas för nya instanser när gamla instanser tas bort.

>[!NOTE]
> Det finns **ingen garanti** för hur instans-ID: n tilldelas de virtuella datorerna i skalnings uppsättningen. De kan verka i stigande tid, men det är inte alltid fallet. Ta inte ett beroende på det sätt på vilket instans-ID: n tilldelas till de virtuella datorerna.

## <a name="scale-set-vm-names"></a>Namn på skalnings uppsättningens virtuella datorer

I exemplet ovan finns det också ett "namn" för den virtuella datorn. Det här namnet använder formatet "{Scale-set-Name} _ {instance-ID}". Det här namnet är det som visas i Azure Portal när du listar instanser i en skalnings uppsättning:

![Skärm bild som visar en lista över instanser i en skalnings uppsättning för virtuella datorer i Azure Portal.](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

Den {instance-ID}-delen av namnet har samma decimal tal som egenskapen "instanceId" som beskrevs tidigare.

## <a name="instance-metadata-vm-name"></a>Namn på virtuell instans-metadata

Om du frågar efter [instansen metadata](../virtual-machines/windows/instance-metadata-service.md) från en virtuell dator med skalnings uppsättning visas ett "namn" i utdata:

```output
{
  "compute": {
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Det här namnet är samma som det namn som beskrevs tidigare.

## <a name="scale-set-vm-computer-name"></a>Namn på virtuell dator för skalnings uppsättning

Varje virtuell dator i en skalnings uppsättning får också ett tilldelat dator namn. Det här dator namnet är värd namnet för den virtuella datorn i den [virtuella Azure-DNS-namnmatchning i det virtuella nätverket](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Det här dator namnet har formatet {Computer-name-prefix} {Base-36-instance-ID}.

{Base-36-instance-ID} är i [bas-36](https://en.wikipedia.org/wiki/Base36) och är alltid sex siffror långt. Om bas 36-representationen av talet tar färre än sex siffror fylls {Base-36-instans-ID} i med nollor för att göra det med en längd på sex siffror. Till exempel kommer en instans med {Computer-name-prefix} "nsgvmss" och instans-ID 85 att ha dator namnet "nsgvmss00002D".

>[!NOTE]
> Prefixet dator namn är en egenskap för den skalnings uppsättnings modell som du kan ange, så det kan vara en annan från skalnings uppsättningens namn.
