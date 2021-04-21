---
title: Använda Windows-licens för virtuella sessionsvärddatorer – Azure
description: Beskriver hur du tillämpar Windows-licensen för Windows Virtual Desktop virtuella datorer.
author: Heidilohr
ms.topic: how-to
ms.date: 08/14/2019
ms.author: helohr
ms.openlocfilehash: fa3c9f82e99536b07a27656e0143d6b2fcc89a44
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833876"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>Använda Windows-licens för virtuella sessionsvärddatorer

Kunder som har rätt licens för att köra Windows Virtual Desktop arbetsbelastningar är berättigade att tillämpa en Windows-licens på sina virtuella sessionsvärddatorer och köra dem utan att betala för en annan licens. Mer information finns i [Windows Virtual Desktop prissättning.](https://azure.microsoft.com/pricing/details/virtual-desktop/)

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>Sätt att använda din Windows Virtual Desktop licens
Windows Virtual Desktop licens kan du tillämpa en licens på en virtuell Windows- eller Windows Server-dator som är registrerad som en sessionsvärd i en värdpool och tar emot användaranslutningar. Den här licensen gäller inte för virtuella datorer som körs som filresursservrar, domänkontrollanter och så vidare.

Det finns några sätt att använda Windows Virtual Desktop licensen:
- Du kan skapa en värdpool och dess virtuella sessionsvärddatorer med hjälp Azure Marketplace [erbjudandet](./create-host-pools-azure-marketplace.md). Licensen tillämpas automatiskt för virtuella datorer som skapas på det här sättet.
- Du kan skapa en värdpool och dess virtuella sessionsvärddatorer med hjälp av [GitHub Azure Resource Manager mallen](./virtual-desktop-fall-2019/create-host-pools-arm-template.md). De virtuella datorer som skapas på det här sättet får automatiskt licensen tillämpad.
- Du kan använda en licens för en befintlig virtuell sessionsvärddator. Om du vill göra detta följer du först anvisningarna i Skapa en värdpool med [PowerShell](./create-host-pools-powershell.md) för att skapa en värdpool och associerade virtuella datorer. Gå sedan tillbaka till den här artikeln för att lära dig hur du tillämpar licensen.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>Tillämpa en Windows-licens på en virtuell sessionsvärddator
Kontrollera att du har [installerat och konfigurerat den senaste Azure PowerShell](/powershell/azure/). Kör följande PowerShell-cmdlet för att tillämpa Windows-licensen:

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>Kontrollera att sessionsvärddatorns virtuella dator använder licensförmånen
När du har distribuerat den virtuella datorn kör du denna cmdlet för att verifiera licenstypen:
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

En virtuell sessionsvärddator med den tillämpade Windows-licensen visar ungefär så här:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Virtuella datorer utan den tillämpade Windows-licensen visar ungefär så här:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

Kör följande cmdlet för att se en lista över alla virtuella sessionsvärdar som har Windows-licensen tillämpad i din Azure-prenumeration:

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```

## <a name="requirements-for-deploying-windows-server-remote-desktop-services"></a>Krav för att distribuera Windows Server-Fjärrskrivbordstjänster

Om du distribuerar Windows Server 2019, 2016 eller 2012 R2 som Windows Virtual Desktop-värdar i distributionen måste en Fjärrskrivbordstjänster-licensserver vara tillgänglig från dessa virtuella datorer. Den Fjärrskrivbordstjänster licensservern kan finnas lokalt eller i Azure. Mer information finns i [Aktivera Fjärrskrivbordstjänster licensservern.](/windows-server/remote/remote-desktop-services/rds-activate-license-server)
