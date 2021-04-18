---
title: Felsöka kända problem med virtuella DATORER med HPC och GPU – Azure Virtual Machines | Microsoft Docs
description: Lär dig mer om felsökning av kända problem med HPC- och GPU VM-storlekar i Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/16/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f5bdae17126048da153f70bf27609bcc4b92fe21
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599595"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>Kända problem med virtuella datorer i H-serien och N-serien

Den här artikeln försöker lista de senaste vanliga problemen och deras lösningar när du använder de virtuella datorerna [HPC](../../sizes-hpc.md) och GPU i [H-serien](../../sizes-gpu.md) och N-serien.

## <a name="qp0-access-restriction"></a>qp0-åtkomstbegränsning

För att förhindra åtkomst på låg nivå av maskinvara som kan leda till säkerhetsproblem är köpar 0 inte tillgängligt för virtuella gäst datorer. Detta bör endast påverka åtgärder som vanligtvis är associerade med administration av ConnectX InfiniBand-nätverkskortet och som kör viss InfiniBand-diagnostik som ibdia diagnostict, men inte slutanvändarprogram.

## <a name="mofed-installation-on-ubuntu"></a>MOFED-installation på Ubuntu
På Ubuntu-18.04-baserade VM-avbildningar på Marketplace med kernelversion och senare är vissa äldre Mellanox OFED inkompatibla, vilket i vissa fall leder till en ökning av VM-starttiden på upp till `5.4.0-1039-azure #42` 30 minuter. Detta har rapporterats för både Mellanox OFED-versionerna 5.2-1.0.4.0 och 5.2-2.2.0.0. Problemet löses med Mellanox OFED 5.3-1.0.0.1.
Om det är nödvändigt att använda den inkompatibla OFED är en lösning att använda **avbildningen Canonical:UbuntuServer:18_04-lts-gen2:18.04.202101290** marketplace eller äldre och inte uppdatera kerneln.

## <a name="mpi-qp-creation-errors"></a>Fel vid skapande av MPI QP
Om det uppstår fel vid skapande av InfiniBand QP som visas nedan i körningen av MPI-arbetsbelastningar föreslår vi att du startar om den virtuella datorn och försöker arbetsbelastningen igen. Det här problemet kommer att åtgärdas i framtiden.

```bash
ib_mlx5_dv.c:150  UCX  ERROR mlx5dv_devx_obj_create(QP) failed, syndrome 0: Invalid argument
```

Du kan verifiera värdena för det maximala antalet köpar när problemet observeras på följande sätt.
```bash
[user@azurehpc-vm ~]$ ibv_devinfo -vv | grep qp
max_qp: 4096
```

## <a name="accelerated-networking-on-hb-hc-hbv2-and-ndv2"></a>Accelererat nätverk på HB, HC, HBv2 och NDv2

[Azure-accelererat nätverk](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) är nu tillgängligt på RDMA- och InfiniBand-kompatibla och SR-IOV-aktiverade VM-storlekar [HB,](../../hb-series.md) [HC,](../../hc-series.md) [HBv2](../../hbv2-series.md)och [NDv2.](../../ndv2-series.md) Den här funktionen kan nu förbättras i hela (upp till 30 Gbit/s) och svarstider i Azure Ethernet-nätverket. Även om detta är separat från RDMA-funktionerna i InfiniBand-nätverket kan vissa plattformsändringar för den här funktionen påverka beteendet för vissa MPI-implementeringar när jobb körs över InfiniBand. Mer specifikt kan InfiniBand-gränssnittet på vissa virtuella datorer ha ett något annorlunda namn (mlx5_1 till skillnad från tidigare mlx5_0) och detta kan kräva justeringar av MPI-kommandoraderna, särskilt när du använder UCX-gränssnittet (vanligtvis med OpenMPI och HPC-X). Den enklaste lösningen för närvarande är att använda den senaste HPC-X på avbildningarna av virtuella CentOS-HPC-datorer eller inaktivera accelererat nätverk om det inte behövs.
Mer information om detta finns i den här [TechCommunity-artikeln](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-and-hbv2/ba-p/2067965) med instruktioner om hur du kan åtgärda eventuella observerade problem.

## <a name="infiniband-driver-installation-on-non-sr-iov-vms"></a>Installation av InfiniBand-drivrutin på virtuella datorer som inte är SR-IOV

