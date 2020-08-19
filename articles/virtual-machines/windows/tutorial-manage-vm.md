---
title: Självstudie – Skapa och hantera virtuella Windows-datorer med Azure PowerShell
description: I den här självstudien lär du dig hur du använder Azure PowerShell för att skapa och hantera virtuella Windows-datorer i Azure
author: cynthn
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 06/06/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: b89b3e2cf799e1aa182f7005d92e2c91ce4b76ce
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263086"
---
# <a name="tutorial-create-and-manage-windows-vms-with-azure-powershell"></a>Självstudie: skapa och hantera virtuella Windows-datorer med Azure PowerShell

Med virtuella Azure-datorer får du en fullständigt konfigurerbar och flexibel datormiljö. I den här självstudien beskrivs de grundläggande distributionsuppgifterna för virtuella Azure-datorer, till exempel att välja en VM-storlek, välja en VM-avbildning och distribuera en virtuell dator. Lär dig att:

> [!div class="checklist"]
> * Skapa och ansluta till en virtuell dator
> * Välja och använda VM-avbildningar
> * Visa och använda specifika VM-storlekar
> * Ändra storlek på en virtuell dator
> * Visa och förstå tillstånd för virtuella datorer

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en resursgrupp med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. En resursgrupp måste skapas före den virtuella datorn. I följande exempel skapas en resurs grupp med namnet *myResourceGroupVM* i regionen *östra* :

```azurepowershell-interactive
New-AzResourceGroup `
   -ResourceGroupName "myResourceGroupVM" `
   -Location "EastUS"
```

Resursgruppen som anges när du skapar eller ändrar en virtuell dator visas i hela den här självstudien.

## <a name="create-a-vm"></a>Skapa en virtuell dator

När du skapar en virtuell dator finns flera tillgängliga alternativ, som t.ex. avbildning av operativsystemet, nätverkskonfiguration och administrativa autentiseringsuppgifter. Det här exemplet skapar en virtuell dator med namnet *myVM*, som kör standardversionen av Windows Server 2016 Datacenter.

Ange användarnamn och lösenord för administratörskontot på den virtuella datorn med [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-6):

```azurepowershell-interactive
$cred = Get-Credential
```

