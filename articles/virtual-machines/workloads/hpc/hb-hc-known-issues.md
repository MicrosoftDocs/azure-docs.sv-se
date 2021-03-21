---
title: Felsöka kända problem med HPC-och GPU-VM – Azure Virtual Machines | Microsoft Docs
description: Lär dig mer om fel sökning av kända problem med HPC-och GPU-VM-storlekar i Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: e8d191dfed5b33116dadaf34b17d5f6525060e13
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721212"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>Kända problem med virtuella datorer i H-serien och N-serien

Den här artikeln försöker visa en lista över de senaste vanliga problemen och deras lösningar när de använder HPC-och [N-seriens](../../sizes-gpu.md) HPC [-](../../sizes-hpc.md) och GPU-datorer.

## <a name="mofed-installation-on-ubuntu"></a>MOFED installation på Ubuntu
På Ubuntu-18,04 är kernel-versionen 5.4.0-1041-Azure inte kompatibel med MOFED-versionerna 5.2-2 och 5.2-1.0.4.0. Vi rekommenderar att du återställer till kernel-version 5.4.0-1040-Azure eller använder en Marketplace-avbildning med äldre kernel och inte uppdaterar kärnan. Det här problemet förväntas lösas med en nyare MOFED (TBD).

## <a name="known-issues-on-hbv3"></a>Kända problem på HBv3
- För närvarande stöds bara InfiniBand på den virtuella datorn med 120 kärnor (Standard_HB120rs_v3).
- Azure-accelererade nätverk stöds för närvarande inte i HBv3-serien i alla regioner.

## <a name="accelerated-networking-on-hb-hc-hbv2-and-ndv2"></a>Accelererat nätverk på HB, HC, HBv2 och NDv2

[Azure-accelererat nätverk](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) är nu tillgängligt på RDMA-och InfiniBand-kompatibla och SR-IOV-aktiverade VM-storlekar [HB](../../hb-series.md), [HC](../../hc-series.md), [HBv2](../../hbv2-series.md)och [NDv2](../../ndv2-series.md). Den här funktionen kan nu utökas i hela (upp till 30 Gbit/s) och fördröjning i Azure Ethernet-nätverket. Även om detta är skilt från RDMA-funktionerna i InfiniBand-nätverket kan vissa plattforms ändringar för den här funktionen påverka beteendet för vissa MPI-implementeringar när du kör jobb över InfiniBand. Särskilt InfiniBand-gränssnittet på vissa virtuella datorer kan ha ett något annorlunda namn (mlx5_1 till skillnad från tidigare mlx5_0) och detta kan kräva att kommando raderna i MPI ändras särskilt när du använder UCX-gränssnittet (vanligt vis med OpenMPI och HPC-X). Den enklaste lösningen kan för närvarande vara att använda de senaste HPC-X-avbildningarna i CentOS-HPC-avbildningar eller inaktivera accelererat nätverk om det inte behövs.
Mer information om detta finns i den här [TechCommunity-artikeln](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-and-hbv2/ba-p/2067965) med anvisningar om hur du åtgärdar eventuella observerade problem.

## <a name="infiniband-driver-installation-on-non-sr-iov-vms"></a>InfiniBand-drivrutin installation på virtuella datorer som inte är SR-IOV

