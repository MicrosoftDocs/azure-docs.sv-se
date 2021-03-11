---
title: Konfiguration av GPU-drivrutin för Azure N-serien för Linux
description: Konfigurera NVIDIA GPU-drivrutiner för virtuella datorer i N-serien som kör Linux i Azure
services: virtual-machines
author: vikancha-MSFT
ms.service: virtual-machines
ms.subervice: vm-sizes-gpu
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2019
ms.author: vikancha
ms.openlocfilehash: c4c6bee6d3f9e423d83458ad48d213fe65223514
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102551768"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Installera NVIDIA GPU-drivrutiner på virtuella datorer i N-serien som kör Linux

Om du vill dra nytta av GPU-funktionerna i Azure N-seriens virtuella datorer som backas upp av NVIDIA GPU: er måste du installera NVIDIA GPU-drivrutiner. [NVidia GPU-drivrutinen](../extensions/hpccompute-gpu-linux.md) installerar lämpliga NVIDIA-CUDA eller rutnäts driv rutiner på en virtuell dator i N-serien. Installera eller hantera tillägget med hjälp av Azure Portal eller verktyg som Azure CLI eller Azure Resource Manager mallar. Se [dokumentationen för NVIDIA GPU-drivrutin](../extensions/hpccompute-gpu-linux.md) för distributioner som stöds och distributions steg.

Om du väljer att installera NVIDIA GPU-drivrutiner manuellt innehåller den här artikeln distributioner, driv rutiner och installations-och verifierings steg som stöds. Det finns även information om manuell driv rutins installation för [virtuella Windows-datorer](../windows/n-series-driver-setup.md).

