---
title: Optimera data flöde för virtuella dator nätverk | Microsoft Docs
description: Optimera nätverks data flöde för Microsoft Azure virtuella Windows-och Linux-datorer, inklusive större distributioner som Ubuntu, CentOS och Red Hat.
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/06/2020
ms.author: steveesp
ms.openlocfilehash: bb9235f4d1190bf7f71ddc007f09c9666c353234
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98216809"
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Optimera nätverks data flöde för virtuella Azure-datorer

Virtuella Azure-datorer har standard nätverks inställningar som kan optimeras ytterligare för nätverks data flöde. Den här artikeln beskriver hur du optimerar nätverks data flöde för Microsoft Azure virtuella Windows-och Linux-datorer, inklusive större distributioner som Ubuntu, CentOS och Red Hat.

## <a name="windows-vm"></a>Virtuell Windows-dator

Om din virtuella Windows-dator har stöd för [accelererat nätverk](create-vm-accelerated-networking-powershell.md), kan det vara en optimal konfiguration för data flödet. För alla andra virtuella Windows-datorer kan med hjälp av skalning på mottagar sidan (RSS) uppnå högre maximalt data flöde än en virtuell dator utan RSS. RSS kan vara inaktiverat som standard på en virtuell Windows-dator. För att avgöra om RSS är aktiverat och aktivera det om det är inaktiverat, slutför du följande steg:

1. Se om RSS är aktiverat för ett nätverkskort med PowerShell- `Get-NetAdapterRss` kommandot. I följande exempel utdata som returneras från `Get-NetAdapterRss` , är RSS inte aktiverat.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Ange följande kommando för att aktivera RSS:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    Föregående kommando saknar utdata. Kommandot ändrade NIC-inställningar, vilket orsakar tillfälligt anslutnings avbrott i ungefär en minut. En dialog ruta för att återansluta visas när anslutningen bryts. Anslutningen återställs vanligt vis efter det tredje försöket.
3. Bekräfta att RSS är aktiverat på den virtuella datorn genom att ange `Get-NetAdapterRss` kommandot igen. Om det lyckas returneras följande exempel utdata:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>Virtuell Linux-dator

RSS är alltid aktiverat som standard i en virtuell Azure Linux-dator. Linux-kerneler som släpptes sedan oktober 2017 innehåller alternativ för nya nätverks optimeringar som gör det möjligt för en virtuell Linux-dator att uppnå högre nätverks data flöde.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu för nya distributioner

Ubuntu Azure-kärnan är den mest optimerade för nätverks prestanda på Azure. För att få de senaste Optimeringarna installerar du först den senaste versionen av 18,04-LTS som stöds, enligt följande:

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "18.04-LTS",
"Version": "latest"
```

När den har skapats anger du följande kommandon för att få de senaste uppdateringarna. De här stegen fungerar också för virtuella datorer som kör Ubuntu Azure-kernel.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

Följande valfria kommando uppsättningar kan vara användbara för befintliga Ubuntu-distributioner som redan har Azure-kärnan men som inte har kunnat uppdatera med fel.

```bash
#optional steps may be helpful in existing deployments with the Azure kernel
#run as root or preface with sudo
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Ubuntu Azure kernel-uppgradering för befintliga virtuella datorer

Betydande data flödes prestanda kan uppnås genom att uppgradera till Azure Linux-kärnan. Kontrol lera kernel-versionen för att kontrol lera om du har denna kernel. Det bör vara samma eller senare än exemplet.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.13.0-1007-azure
```

Om den virtuella datorn inte har Azure-kärnan börjar versions numret vanligt vis med "4,4". Om den virtuella datorn inte har Azure-kernel kör du följande kommandon som rot:

```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

För att få den senaste optimeringen är det bäst att skapa en virtuell dator med den senaste versionen genom att ange följande parametrar:

```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.7",
"Version": "latest"
```

Nya och befintliga virtuella datorer kan dra nytta av att installera de senaste Linux Integration Services (LIS). Data flödes optimeringen är i LIS, från och med 4.2.2-2, även om senare versioner innehåller ytterligare förbättringar. Ange följande kommandon för att installera den senaste LIS-filen:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

För att kunna hämta optimeringar är det bäst att skapa en virtuell dator med den senast version som stöds genom att ange följande parametrar:

```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```

Nya och befintliga virtuella datorer kan dra nytta av att installera de senaste Linux Integration Services (LIS). Data flödes optimeringen är i LIS, från 4,2. Ange följande kommandon för att ladda ned och installera LIS:

```bash
wget https://aka.ms/lis
tar xvf lis
cd LISISO
sudo ./install.sh #or upgrade.sh if prior LIS was previously installed
```

Läs mer om Linux Integration Services version 4,2 för Hyper-V genom att visa [hämtnings sidan](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Nästa steg
* Distribuera virtuella datorer nära varandra för låg latens med [närhets placerings grupp](../virtual-machines/co-location.md)
* Se det optimerade resultatet med [bandbredd/data flöde som testar Azure VM](virtual-network-bandwidth-testing.md) för ditt scenario.
* Läs om hur [bandbredd allokeras till virtuella datorer](virtual-machine-network-throughput.md)
* Lär dig mer med [Azure Virtual Network vanliga frågor och svar](virtual-networks-faq.md)