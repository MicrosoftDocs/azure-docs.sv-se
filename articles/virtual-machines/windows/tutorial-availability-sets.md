---
title: Distribuera virtuella datorer i en tillgänglighets uppsättning med hjälp av Azure PowerShell
description: Lär dig hur du använder Azure PowerShell för att distribuera virtuella datorer med hög tillgänglighet i tillgänglighets uppsättningar
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 3/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.custom: mvc
ms.openlocfilehash: 178a29ea37195ddd2013ca5220663a75132beb24
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555915"
---
# <a name="create-and-deploy-virtual-machines-in-an-availability-set-using-azure-powershell"></a>Skapa och distribuera virtuella datorer i en tillgänglighets uppsättning med hjälp av Azure PowerShell

I den här självstudien lär du dig att öka tillgängligheten och tillförlitligheten för dina virtuella datorer (VM) med hjälp av tillgänglighetsuppsättningar. Tillgänglighetsuppsättningarna ser till att de virtuella datorer som du distribuerar i Azure distribueras över flera isolerade maskinvarunoder i ett kluster. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en tillgänglighetsuppsättning
> * Skapa en virtuell dator i en tillgänglighetsuppsättning
> * Kontrollera tillgängliga VM-storlekar
> * Kontrollera Azure Advisor


## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

## <a name="create-an-availability-set"></a>Skapa en tillgänglighetsuppsättning

Maskinvaran på en plats är uppdelad i flera uppdateringsdomäner och feldomäner. En **uppdateringsdomän** är en grupp av underliggande fysisk maskinvara som kan startas om samtidigt. De virtuella datorerna i samma **feldomän** delar samma lagring, strömkälla och nätverksväxel.  

Du kan skapa en tillgänglighetsuppsättning med hjälp av [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). I det här exemplet är antalet för både uppdateringsdomäner och feldomäner *2*, och tillgänglighetsuppsättningen heter *myAvailabilitySet*.

Skapa en resursgrupp.

```azurepowershell-interactive
New-AzResourceGroup `
   -Name myResourceGroupAvailability `
   -Location EastUS
```

Skapa en hanterad tillgänglighetsuppsättning med hjälp av [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) med parametern `-sku aligned`.

```azurepowershell-interactive
New-AzAvailabilitySet `
   -Location "EastUS" `
   -Name "myAvailabilitySet" `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Skapa virtuella datorer i en tillgänglighetsuppsättning
De virtuella datorerna måste skapas i tillgänglighetsuppsättningen för att säkerställa att de distribueras i maskinvaran. Du kan inte lägga till en befintlig virtuell dator i en tillgänglighetsuppsättning efter att den har skapats. 


När du skapar en virtuell dator med hjälp av [New-AzVM](/powershell/module/az.compute/new-azvm) använder du parametern `-AvailabilitySetName` för att ange namnet på tillgänglighetsuppsättningen.

Ange först ett administratörsanvändarnamn och lösenord för den virtuella datorn med [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Skapa nu två virtuella datorer med hjälp av [New-AzVM](/powershell/module/az.compute/new-azvm) i tillgänglighetsuppsättningen.

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupAvailability" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -PublicIpAddressName "myPublicIpAddress$i" `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred
}
```

Det tar några minuter att skapa och konfigurera de virtuella datorerna. När du är klar har du två virtuella datorer distribuerade över den underliggande maskinvaran. 

Om du tittar på tillgänglighets uppsättningen i portalen genom att gå till **resurs grupper**  >  **myResourceGroupAvailability**  >  **myAvailabilitySet** bör du se hur de virtuella datorerna distribueras mellan de två fel-och uppdaterings domänerna.

![Tillgänglighetsuppsättning i portalen](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Kontrollera tillgängliga VM-storlekar 

När du skapar en virtuell dator inuti en tillgänglighets uppsättning måste du veta vilka VM-storlekar som är tillgängliga på maskin varan. Använd kommandot [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) för att hämta alla tillgängliga storlekar för virtuella datorer som du kan distribuera i tillgänglighets uppsättningen.

```azurepowershell-interactive
Get-AzVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Kontrollera Azure Advisor 

Du kan även använda Azure Advisor för att få mer information om hur du kan förbättra tillgängligheten för dina virtuella datorer. Azure Advisor analyserar din konfiguration och användningstelemetri och rekommenderar sedan lösningar som kan hjälpa dig att förbättra kostnadseffektiviteten, prestanda, tillgängligheten och säkerheten för dina Azure-resurser.

Logga in på [Azure Portal](https://portal.azure.com), välj **Alla tjänster** och skriv **Advisor**. Advisor-instrumentpanelen visar anpassade rekommendationer för den valda prenumerationen. Mer information finns i [Komma igång med Azure Advisor](../../advisor/advisor-get-started.md).


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en tillgänglighetsuppsättning
> * Skapa en virtuell dator i en tillgänglighetsuppsättning
> * Kontrollera tillgängliga VM-storlekar
> * Kontrollera Azure Advisor

Gå vidare till nästa kurs vill veta mer om VM-skalningsuppsättningar.

> [!div class="nextstepaction"]
> [Skapa en VM-skalningsuppsättning](tutorial-create-vmss.md)