Skapa den virtuella datorn med hjälp av [New-AzVM](/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="connect-to-vm"></a>Ansluta till virtuell dator

När distributionen har slutförts kan du skapa en fjärrskrivbordsanslutning med den virtuella datorn.

Kör följande kommandon för att returnera den offentliga IP-adressen för den virtuella datorn. Anteckna denna IP-adress så att du kan ansluta till den till din webbläsare för att testa webbanslutningen i ett framtida steg.

```azurepowershell-interactive
Get-AzPublicIpAddress `
   -ResourceGroupName "myResourceGroupVM"  | Select IpAddress
```

Använd följande kommando på den lokala datorn för att skapa en fjärrskrivbordssession med den virtuella datorn. Ersätt IP-adressen med den virtuella datorns *publicIPAddress*. När du uppmanas att göra det anger du de autentiseringsuppgifter som användes när du skapade den virtuella datorn.

```powershell
mstsc /v:<publicIpAddress>
```

I fönstret **Windows-säkerhet** väljer du **fler alternativ** och sedan **använd ett annat konto**. Ange det användarnamn och lösenord som du skapade för den virtuella datorn och klicka sedan på **OK**.

## <a name="understand-marketplace-images"></a>Förstå Marketplace-avbildningar

På Azures marknadsplats finns många avbildningar som kan användas för att skapa en ny virtuell dator. I de föregående stegen skapades en virtuell dator med avbildningen Windows Server 2016 Datacenter. I det här steget används PowerShell-modulen för att söka på marknadsplatsen efter andra Windows-avbildningar som också kan användas som bas för nya virtuella datorer. Den här processen består av att hitta utgivare, erbjudande, SKU och eventuellt ett versionsnummer för att [identifiera](cli-ps-findimage.md#terminology) avbildningen.

Använd kommandot [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) till att returnera en lista med avbildningsutgivare:

```azurepowershell-interactive
Get-AzVMImagePublisher -Location "EastUS"
```

Använd [Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer) till att returnera en lista med avbildningserbjudanden. Den returnerade listan filtreras på den angivna utgivaren med det här kommandot som heter `MicrosoftWindowsServer`:

```azurepowershell-interactive
Get-AzVMImageOffer `
   -Location "EastUS" `
   -PublisherName "MicrosoftWindowsServer"
```

Resultatet ser ut ungefär som i följande exempel: 

```powershell
Offer             PublisherName          Location
-----             -------------          --------
Windows-HUB       MicrosoftWindowsServer EastUS
WindowsServer     MicrosoftWindowsServer EastUS
WindowsServer-HUB MicrosoftWindowsServer EastUS
```

Kommandot [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) filtrerar sedan på namnet på utgivaren och erbjudandet och returnerar en lista med avbildningsnamn.

```azurepowershell-interactive
Get-AzVMImageSku `
   -Location "EastUS" `
   -PublisherName "MicrosoftWindowsServer" `
   -Offer "WindowsServer"
```

Resultatet ser ut ungefär som i följande exempel: 

```powershell
Skus                                      Offer         PublisherName          Location
----                                      -----         -------------          --------
2008-R2-SP1                               WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-smalldisk                     WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter                        WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-smalldisk              WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core               WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core-smalldisk     WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers           WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers-smalldisk WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-RDSH                 WindowsServer MicrosoftWindowsServer EastUS
2016-Nano-Server                          WindowsServer MicrosoftWindowsServer EastUS
```

Den här informationen kan användas till att distribuera en virtuell dator med en viss avbildning. Det här exemplet distribuerar en virtuell dator med hjälp av den senaste versionen av en avbildning av Windows Server 2016 med Containers.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM2" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress2" `
    -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
    -Credential $cred `
    -AsJob
```

Parametern `-AsJob` skapar den virtuella datorn som en bakgrundsaktivitet så att PowerShell-kommandotolkarna återgår till dig. Du kan visa information om bakgrundsjobb med cmdleten `Get-Job`.

## <a name="understand-vm-sizes"></a>Förstå VM-storlekar

Storleken på den virtuella datorn avgör hur mycket beräkningsresurser (t.ex. CPU, GPU och minne) som är tillgängligt för den virtuella datorn. Virtuella datorer måste skapas med en virtuell dator med lämplig storlek för arbetsbelastningen. Om arbetsbelastningen ökar kan man även ändra storlek på den befintliga virtuella datorn.

### <a name="vm-sizes"></a>VM-storlekar

I följande tabell kategoriseras storlekarna i användningsfall.  

| Typ                     | Normala storlekar           |    Beskrivning       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Generell användning](../sizes-general.md)         |B, Dsv3, Dv3, DSv2, Dv2, Av2, DC| Balanserat förhållande mellan processor och minne. Perfekt för utveckling eller test samt små till medelstora lösningar för program och data.  |
| [Beräkningsoptimerad](../sizes-compute.md)   | Fsv2          | Högt förhållande mellan processor och minne. Bra för program med medelhög trafik, nätverkstillämpningar och batchprocesser.        |
| [Minnesoptimerad](../sizes-memory.md)    | Esv3, Ev3, M, DSv2, Dv2  | Högt förhållande mellan minne och kärna. Utmärkt för relationsdatabaser, mellanstora till stora cacheminnen och minnesinterna analyser.                 |
| [Lagringsoptimerad](../sizes-storage.md)      | Lsv2, LS              | Högt diskgenomflöde och I/O. Perfekt för stordata, SQL- och NoSQL-databaser.                                                         |
| [GPU](../sizes-gpu.md)          | NV, NVv2, NC, NCv2, NCv3, ND            | Virtuella specialdatorer som är avsedda för tung grafisk rendering och videoredigering.       |
| [Höga prestanda](../sizes-hpc.md) | H        | Virtuella datorer med de kraftfullaste processorerna och nätverksgränssnitt för stora dataflöden (RDMA). |

### <a name="find-available-vm-sizes"></a>Hitta tillgängliga VM-storlekar

Om du vill se en lista med VM-storlekar som är tillgängliga i en viss region kan du använda kommandot [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize).

```azurepowershell-interactive
Get-AzVMSize -Location "EastUS"
```

## <a name="resize-a-vm"></a>Ändra storlek på en virtuell dator

När en virtuell dator har distribuerats kan storleken ändras för att öka eller minska resurstilldelningen.

Kontrollera om önskad storlek är tillgänglig i den aktuella virtuella datorns kluster innan du ändrar storleken på en virtuell dator. Kommandot [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) returnerar en lista med storlekar.

```azurepowershell-interactive
Get-AzVMSize -ResourceGroupName "myResourceGroupVM" -VMName "myVM"
```

Om storleken är tillgänglig kan storleken på den virtuella datorn ändras medan den är igång, men den startas om under åtgärden.

```azurepowershell-interactive
$vm = Get-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_DS3_v2"
Update-AzVM `
   -VM $vm `
   -ResourceGroupName "myResourceGroupVM"
```

Om önskad storlek inte finns i det aktuella klustret måste den virtuella datorn frigöras innan åtgärden för att ändra storlek kan utföras. När en virtuell datorn frigörs kommer alla data på den tillfälliga disken tas bort och den offentliga IP-adressen ändras, såvida inte en statisk IP-adress används.

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM" -Force
$vm = Get-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_E2s_v3"
Update-AzVM -VM $vm `
   -ResourceGroupName "myResourceGroupVM"
Start-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -Name $vm.name
```

## <a name="vm-power-states"></a>Energinivåer för VM

En virtuell Azure-dator kan ha en av många energinivåer. 


| Energinivå | Beskrivning
|----|----|
| Startar | Den virtuella datorn startas. |
| Körs | Den virtuella datorn körs. |
| Stoppas | Den virtuella datorn stoppas. |
| Stoppad | Den virtuella datorn har stoppats. Virtuella datorer i ett stoppat tillstånd kan fortfarande medföra debitering.  |
| Frigör | Den virtuella datorn frigörs. |
| Frigjord | Anger att den virtuella datorn är frånkopplad från hypervisor-programmet, men att den fortfarande är tillgänglig i kontrollplanet. Virtuella datorer med tillståndet `Deallocated` medför inte några debiteringar. |
| - | Energinivån för den virtuella datorn är okänd. |


Om du vill hämta tillståndet för en viss virtuell dator kan du använda kommandot [Get-AzVM](/powershell/module/az.compute/get-azvm). Du måste ange ett giltigt namn för en virtuell dator och resursgrupp.

```azurepowershell-interactive
Get-AzVM `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

Utdata ser ut ungefär som i följande exempel:

```powershell
Status
------
PowerState/running
```

Om du vill hämta energi läget för alla virtuella datorer i din prenumeration använder du [Virtual Machines-List all API](https://docs.microsoft.com/rest/api/compute/virtualmachines/listall) med parametern **statusOnly** inställd på *True*.

## <a name="management-tasks"></a>Hanteringsuppgifter

Under livscykeln för en virtuell dator kan du köra administrativa uppgifter, genom att till exempel starta, stoppa eller ta bort en virtuell dator. Dessutom kanske du vill skapa skript för att automatisera repetitiva och komplicerade uppgifter. Med hjälp av Azure PowerShell kan många vanliga administrativa uppgifter köras från kommandoraden eller i skript.

### <a name="stop-a-vm"></a>Stoppa en virtuell dator

Stoppa och frigöra en virtuell dator med [Stop-AzVM](/powershell/module/az.compute/stop-azvm):

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM" -Force
```

Använd parametern -StayProvisioned om du vill behålla den virtuella datorn i ett etablerat tillstånd.

### <a name="start-a-vm"></a>Starta en virtuell dator

```azurepowershell-interactive
Start-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM"
```

### <a name="delete-resource-group"></a>Ta bort resursgrupp

Allt i en resursgrupp tas bort när du tar bort resursgruppen.

```azurepowershell-interactive
Remove-AzResourceGroup `
   -Name "myResourceGroupVM" `
   -Force
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om grundläggande VM-skapande och hantering, till exempel att:

> [!div class="checklist"]
> * Skapa och ansluta till en virtuell dator
> * Välja och använda VM-avbildningar
> * Visa och använda specifika VM-storlekar
> * Ändra storlek på en virtuell dator
> * Visa och förstå tillstånd för virtuella datorer

Gå vidare till nästa självstudie om du vill lära dig mer om diskar i virtuella dator.  

> [!div class="nextstepaction"]
> [Skapa och hantera virtuella dator diskar](./tutorial-manage-data-disk.md)
