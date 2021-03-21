---
title: Aktivera InifinBand på virtuella HPC-datorer – Azure Virtual Machines | Microsoft Docs
description: Lär dig hur du aktiverar InfiniBand på virtuella Azure HPC-datorer.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 089976f2009e006f53dd2a77f09f57d5090429b7
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721234"
---
# <a name="enable-infiniband"></a>Aktivera InfiniBand

[RDMA-kompatibla](../../sizes-hpc.md#rdma-capable-instances) [H-](../../sizes-hpc.md) och [N-seriens](../../sizes-gpu.md) virtuella datorer kommunicerar via ett InfiniBand-nätverk med låg latens och hög bandbredd. RDMA-funktionen över en sådan samman koppling är nödvändig för att öka skalbarheten och prestandan för HPC-och AI-arbetsbelastningar med distribuerad nod. De InfiniBand-baserade virtuella datorerna i H-serien och N-serien är anslutna i ett icke-blockerande fat-träd med en design med låg diameter för optimerad och konsekvent RDMA-prestanda.

Det finns olika sätt att aktivera InfiniBand på de kompatibla VM-storlekarna.

## <a name="vm-images-with-infiniband-drivers"></a>VM-avbildningar med InfiniBand-drivrutiner
Se [VM-avbildningar](configure.md#vm-images) för en lista över virtuella dator avbildningar som stöds på Marketplace, som är förinstallerade med InfiniBand-drivrutiner (för SR-IOV eller icke-SR-IOV-VM) eller som kan konfigureras med lämpliga driv rutiner för [RDMA-kompatibla virtuella datorer](../../sizes-hpc.md#rdma-capable-instances).
- De virtuella [CentOS-HPC-](configure.md#centos-hpc-vm-images) avbildningarna i Marketplace är det enklaste sättet att komma igång.
- De virtuella [Ubuntu](configure.md#ubuntu-vm-images) -avbildningarna kan konfigureras med rätt IB-drivrutiner.

## <a name="infiniband-driver-vm-extensions"></a>InfiniBand-drivrutin för VM-tillägg
I Linux kan [INFINIBANDDRIVERLINUX VM-tillägget](../../extensions/hpc-compute-infiniband-linux.md) användas för att installera Mellanox ofed-drivrutinerna och aktivera InfiniBand på de virtuella SR-IOV-datorerna H-och N-serien.

I Windows installerar [INFINIBANDDRIVERWINDOWS VM-tillägget](../../extensions/hpc-compute-infiniband-windows.md) Windows Network Direct-drivrutiner (på datorer som inte är SR-IOV) eller Mellanox ofed-drivrutiner (på SR-IOV VM) för RDMA-anslutning. I vissa distributioner av A8-och A9-instanser läggs HpcVmDrivers-tillägget till automatiskt. Observera att HpcVmDrivers VM-tillägget är inaktuellt. den kommer inte att uppdateras.

Om du vill lägga till ett VM-tillägg i en virtuell dator kan du använda [Azure PowerShell](/powershell/azure/) -cmdletar. Mer information finns i [tillägg och funktioner för virtuella datorer](../../extensions/overview.md). Du kan också arbeta med tillägg för virtuella datorer som distribueras i den [klassiska distributions modellen](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

## <a name="manual-installation"></a>Manuell installation
[Mellanox OpenFabrics-drivrutiner (ofed)](https://www.mellanox.com/products/InfiniBand-VPI-Software) kan installeras manuellt i [SR-IOV-aktiverade](../../sizes-hpc.md#rdma-capable-instances) virtuella datorer i [H-serien](../../sizes-hpc.md) och [N-serien](../../sizes-gpu.md) .

### <a name="linux"></a>Linux
[Ofed-drivrutinerna för Linux](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed) kan installeras med exemplet nedan. Även om exemplet här är för RHEL/CentOS, men stegen är allmänna och kan användas för alla kompatibla Linux-operativsystem som Ubuntu (16,04, 18,04 19,04, 20,04) och SLES (12 SP4 och 15). Fler exempel på andra distributioner finns på [azhpc-images-lagrings platsen](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mellanoxofed.sh). Inbox-drivrutinerna fungerar också också, men Mellanox OFED-drivrutinerna innehåller fler funktioner.

```bash
MLNX_OFED_DOWNLOAD_URL=http://content.mellanox.com/ofed/MLNX_OFED-5.0-2.1.8.0/MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz
# Optionally verify checksum
wget --retry-connrefused --tries=3 --waitretry=5 $MLNX_OFED_DOWNLOAD_URL
tar zxvf MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz

KERNEL=( $(rpm -q kernel | sed 's/kernel\-//g') )
KERNEL=${KERNEL[-1]}
# Uncomment the lines below if you are running this on a VM
#RELEASE=( $(cat /etc/centos-release | awk '{print $4}') )
#yum -y install http://olcentgbl.trafficmanager.net/centos/${RELEASE}/updates/x86_64/kernel-devel-${KERNEL}.rpm
yum install -y kernel-devel-${KERNEL}
./MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64/mlnxofedinstall --kernel $KERNEL --kernel-sources /usr/src/kernels/${KERNEL} --add-kernel-support --skip-repo
```

### <a name="windows"></a>Windows
För Windows hämtar och installerar du [Mellanox-ofed för Windows-drivrutiner](https://www.mellanox.com/products/adapter-software/ethernet/windows/winof-2).

## <a name="enable-ip-over-infiniband-ib"></a>Aktivera IP över InfiniBand (IB)
Om du planerar att köra MPI-jobb behöver du vanligt vis inte IPoIB. MPI-biblioteket använder verben för IB-kommunikation (om du inte uttryckligen använder MPI-bibliotekets TCP/IP-kanal). Men om du har en app som använder TCP/IP för kommunikation och du vill köra över IB kan du använda IPoIB över IB-gränssnittet. Använd följande kommandon (för RHEL/CentOS) för att aktivera IP över InfiniBand.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=n/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om att installera olika [MPI-bibliotek som stöds](setup-mpi.md) och deras optimala konfiguration på de virtuella datorerna.
- Läs översikt över [HBv3-serien](hbv3-series-overview.md) och [HC-serien](hc-series-overview.md).
- Läs om de senaste meddelandena, HPC-arbetsbelastnings exempel och prestanda resultat på [Azure Compute Tech-Webbgruppens Bloggar](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- En arkitektur för högre nivå för att köra HPC-arbetsbelastningar finns i [HPC (data behandling med höga prestanda) i Azure](/azure/architecture/topics/high-performance-computing/).
