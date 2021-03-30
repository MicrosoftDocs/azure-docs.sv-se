---
title: Använd Windows-licens för Session Host-virtuella datorer – Azure
description: Beskriver hur du tillämpar Windows-licensen för virtuella Windows-datorer med virtuella datorer.
author: ChristianMontoya
ms.topic: how-to
ms.date: 08/14/2019
ms.author: chrimo
ms.openlocfilehash: 5f3749be36f5f035e49fcb862f92180e4902101f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88010148"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>Använd Windows-licens för sessioner som är värdar för virtuella datorer

Kunder som är korrekt licensierade att köra Windows-arbetsbelastningar med virtuella skriv bord är berättigade att tillämpa en Windows-licens på sina sessioners värdbaserade virtuella datorer och köra dem utan att betala för en annan licens. Mer information finns i [priser för virtuella Windows-datorer](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>Sätt att använda din Windows Virtual Desktop-licens
Med Windows Virtual Desktop-licensiering kan du tillämpa en licens på en virtuell Windows-eller Windows Server-dator som är registrerad som en sessions-värd i en värdbaserad pool och tar emot användar anslutningar. Den här licensen gäller inte för virtuella datorer som körs som fil resurs servrar, domänkontrollanter och så vidare.

Det finns några sätt att använda Windows Virtual Desktop-licensen:
- Du kan skapa en adresspool och dess sessions värd för virtuella datorer med hjälp av [Azure Marketplace-erbjudandet](./create-host-pools-azure-marketplace.md). Virtuella datorer som skapas på det här sättet har licensen tillämpats automatiskt.
- Du kan skapa en adresspool och dess sessions värd för virtuella datorer med hjälp av [GitHub Azure Resource Manager-mallen](./virtual-desktop-fall-2019/create-host-pools-arm-template.md). Virtuella datorer som skapas på det här sättet har licensen tillämpats automatiskt.
- Du kan tillämpa en licens på en befintlig virtuell Session Host-dator. Det gör du genom att följa anvisningarna i [skapa en adresspool med PowerShell](./create-host-pools-powershell.md) för att skapa en adresspool och associerade virtuella datorer. gå sedan tillbaka till den här artikeln och lär dig hur du tillämpar licensen.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>Tillämpa en Windows-licens på en virtuell dator för en fjärrskrivbordssession
Kontrol lera att du har [installerat och konfigurerat de senaste Azure PowerShell](/powershell/azure/). Kör följande PowerShell-cmdlet för att tillämpa Windows-licensen:

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>Kontrol lera att den virtuella datorns värd för sessionen använder licens förmånen
När du har distribuerat den virtuella datorn kör du denna cmdlet: verifiera licens typen:
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

En virtuell dator i en session som är värd för den tillämpade Windows-licensen visar något som liknar detta:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

För virtuella datorer utan den tillämpade Windows-licensen visas något som liknar detta:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

Kör följande cmdlet för att se en lista över alla VM-värdar som har Windows-licensen som tillämpas i din Azure-prenumeration:

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```
