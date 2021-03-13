---
title: Fel sökning av problem med Windows VM-tillägg
description: Lär dig mer om fel sökning av problem med Azure Windows VM-tillägg
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/29/2016
ms.openlocfilehash: 8cc8a0b5ae0a83a152168b14a2c5577f8f7b48ab
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564806"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Felsöka problem med Azure Windows VM-tillägg
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Visa tilläggs status
Azure Resource Manager mallar kan köras från Azure PowerShell. När mallen har körts kan tilläggets status visas från Azure Resource Explorer eller kommando rads verktygen.

Här är ett exempel:

Azure PowerShell:

```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

Här är exempel på utdata:

```output
Extensions:  {
  "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
  "Name": "myCustomScriptExtension",
  "SubStatuses": [
    {
      "Code": "ComponentStatus/StdOut/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
          \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
          test.txt                          \\n\\n",
                  "Time": null
      },
    {
      "Code": "ComponentStatus/StdErr/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "",
      "Time": null
    }
  ]
}
```

## <a name="troubleshooting-extension-failures"></a>Felsöka tilläggs fel

### <a name="rerun-the-extension-on-the-vm"></a>Kör tillägget på den virtuella datorn igen
Om du kör skript på den virtuella datorn med ett anpassat skript tillägg kan du ibland stöta på ett fel där den virtuella datorn skapades men skriptet har misslyckats. Under dessa förhållanden är det rekommenderade sättet att återställa från det här felet att ta bort tillägget och köra mallen igen.
Obs! i framtiden skulle den här funktionen förbättras för att ta bort behovet av att avinstallera tillägget.

#### <a name="remove-the-extension-from-azure-powershell"></a>Ta bort tillägget från Azure PowerShell
```azurepowershell
Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"
```

När tillägget har tagits bort kan mallen köras igen för att köra skripten på den virtuella datorn.

### <a name="trigger-a-new-goalstate-to-the-vm"></a>Utlös en ny GoalState för den virtuella datorn
Du kanske märker att ett tillägg inte har körts eller inte kan köras på grund av ett saknat "Windows Azure CRP Certificate Generator" (det certifikatet används för att skydda transporten av tilläggets skyddade inställningar).
Certifikatet återskapas automatiskt genom att starta om Windows gäst agenten inifrån den virtuella datorn:
- Öppna aktivitets hanteraren
- Gå till fliken information
- Hitta WindowsAzureGuestAgent.exes processen
- Högerklicka på och välj "Avsluta aktivitet". Processen kommer att startas om automatiskt


Du kan också utlösa en ny GoalState för den virtuella datorn genom att köra en "VM reapply". VM-återaktivering är ett API som introducerades i 2020 för att [återanvända en](/rest/api/compute/virtualmachines/reapply) VM-status. Vi rekommenderar att du gör detta vid en tidpunkt när du kan tolerera en kort VM-nedtid. När återaktivering av sig inte orsakar en VM-omstart, och de allra flesta gånger som anropar tillämpa inte, startar inte om den virtuella datorn, det finns en mycket liten risk för att vissa andra väntande uppdateringar av VM-modellen tillämpas när utlösare är ett nytt mål tillstånd och att andra ändringar kan kräva en omstart. 

Azure Portal:

I portalen väljer du den virtuella datorn och i den vänstra rutan under **support + fel sökning** väljer du **distribuera om + Verkställ igen** och väljer sedan **tillämpa igen**.


Azure PowerShell *(Ersätt RG namn och VM-namn med dina värden)*:

```azurepowershell
Set-AzVM -ResourceGroupName <RG Name> -Name <VM Name> -Reapply
```

Azure CLI *(Ersätt RG-namn och VM-namn med dina värden)*:

```azurecli
az vm reapply -g <RG Name> -n <VM Name>
```

Om en "VM-återanvändning" inte fungerade kan du lägga till en ny tom datadisk till den virtuella datorn från Azure-Hanteringsportal och sedan ta bort den senare när certifikatet har lagts till igen.