---
title: Konfigurera gränssnitt för meddelande överföring för HPC-Azure-Virtual Machines | Microsoft Docs
description: Lär dig hur du konfigurerar MPI för HPC i Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 08/06/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 9804ed23da4cb9ccbb7515cec03fcc9b4147f749
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673259"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Konfigurera Message Passing Interface för HPC

[MPI (Message Passing Interface)](https://en.wikipedia.org/wiki/Message_Passing_Interface) är ett öppet bibliotek och de facto-standarder som används för distribuerat minne parallellisering. Den används ofta för många HPC-arbetsbelastningar. HPC-arbetsbelastningar på virtuella datorer med [RDMA](../../sizes-hpc.md#rdma-capable-instances) [-](../../sizes-hpc.md) kapacitet och [N-serien](../../sizes-gpu.md) kan använda MPI för att kommunicera via ett InfiniBand-nätverk med låg latens och hög bandbredd.

SR-IOV-aktiverade VM-storlekar på Azure (HBv2, HB, HC, NCv3, NDv2) gör att du kan använda nästan vilken variant av MPI som ska användas med Mellanox OFED. På virtuella datorer som inte är SR-IOV-aktiverade använder MPI-implementeringar som stöds av Microsoft Network Direct (ND)-gränssnittet för att kommunicera mellan virtuella datorer. Därför stöds endast Microsoft MPI (MS-MPI) 2012 R2 eller senare och Intel MPI 5. x-versioner. Senare versioner (2017, 2018) av Intel MPI runtime-biblioteket kan vara kompatibla med Azure RDMA-drivrutinerna.

För SR-IOV-aktiverade [RDMA-kompatibla virtuella datorer](../../sizes-hpc.md#rdma-capable-instances)är [CentOS-HPC version 7,6 eller en senare](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) version av VM-avbildningar på Marketplace optimerad och FÖRinstallerad med ofed-drivrutinerna för RDMA och olika vanliga MPI-bibliotek och vetenskapliga data behandlings paket och är det enklaste sättet att komma igång.

Även om exemplen här är för RHEL/CentOS, men stegen är allmänna och kan användas för alla kompatibla Linux-operativsystem som Ubuntu (16,04, 18,04 19,04, 20,04) och SLES (12 SP4 och 15). Fler exempel på hur du konfigurerar andra MPI-implementeringar på andra distributioner finns på [azhpc-images lagrings platsen](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh).

> [!NOTE]
> Om du kör MPI-jobb på SR-IOV-aktiverade virtuella datorer måste du ställa in partitionsnyckel (p-Keys) över en klient för isolering och säkerhet. Följ stegen i avsnittet [identifiera partitionsalternativ](#discover-partition-keys) om du vill ha mer information om hur du fastställer p-nyckel värden och ställer in dem korrekt för ett MPI-jobb.

## <a name="ucx"></a>UCX

[Unified Communication X (UCX)](https://github.com/openucx/ucx) är ett ramverk med kommunikations-API: er för HPC. Den är optimerad för MPI-kommunikation över InfiniBand och fungerar med många MPI-implementeringar som OpenMPI och MPICH.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="hpc-x"></a>HPC-X

[HPC-X-programverktyget](https://www.mellanox.com/products/hpc-x-toolkit) innehåller UCX och HCOLL.

```bash
HPCX_VERSION="v2.6.0"
HPCX_DOWNLOAD_URL=https://azhpcstor.blob.core.windows.net/azhpc-images-store/hpcx-v2.6.0-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
get --retry-connrefused --tries=3 --waitretry=5 $HPCX_DOWNLOAD_URL
tar -xvf hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
mv hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64 ${INSTALL_PREFIX}
HPCX_PATH=${INSTALL_PREFIX}/hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64
```

Köra HPC-X

```bash
${HPCX_PATH}mpirun -np 2 --map-by ppr:2:node -x UCX_TLS=rc ${HPCX_PATH}/ompi/tests/osu-micro-benchmarks-5.3.2/osu_latency
```

## <a name="openmpi"></a>OpenMPI

Installera UCX enligt beskrivningen ovan. HCOLL är en del av [HPC-X-programverktyget](https://www.mellanox.com/products/hpc-x-toolkit) och kräver ingen särskild installation.

Installera OpenMPI från de paket som är tillgängliga i lagrings platsen.

```bash
sudo yum install –y openmpi
```

Bygg OpenMPI.

```bash
OMPI_VERSION="4.0.3"
OMPI_DOWNLOAD_URL=https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-${OMPI_VERSION}.tar.gz
wget --retry-connrefused --tries=3 --waitretry=5 $OMPI_DOWNLOAD_URL
tar -xvf openmpi-${OMPI_VERSION}.tar.gz
cd openmpi-${OMPI_VERSION}
./configure --prefix=${INSTALL_PREFIX}/openmpi-${OMPI_VERSION} --with-ucx=${UCX_PATH} --with-hcoll=${HCOLL_PATH} --enable-mpirun-prefix-by-default --with-platform=contrib/platform/mellanox/optimized && make -j$(nproc) && make install
```

Kör OpenMPI.

```bash
${INSTALL_PREFIX}/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Kontrol lera partitionsnyckel som nämnts ovan.

## <a name="intel-mpi"></a>Intel-MPI

Ladda ned ditt val av [Intel-MPI](https://software.intel.com/mpi-library/choose-download). Ändra I_MPI_FABRICS-miljövariabeln beroende på version. För Intel MPI 2018 använder du `I_MPI_FABRICS=shm:ofa` och för 2019 `I_MPI_FABRICS=shm:ofi` .

### <a name="non-sr-iov-vms"></a>Icke-SR-IOV-VM
För icke-SR-IOV-datorer är ett exempel på hur du hämtar den [kostnads fria utvärderings versionen](https://registrationcenter.intel.com/en/forms/?productid=1740) av 5. x:
```bash
wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
```
Installations anvisningar finns i [installations guiden för Intel MPI Library](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).
Alternativt kanske du vill aktivera ptrace för processer som inte är rot processer (behövs för de senaste versionerna av Intel MPI).
```bash
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```

### <a name="suse-linux"></a>SUSE Linux
För SUSE Linux Enterprise Server VM-avbildningsfiler – SLES 12 SP3 för HPC, SLES 12 SP3 for HPC (Premium), SLES 12 SP1 för HPC, SLES 12 SP1 för HPC (Premium), SLES 12 SP4 och SLES 15, installeras RDMA-drivrutinerna och Intel MPI-paket distribueras på den virtuella datorn. Installera Intel-MPI genom att köra följande kommando:
```bash
sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
```

## <a name="mpich"></a>MPICH

Installera UCX enligt beskrivningen ovan. Bygg MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=${UCX_PATH} --prefix=${INSTALL_PREFIX} --with-device=ch4:ucx
make -j 8 && make install
```

Kör MPICH.

```bash
${INSTALL_PREFIX}/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Kontrol lera partitionsnyckel som nämnts ovan.

## <a name="mvapich2"></a>MVAPICH2

Bygg MVAPICH2.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=${INSTALL_PREFIX}
make -j 8 && make install
```

Kör MVAPICH2.

```bash
${INSTALL_PREFIX}/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>Plattform MPI Community Edition

Installera nödvändiga paket för plattformen MPI.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Följ installations processen.

## <a name="osu-mpi-benchmarks"></a>OSU MPI-prestandatest

Ladda ned [Osu MPI-benchmarks](http://mvapich.cse.ohio-state.edu/benchmarks/) och untar.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Bygg benchmarks med ett visst MPI-bibliotek:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI-benchmarks finns i `mpi/` mappen.


## <a name="discover-partition-keys"></a>Identifiera partitionerings nycklar

Identifiera partitionsalternativ (p-Keys) för att kommunicera med andra virtuella datorer inom samma klient organisation (tillgänglighets uppsättning eller skalnings uppsättning för virtuell dator).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

Den större av de två är den klient nyckel som ska användas med MPI. Exempel: om följande är p-Keys ska 0x800b användas med MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Använd en annan partition än standard-0x7FFF (partition Key). UCX kräver att MSB för p-nyckeln har rensats. Ange till exempel UCX_IB_PKEY som 0x000B för 0x800b.

Observera också att PKEYs förblir samma så länge klient organisationen (tillgänglighets uppsättning eller virtuell dator skal uppsättning) finns. Detta gäller även när noder läggs till/tas bort. Nya klienter får olika PKEYs.


## <a name="set-up-user-limits-for-mpi"></a>Konfigurera användar begränsningar för MPI

Konfigurera användar begränsningar för MPI.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```

## <a name="set-up-ssh-keys-for-mpi"></a>Konfigurera SSH-nycklar för MPI

Konfigurera SSH-nycklar för MPI-typer som kräver det.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

Ovanstående syntax förutsätter en delad arbets katalog, annars måste du kopiera SSH-katalogen till varje nod.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [InfiniBand-aktiverade](../../sizes-hpc.md#rdma-capable-instances) virtuella datorer i [H-serien](../../sizes-hpc.md) och [N-serien](../../sizes-gpu.md)
- Läs översikten över [HB-serien](hb-series-overview.md) och [HC-serien](hc-series-overview.md) för att lära dig mer om att konfigurera arbets belastningar optimalt för prestanda och skalbarhet.
- Läs om de senaste meddelandena och några HPC-exempel och resultat i [Azure Compute Tech community-Bloggar](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- En arkitektur för högre nivå för att köra HPC-arbetsbelastningar finns i [HPC (data behandling med höga prestanda) i Azure](/azure/architecture/topics/high-performance-computing/).
