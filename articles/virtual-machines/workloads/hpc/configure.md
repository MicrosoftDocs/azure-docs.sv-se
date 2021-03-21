---
title: Konfiguration och optimering av InfiniBand-aktiverade H-och N-seriens Azure-Virtual Machines
description: Lär dig mer om att konfigurera och optimera InfiniBand-aktiverade virtuella datorer i H-serien och N-serien för HPC.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 0c6f5dc55f7406aba7d6e3dc1a278b57fe4ec9ba
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721277"
---
# <a name="configure-and-optimize-vms"></a>Konfigurera och optimera virtuella datorer

Den här artikeln ger vägledning om hur du konfigurerar och optimerar de InfiniBand-aktiverade virtuella datorer i [H-serien](../../sizes-hpc.md) och [N-serien](../../sizes-gpu.md) för HPC.

## <a name="vm-images"></a>VM-avbildningar
På InfiniBand-aktiverade virtuella datorer krävs lämpliga driv rutiner för att aktivera RDMA.
- De [virtuella CentOS-HPC-avbildningarna](#centos-hpc-vm-images) på Marketplace är förkonfigurerade med lämpliga IB-drivrutiner och är det enklaste sättet att komma igång.
- De [virtuella Ubuntu-avbildningarna](#ubuntu-vm-images) kan konfigureras med rätt IB-drivrutiner. Vi rekommenderar att du skapar [anpassade VM-avbildningar](../../linux/tutorial-custom-images.md) med lämpliga driv rutiner och konfiguration och återanvänder dem på ett återkommande sätt.

På GPU-aktiverade virtuella datorer i [N-serien](../../sizes-gpu.md) krävs dessutom lämpliga GPU-drivrutiner som kan läggas till via [VM-tilläggen](../../extensions/hpccompute-gpu-linux.md) eller [manuellt](../../linux/n-series-driver-setup.md). Vissa VM-avbildningar på Marketplace levereras också som förinstallerade med nVidia GPU-drivrutinerna, inklusive vissa VM-avbildningar från NVIDIA.

### <a name="centos-hpc-vm-images"></a>CentOS-HPC VM-avbildningar

#### <a name="sr-iov-enabled-vms"></a>SR-IOV-aktiverade virtuella datorer
För SR-IOV-aktiverade [RDMA-kompatibla virtuella datorer](../../sizes-hpc.md#rdma-capable-instances)är [CentOS-HPC-avbildningar på Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) version 7,6 och senare lämpliga. De här VM-avbildningarna är optimerade och förinstallerade med OFED-drivrutinerna för RDMA och olika vanliga MPI-bibliotek och vetenskapliga data behandlings paket och är det enklaste sättet att komma igång.
- Information om vad som ingår i CentOS-HPC version 7,6 och senare VM-avbildningar finns i en [TechCommunity-artikel](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557).
- Skript som används för att skapa CentOS-HPC version 7,6 och senare VM-avbildningar från en CentOS Marketplace-avbildning finns på [azhpc-images lagrings platsen](https://github.com/Azure/azhpc-images/tree/master/centos).
  
> [!NOTE] 
> De senaste Azure HPC Marketplace-avbildningarna har Mellanox OFED 5,1 och senare, som inte stöder ConnectX3-Pro InfiniBand-kort. SR-IOV-aktiverade virtuella datorer i N-serien med FDR InfiniBand (t. ex. NCv3 och äldre) kommer att kunna använda följande VM-avbildning för CentOS-HPC eller äldre versioner från Marketplace:
>- OpenLogic: CentOS-HPC: 7.6:7.6.2020062900
>- OpenLogic: CentOS-HPC: 7_6gen2:7.6.2020062901
>- OpenLogic: CentOS-HPC: 7.7:7.7.2020062600
>- OpenLogic: CentOS-HPC: 7_7-Gen2:7.7.2020062601
>- OpenLogic: CentOS-HPC: 8_1:8.1.2020062400
>- OpenLogic: CentOS-HPC: 8_1-Gen2:8.1.2020062401

#### <a name="non-sr-iov-enabled-vms"></a>Ej SR-IOV-aktiverade virtuella datorer
För icke-SR-IOV-aktiverade [RDMA-kompatibla virtuella datorer](../../sizes-hpc.md#rdma-capable-instances), CENTOS-HPC version 6,5 eller en senare version, är upp till 7,4 på Marketplace lämplig. För [virtuella datorer i H16-serien](../../h-series.md)rekommenderas till exempel version 7,1 till 7,4. De här VM-avbildningarna levereras i förväg med nätverks Direct-drivrutinerna för RDMA och Intel MPI version 5,1.

> [!NOTE]
> På dessa CentOS-baserade HPC-avbildningar för virtuella datorer som inte är SR-IOV-aktiverade, inaktive ras kernel-uppdateringar i **yum** -konfigurationsfilen. Detta beror på att NetworkDirect Linux RDMA-drivrutinerna distribueras som ett RPM-paket, och driv rutins uppdateringar kanske inte fungerar om kerneln uppdateras.

### <a name="rhelcentos-vm-images"></a>RHEL/CentOS VM-avbildningar
RHEL-eller CentOS-baserade VM-avbildningar på Marketplace kan konfigureras för användning i SR-IOV-aktiverade [virtuella datorer med RDMA-kapacitet](../../sizes-hpc.md#rdma-capable-instances). Läs mer om hur du [aktiverar InfiniBand](enable-infiniband.md) och [konfigurerar MPI](setup-mpi.md) på de virtuella datorerna.
- Skript som används för att skapa CentOS-HPC version 7,6 och senare VM-avbildningar från en CentOS Marketplace-avbildning från [azhpc-image lagrings platsen](https://github.com/Azure/azhpc-images/tree/master/centos) kan också användas.
  
> [!NOTE]
> Mellanox OFED 5,1 och senare stöder inte ConnectX3-Pro InfiniBand-kort på SR-IOV-aktiverade VM-storlekar i N-serien med FDR InfiniBand (t. ex. NCv3). Använd LTS Mellanox OFED version 4.9-0.1.7.0 eller äldre på den virtuella datorn i N-serien med ConnectX3-Pro-kort. Mer information finns [här](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed).

### <a name="ubuntu-vm-images"></a>Ubuntu VM-avbildningar
Ubuntu Server 16,04 LTS, 18,04 LTS och 20,04 LTS VM-avbildningar på Marketplace stöds för både SR-IOV-och icke-SR-IOV [RDMA-kompatibla virtuella datorer](../../sizes-hpc.md#rdma-capable-instances). Läs mer om hur du [aktiverar InfiniBand](enable-infiniband.md) och [konfigurerar MPI](setup-mpi.md) på de virtuella datorerna.
- Instruktioner för att aktivera InfiniBand på Ubuntu VM-avbildningar finns i en [TechCommunity-artikel](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351).
- Skript som används för att skapa Ubuntu 18,04-och 20,04 LTS-baserade HPC VM-avbildningar från en Base Ubuntu Marketplace-avbildning finns på [azhpc-images lagrings platsen](https://github.com/Azure/azhpc-images/tree/master/ubuntu).

### <a name="suse-linux-enterprise-server-vm-images"></a>SUSE Linux Enterprise Server VM-avbildningar
SLES 12 SP3 för HPC, SLES 12 SP3 för HPC (Premium), SLES 12 SP1 för HPC, SLES 12 SP1 för HPC (Premium), SLES 12 SP4 och SLES 15 VM-avbildningar på Marketplace stöds. De här VM-avbildningarna levereras i förväg med nätverks Direct-drivrutinerna för RDMA och Intel MPI version 5,1. Läs mer om hur du [konfigurerar MPI](setup-mpi.md) på de virtuella datorerna.

## <a name="optimize-vms"></a>Optimera virtuella datorer

Följande är några valfria optimerings inställningar för förbättrad prestanda på den virtuella datorn.

### <a name="update-lis"></a>Uppdatera LIS

Om det behövs för funktionalitet eller prestanda kan [Linux Integration Services (LIS)-driv rutiner](../../linux/endorsed-distros.md) installeras eller uppdateras på ett OS-distributioner som stöds, särskilt distribueras med hjälp av en anpassad avbildning eller en äldre OS-version, till exempel CENTOS/RHEL 6. x eller tidigare version av 7. x.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>Frigör minne

Förbättra prestanda genom att automatiskt frigöra minne för att undvika åtkomst till fjärrminne.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Så här gör du detta kvar efter omstarter av virtuella datorer:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### <a name="disable-firewall-and-selinux"></a>Inaktivera brand Väggs-och SELinux

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
Om du vill kan WALinuxAgent vara inaktive rad som ett föret jobb-steg och aktive ras tillbaka efter maximal tillgänglighet för VM-resurser för HPC-arbetsbelastningen.


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om hur du [aktiverar InfiniBand](enable-infiniband.md) på den InfiniBand-aktiverade [H-serien](../../sizes-hpc.md) och [N-seriens](../../sizes-gpu.md) virtuella datorer.
- Lär dig mer om att installera olika [MPI-bibliotek som stöds](setup-mpi.md) och deras optimala konfiguration på de virtuella datorerna.
- Läs översikt över [HBv3-serien](hbv3-series-overview.md) och [HC-serien](hc-series-overview.md).
- Läs om de senaste meddelandena, HPC-arbetsbelastnings exempel och prestanda resultat på [Azure Compute Tech-Webbgruppens Bloggar](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- En arkitektur för högre nivå för att köra HPC-arbetsbelastningar finns i [HPC (data behandling med höga prestanda) i Azure](/azure/architecture/topics/high-performance-computing/).
