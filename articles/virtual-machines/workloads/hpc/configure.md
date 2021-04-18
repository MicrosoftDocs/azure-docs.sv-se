---
title: Konfiguration och optimering av InfiniBand-aktiverade H-serier och N-serien i Azure Virtual Machines
description: Lär dig mer om att konfigurera och optimera infiniBand-aktiverade virtuella datorer i H-serien och N-serien för HPC.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 470d5efae68366b5cc96243bab4ebb8552771650
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600887"
---
# <a name="configure-and-optimize-vms"></a>Konfigurera och optimera virtuella datorer

Den här artikeln innehåller vägledning om hur du konfigurerar och optimerar de infiniBand-aktiverade virtuella datorerna i [H-serien](../../sizes-hpc.md) och [N-serien](../../sizes-gpu.md) för HPC.

## <a name="vm-images"></a>VM-avbildningar
På InfiniBand-aktiverade virtuella datorer krävs lämpliga drivrutiner för att aktivera RDMA.
- [Avbildningarna av virtuella CentOS-HPC-datorer](#centos-hpc-vm-images) på Marketplace är förkonfigurerade med lämpliga IB-drivrutiner och är det enklaste sättet att komma igång.
- [Avbildningar av virtuella Ubuntu-datorer](#ubuntu-vm-images) kan konfigureras med rätt IB-drivrutiner. Vi rekommenderar att du skapar anpassade [VM-avbildningar](../../linux/tutorial-custom-images.md) med lämpliga drivrutiner och konfigurationer och återanvänder dem återkommande.

På [GPU-aktiverade virtuella datorer i N-serien](../../sizes-gpu.md) krävs dessutom lämpliga GPU-drivrutiner som kan läggas till via [VM-tilläggen](../../extensions/hpccompute-gpu-linux.md) eller [manuellt.](../../linux/n-series-driver-setup.md) Vissa VM-avbildningar på Marketplace är också förinstallerade med Nvidia GPU-drivrutiner, inklusive vissa VM-avbildningar från Nvidia.

### <a name="centos-hpc-vm-images"></a>Avbildningar av virtuella CentOS-HPC-datorer

#### <a name="sr-iov-enabled-vms"></a>SR-IOV-aktiverade virtuella datorer
För SR-IOV-aktiverade [RDMA-kompatibla](../../sizes-hpc.md#rdma-capable-instances)virtuella datorer är [CentOS-HPC VM-avbildningar](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) på Marketplace version 7.6 och senare lämpliga. Dessa VM-avbildningar är optimerade och förinstallerade med OFED-drivrutinerna för RDMA och olika vanliga MPI-bibliotek och vetenskapliga databehandlingspaket och är det enklaste sättet att komma igång.
- Information om vad som ingår i avbildningarna av den virtuella datorn CentOS-HPC version 7.6 och senare finns i [techcommunity-artikeln](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557).
- Skript som används för att skapa avbildningarna av den virtuella datorn CentOS-HPC version 7.6 och senare från en grundläggande CentOS Marketplace-avbildning finns på [lagringsplatsen azhpc-images.](https://github.com/Azure/azhpc-images/tree/master/centos)
  
> [!NOTE] 
> De senaste Azure HPC Marketplace-avbildningarna har Mellanox OFED 5.1 och högre, som inte stöder ConnectX3-Pro InfiniBand-kort. Vm-storlekar i SR-IOV-aktiverade N-serien med FDR InfiniBand (t.ex. NCv3 och äldre) kommer att kunna använda följande CentOS-HPC VM-avbildning eller äldre versioner från Marketplace:
>- OpenLogic:CentOS-HPC:7.6:7.6.2020062900
>- OpenLogic:CentOS-HPC:7_6gen2:7.6.2020062901
>- OpenLogic:CentOS-HPC:7.7:7.7.2020062600
>- OpenLogic:CentOS-HPC:7_7-gen2:7.7.2020062601
>- OpenLogic:CentOS-HPC:8_1:8.1.2020062400
>- OpenLogic:CentOS-HPC:8_1-gen2:8.1.2020062401

#### <a name="non-sr-iov-enabled-vms"></a>Icke SR-IOV-aktiverade virtuella datorer
För icke-SR-IOV-aktiverade [RDMA-kompatibla](../../sizes-hpc.md#rdma-capable-instances)virtuella datorer är CentOS-HPC version 6.5 eller en senare version, upp till 7.4 på Marketplace lämpliga. För virtuella datorer i [H16-serien](../../h-series.md)rekommenderas till exempel versionerna 7.1 till 7.4. Dessa VM-avbildningar är förinstallerade med Network Direct-drivrutiner för RDMA och Intel MPI version 5.1.

> [!NOTE]
> På dessa CentOS-baserade HPC-avbildningar för icke-SR-IOV-aktiverade virtuella datorer inaktiveras kerneluppdateringar i **yum-konfigurationsfilen.** Det beror på att NetworkDirect Linux RDMA-drivrutiner distribueras som ett RPM-paket och drivrutinsuppdateringar kanske inte fungerar om kerneln uppdateras.

### <a name="rhelcentos-vm-images"></a>RHEL/CentOS VM-avbildningar
RHEL- eller CentOS-baserade icke-HPC VM-avbildningar på Marketplace kan konfigureras för användning på SR-IOV-aktiverade [RDMA-kompatibla virtuella datorer.](../../sizes-hpc.md#rdma-capable-instances) Läs mer om [att aktivera InfiniBand](enable-infiniband.md) [och konfigurera MPI](setup-mpi.md) på de virtuella datorerna.
- Skript som används för att skapa avbildningar av virtuella datorer med CentOS-HPC version 7.6 och senare från en grundläggande CentOS Marketplace-avbildning från [lagringsplatsen azhpc-images](https://github.com/Azure/azhpc-images/tree/master/centos) kan också användas.
  
> [!NOTE]
> Mellanox OFED 5.1 och högre stöder inte ConnectX3-Pro InfiniBand-kort på SR-IOV-aktiverade VM-storlekar i N-serien med FDR InfiniBand (t.ex. NCv3). Använd LTS Mellanox OFED version 4.9-0.1.7.0 eller äldre på virtuella datorer i N-serien med ConnectX3-Pro kort. Mer information finns [här.](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed)

### <a name="ubuntu-vm-images"></a>Avbildningar av virtuella Ubuntu-datorer
Ubuntu Server 16.04 LTS, 18.04 LTS och 20.04 LTS VM-avbildningar på Marketplace stöds för både SR-IOV- och icke-SR-IOV [RDMA-kompatibla virtuella datorer.](../../sizes-hpc.md#rdma-capable-instances) Läs mer om [att aktivera InfiniBand](enable-infiniband.md) och [konfigurera MPI](setup-mpi.md) på de virtuella datorerna.
- Anvisningar för att aktivera InfiniBand på avbildningar av virtuella Ubuntu-datorer finns i [en TechCommunity-artikel.](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)
- Skript som används för att skapa Ubuntu 18.04- och 20.04 LTS-baserade HPC VM-avbildningar från en grundläggande Ubuntu Marketplace-avbildning finns på [lagringsplatsen azhpc-images.](https://github.com/Azure/azhpc-images/tree/master/ubuntu)

### <a name="suse-linux-enterprise-server-vm-images"></a>SUSE Linux Enterprise Server VM-avbildningar
SLES 12 SP3 för HPC, SLES 12 SP3 för HPC (Premium), SLES 12 SP1 för HPC, SLES 12 SP1 för HPC (Premium), SLES 12 SP4- och SLES 15 VM-avbildningar på Marketplace stöds. Dessa VM-avbildningar är förinstallerade med Network Direct-drivrutiner för RDMA och Intel MPI version 5.1. Läs mer om [att konfigurera MPI](setup-mpi.md) på de virtuella datorerna.

## <a name="optimize-vms"></a>Optimera virtuella datorer

Följande är några valfria optimeringsinställningar för bättre prestanda på den virtuella datorn.

### <a name="update-lis"></a>Uppdatera LIS

Om det behövs för funktionalitet eller prestanda kan [LIS-drivrutiner (Linux Integration Services)](../../linux/endorsed-distros.md) installeras eller uppdateras i operativsystemdistributioner som stöds, särskilt distribueras med hjälp av en anpassad avbildning eller en äldre os-version som CentOS/RHEL 6.x eller en tidigare version av 7.x.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>Återta minne

Förbättra prestanda genom att automatiskt frigöra minne för att undvika fjärråtkomst till minnet.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Så här gör du för att bevara detta när den virtuella datorn har startats om:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### <a name="disable-firewall-and-selinux"></a>Inaktivera brandvägg och SELinux

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

### <a name="disable-cpupower"></a>Inaktivera cpupower

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

### <a name="configure-walinuxagent"></a>Konfigurera WALinuxAgent

```bash
sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
```
Alternativt kan WALinuxAgent inaktiveras som ett förjobbssteg och aktiveras tillbaka efter jobbet för maximal tillgänglighet för VM-resurser till HPC-arbetsbelastningen.


## <a name="next-steps"></a>Nästa steg

- Läs mer om [hur du aktiverar InfiniBand på](enable-infiniband.md) infiniBand-aktiverade virtuella datorer i [H-serien](../../sizes-hpc.md) och [N-serien.](../../sizes-gpu.md)
- Läs mer om att installera och köra olika [MPI-bibliotek som stöds](setup-mpi.md) på de virtuella datorerna.
- Läs [översikten över HBv3-serien och](hbv3-series-overview.md) [översikten över HC-serien.](hc-series-overview.md)
- Läs om de senaste meddelandena, HPC-arbetsbelastningsexempel och prestandaresultat på [Azure Compute Tech Community Blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- En arkitekturvy på högre nivå av att köra HPC-arbetsbelastningar finns [i Databehandling med höga prestanda (HPC) på Azure.](/azure/architecture/topics/high-performance-computing/)
