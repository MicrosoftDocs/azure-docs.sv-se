---
title: Återställa Fjärrskrivbordstjänster eller dess administratörs lösen ord på en virtuell Windows-dator | Microsoft Docs
description: Lär dig hur du återställer ett konto lösen ord eller Fjärrskrivbordstjänster på en virtuell Windows-dator med hjälp av Azure Portal eller Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/25/2019
ms.author: genli
ms.openlocfilehash: 720d25079e1350315c9f403a8215f650db49ceb7
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743091"
---
# <a name="reset-remote-desktop-services-or-its-administrator-password-in-a-windows-vm"></a>Återställa Fjärrskrivbordstjänster eller dess administratörs lösen ord på en virtuell Windows-dator
Om du inte kan ansluta till en virtuell Windows-dator (VM) kan du återställa det lokala administratörs lösen ordet eller återställa Fjärrskrivbordstjänster-konfigurationen (stöds inte på Windows-domänkontrollanter). Om du vill återställa lösenordet använder du Azure-portalen eller tillägget för VM-åtkomst i Azure PowerShell. När du har loggat in till den virtuella datorn kan du återställa lösenordet för den lokala administratören.  
Om du använder PowerShell ser du till att du har den [senaste PowerShell-modulen installerad och konfigurerad](/powershell/azure/) och är inloggad i din Azure-prenumeration. Du kan också [utföra de här stegen för virtuella datorer som skapas med den klassiska distributionsmodellen](/previous-versions/azure/virtual-machines/windows/classic/reset-rdp).

Du kan återställa Fjärrskrivbordstjänster och autentiseringsuppgifter på följande sätt:

- [Återställa med hjälp av Azure Portal](#reset-by-using-the-azure-portal)

- [Återställa med hjälp av VMAccess-tillägget och PowerShell](#reset-by-using-the-vmaccess-extension-and-powershell)

## <a name="reset-by-using-the-azure-portal"></a>Återställa med hjälp av Azure Portal

Logga först in på [Azure Portal](https://portal.azure.com) och välj sedan **virtuella datorer** på den vänstra menyn. 

### <a name="reset-the-local-administrator-account-password"></a>**Återställa lösen ordet för det lokala administratörs kontot**

1. Välj din virtuella Windows-dator och välj sedan **Återställ lösen ord** under **support + fel sökning**. Fönstret **Återställ lösen ord** visas.

2. Välj **Återställ lösen ord**, ange ett användar namn och ett lösen ord och välj sedan **Uppdatera**. 

3. Försök att ansluta till den virtuella datorn igen.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Återställa Fjärrskrivbordstjänster-konfigurationen**

Den här processen aktiverar fjärr skrivbords tjänsten på den virtuella datorn och skapar en brand Väggs regel för RDP-standardporten 3389.

1. Välj din virtuella Windows-dator och välj sedan **Återställ lösen ord** under **support + fel sökning**. Fönstret **Återställ lösen ord** visas. 

2. Välj **Återställ endast konfiguration** och välj sedan **Uppdatera**. 

3. Försök att ansluta till den virtuella datorn igen.

## <a name="reset-by-using-the-vmaccess-extension-and-powershell"></a>Återställa med hjälp av VMAccess-tillägget och PowerShell

Kontrol lera först att du har den [senaste PowerShell-modulen installerad och konfigurerad](/powershell/azure/) och att den är inloggad i din Azure-prenumeration med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

### <a name="reset-the-local-administrator-account-password"></a>**Återställa lösen ordet för det lokala administratörs kontot**

- Återställ administratörs lösen ordet eller användar namnet med PowerShell-cmdleten [set-AzVMAccessExtension](/powershell/module/az.compute/set-azvmaccessextension) . `typeHandlerVersion`Inställningen måste vara 2,0 eller större, eftersom version 1 är föråldrad. 

    ```powershell
    $SubID = "<SUBSCRIPTION ID>" 
    $RgName = "<RESOURCE GROUP NAME>" 
    $VmName = "<VM NAME>" 
    $Location = "<LOCATION>" 
 
    Connect-AzAccount 
    Select-AzSubscription -SubscriptionId $SubID 
    Set-AzVMAccessExtension -ResourceGroupName $RgName -Location $Location -VMName $VmName -Credential (get-credential) -typeHandlerVersion "2.0" -Name VMAccessAgent 
    ```

    > [!NOTE] 
    > Om du anger ett annat namn än det aktuella lokala administratörs kontot på den virtuella datorn, lägger VMAccess-tillägget till ett lokalt administratörs konto med det namnet och tilldelar ditt angivna lösen ord till det kontot. Om det lokala administratörs kontot på den virtuella datorn finns, kommer VMAccess-tillägget att återställa lösen ordet. Om kontot är inaktiverat kommer VMAccess-tillägget att aktivera det.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Återställa Fjärrskrivbordstjänster-konfigurationen**

1. Återställ fjärråtkomst till den virtuella datorn med PowerShell [-cmdleten Set-AzVMAccessExtension](/powershell/module/az.compute/set-azvmaccessextension) . I följande exempel återställs åtkomst tillägget som heter `myVMAccess` på den virtuella datorn med namnet `myVM` i `myResourceGroup` resurs gruppen:

    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
    ```

    > [!TIP]
    > En virtuell dator kan när som helst ha en enda VM-programåtkomst agent. Använd alternativet för att ställa in agent egenskaperna för VM-åtkomst `-ForceRerun` . När du använder `-ForceRerun` , se till att du använder samma namn för VM-agenttjänsten som du kanske har använt i tidigare kommandon.

1. Om du fortfarande inte kan fjärrans luta till den virtuella datorn kan du läsa [Felsök fjärr skrivbords anslutningar till en Windows-baserad virtuell Azure-dator](troubleshoot-rdp-connection.md). Om du förlorar anslutningen till Windows-domänkontrollanten måste du återställa den från en säkerhets kopia av domänkontrollanten.

## <a name="next-steps"></a>Nästa steg


- Om tillägget Azure VM Access inte kan installeras kan du [Felsöka problem med VM-tillägg](../extensions/troubleshoot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

- Om du inte kan återställa lösen ordet med hjälp av tillägget för VM-åtkomst kan du [återställa det lokala Windows-lösenordet offline](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Den här metoden är mer avancerad och kräver att du ansluter den virtuella hård disken för den felande virtuella datorn till en annan virtuell dator. Följ stegen som beskrivs i den här artikeln först och försök att återställa lösen ord för offline endast om de här stegen inte fungerar.

- [Lär dig mer om Azure VM-tillägg och-funktioner](../extensions/features-windows.md).

- [Anslut till en virtuell Azure-dator med RDP eller SSH](/previous-versions/azure/dn535788(v=azure.100)).


- [Felsök fjärr skrivbords anslutningar till en Windows-baserad virtuell Azure-dator](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
