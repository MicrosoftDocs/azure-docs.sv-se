---
title: DPDK i en virtuell Azure Linux-dator | Microsoft Docs
description: Lär dig fördelarna med data plan Development Kit (DPDK) och hur du konfigurerar DPDK på en virtuell Linux-dator.
services: virtual-network
documentationcenter: na
author: laxmanrb
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2020
ms.author: labattul
ms.openlocfilehash: ba7c2a37d58f20ac4ff1f49a46a406d1b1f70106
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704426"
---
# <a name="set-up-dpdk-in-a-linux-virtual-machine"></a>Konfigurera DPDK i en virtuell Linux-dator

Data plan Development Kit (DPDK) i Azure erbjuder ett snabbare paket bearbetnings ramverk för användar utrymme för prestanda intensiva program. Detta ramverk kringgår den virtuella datorns kernel Network-stack.

I en typisk paket bearbetning som använder kernel Network-stacken är processen avbruten. När nätverks gränssnittet tar emot inkommande paket, finns det ett kernel-avbrott för att bearbeta paketet och en kontext växel från kernel-utrymmet till användar utrymmet. DPDK eliminerar kontext växling och avbrotts driven metod till förmån för en användar rymds implementering som använder avsöknings läges driv rutiner för snabb paket bearbetning.

DPDK består av uppsättningar med bibliotek för användar utrymme som ger åtkomst till resurser på lägre nivå. Dessa resurser kan omfatta driv rutiner för maskin vara, logiska kärnor, minnes hantering och avsöknings läge för nätverkskort.

DPDK kan köras på virtuella Azure-datorer som stöder flera operativ Systems distributioner. DPDK tillhandahåller nyckel prestanda differentiering vid körning av nätverksvirtualisering för nätverks funktioner. Dessa implementeringar kan ha formen av virtuella nätverks installationer (NVA), till exempel virtuella routrar, brand väggar, VPN, belastningsutjämnare, uppfångade paket kärnor och DDoS-program (Denial-of-Service).

## <a name="benefit"></a>Fördelar

**Högre antal paket per sekund (PPS)**: att kringgå kärnan och ta kontroll över paket i användar utrymmet minskar antalet cykler genom att eliminera kontext byten. Det förbättrar också antalet paket som bearbetas per sekund i virtuella Azure Linux-datorer.


## <a name="supported-operating-systems"></a>Operativsystem som stöds

Följande distributioner från Azure Marketplace stöds:

| Linux-operativsystem     | Kernelversion               | 
|--------------|---------------------------   |
| Ubuntu 16.04 | 4.15.0-1014 – Azure +           | 
| Ubuntu 18.04 | 4.15.0-1014 – Azure +           |
| SLES 15 SP1  | 4.12.14-8.19 – Azure +          | 
| RHEL 7.5     | 3.10.0-862.11.6.el7.x86_64 +  | 
| CentOS 7.5   | 3.10.0-862.11.6.el7.x86_64 +  | 

**Anpassat stöd för kernel**

