---
title: Importera virtuella datorer från ett annat labb i Azure DevTest Labs
description: I den här artikeln beskrivs hur du importerar virtuella datorer från ett annat labb till det aktuella labbet i Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0f664a0ae399575ee936565adaf7364fd1c5ce5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85475942"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Importera virtuella datorer från ett annat labb i Azure DevTest Labs
Den här artikeln innehåller information om hur du importerar virtuella datorer från ett annat labb till labbet.

## <a name="scenarios"></a>Scenarier
Här följer några scenarier där du behöver importera virtuella datorer från ett labb till ett annat labb:

- En individ i teamet flyttar till en annan grupp inom företaget och vill bli Developer Desktop till det nya teamets DevTest Labs.
- Gruppen har nått en [kvot på prenumerations nivå](../azure-resource-manager/management/azure-subscription-service-limits.md) och vill dela upp teamen i några få prenumerationer
- Företaget flyttar till Express Route (eller någon annan ny nätverkstopologi) och teamet vill flytta Virtual Machines att använda den här nya infrastrukturen

## <a name="solution-and-constraints"></a>Lösning och begränsningar
Med den här funktionen kan du importera virtuella datorer i ett labb (källa) till ett annat labb (mål). Du kan också ange ett nytt namn för den virtuella mål datorn i processen. Import processen omfattar alla beroenden som diskar, scheman, nätverks inställningar och så vidare.

Processen tar lite tid och påverkas av följande faktorer:

- Antal och storlek på de diskar som är kopplade till käll datorn (eftersom det är en kopierings åtgärd och inte en flyttnings åtgärd)
- Avstånd till målet (till exempel USA, östra region till Sydostasien).

När processen är klar förblir den virtuella käll datorn avstängd och den nya körs i mål labbet.

Det finns två viktiga begränsningar som du bör känna till när du planerar att importera virtuella datorer från ett labb i till ett annat labb:

- Import av virtuella datorer över prenumerationer och över regioner stöds, men prenumerationerna måste kopplas till samma Azure Active Directory-klient.
- Virtual Machines får inte vara i ett anspråk tillstånd i käll labbet.
- Du är ägare till den virtuella datorn i käll labbet och ägare av labbet i mål labbet.
- Den här funktionen stöds för närvarande bara via PowerShell och REST API.

## <a name="use-powershell"></a>Använd PowerShell
Ladda ned ImportVirtualMachines.ps1-filen från [GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines). Du kan använda skriptet för att importera en enskild virtuell dator eller alla virtuella datorer i käll labbet till mål labbet.

### <a name="use-powershell-to-import-a-single-vm"></a>Använda PowerShell för att importera en enskild virtuell dator
För att köra det här PowerShell-skriptet måste du identifiera den virtuella käll datorn och mål labbet och eventuellt tillhandahålla ett nytt namn som ska användas för mål datorn:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>Använd PowerShell för att importera alla virtuella datorer i käll labbet
Om den virtuella käll datorn inte anges importerar skriptet automatiskt alla virtuella datorer i DevTest Labs.  Exempel:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>Använda HTTP REST för att importera en virtuell dator
REST-anropet är enkelt. Du ger tillräckligt med information för att identifiera käll-och mål resurserna. Kom ihåg att åtgärden sker i mål labbs resursen.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Ange principer för ett labb](devtest-lab-set-lab-policy.md)
- [Vanliga frågor och svar](devtest-lab-faq.md)
