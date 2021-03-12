---
title: Flytta en virtuell Windows-resurs i Azure
description: Flytta en virtuell Windows-dator till en annan Azure-prenumeration eller resurs grupp i distributions modellen för Resource Manager.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: c5d3445144178ce855a5ce904b42d6e68a8e7d29
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555252"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Flytta en virtuell Windows-dator till en annan Azure-prenumeration eller resursgrupp
Den här artikeln vägleder dig igenom hur du flyttar en virtuell Windows-dator (VM) mellan resurs grupper eller prenumerationer. Det kan vara praktiskt att flytta mellan prenumerationer om du ursprungligen skapade en virtuell dator i en personlig prenumeration och nu vill flytta den till företagets prenumeration för att fortsätta med ditt arbete. Du behöver inte stoppa den virtuella datorn för att kunna flytta den och den bör fortsätta att köras under flytten.

> [!IMPORTANT]
>Nya resurs-ID: n skapas som en del av flytten. När den virtuella datorn har flyttats måste du uppdatera dina verktyg och skript för att använda de nya resurs-ID: na.
>
>

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Använd PowerShell för att flytta en virtuell dator

Om du vill flytta en virtuell dator till en annan resurs grupp måste du se till att du också flyttar alla beroende resurser. Om du vill hämta en lista med resurs-ID för var och en av dessa resurser använder du cmdleten [Get-AzResource](/powershell/module/az.resources/get-azresource) .

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName myResourceGroup | Format-table -wrap -Property ResourceId
```

Du kan använda utdata från föregående kommando för att skapa en kommaavgränsad lista med resurs-ID: n till [Move-AzResource](/powershell/module/az.resources/move-azresource) för att flytta varje resurs till målet.

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "myDestinationResourceGroup" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```

Om du vill flytta resurserna till en annan prenumeration inkluderar du parametern **-DestinationSubscriptionId** .

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


När du uppmanas att bekräfta att du vill flytta de angivna resurserna anger du **Y** för att bekräfta.

## <a name="next-steps"></a>Nästa steg
Du kan flytta många olika typer av resurser mellan resurs grupper och prenumerationer. Mer information finns i [Flytta resurser till en ny resurs grupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md).    