För alla Linux kernel-versioner som inte finns med i listan, se [patchar för att skapa en Azure-justerad Linux-kernel](https://github.com/microsoft/azure-linux-kernel). Du kan också kontakta om du vill ha mer information [aznetdpdk@microsoft.com](mailto:aznetdpdk@microsoft.com) . 

## <a name="region-support"></a>Stöd för regioner

Alla Azure-regioner stöder DPDK.

## <a name="prerequisites"></a>Förutsättningar

Accelererat nätverk måste vara aktiverat på en virtuell Linux-dator. Den virtuella datorn måste ha minst två nätverks gränssnitt, med ett gränssnitt för hantering. Lär dig hur du [skapar en virtuell Linux-dator med accelererat nätverk aktiverat](create-vm-accelerated-networking-cli.md).

## <a name="install-dpdk-dependencies"></a>Installera DPDK-beroenden

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="rhel75centos-75"></a>RHEL 7.5/CentOS 7,5

```bash
yum -y groupinstall "Infiniband Support"
sudo dracut --add-drivers "mlx4_en mlx4_ib mlx5_ib" -f
yum install -y gcc kernel-devel-`uname -r` numactl-devel.x86_64 librdmacm-devel libmnl-devel
```

### <a name="sles-15-sp1"></a>SLES 15 SP1

**Azure-kernel**

```bash
zypper  \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-azure kernel-devel-azure gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

**Standard kärna**

```bash
zypper \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-default-devel gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

## <a name="set-up-the-virtual-machine-environment-once"></a>Konfigurera den virtuella dator miljön (en gång)

1. [Hämta de senaste DPDK](https://core.dpdk.org/download). Version 18,11 LTS eller 19,11 LTS krävs för Azure.
2. Bygg standard konfigurationen med `make config T=x86_64-native-linuxapp-gcc` .
3. Aktivera Mellanox-PMDs i den genererade konfigurationen med `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config` .
4. Kompilera med `make` .
5. Installera med `make install DESTDIR=<output folder>` .

## <a name="configure-the-runtime-environment"></a>Konfigurera körnings miljön

När du har startat om kör du följande kommandon en gång:

1. Hugepages

   * Konfigurera hugepage genom att köra följande kommando, en gång för varje NUMA-nod:

     ```bash
     echo 1024 | sudo tee /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   * Skapa en katalog för montering med `mkdir /mnt/huge` .
   * Montera hugepages med `mount -t hugetlbfs nodev /mnt/huge` .
   * Kontrol lera att hugepages är reserverade med `grep Huge /proc/meminfo` .

     > Lägg Det finns ett sätt att ändra grub-filen så att hugepages reserveras vid start genom att följa [anvisningarna](https://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) för DPDK. Instruktionerna visas längst ned på sidan. När du använder en virtuell Azure Linux-dator ändrar du filerna under **/etc/config/grub.d** i stället för att reservera hugepages över omstarter.

2. MAC-& IP-adresser: används `ifconfig –a` för att visa Mac-och IP-adressen för nätverks gränssnitten. Nätverks gränssnittet *VF* och *NETVSC* har samma Mac-adress, men bara *NETVSC* -nätverks gränssnittet har en IP-adress. *VF* -gränssnitt körs som underordnade gränssnitt för *NETVSC* -gränssnitt.

3. PCI-adresser

   * Använd `ethtool -i <vf interface name>` för att ta reda på vilken PCI-adress som ska användas för *VF*.
   * Om *eth0* har accelererat nätverk aktiverat kontrollerar du att testpmd inte oavsiktligt tar över *VF* PCI-enheten för *eth0*. Om DPDK-programmet oavsiktligt tar över hanterings nätverks gränssnittet och gör att du förlorar SSH-anslutningen kan du använda serie konsolen för att stoppa DPDK-programmet. Du kan också använda serie konsolen för att stoppa eller starta den virtuella datorn.

4. Läs in *ibuverbs* vid varje omstart med `modprobe -a ib_uverbs` . Läs även *mlx4_ib* med för SLES 15 `modprobe -a mlx4_ib` .

## <a name="failsafe-pmd"></a>Failsafe PMD

DPDK-program måste köras via Failsafe-PMD som visas i Azure. Om programmet körs direkt över *VF* -PMD får det inte **alla** paket som är avsedda för den virtuella datorn, eftersom vissa paket visas över det syntetiska gränssnittet. 

Om du kör ett DPDK-program över Failsafe-PMD garanterar det att programmet tar emot alla paket som är avsedda för det. Det ser också till att programmet fortsätter att köras i DPDK-läge, även om VF-objektet återkallas när värden betjänas. Mer information om Failsafe-PMD finns i [fel säkert avsöknings läge driv rutins bibliotek](https://doc.dpdk.org/guides/nics/fail_safe.html).

## <a name="run-testpmd"></a>Kör testpmd

Om du vill köra testpmd i rot läge använder `sudo` du före kommandot *testpmd* .

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>Basic: Sanity check, Failsafe adapter-initiering

1. Kör följande kommandon för att starta ett testpmd-program för en port:

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. Kör följande kommandon för att starta ett testpmd-program med dubbla portar:

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   Om du kör testpmd med fler än två nätverkskort, `--vdev` följer argumentet följande mönster: `net_vdev_netvsc<id>,iface=<vf’s pairing eth>` .

3.  När den har startats kör `show port info all` du för att kontrol lera port information. Du bör se en eller två DPDK-portar som är net_failsafe (inte *net_mlx4*).
4.  Används `start <port> /stop <port>` för att starta trafiken.

De tidigare kommandona startar *testpmd* i interaktivt läge, vilket rekommenderas för att testa testpmd-kommandon.

### <a name="basic-single-sendersingle-receiver"></a>Basic: enskild avsändare/enskild mottagare

Följande kommandon skriver regelbundet ut paketen per sekund statistik:

1. Kör följande kommando på TX-sidan:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
   ```

2. Kör följande kommando på MOTTAGNINGs sidan:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=rxonly \
     --eth-peer=<port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
   ```

När du kör de tidigare kommandona på en virtuell dator ändrar du *IP_SRC_ADDR* och *IP_DST_ADDR* i `app/test-pmd/txonly.c` för att matcha den faktiska IP-adressen för de virtuella datorerna innan du kompilerar. Annars släpps paketen innan de når mottagaren.

### <a name="advanced-single-sendersingle-forwarder"></a>Avancerat: enskild avsändare/enskild vidarebefordrare
Följande kommandon skriver regelbundet ut paketen per sekund statistik:

1. Kör följande kommando på TX-sidan:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
    ```

2. Kör följande kommando på FWD-sidan:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address NIC1> \
     -w <pci address NIC2> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     --vdev="net_vdev_netvsc<2nd id>,iface=<2nd iface to attach to>" (you need as many --vdev arguments as the number of devices used by testpmd, in this case) \
     -- --nb-cores <number of cores to use for test pmd> \
     --forward-mode=io \
     --eth-peer=<recv port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
    ```

När du kör de tidigare kommandona på en virtuell dator ändrar du *IP_SRC_ADDR* och *IP_DST_ADDR* i `app/test-pmd/txonly.c` för att matcha den faktiska IP-adressen för de virtuella datorerna innan du kompilerar. Annars släpps paketen innan de når vidarebefordraren. Du kommer inte att kunna ha en tredje dator som tar emot vidarebefordrad trafik eftersom *testpmd* -vidarebefordraren inte ändrar lager-3-adresserna, såvida du inte gör några kod ändringar.

## <a name="references"></a>Referenser

* [EAL-alternativ](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Testpmd-kommandon](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
