---
title: Konfigurera privata IP-adresser för virtuella datorer (klassisk) – Azure PowerShell | Microsoft Docs
description: Lär dig hur du konfigurerar privata IP-adresser för virtuella datorer (klassisk) med hjälp av PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
tags: azure-service-management
ms.assetid: 60c7b489-46ae-48af-a453-2b429a474afd
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2e798f81102017b4d34c4b1b219a9f23035b0359
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222844"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-powershell"></a>Konfigurera privata IP-adresser för en virtuell dator (klassisk) med hjälp av PowerShell

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen. Du kan också [hantera en statisk privat IP-adress i distributions modellen för Resource Manager](virtual-networks-static-private-ip-arm-ps.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

PowerShell-kommandona nedan förväntar sig en enkel miljö som redan har skapats. Om du vill köra kommandona som de visas i det här dokumentet skapar du först test miljön som beskrivs i [skapa ett VNet](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-netcfg-ps).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Så här kontrollerar du om en speciell IP-adress är tillgänglig
Kontrol lera att IP- *192.168.1.101* är tillgänglig i ett VNet med namnet *TestVNet* genom att köra följande PowerShell-kommando och kontrol lera värdet för *IsAvailable*:

```azurepowershell
Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 192.168.1.101 
```

Förväntad utdata:

```output
IsAvailable          : True
AvailableAddresses   : {}
OperationDescription : Test-AzureStaticVNetIP
OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
OperationStatus      : Succeeded
```

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Så här anger du en statisk privat IP-adress när du skapar en virtuell dator
PowerShell-skriptet nedan skapar en ny moln tjänst med namnet *TestService* och hämtar sedan en avbildning från Azure, skapar en virtuell dator med namnet *DNS01* i den nya moln tjänsten med hjälp av den hämtade avbildningen, anger att den virtuella datorn är i ett undernät med namnet *FrontEnd* och anger *192.168.1.7* som en statisk privat IP-adress för den virtuella datorn:

```azurepowershell
New-AzureService -ServiceName TestService -Location "Central US"
$image = Get-AzureVMImage | where {$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
New-AzureVMConfig -Name DNS01 -InstanceSize Small -ImageName $image.ImageName |
    Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! |
    Set-AzureSubnet –SubnetNames FrontEnd |
    Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
    New-AzureVM -ServiceName TestService –VNetName TestVNet
```

Förväntad utdata:

```output
WARNING: No deployment found in service: 'TestService'.
OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
New-AzureService     fcf705f1-d902-011c-95c7-b690735e7412 Succeeded      
New-AzureVM          3b99a86d-84f8-04e5-888e-b6fc3c73c4b9 Succeeded  
```

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hämta information om statisk privat IP-adress för en virtuell dator
Om du vill visa information om statisk privat IP-adress för den virtuella datorn som skapades med skriptet ovan kör du följande PowerShell-kommando och observera värdena för *IpAddress*:

```azurepowershell
Get-AzureVM -Name DNS01 -ServiceName TestService
```

Förväntad utdata:

```output
DeploymentName              : TestService
Name                        : DNS01
Label                       : 
VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
InstanceStatus              : Provisioning
IpAddress                   : 192.168.1.7
InstanceStateDetails        : Windows is preparing your computer for first use...
PowerState                  : Started
InstanceErrorCode           : 
InstanceFaultDomain         : 0
InstanceName                : DNS01
InstanceUpgradeDomain       : 0
InstanceSize                : Small
HostName                    : rsR2-797
AvailabilitySetName         : 
DNSName                     : http://testservice000.cloudapp.net/
Status                      : Provisioning
GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
PublicIPAddress             : 
PublicIPName                : 
NetworkInterfaces           : {}
ServiceName                 : TestService
OperationDescription        : Get-AzureVM
OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
OperationStatus             : OK
```

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Ta bort en statisk privat IP-adress från en virtuell dator
Om du vill ta bort den statiska privata IP-adressen som lagts till den virtuella datorn i skriptet ovan kör du följande PowerShell-kommando:

```azurepowershell
Get-AzureVM -ServiceName TestService -Name DNS01 |
    Remove-AzureStaticVNetIP |
    Update-AzureVM
```

Förväntad utdata:

```output
OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       052fa6f6-1483-0ede-a7bf-14f91f805483 Succeeded
```

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Så här lägger du till en statisk privat IP-adress till en befintlig virtuell dator
Om du vill lägga till en statisk privat IP-adress till den virtuella datorn som skapades med skriptet ovan kör du följande kommando:

```azurepowershell
Get-AzureVM -ServiceName TestService -Name DNS01 |
    Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
    Update-AzureVM
```

Förväntad utdata:

```output
OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       77d8cae2-87e6-0ead-9738-7c7dae9810cb Succeeded 
```

## <a name="set-ip-addresses-within-the-operating-system"></a>Ange IP-adresser inom operativ systemet

Vi rekommenderar att du inte statiskt tilldelar den privata IP-adress som tilldelats den virtuella Azure-datorn i operativ systemet på en virtuell dator, om det inte behövs. Om du manuellt ställer in den privata IP-adressen i operativ systemet måste du kontrol lera att den är samma adress som den privata IP-adress som tilldelats den virtuella Azure-datorn, eller så kan du förlora anslutningen till den virtuella datorn. Du bör aldrig manuellt tilldela den offentliga IP-adress som tilldelats till en virtuell Azure-dator i den virtuella datorns operativ system.

## <a name="next-steps"></a>Nästa steg
* Läs mer om [reserverade offentliga IP-](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) adresser.
* Lär dig mer om [offentliga IP-adresser på instans nivå (ILPIP)](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip) .
* Läs [reserverad IP REST-API: er](/previous-versions/azure/reference/dn722420(v=azure.100)).