H16r, H16mr och NC24r är för närvarande inte SR-IOV-aktiverade. Lite information om infiniBand-stackens bication [finns här.](../../sizes-hpc.md#rdma-capable-instances)
InfiniBand kan konfigureras på SR-IOV-aktiverade VM-storlekar med OFED-drivrutiner medan vm-storlekar som inte är SR-IOV kräver ND-drivrutiner. Det här IB-stödet är tillgängligt på lämpligt [sätt för CentOS, RHEL och Ubuntu.](configure.md)

## <a name="duplicate-mac-with-cloud-init-with-ubuntu-on-h-series-and-n-series-vms"></a>Duplicera MAC med cloud-init med Ubuntu på virtuella datorer i H-serien och N-serien

Det finns ett känt problem med cloud-init på virtuella Ubuntu-datoravbildningar när IB-gränssnittet används. Detta kan inträffa antingen vid omstart av den virtuella datorn eller när du försöker skapa en VM-avbildning efter generalisering. Startloggarna för den virtuella datorn kan visa ett fel som liknar följande:
```console
“Starting Network Service...RuntimeError: duplicate mac found! both 'eth1' and 'ib0' have mac”.
```

Det här "duplicera MAC med cloud-init på Ubuntu" är ett känt problem. Detta löses i nyare kernels. Om problemet uppstår är lösningen:
1) Distribuera vm-avbildningen marketplace (Ubuntu 18.04)
2) Installera nödvändiga programvarupaket för att aktivera IB ([instruktion här](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351))
3) Redigera waagent.conf för att ändra EnableRDMA=y
4) Inaktivera nätverk i cloud-init
    ```console
    echo network: {config: disabled} | sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
    ```
5) Redigera nätverkskonfigurationsfilen för netplan som genererats av cloud-init för att ta bort MAC
    ```console
    sudo bash -c "cat > /etc/netplan/50-cloud-init.yaml" <<'EOF'
    network:
      ethernets:
        eth0:
          dhcp4: true
      version: 2
    EOF
    ```

## <a name="dram-on-hb-series-vms"></a>DRAM på virtuella datorer i HB-serien

Virtuella datorer i HB-serien kan bara exponera 228 GB RAM för virtuella gästbaserade datorer just nu. 458 GB på HBv2 och 448 GB på virtuella HBv3-datorer. Detta beror på en känd begränsning i Azure Hypervisor för att förhindra att sidor tilldelas till den lokala DRAM för AMD CCX:s (NUMA-domäner) som är reserverade för den virtuella gästdatorn.

## <a name="gss-proxy"></a>GSS-proxy

GSS Proxy har en känd bugg i CentOS/RHEL 7.5 som kan visas som en betydande prestanda- och svarstidsförsening när den används med NFS. Detta kan åtgärdas med:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Cacherensning

På HPC-system är det ofta praktiskt att rensa minnet när ett jobb har slutförts innan nästa användare tilldelas samma nod. När du har kört program i Linux kan det hända att ditt tillgängliga minne minskar medan buffertminnet ökar, trots att du inte kör några program.

![Skärmbild av kommandotolken före rensning](./media/known-issues/cache-cleaning-1.png)

Med `numactl -H` hjälp av visas vilka NUMAnode(er) som minnet buffrats med (eventuellt alla). I Linux kan användare rensa cacheminnen på tre sätt för att returnera buffrat eller cachelagrat minne till "ledigt". Du måste vara rot eller ha sudo-behörigheter.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Skärmbild av kommandotolken efter rensning](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Kernelvarningar

Du kan ignorera följande kernelvarningsmeddelanden när du startar en virtuell dator i HB-serien under Linux. Detta beror på en känd begränsning i Azure-hypervisor-programmet som kommer att åtgärdas över tid.

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```


## <a name="next-steps"></a>Nästa steg

- Läs [översikten över HB-serien och](hb-series-overview.md) [översikten över HC-serien](hc-series-overview.md) om du vill veta mer om hur du konfigurerar arbetsbelastningar optimalt för prestanda och skalbarhet.
- Läs om de senaste meddelandena, HPC-arbetsbelastningsexempel och prestandaresultat på [Azure Compute Tech Community Blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- En arkitekturvy på högre nivå av att köra HPC-arbetsbelastningar finns i [Databehandling med höga prestanda (HPC) på Azure.](/azure/architecture/topics/high-performance-computing/)
