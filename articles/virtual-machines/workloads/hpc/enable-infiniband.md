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
ms.openlocfilehash: dba336c8690bba2bb388a8b9ab2d52b651166da5
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599612"
---
# <a name="enable-infiniband"></a>Aktivera InfiniBand

[RDMA-kompatibla](../../sizes-hpc.md#rdma-capable-instances) [virtuella datorer i H-serien](../../sizes-hpc.md) och N-serien kommunicerar via InfiniBand-nätverket med låg latens och hög bandbredd. [](../../sizes-gpu.md) RDMA-funktionen över en sådan sammankoppling är viktig för att öka skalbarheten och prestandan för distribuerade HPC- och AI-arbetsbelastningar. De infiniBand-aktiverade virtuella datorerna i H-serien och N-serien är anslutna i ett icke-blockerande fat tree med en design med låg vikt för optimerad och konsekvent RDMA-prestanda.

Det finns olika sätt att aktivera InfiniBand på de kompatibla VM-storlekarna.

## <a name="vm-images-with-infiniband-drivers"></a>VM-avbildningar med InfiniBand-drivrutiner
Se [](configure.md#vm-images) VM-avbildningar för en lista över VM-avbildningar som stöds på Marketplace, som är förinstallerade med InfiniBand-drivrutiner (för virtuella SR-IOV- eller icke-SR-IOV-datorer) eller som kan konfigureras med lämpliga drivrutiner för [RDMA-kompatibla virtuella datorer.](../../sizes-hpc.md#rdma-capable-instances)
- [CentOS-HPC](configure.md#centos-hpc-vm-images) VM-avbildningarna på Marketplace är det enklaste sättet att komma igång.
- Avbildningar av virtuella [Ubuntu-datorer](configure.md#ubuntu-vm-images) kan konfigureras med rätt IB-drivrutiner.

## <a name="infiniband-driver-vm-extensions"></a>Vm-tillägg för InfiniBand-drivrutin
I Linux kan vm-tillägget [InfiniBandDriverLinux](../../extensions/hpc-compute-infiniband-linux.md) användas för att installera Mellanox OFED-drivrutiner och aktivera InfiniBand på de SR-IOV-aktiverade virtuella datorerna i H- och N-serien.

I Windows installerar [vm-tillägget InfiniBandDriverWindows](../../extensions/hpc-compute-infiniband-windows.md) Windows Network Direct-drivrutiner (på virtuella datorer som inte är SR-IOV) eller Mellanox OFED-drivrutiner (på virtuella SR-IOV-datorer) för RDMA-anslutning. I vissa distributioner av A8- och A9-instanser läggs tillägget HpcVmDrivers till automatiskt. Observera att det virtuella datortillägget HpcVmDrivers håller på att bli inaktuellt. Den uppdateras inte.

Om du vill lägga till VM-tillägget till en virtuell dator kan du använda Azure PowerShell-cmdlets. [](/powershell/azure/) Mer information finns i Tillägg [och funktioner för virtuella datorer.](../../extensions/overview.md) Du kan också arbeta med tillägg för virtuella datorer som distribueras i den [klassiska distributionsmodellen](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

## <a name="manual-installation"></a>Manuell installation
[Mellanox OpenFabrics-drivrutiner (OFED)](https://www.mellanox.com/products/InfiniBand-VPI-Software) kan installeras manuellt på de [SR-IOV-aktiverade](../../sizes-hpc.md#rdma-capable-instances) virtuella datorerna i [H-serien](../../sizes-hpc.md) och [N-serien.](../../sizes-gpu.md)

### <a name="linux"></a>Linux
[OFED-drivrutinerna för Linux](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed) kan installeras med exemplet nedan. Även om exemplet här är för RHEL/CentOS, men stegen är allmänna och kan användas för alla kompatibla Linux-operativsystem som Ubuntu (16.04, 18.04 19.04, 20.04) och SLES (12 SP4 och 15). Fler exempel för andra distributioner finns på [lagringsplatsen azhpc-images.](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mellanoxofed.sh) Inkorgsdrivrutinerna fungerar också, men Mellanox OFED-drivrutinerna ger fler funktioner.

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
För Windows laddar du ned och installerar [Mellanox OFED för Windows-drivrutiner.](https://www.mellanox.com/products/adapter-software/ethernet/windows/winof-2)

## <a name="enable-ip-over-infiniband-ib"></a>Aktivera IP över InfiniBand (IB)
Om du planerar att köra MPI-jobb behöver du vanligtvis inte IPoIB. MPI-biblioteket använder verbgränssnittet för IB-kommunikation (såvida du inte uttryckligen använder TCP/IP-kanalen för MPI-biblioteket). Men om du har en app som använder TCP/IP för kommunikation och du vill köra över IB kan du använda IPoIB över IB-gränssnittet. Använd följande kommandon (för RHEL/CentOS) för att aktivera IP över InfiniBand.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=n/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om att installera och köra olika [MPI-bibliotek som stöds](setup-mpi.md) på de virtuella datorerna.
- Läs [översikten över HBv3-serien och](hbv3-series-overview.md) [översikten över HC-serien.](hc-series-overview.md)
- Läs om de senaste meddelandena, HPC-arbetsbelastningsexempel och prestandaresultat [på Azure Compute Tech Community Blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- En arkitekturvy på högre nivå över körning av HPC-arbetsbelastningar finns [i Hpc (Databehandling med höga prestanda) på Azure.](/azure/architecture/topics/high-performance-computing/)
