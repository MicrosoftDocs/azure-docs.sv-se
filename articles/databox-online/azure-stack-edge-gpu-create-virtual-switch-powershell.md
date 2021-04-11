---
title: Skapa en ny virtuell växel i Azure Stack Edge via PowerShell
description: Beskriver hur du skapar en virtuell växel på en Azure Stack Edge-enhet med hjälp av PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/06/2021
ms.author: alkohli
ms.openlocfilehash: 1ad86695510a8fe93bbeeab27db53f5afbef92fd
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556255"
---
# <a name="create-a-new-virtual-switch-in-azure-stack-edge-pro-gpu-via-powershell"></a>Skapa en ny virtuell växel i Azure Stack Edge Pro GPU via PowerShell

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Den här artikeln beskriver hur du skapar en ny virtuell växel på din Azure Stack Edge Pro GPU-enhet. Du kan till exempel skapa en ny virtuell växel om du vill att dina virtuella datorer ska ansluta via en annan fysisk nätverks port.

## <a name="vm-deployment-workflow"></a>Arbets flöde för distribution av virtuell dator

1. Anslut till PowerShell-gränssnittet på enheten.
2. Fråga tillgängliga fysiska nätverks gränssnitt.
3. Skapa en virtuell växel.
4. Verifiera det virtuella nätverk och undernät som skapas automatiskt.

## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

- Du har åtkomst till en klient dator som har åtkomst till PowerShell-gränssnittet på enheten. Se [ansluta till PowerShell-gränssnittet](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 

    Klient datorn måste köra ett [operativ system som stöds](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device).

- Använd det lokala användar gränssnittet för att aktivera beräkning av ett av de fysiska nätverks gränssnitten på enheten enligt instruktionerna i [Aktivera beräknings nätverk](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) på enheten. 


## <a name="connect-to-the-powershell-interface"></a>Ansluta till PowerShell-gränssnittet

[Anslut till PowerShell-gränssnittet på enheten](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

## <a name="query-available-network-interfaces"></a>Fråga tillgängliga nätverks gränssnitt

1. Använd följande kommando för att visa en lista över fysiska nätverks gränssnitt som du kan använda för att skapa en ny virtuell växel. Du kommer att välja ett av dessa nätverks gränssnitt.

    ```powershell
    Get-NetAdapter -Physical
    ```
    Här är ett exempel på utdata:
    
    ```powershell
        [10.100.10.10]: PS>Get-NetAdapter -Physical
        
        Name                      InterfaceDescription                    ifIndex Status       MacAddress       LinkSpeed
        ----                      --------------------                    ------- ------       ----------        -----
        Port2                     QLogic 2x1GE+2x25GE QL41234HMCU NIC ...      12 Up           34-80-0D-05-26-EA ...ps
        Ethernet                  Remote NDIS Compatible Device                11 Up           F4-02-70-CD-41-39 ...ps
        Port1                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#3       9 Up           34-80-0D-05-26-EB ...ps
        Port5                     Mellanox ConnectX-4 Lx Ethernet Ad...#2       8 Up           0C-42-A1-C0-E3-99 ...ps
        Port3                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#4       7 Up           34-80-0D-05-26-E9 ...ps
        Port6                     Mellanox ConnectX-4 Lx Ethernet Adapter       6 Up           0C-42-A1-C0-E3-98 ...ps
        Port4                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#2       4 Up           34-80-0D-05-26-E8 ...ps
        
        [10.100.10.10]: PS>
    ```
2. Välj ett nätverks gränssnitt som är:

    - I status **upp** . 
    - Används inte av befintliga virtuella växlar. För närvarande kan endast en vSwitch konfigureras per nätverks gränssnitt. 
    
    Kör kommandot för att kontrol lera den befintliga associationen för virtuell växel och nätverks gränssnitt `Get-HcsExternalVirtualSwitch` .
 
    Här är ett exempel på utdata.

    ```powershell
    [10.100.10.10]: PS>Get-HcsExternalVirtualSwitch

    Name                          : vSwitch1
    InterfaceAlias                : {Port2}
    EnableIov                     : True
    MacAddressPools               :
    IPAddressPools                : {}
    ConfigurationSource           : Dsc
    EnabledForCompute             : True
    SupportsAcceleratedNetworking : False
    DbeDhcpHostVnicName           : f4a92de8-26ed-4597-a141-cb233c2ba0aa
    Type                          : External
    
    [10.100.10.10]: PS>
    ```
    I den här instansen är port 2 kopplad till en befintlig virtuell växel och bör inte användas.

## <a name="create-a-virtual-switch"></a>Skapa en virtuell växel

Använd följande cmdlet för att skapa en ny virtuell växel på ditt specifika nätverks gränssnitt. När den här åtgärden har slutförts kan dina beräknings instanser använda det nya virtuella nätverket.

```powershell
Add-HcsExternalVirtualSwitch -InterfaceAlias <Network interface name> -WaitForSwitchCreation $true
```

Använd `Get-HcsExternalVirtualSwitch` kommandot för att identifiera den nyss skapade växeln. Den nya växeln som skapas heter som `vswitch-<InterfaceAlias>` . 

Här är ett exempel på utdata:

```powershell
[10.100.10.10]: P> Add-HcsExternalVirtualSwitch -InterfaceAlias Port5 -WaitForSwitchCreation $true
[10.100.10.10]: PS>Get-HcsExternalVirtualSwitch

Name                          : vSwitch1
InterfaceAlias                : {Port2}
EnableIov                     : True
MacAddressPools               :
IPAddressPools                : {}
ConfigurationSource           : Dsc
EnabledForCompute             : True
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : f4a92de8-26ed-4597-a141-cb233c2ba0aa
Type                          : External

Name                          : vswitch-Port5
InterfaceAlias                : {Port5}
EnableIov                     : True
MacAddressPools               :
IPAddressPools                :
ConfigurationSource           : Dsc
EnabledForCompute             : False
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : 9b301c40-3daa-49bf-a20b-9f7889820129
Type                          : External

[10.100.10.10]: PS>
```

## <a name="verify-network-subnet"></a>Verifiera nätverk, undernät 

När du har skapat den nya virtuella växeln skapar Azure Stack Edge Pro GPU automatiskt ett virtuellt nätverk och ett undernät som motsvarar det. Du kan använda det här virtuella nätverket när du skapar virtuella datorer.

<!--To identify the virtual network and subnet associated with the new switch that you created, use the `Get-HcsVirtualNetwork` command. This cmdlet will be released in April some time. -->

## <a name="next-steps"></a>Nästa steg

- [Distribuera virtuella datorer på Azure Stack Edge Pro GPU-enhet via Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)

- [Distribuera virtuella datorer på Azure Stack Edge Pro GPU-enhet via Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
