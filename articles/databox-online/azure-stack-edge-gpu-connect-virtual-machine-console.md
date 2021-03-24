---
title: Anslut till konsolen för virtuella datorer på Azure Stack Edge Pro GPU-enhet
description: Beskriver hur du ansluter till den virtuella dator konsolen på en virtuell dator som körs på Azure Stack Edge Pro GPU-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/22/2021
ms.author: alkohli
ms.openlocfilehash: 68cf157a512e9b1f6caee4734869c5bb4b836e2f
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104962742"
---
# <a name="connect-to-a-virtual-machine-console-on-an-azure-stack-edge-pro-gpu-device"></a>Ansluta till en virtuell dator konsol på en Azure Stack Edge Pro GPU-enhet

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro GPU-lösningen kör arbets belastningar som inte är i behållare via de virtuella datorerna. Den här artikeln beskriver hur du ansluter till-konsolen för en virtuell dator som har distribuerats på enheten. 

Med konsolen för virtuella datorer kan du komma åt dina virtuella datorer med tangent bord, mus och skärm funktioner med hjälp av de vanligaste verktygen för fjärr skrivbord. Du kan komma åt konsolen och felsöka eventuella problem som har uppstått när du distribuerar en virtuell dator på enheten. Du kan ansluta till konsolen för virtuella datorer även om den virtuella datorn inte kunde etableras.


## <a name="workflow"></a>Arbetsflöde

Arbets flödet på hög nivå har följande steg:

1. Anslut till PowerShell-gränssnittet på enheten.
1. Aktivera konsol åtkomst till den virtuella datorn.
1. Anslut till den virtuella datorn med Remote Desktop Protocol (RDP).
1. Återkalla konsol åtkomst till den virtuella datorn.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrol lera att du har slutfört följande krav:

#### <a name="for-your-device"></a>För din enhet

Du bör ha åtkomst till en Azure Stack Edge Pro GPU-enhet som är aktive rad. Enheten måste ha en eller flera virtuella datorer som har distribuerats på den. Du kan distribuera virtuella datorer via Azure PowerShell, via mallarna eller via Azure Portal.

#### <a name="for-client-accessing-the-device"></a>För klient åtkomst till enheten

Kontrol lera att du har åtkomst till ett klient system som:

- Har åtkomst till PowerShell-gränssnittet på enheten. Klienten kör ett [operativ system som stöds](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device).
- Klienten kör PowerShell 7,0 eller senare. Den här versionen av PowerShell fungerar för Windows-, Mac-och Linux-klienter. Se anvisningar för att [Installera PowerShell 7,0](/powershell/scripting/whats-new/what-s-new-in-powershell-70?view=powershell-7.1&preserve-view=true).
- Har funktioner för fjärr skrivbord. Beroende på om du använder Windows, macOS eller Linux bör du installera en av dessa [fjärr skrivbords klienter](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients). Den här artikeln innehåller instruktioner för [Windows fjärr skrivbord](/windows-server/remote/remote-desktop-services/clients/windowsdesktop#install-the-client) och [FreeRDP](https://www.freerdp.com/). <!--Which version of FreeRDP to use?-->


## <a name="connect-to-vm-console"></a>Anslut till konsolen för virtuella datorer

Följ dessa steg för att ansluta till konsolen för virtuella datorer på enheten.

### <a name="connect-to-the-powershell-interface-on-your-device"></a>Anslut till PowerShell-gränssnittet på din enhet

Det första steget är att [ansluta till PowerShell-gränssnittet](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) på enheten. 

### <a name="enable-console-access-to-the-vm"></a>Aktivera konsol åtkomst till den virtuella datorn

1.  I PowerShell-gränssnittet kör du följande kommando för att aktivera åtkomst till konsolen för virtuella datorer.

    ```powershell
    Grant-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
    ```
2. Anteckna det virtuella dator-ID: t i exempel resultatet. Du behöver detta för ett senare steg.

    ```powershell
    [10.100.10.10]: PS>Grant-HcsVMConnectAccess -ResourceGroupName mywindowsvm1rg -VirtualMachineName mywindowsvm1
        
    VirtualMachineId       : 81462e0a-decb-4cd4-96e9-057094040063
    VirtualMachineHostName : 3V78B03
    ResourceGroupName      : mywindowsvm1rg
    VirtualMachineName     : mywindowsvm1
    Id                     : 81462e0a-decb-4cd4-96e9-057094040063
    [10.100.10.10]: PS>
    ```

### <a name="connect-to-the-vm"></a>Anslut till VM:en

Nu kan du använda en fjärr skrivbords klient för att ansluta till konsolen för virtuella datorer.

#### <a name="use-windows-remote-desktop"></a>Använd Windows fjärr skrivbord

1. Skapa en ny textfil och ange följande text.

    ```
    pcb:s:<VM ID from PowerShell>;EnhancedMode=0
    full address:s:<IP address of the device>   
    server port:i:2179
    username:s:EdgeARMUser
    negotiate security layer:i:0
    ```
1. Spara filen som **. RDP* på klient systemet. Du använder den här profilen för att ansluta till den virtuella datorn.
1. Dubbelklicka på profilen för att ansluta till den virtuella datorn. Ange följande autentiseringsuppgifter:

    - **Användar namn**: Logga in som EdgeARMUser.
    - **Lösen ord**: Ange det lokala Azure Resource Manager lösen ordet för enheten. Om du har glömt lösen ordet [återställer du Azure Resource Manager lösen ord via Azure Portal](azure-stack-edge-gpu-set-azure-resource-manager-password.md#reset-password-via-the-azure-portal). 

#### <a name="use-freerdp"></a>Använd FreeRDP

Om du använder FreeRDP på Linux-klienten kör du följande kommando: 

```powershell
./wfreerdp /u:EdgeARMUser /vmconnect:<VM ID from PowerShell> /v:<IP address of the device>
```

## <a name="revoke-vm-console-access"></a>Återkalla åtkomst till konsolen för virtuella datorer

Om du vill återkalla åtkomsten till konsolen för virtuella datorer går du tillbaka till PowerShell-gränssnittet på enheten. Kör följande kommando:

```
Revoke-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
```
Här är ett exempel på utdata:

```powershell
[10.100.10.10]: PS>Revoke-HcsVMConnectAccess -ResourceGroupName mywindowsvm1rg -VirtualMachineName mywindowsvm1

VirtualMachineId       : 81462e0a-decb-4cd4-96e9-057094040063
VirtualMachineHostName : 3V78B03
ResourceGroupName      : mywindowsvm1rg
VirtualMachineName     : mywindowsvm1
Id                     : 81462e0a-decb-4cd4-96e9-057094040063

[10.100.10.10]: PS>
```
> [!NOTE] 
> När du är färdig med den virtuella dator konsolen rekommenderar vi att du antingen återkallar åtkomsten eller stänger PowerShell-fönstret för att avsluta sessionen. 

## <a name="next-steps"></a>Nästa steg

- Felsök [Azure Stack Edge Pro](azure-stack-edge-gpu-troubleshoot.md) i Azure Portal.