För närvarande är H16r, H16mr och NC24r inte SR-IOV aktiverat. Viss information om InfiniBand stack-bifurcation finns [här](../../sizes-hpc.md#rdma-capable-instances).
InfiniBand kan konfigureras på SR-IOV-aktiverade VM-storlekar med OFED-drivrutinerna medan icke-SR-IOV VM-storlekar kräver ND-drivrutiner. Detta IB-stöd är lämpligt för [CentOS, RHEL och Ubuntu](configure.md).

## <a name="duplicate-mac-with-cloud-init-with-ubuntu-on-h-series-and-n-series-vms"></a>Duplicera MAC med Cloud-Init med Ubuntu på virtuella datorer i H-serien och N-serien

Det finns ett känt problem med Cloud-Init på Ubuntu VM-avbildningar eftersom det försöker öppna IB-gränssnittet. Detta kan inträffa antingen vid omstart av virtuella datorer eller när du försöker skapa en VM-avbildning efter generalisering. Den virtuella datorns start loggar kan visa ett fel som så här:
```console
“Starting Network Service...RuntimeError: duplicate mac found! both 'eth1' and 'ib0' have mac”.
```

Detta är ett känt problem med att duplicera MAC med Cloud-Init på Ubuntu. Detta kommer att lösas i nyare kärnor. Om problemet uppstår är lösningen följande:
1) Distribuera VM-avbildningen (Ubuntu 18,04) för Marketplace
2) Installera nödvändiga program varu paket för att aktivera IB ([instruktion här](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351))
3) Redigera waagent. conf för att ändra EnableRDMA = y
4) Inaktivera nätverk i Cloud-Init
    ```console
    echo network: {config: disabled} | sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
    ```
5) Redigera nätverks konfigurations filen för netplan som genererats av Cloud-Init för att ta bort MAC
    ```console
    sudo bash -c "cat > /etc/netplan/50-cloud-init.yaml" <<'EOF'
    network:
      ethernets:
        eth0:
          dhcp4: true
      version: 2
    EOF
    ```

## <a name="qp0-access-restriction"></a>Åtkomst begränsning för qp0

För att förhindra maskin vara på låg nivå som kan leda till säkerhets risker, är Queue-paret 0 inte tillgängligt för virtuella gäst datorer. Detta bör endast påverka åtgärder som vanligt vis är kopplade till administration av ConnectX-5-NÄTVERKSKORTet och som kör en viss InfiniBand-diagnostik som ibdiagnet, men inte slutanvändarens program.

## <a name="dram-on-hb-series-vms"></a>DRAM på virtuella datorer i HB-serien

Virtuella datorer i HB-serien kan bara exponera 228 GB RAM-minne för virtuella gäst datorer för tillfället. På samma sätt är 458 GB på HBv2 och 448 GB på virtuella HBv3-datorer. Detta beror på en känd begränsning i Azure hypervisor för att förhindra att sidor tilldelas till det lokala DRAM av AMD CCX (NUMA-domäner) som är reserverade för den virtuella gäst datorn.

## <a name="gss-proxy"></a>GSS-proxy

GSS proxy har en känd bugg i CentOS/RHEL 7,5 som kan identifiera en betydande prestanda och svars tid när den används med NFS. Detta kan minimeras med:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Rensning av cache

På HPC-system är det ofta användbart att rensa minnet när ett jobb har avslut ATS innan nästa användare tilldelas samma nod. När du har kört program i Linux kan du se att det tillgängliga minnet minskar medan buffertutrymme ökar, trots att inga program körs.

![Skärm bild av kommando tolken före rensning](./media/known-issues/cache-cleaning-1.png)

Om `numactl -H` du använder visas vilka NUMAnode som minnet buffras med (möjligen alla). I Linux kan användare rensa cacheminnen på tre sätt för att returnera buffrat eller cachelagrat minne till ledigt. Du måste vara rot eller ha sudo-behörigheter.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Skärm bild av kommando tolken efter rensning](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Kernel-varningar

Du kan ignorera följande kernel-varnings meddelanden när du startar en virtuell dator med HB-serien under Linux. Detta beror på en känd begränsning i Azure-hypervisorn som kommer att åtgärdas över tid.

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

- Läs översikten över [HB-serien](hb-series-overview.md) och [HC-serien](hc-series-overview.md) för att lära dig mer om att konfigurera arbets belastningar optimalt för prestanda och skalbarhet.
- Läs om de senaste meddelandena, HPC-arbetsbelastnings exempel och prestanda resultat på [Azure Compute Tech-Webbgruppens Bloggar](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- En mer övergripande arkitektur för att köra HPC-arbetsbelastningar finns i [HPC (data behandling med höga prestanda) i Azure](/azure/architecture/topics/high-performance-computing/).