För specifikationer för virtuella datorer i N-serien, lagrings kapacitet och disk information, se [storlekar för virtuella GPU Linux-datorer](../sizes-gpu.md?toc=/azure/virtual-machines/linux/toc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>Installera CUDA-drivrutiner för virtuella datorer i N-serien

Här följer steg för att installera CUDA-drivrutiner från NVIDIA CUDA Toolkit på virtuella datorer i N-serien. 

C-och C++-utvecklare kan också installera hela verktygs uppsättningen för att bygga GPU-accelererade program. Mer information finns i [installations guiden för CUDA](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

Om du vill installera CUDA-drivrutiner gör du en SSH-anslutning till varje virtuell dator. Verifiera att systemet har en CUDA-kompatibel GPU genom att köra följande kommando:

```bash
lspci | grep -i NVIDIA
```
Du ser utdata som liknar följande exempel (visar ett NVIDIA Tesla K80-kort):

![utdata för lspci-kommandot](./media/n-series-driver-setup/lspci.png)

lspci visar en lista över PCIe-enheter på den virtuella datorn, inklusive InfiniBand NIC och GPU, om det finns några. Om lspci inte kan slutföras utan problem kan du behöva installera LIS på CentOS/RHEL (anvisningarna nedan).
Kör sedan installations kommandon som är speciella för distributionen.

### <a name="ubuntu"></a>Ubuntu 

1. Hämta och installera CUDA-drivrutinerna från NVIDIA-webbplatsen. 
    > [!NOTE]
   >  Exemplet nedan visar CUDA-paket Sök vägen för Ubuntu 16,04. Ersätt sökvägen som är speciell för den version som du planerar att använda. 
   >  
   >  Gå till [NVIDIA Download Center] ( https://developer.download.nvidia.com/compute/cuda/repos/) för den fullständiga sökvägen som är speciell för varje version. 
   > 
   ```bash
   CUDA_REPO_PKG=cuda-repo-ubuntu1604_10.0.130-1_amd64.deb
   wget -O /tmp/${CUDA_REPO_PKG} https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

   sudo dpkg -i /tmp/${CUDA_REPO_PKG}
   sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 
   rm -f /tmp/${CUDA_REPO_PKG}

   sudo apt-get update
   sudo apt-get install cuda-drivers
   ```

   Installationen kan ta flera minuter.
 

2. Om du vill installera hela CUDA Toolkit skriver du:

   ```bash
   sudo apt-get install cuda
   ```

3. Starta om den virtuella datorn och fortsätt att verifiera installationen.

#### <a name="cuda-driver-updates"></a>CUDA driv rutins uppdateringar

Vi rekommenderar att du regelbundet uppdaterar CUDA-drivrutiner efter distributionen.

```bash
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get dist-upgrade -y
sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS eller Red Hat Enterprise Linux

1. Uppdatera kärnan (rekommenderas). Om du väljer att inte uppdatera kärnan kontrollerar du att versionerna av `kernel-devel` och `dkms` är lämpliga för din kernel.

   ```
   sudo yum install kernel kernel-tools kernel-headers kernel-devel
   sudo reboot
   ```

2. Installera de senaste [Linux-integrerings tjänsterna för Hyper-V och Azure](https://www.microsoft.com/download/details.aspx?id=55106). Kontrol lera om LIS krävs genom att verifiera resultatet av lspci. Om alla GPU-enheter visas som förväntat (och dokumenteras ovan) krävs inte att du installerar LIS.

   Observera att LIS är tillämpligt för Red Hat Enterprise Linux, CentOS och Oracle Linux Red Hat Compatible kernel 5.2-5.11, 6.0-6.10 och 7.0-7.7. Mer information finns i [Linux Integration Services-dokumentationen] https://www.microsoft.com/en-us/download/details.aspx?id=55106) . 
   Hoppa över det här steget om du planerar att använda CentOS/RHEL 7,8 (eller senare versioner) eftersom LIS inte längre krävs för dessa versioner.

      ```bash
      wget https://aka.ms/lis
      tar xvzf lis
      cd LISISO

      sudo ./install.sh
      sudo reboot
      ```

3. Återanslut till den virtuella datorn och fortsätt installationen med följande kommandon:

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
   sudo yum install dkms
   
   CUDA_REPO_PKG=cuda-repo-rhel7-10.0.130-1.x86_64.rpm
   wget https://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}
   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

   Installationen kan ta flera minuter. 

4. Om du vill installera hela CUDA Toolkit skriver du:

   ```bash
   sudo yum install cuda
   ```
   > [!NOTE]
   >  Om du ser ett fel meddelande som rör saknade paket som vulkan-filsystem kan du behöva redigera/etc/yum.Repos.d/RH-Cloud, leta efter valfria-RPMS och ange aktiverat till 1
   >  

5. Starta om den virtuella datorn och fortsätt att verifiera installationen.

### <a name="verify-driver-installation"></a>Verifiera installation av driv rutin

För att fråga GPU-enhetens tillstånd, SSH till den virtuella datorn och köra kommando rads verktyget [NVIDIA-SMI](https://developer.nvidia.com/nvidia-system-management-interface) installerat med driv rutinen. 

Om driv rutinen är installerad visas utdata som liknar följande. Observera att **GPU-util** visar 0% om du för närvarande kör en GPU-arbetsbelastning på den virtuella datorn. Driv rutins versionen och GPU-informationen kan skilja sig från de som visas.

![Status för NVIDIA-enhet](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>RDMA-nätverksanslutning

RDMA-nätverksanslutning kan aktive ras på virtuella datorer med RDMA-kapacitet, till exempel NC24r som distribuerats i samma tillgänglighets uppsättning eller i en enda placerings grupp i en skalnings uppsättning för virtuell dator (VM). RDMA-nätverket stöder MPI-trafik (Message Passing Interface) för program som körs med Intel MPI 5. x eller en senare version. Ytterligare krav följer:

### <a name="distributions"></a>Distributioner

Distribuera RDMA-kompatibla virtuella datorer i N-serien från en av avbildningarna på Azure Marketplace som har stöd för RDMA-anslutning på virtuella datorer i N-serien:
  
* **Ubuntu 16,04 LTS** – konfigurera RDMA-drivrutiner på den virtuella datorn och registrera dig för Intel för att ladda ned Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **CentOS-baserade 7,4 HPC** -drivrutiner och Intel MPI 5,1 är installerade på den virtuella datorn.

* **CentOS-baserade HPC** -CENTOS-HPC 7,6 och senare (för SKU: er där InfiniBand stöds i SR-IOV). De här avbildningarna har Mellanox OFED-och MPI-bibliotek förinstallerade.

> [!NOTE]
> CX3-Pro kort stöds bara med LTS-versioner av Mellanox OFED. Använd LTS Mellanox OFED version (4.9-0.1.7.0) på virtuella datorer i N-serien med ConnectX3-Pro-kort. Mer information finns i [Linux-drivrutiner](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed).
>
> Några av de senaste HPC-avbildningarna för Azure Marketplace har också Mellanox OFED 5,1 och senare, vilket inte stöder ConnectX3-Pro kort. Kontrol lera Mellanox OFED-versionen i HPC-avbildningen innan du använder den på virtuella datorer med ConnectX3-Pro-kort.
>
> Följande avbildningar är de senaste CentOS-HPC-avbildningarna som stöder ConnectX3-Pro kort:
>
> - OpenLogic: CentOS-HPC: 7.6:7.6.2020062900
> - OpenLogic: CentOS-HPC: 7_6gen2:7.6.2020062901
> - OpenLogic: CentOS-HPC: 7.7:7.7.2020062600
> - OpenLogic: CentOS-HPC: 7_7-Gen2:7.7.2020062601
> - OpenLogic: CentOS-HPC: 8_1:8.1.2020062400
> - OpenLogic: CentOS-HPC: 8_1-Gen2:8.1.2020062401
>

## <a name="install-grid-drivers-on-nv-or-nvv3-series-vms"></a>Installera RUTNÄTs driv rutiner på virtuella datorer med NV eller NVv3-serien

Om du vill installera NVIDIA GRID-drivrutiner på virtuella datorer med NV eller NVv3-serien gör du en SSH-anslutning till varje virtuell dator och följer stegen för din Linux-distribution. 

### <a name="ubuntu"></a>Ubuntu 

1. Kör kommandot `lspci`. Kontrol lera att kortet eller korten i NVIDIA M60 visas som PCI-enheter.

2. Installera uppdateringar.

   ```bash
   sudo apt-get update
   sudo apt-get upgrade -y
   sudo apt-get dist-upgrade -y
   sudo apt-get install build-essential ubuntu-desktop -y
   sudo apt-get install linux-azure -y
   ```
3. Inaktivera Nouveau kernel-drivrutinen, som inte är kompatibel med NVIDIA-drivrutinen. (Använd bara NVIDIA-drivrutinen på NV-eller NVv2 virtuella datorer.) Det gör du genom att skapa en fil med `/etc/modprobe.d` namnet `nouveau.conf` med följande innehåll:

   ```
   blacklist nouveau
   blacklist lbm-nouveau
   ```


4. Starta om den virtuella datorn och återanslut. Avsluta X-Server:

   ```bash
   sudo systemctl stop lightdm.service
   ```

5. Hämta och installera RUTNÄTs driv rutinen:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  
   chmod +x NVIDIA-Linux-x86_64-grid.run
   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. När du tillfrågas om du vill köra nvidia-xconfig-verktyget för att uppdatera X-konfigurationsfilen väljer du **Ja**.

7. När installationen är klar kopierar du/etc/NVIDIA/gridd.conf.template till en ny fil med rutnätet. conf på plats/etc/NVIDIA/

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. Lägg till följande i `/etc/nvidia/gridd.conf` :
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE
   ```
   
9. Ta bort följande från `/etc/nvidia/gridd.conf` om det finns:
 
   ```
   FeatureType=0
   ```
10. Starta om den virtuella datorn och fortsätt att verifiera installationen.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS eller Red Hat Enterprise Linux 

1. Uppdatera kernel-och DKMS (rekommenderas). Om du väljer att inte uppdatera kärnan kontrollerar du att versionerna av `kernel-devel` och `dkms` är lämpliga för din kernel.
 
   ```bash  
   sudo yum update
   sudo yum install kernel-devel
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
   sudo yum install dkms
   sudo yum install hyperv-daemons
   ```

2. Inaktivera Nouveau kernel-drivrutinen, som inte är kompatibel med NVIDIA-drivrutinen. (Använd bara NVIDIA-drivrutinen på NV-eller NV3 virtuella datorer.) Det gör du genom att skapa en fil med `/etc/modprobe.d` namnet `nouveau.conf` med följande innehåll:

   ```
   blacklist nouveau
   blacklist lbm-nouveau
   ```

3. Starta om den virtuella datorn, återanslut och installera de senaste [Linux-integrerings tjänsterna för Hyper-V och Azure](https://www.microsoft.com/download/details.aspx?id=55106). Kontrol lera om LIS krävs genom att verifiera resultatet av lspci. Om alla GPU-enheter visas som förväntat (och dokumenteras ovan) krävs inte att du installerar LIS. 

   Hoppa över det här steget om du planerar att använda CentOS/RHEL 7,8 (eller senare versioner) eftersom LIS inte längre krävs för dessa versioner.

      ```bash
      wget https://aka.ms/lis
      tar xvzf lis
      cd LISISO

      sudo ./install.sh
      sudo reboot

      ```
 
4. Återanslut till den virtuella datorn och kör `lspci` kommandot. Kontrol lera att kortet eller korten i NVIDIA M60 visas som PCI-enheter.
 
5. Hämta och installera RUTNÄTs driv rutinen:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  
   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. När du tillfrågas om du vill köra nvidia-xconfig-verktyget för att uppdatera X-konfigurationsfilen väljer du **Ja**.

7. När installationen är klar kopierar du/etc/NVIDIA/gridd.conf.template till en ny fil med rutnätet. conf på plats/etc/NVIDIA/
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. Lägg till följande i `/etc/nvidia/gridd.conf` :
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE 
   ```
9. Ta bort följande från `/etc/nvidia/gridd.conf` om det finns:
 
   ```
   FeatureType=0
   ```
10. Starta om den virtuella datorn och fortsätt att verifiera installationen.


### <a name="verify-driver-installation"></a>Verifiera installation av driv rutin


För att fråga GPU-enhetens tillstånd, SSH till den virtuella datorn och köra kommando rads verktyget [NVIDIA-SMI](https://developer.nvidia.com/nvidia-system-management-interface) installerat med driv rutinen. 

Om driv rutinen är installerad visas utdata som liknar följande. Observera att **GPU-util** visar 0% om du för närvarande kör en GPU-arbetsbelastning på den virtuella datorn. Driv rutins versionen och GPU-informationen kan skilja sig från de som visas.

![Skärm bild som visar utdata när GPU-enhetens tillstånd har frågats.](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>Begäran om x11-Server
Om du behöver en begäran om x11-Server för fjärr anslutningar till en NV-eller NVv2-VM rekommenderas [x11vnc](http://www.karlrunge.com/x11vnc/) eftersom den tillåter grafikens maskin varu acceleration. BusID för den M60-enheten måste läggas till manuellt i begäran om x11-konfigurationsfilen (vanligt vis `etc/X11/xorg.conf` ). Lägg till ett `"Device"` avsnitt som liknar följande:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Uppdatera dessutom ditt `"Screen"` avsnitt för att använda den här enheten.
 
Du kan hitta decimal BusID genom att köra

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
BusID kan ändras när en virtuell dator tilldelas eller startas om. Därför kanske du vill skapa ett skript för att uppdatera BusID i begäran om x11-konfigurationen när en virtuell dator startas om. Du kan till exempel skapa ett skript `busidupdate.sh` med namnet (eller ett annat namn som du väljer) med innehåll som liknar följande:

```bash 
#!/bin/bash
XCONFIG="/etc/X11/xorg.conf"
OLDBUSID=`awk '/BusID/{gsub(/"/, "", $2); print $2}' ${XCONFIG}`
NEWBUSID=`nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'`

if [[ "${OLDBUSID}" == "${NEWBUSID}" ]] ; then
        echo "NVIDIA BUSID not changed - nothing to do"
else
        echo "NVIDIA BUSID changed from \"${OLDBUSID}\" to \"${NEWBUSID}\": Updating ${XCONFIG}" 
        sed -e 's|BusID.*|BusID          '\"${NEWBUSID}\"'|' -i ${XCONFIG}
fi
```

Skapa sedan en post för ditt uppdaterings skript i `/etc/rc.d/rc3.d` så att skriptet anropas som rot vid start.

## <a name="troubleshooting"></a>Felsökning

* Du kan ställa in persistence-läge med `nvidia-smi` så att kommandots utdata går snabbare när du behöver fråga kort. Kör om du vill ställa in persistence-läge `nvidia-smi -pm 1` . Observera att om den virtuella datorn startas om så går läges inställningen bort. Du kan alltid skripta läges inställningen så att den körs vid start.
* Om du har uppdaterat NVIDIA CUDA-drivrutinerna till den senaste versionen och inte längre kan hitta RDMA-anslutningen kan du [installera om RDMA-drivrutinerna](#rdma-network-connectivity) för att återupprätta anslutningen. 
* Om en viss CentOS/RHEL OS-version (eller kernel) inte stöds för LIS under installationen av LIS, utlöses felet "en kernel-version som inte stöds". Rapportera det här felet tillsammans med operativ system-och kernel-versionerna.

## <a name="next-steps"></a>Nästa steg

* Om du vill avbilda en virtuell Linux-avbildning med dina installerade NVIDIA-drivrutiner, se [hur man generaliserar och fångar in en virtuell Linux-dator](capture-image.md).
