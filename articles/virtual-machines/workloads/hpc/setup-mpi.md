---
title: Konfigurera Message Passing Interface (MPI) för HPC – Azure Virtual Machines | Microsoft Docs
description: Lär dig hur du ställer in MPI för HPC på Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/16/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f43fc94174ebdcfdf447d3635a696193959849fa
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600305"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Konfigurera Message Passing Interface hpc

MPI [(Message Passing Interface)](https://en.wikipedia.org/wiki/Message_Passing_Interface) är ett öppet bibliotek och en standard för distribuerad minnesparallellisering. Det används ofta för många HPC-arbetsbelastningar. HPC-arbetsbelastningar på [RDMA-kompatibla](../../sizes-hpc.md#rdma-capable-instances) virtuella datorer i H-serien och [N-serien](../../sizes-gpu.md) kan använda MPI för att kommunicera via InfiniBand-nätverket med kort svarstid och hög bandbredd. [](../../sizes-hpc.md)
- Med SR-IOV-aktiverade VM-storlekar på Azure kan nästan alla varianter av MPI användas med Mellanox OFED.
- På icke-SR-IOV-aktiverade virtuella datorer använder MPI-implementeringar som stöds Gränssnittet Microsoft Network Direct (ND) för att kommunicera mellan virtuella datorer. Därför stöds endast Microsoft MPI (MS-MPI) 2012 R2 eller senare och Intel MPI 5.x-versioner. Senare versioner (2017, 2018) av Intel MPI-körningsbiblioteket kan vara kompatibla med Azure RDMA-drivrutinerna.

För SR-IOV-aktiverade [RDMA-kompatibla](../../sizes-hpc.md#rdma-capable-instances)virtuella datorer är [CentOS-HPC VM-avbildningar](configure.md#centos-hpc-vm-images) version 7.6 och senare lämpliga. Dessa VM-avbildningar är optimerade och förinstallerade med OFED-drivrutinerna för RDMA och olika vanliga MPI-bibliotek och vetenskapliga databehandlingspaket och är det enklaste sättet att komma igång.

Exemplen här är för RHEL/CentOS, men stegen är allmänna och kan användas för alla kompatibla Linux-operativsystem som Ubuntu (16.04, 18.04 19.04, 20.04) och SLES (12 SP4 och 15). Fler exempel för att konfigurera andra MPI-implementeringar på andra distributioner finns på [lagringsplatsen azhpc-images.](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh)

> [!NOTE]
> Att köra MPI-jobb på SR-IOV-aktiverade virtuella datorer med vissa MPI-bibliotek (till exempel plattforms-MPI) kan kräva att partitionsnycklar (p-nycklar) konfigureras i en klient för isolering och säkerhet. Följ stegen i avsnittet [Identifiera partitionsnycklar](#discover-partition-keys) för mer information om hur du fastställer p-nyckelvärdena och anger dem korrekt för ett MPI-jobb med det MPI-biblioteket.

> [!NOTE]
> Kodfragmenten nedan är exempel. Vi rekommenderar att du använder de senaste stabila versionerna av paketen eller refererar till [lagringsplatsen azhpc-images](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh).

## <a name="choosing-mpi-library"></a>Välja MPI-bibliotek
Om ett HPC-program rekommenderar ett visst MPI-bibliotek provar du den versionen först. Om du har flexibilitet vad gäller vilket MPI du kan välja och vill ha bästa prestanda kan du prova HPC-X. Generellt presterar HPC-X MPI bäst med hjälp av UCX-ramverket för InfiniBand-gränssnittet och drar nytta av alla Mellanox InfiniBand-funktioner för maskin- och programvara. Dessutom är HPC-X och OpenMPI ABI-kompatibla, så att du dynamiskt kan köra ett HPC-program med HPC-X som har skapats med OpenMPI. På samma sätt är Intel MPI, MVAPICH och MPICH ABI-kompatibla.

Följande bild illustrerar arkitekturen för de populära MPI-biblioteken.

![Arkitektur för populära MPI-bibliotek](./media/mpi-architecture.png)

## <a name="ucx"></a>UCX

[Unified Communication X (UCX)](https://github.com/openucx/ucx) är ett ramverk för kommunikations-API:er för HPC. Den är optimerad för MPI-kommunikation över InfiniBand och fungerar med många MPI-implementeringar som OpenMPI och MPICH.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

> [!NOTE]
> De senaste versionerna av UCX har åtgärdat ett problem där rätt InfiniBand-gränssnitt väljs i närvaro av flera NIC-gränssnitt. [](https://github.com/openucx/ucx/pull/5965) Mer information [här om](hb-hc-known-issues.md#accelerated-networking-on-hb-hc-hbv2-and-ndv2) hur du kör MPI över InfiniBand när accelererat nätverk är aktiverat på den virtuella datorn.

## <a name="hpc-x"></a>HPC-X

[HPC-X-programvaruverktygen](https://www.mellanox.com/products/hpc-x-toolkit) innehåller UCX och HCOLL och kan byggas mot UCX.

```bash
HPCX_VERSION="v2.6.0"
HPCX_DOWNLOAD_URL=https://azhpcstor.blob.core.windows.net/azhpc-images-store/hpcx-v2.6.0-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
get --retry-connrefused --tries=3 --waitretry=5 $HPCX_DOWNLOAD_URL
tar -xvf hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
mv hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64 ${INSTALL_PREFIX}
HPCX_PATH=${INSTALL_PREFIX}/hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64
```

Följande kommando illustrerar några rekommenderade mpirun-argument för HPC-X och OpenMPI.
```bash
mpirun -n $NPROCS --hostfile $HOSTFILE --map-by ppr:$NUMBER_PROCESSES_PER_NUMA:numa:pe=$NUMBER_THREADS_PER_PROCESS -report-bindings $MPI_EXECUTABLE
```
där:

|Parameter|Beskrivning                                        |
|---------|---------------------------------------------------|
|`NPROCS`   |Anger antalet MPI-processer. Exempel: `-n 16`.|
|`$HOSTFILE`|Anger en fil som innehåller värdnamnet eller IP-adressen för att ange var MPI-processerna ska köras. Exempel: `--hostfile hosts`.|
|`$NUMBER_PROCESSES_PER_NUMA`   |Anger antalet MPI-processer som ska köras i varje NUMA-domän. Om du till exempel vill ange fyra MPI-processer per NUMA använder du `--map-by ppr:4:numa:pe=1` .|
|`$NUMBER_THREADS_PER_PROCESS`  |Anger antalet trådar per MPI-process. Om du till exempel vill ange en MPI-process och fyra trådar per NUMA använder du `--map-by ppr:1:numa:pe=4` .|
|`-report-bindings` |Skriver ut mappning av MPI-processer till kärnor, vilket är användbart för att verifiera att MPI-processens stiftning är korrekt.|
|`$MPI_EXECUTABLE`  |Anger den körbara MPI-filen som skapats för länkning i MPI-bibliotek. MPI-kompilatoromslutningar gör detta automatiskt. Till exempel: `mpicc` eller `mpif90` .|

Ett exempel på hur du kör mikrobenchmark för OSU-svarstid är följande:
```bash
${HPCX_PATH}mpirun -np 2 --map-by ppr:2:node -x UCX_TLS=rc ${HPCX_PATH}/ompi/tests/osu-micro-benchmarks-5.3.2/osu_latency
```

### <a name="optimizing-mpi-collectives"></a>Optimera MPI-kollektiv

MPI Collective Communication Primitives erbjuder ett flexibelt, portabelt sätt att implementera gruppkommunikationsåtgärder. De används ofta i olika vetenskapliga parallella program och har en betydande inverkan på den övergripande programprestandan. Se [TechCommunity-artikeln](https://techcommunity.microsoft.com/t5/azure-compute/optimizing-mpi-collective-communication-using-hpc-x-on-azurehpc/ba-p/1356740) för information om konfigurationsparametrar för att optimera samlade kommunikationsprestanda med HPC-X och HCOLL-bibliotek för samlad kommunikation.

Om du till exempel misstänker att ditt nära kopplade MPI-program utför en stor mängd samlad kommunikation kan du prova att aktivera hierarkiska kollektiv (HCOLL). Använd följande parametrar för att aktivera dessa funktioner.
```bash
-mca coll_hcoll_enable 1 -x HCOLL_MAIN_IB=<MLX device>:<Port>
```

> [!NOTE] 
> Med HPC-X 2.7.4+ kan det vara nödvändigt att uttryckligen skicka LD_LIBRARY_PATH om UCX-versionen på MOFED jämfört med hpc-x är annorlunda.

## <a name="openmpi"></a>Openmpi

Installera UCX enligt beskrivningen ovan. HCOLL är en del [av HPC-X-programvaruverktygen](https://www.mellanox.com/products/hpc-x-toolkit) och kräver ingen särskild installation.

OpenMPI kan installeras från de paket som är tillgängliga i lagringsplatsen.

```bash
sudo yum install –y openmpi
```

Vi rekommenderar att du skapar en senaste, stabil version av OpenMPI med UCX.

```bash
OMPI_VERSION="4.0.3"
OMPI_DOWNLOAD_URL=https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-${OMPI_VERSION}.tar.gz
wget --retry-connrefused --tries=3 --waitretry=5 $OMPI_DOWNLOAD_URL
tar -xvf openmpi-${OMPI_VERSION}.tar.gz
cd openmpi-${OMPI_VERSION}
./configure --prefix=${INSTALL_PREFIX}/openmpi-${OMPI_VERSION} --with-ucx=${UCX_PATH} --with-hcoll=${HCOLL_PATH} --enable-mpirun-prefix-by-default --with-platform=contrib/platform/mellanox/optimized && make -j$(nproc) && make install
```

För optimala prestanda kör du OpenMPI med `ucx` och `hcoll` . Se även exemplet med [HPC-X](#hpc-x).

```bash
${INSTALL_PREFIX}/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Kontrollera partitionsnyckeln enligt ovan.

## <a name="intel-mpi"></a>Intel MPI

Ladda ned valfri version av [Intel MPI](https://software.intel.com/mpi-library/choose-download). Intel MPI 2019-versionen växlade från OPEN Fabrics Alliance (OFA)-ramverket till OPEN Fabrics Interfaces (OFI)-ramverket och har för närvarande stöd för libfabric. Det finns två providers för InfiniBand-stöd: mlx och verb.
Ändra I_MPI_FABRICS miljövariabel beroende på version.
- Intel MPI 2019 och 2021: använd `I_MPI_FABRICS=shm:ofi` , `I_MPI_OFI_PROVIDER=mlx` . Providern `mlx` använder UCX. Användningen av verb har visat sig vara instabil och mindre bra. Mer information [finns i TechCommunity-artikeln.](https://techcommunity.microsoft.com/t5/azure-compute/intelmpi-2019-on-azure-hpc-clusters/ba-p/1403149)
- Intel MPI 2018: använd `I_MPI_FABRICS=shm:ofa`
- Intel MPI 2016: använda `I_MPI_DAPL_PROVIDER=ofa-v2-ib0`

Här är några föreslagna mpirun-argument för Intel MPI 2019 uppdatering 5+.
```bash
export FI_PROVIDER=mlx
export I_MPI_DEBUG=5
export I_MPI_PIN_DOMAIN=numa

mpirun -n $NPROCS -f $HOSTFILE $MPI_EXECUTABLE
```
där:

|Parameter|Beskrivning                                        |
|---------|---------------------------------------------------|
|`FI_PROVIDER`  |Anger vilken libfabric-provider som ska användas, vilket påverkar det API, protokoll och nätverk som används. verb är ett annat alternativ, men vanligtvis ger mlx bättre prestanda.|
|`I_MPI_DEBUG`|Anger nivån för extra felsökningsutdata, som kan ge information om var processer är fästa och vilket protokoll och nätverk som används.|
|`I_MPI_PIN_DOMAIN` |Anger hur du vill fästa dina processer. Du kan till exempel fästa till kärnor, sockets eller NUMA-domäner. I det här exemplet anger du den här miljövariabeln till numa, vilket innebär att processer fästs på NUMA-noddomäner.|

### <a name="optimizing-mpi-collectives"></a>Optimera MPI-kollektiv

Det finns några andra alternativ som du kan prova, särskilt om de samlade åtgärderna förbrukar mycket tid. Intel MPI 2019 update 5+ stöder provide mlx och använder UCX-ramverket för att kommunicera med InfiniBand. Den stöder även HCOLL.
```bash
export FI_PROVIDER=mlx
export I_MPI_COLL_EXTERNAL=1
```

### <a name="non-sr-iov-vms"></a>Virtuella datorer som inte är SR-IOV

För virtuella datorer som inte är SR-IOV är ett exempel på nedladdning av den kostnadsfria utvärderingsversionen 5.x [runtime](https://registrationcenter.intel.com/en/forms/?productid=1740) följande:
```bash
wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
```
Installationssteg finns i installationsguiden [för Intel MPI-bibliotek.](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html)
Du kan också vilja aktivera ptrace för icke-rotbaserade icke-felsökningsprocesser (krävs för de senaste versionerna av Intel MPI).
```bash
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```

### <a name="suse-linux"></a>SUSE Linux
För SUSE Linux Enterprise Server VM-avbildningsversioner – SLES 12 SP3 för HPC, SLES 12 SP3 för HPC (Premium), SLES 12 SP1 för HPC, SLES 12 SP1 för HPC (Premium), SLES 12 SP4 och SLES 15, installeras RDMA-drivrutinerna och Intel MPI-paketen distribueras på den virtuella datorn. Installera Intel MPI genom att köra följande kommando:
```bash
sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
```

## <a name="mvapich"></a>MVAPICH

Följande är ett exempel på hur du skapar MVAPICH2. Observera att nyare versioner kan vara tillgängliga än vad som används nedan.
```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=${INSTALL_PREFIX}
make -j 8 && make install
```

Ett exempel på hur du kör mikrobenchmark för OSU-svarstid är följande:
```bash
${INSTALL_PREFIX}/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

Följande lista innehåller flera rekommenderade `mpirun` argument.
```bash
export MV2_CPU_BINDING_POLICY=scatter
export MV2_CPU_BINDING_LEVEL=numanode
export MV2_SHOW_CPU_BINDING=1
export MV2_SHOW_HCA_BINDING=1

mpirun -n $NPROCS -f $HOSTFILE $MPI_EXECUTABLE
```
där:

|Parameter|Beskrivning                                        |
|---------|---------------------------------------------------|
|`MV2_CPU_BINDING_POLICY`   |Anger vilken bindningsprincip som ska användas, vilket påverkar hur processer fästs på kärn-ID:er. I det här fallet anger du punktdiagram, så att processerna är jämnt spridda mellan NUMA-domänerna.|
|`MV2_CPU_BINDING_LEVEL`|Anger var processer ska fästas. I det här fallet ställer du in den på numanode, vilket innebär att processer är fästa på enheter i NUMA-domäner.|
|`MV2_SHOW_CPU_BINDING` |Anger om du vill hämta felsökningsinformation om var processerna är fästa.|
|`MV2_SHOW_HCA_BINDING` |Anger om du vill hämta felsökningsinformation om vilket värdkanalkort varje process använder.|

## <a name="platform-mpi"></a>Plattforms-MPI

Installera nödvändiga paket för Platform MPI Community Edition.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Följ installationsprocessen.

Följande kommandon är exempel på att köra MPI-pingpong och allreduce med plattforms-MPI på virtuella HBv3-datorer med CentOS-HPC 7.6, 7.8 och 8.1 VM-avbildningar.

```bash
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:1,10.0.0.9:1 -np 2 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 pingpong
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:120,10.0.0.9:120 -np 240 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 allreduce -npmin 240
```


## <a name="mpich"></a>MPICH

Installera UCX enligt beskrivningen ovan. Skapa MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=${UCX_PATH} --prefix=${INSTALL_PREFIX} --with-device=ch4:ucx
make -j 8 && make install
```

Köra MPICH.

```bash
${INSTALL_PREFIX}/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Kontrollera din partitionsnyckel enligt ovan.

## <a name="osu-mpi-benchmarks"></a>OSU MPI-benchmark

Ladda [ned OSU MPI-benchmark och](http://mvapich.cse.ohio-state.edu/benchmarks/) ta bort mål.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Skapa benchmark-mått med ett visst MPI-bibliotek:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI-benchmarks finns under `mpi/` mappen .


## <a name="discover-partition-keys"></a>Identifiera partitionsnycklar

Identifiera partitionsnycklar (p-nycklar) för kommunikation med andra virtuella datorer inom samma klientorganisation (tillgänglighetsuppsättning eller VM-skalningsuppsättning).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

Ju större av de två är klientnyckeln som ska användas med MPI. Exempel: Om följande är p-nycklar bör 0x800b användas med MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Använd en annan partition än standardpartitionsnyckeln (0x7fff). UCX kräver att MSB för p-nyckeln rensas. Ange till exempel UCX_IB_PKEY som 0x000b för 0x800b.

Observera också att så länge klientorganisationen (tillgänglighetsuppsättningen eller VM-skalningsuppsättningen) finns, förblir PKEYs desamma. Detta gäller även när noder läggs till/tas bort. Nya klienter får olika PKEYs.


## <a name="set-up-user-limits-for-mpi"></a>Konfigurera användargränser för MPI

Konfigurera användargränser för MPI.

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

Syntaxen ovan förutsätter en delad hemkatalog, annars måste .ssh-katalogen kopieras till varje nod.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [infiniBand-aktiverade virtuella](../../sizes-hpc.md#rdma-capable-instances) [datorer i H-serien](../../sizes-hpc.md) [och N-serien](../../sizes-gpu.md)
- Läs [översikten över HBv3-serien och](hbv3-series-overview.md) [översikten över HC-serien.](hc-series-overview.md)
- Läs om de senaste meddelandena, HPC-arbetsbelastningsexempel och prestandaresultat på [Azure Compute Tech Community Blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- En arkitekturvy på högre nivå över körning av HPC-arbetsbelastningar finns [i Hpc (Databehandling med höga prestanda) på Azure.](/azure/architecture/topics/high-performance-computing/)
