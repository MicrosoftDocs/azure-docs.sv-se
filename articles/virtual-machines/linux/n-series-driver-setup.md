---
title: Konfiguration av GPU-drivrutin i N-serien i Azure för Linux
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
ms.openlocfilehash: dd9461e30138ee1a59a93db45aa5f739bfe88f94
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565312"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Installera NVIDIA GPU-drivrutiner på virtuella datorer i N-serien som kör Linux

Om du vill dra nytta av GPU-funktionerna i virtuella datorer i Azure N-serien som backas upp av NVIDIA-GPU:er måste du installera NVIDIA GPU-drivrutiner. [NVIDIA GPU-drivrutinstillägget](../extensions/hpccompute-gpu-linux.md) installerar lämpliga NVIDIA CUDA- eller GRID-drivrutiner på en virtuell dator i N-serien. Installera eller hantera tillägget med hjälp Azure Portal eller verktyg som Azure CLI eller Azure Resource Manager mallar. Se dokumentationen [om NVIDIA GPU-drivrutinstillägg för](../extensions/hpccompute-gpu-linux.md) distributioner och distributionssteg som stöds.

Om du väljer att installera NVIDIA GPU-drivrutiner manuellt innehåller den här artikeln distributioner, drivrutiner och installations- och verifieringssteg som stöds. Information om manuell drivrutinsinstallation finns också för [virtuella Windows-datorer.](../windows/n-series-driver-setup.md)

Specifikationer för virtuella datorer i N-serien, lagringskapacitet och diskinformation finns i [VM-storlekar för GPU Linux.](../sizes-gpu.md?toc=/azure/virtual-machines/linux/toc.json) 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>Installera CUDA-drivrutiner på virtuella datorer i N-serien

Här är steg för att installera CUDA-drivrutiner från NVIDIA CUDA Toolkit på virtuella datorer i N-serien. 

C- och C++-utvecklare kan välja att installera hela Toolkit för att skapa GPU-accelererade program. Mer information finns i [installationsguiden för CUDA.](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)

Installera CUDA-drivrutiner genom att upprätta en SSH-anslutning till varje virtuell dator. Kontrollera att systemet har en CUDA-kompatibel GPU genom att köra följande kommando:

```bash
lspci | grep -i NVIDIA
```
Du ser utdata som liknar följande exempel (som visar ett NVIDIA Tesla K80-kort):

![lspci-kommandoutdata](./media/n-series-driver-setup/lspci.png)

Lspci listar PCIe-enheter på den virtuella datorn, inklusive InfiniBand-nätverkskortet och EVENTUELLA GPU:er. Om lspci inte returneras korrekt kan du behöva installera LIS på CentOS/RHEL (anvisningarna nedan).
Kör sedan installationskommandon som är specifika för din distribution.

### <a name="ubuntu"></a>Ubuntu 

1. Ladda ned och installera CUDA-drivrutinerna från NVIDIA-webbplatsen. 
    > [!NOTE]
   >  Exemplet nedan visar CUDA-paketsökvägen för Ubuntu 16.04. Ersätt sökvägen som är specifik för den version som du planerar att använda. 
   >  
   >  Besök [Nvidia Download Center] ( för https://developer.download.nvidia.com/compute/cuda/repos/) den fullständiga sökvägen som är specifik för varje version. 
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
 

2. Om du vill installera hela CUDA-verktygslådan skriver du:

   ```bash
   sudo apt-get install cuda
   ```

3. Starta om den virtuella datorn och verifiera installationen.

#### <a name="cuda-driver-updates"></a>CUDA-drivrutinsuppdateringar

Vi rekommenderar att du regelbundet uppdaterar CUDA-drivrutiner efter distributionen.

```bash
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get dist-upgrade -y
sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS eller Red Hat Enterprise Linux

1. Uppdatera kerneln (rekommenderas). Om du väljer att inte uppdatera kerneln ser du till att versionerna av `kernel-devel` och är lämpliga för din `dkms` kernel.

   ```
   sudo yum install kernel kernel-tools kernel-headers kernel-devel
   sudo reboot
   ```

2. Installera de senaste [Linux-integreringstjänsterna för Hyper-V och Azure](https://www.microsoft.com/download/details.aspx?id=55106). Kontrollera om LIS krävs genom att verifiera resultatet av lspci. Om alla GPU-enheter visas som förväntat (och dokumenteras ovan) krävs inte installation av LIS.

   Observera att LIS gäller för Red Hat Enterprise Linux, CentOS och Oracle Linux Red Hat Compatible Kernel 5.2-5.11, 6.0-6.10 och 7.0-7.7. Mer information finns i [Dokumentation om https://www.microsoft.com/en-us/download/details.aspx?id=55106) Linux-integreringstjänster] ( 
   Hoppa över det här steget om du planerar att använda CentOS/RHEL 7.8 (eller senare versioner) eftersom LIS inte längre krävs för dessa versioner.

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
   
    > [!NOTE]
   >  Besök [Fedora och](https://dl.fedoraproject.org/pub/epel/) Nvidia [CUDA-lagringsplatsen](https://developer.download.nvidia.com/compute/cuda/repos/) för att välja rätt paket för den CentOS- eller RHEL-version som du vill använda.
   >  

Till exempel behöver CentOS 8 och RHEL 8 följande steg.

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
   sudo yum install dkms
   
   CUDA_REPO_PKG=cuda-repo-rhel8-10.2.89-1.x86_64.rpm
   wget https://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}
   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

4. Om du vill installera hela CUDA-verktygslådan skriver du:

   ```bash
   sudo yum install cuda
   ```
   > [!NOTE]
   >  Om du ser ett felmeddelande som rör saknade paket, till exempel molnbaserade filsystem, kan du behöva redigera /etc/yum.repos.d/rh-cloud , leta efter optional-rpms och ange aktiverat till 1
   >  

5. Starta om den virtuella datorn och verifiera installationen.

### <a name="verify-driver-installation"></a>Verifiera drivrutinsinstallationen

Om du vill fråga GPU-enhetens tillstånd SSH till den virtuella datorn och köra [kommandoradsverktyget nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) installerat med drivrutinen. 

Om drivrutinen är installerad visas utdata som liknar följande. Observera att **GPU-Util visar** 0 % om du inte kör en GPU-arbetsbelastning på den virtuella datorn. Drivrutinsversionen och GPU-informationen kan vara annorlunda än den som visas.

![NVIDIA-enhetsstatus](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>RDMA-nätverksanslutning

RDMA-nätverksanslutning kan aktiveras på RDMA-kompatibla virtuella datorer i N-serien, till exempel NC24r, distribuerade i samma tillgänglighetsuppsättning eller i en enda placeringsgrupp i en VM-skalningsuppsättning. RDMA-nätverket stöder Message Passing Interface trafik (MPI) för program som körs med Intel MPI 5.x eller en senare version. Ytterligare krav följer:

### <a name="distributions"></a>Distributioner

Distribuera RDMA-kompatibla virtuella datorer i N-serien från en av avbildningarna i Azure Marketplace stöder RDMA-anslutning på virtuella datorer i N-serien:
  
* **Ubuntu 16.04 LTS –** Konfigurera RDMA-drivrutiner på den virtuella datorn och registrera dig hos Intel för att ladda ned Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **CentOS-baserade 7.4 HPC – RDMA-drivrutiner** och Intel MPI 5.1 är installerade på den virtuella datorn.

* **CentOS-baserad HPC** – CentOS-HPC 7.6 och senare (för SKU:er där InfiniBand stöds via SR-IOV). Dessa avbildningar har Mellanox OFED- och MPI-bibliotek förinstallerade.

> [!NOTE]
> CX3-Pro kort stöds endast via LTS-versioner av Mellanox OFED. Använd LTS Mellanox OFED-versionen (4.9-0.1.7.0) på virtuella datorer i N-serien med ConnectX3-Pro kort. Mer information finns i [Linux-drivrutiner.](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed)
>
> Dessutom har vissa av de Azure Marketplace HPC-avbildningarna Mellanox OFED 5.1 och senare, som inte stöder ConnectX3-Pro kort. Kontrollera Mellanox OFED-versionen i HPC-avbildningen innan du använder den på virtuella datorer med ConnectX3-Pro kort.
>
> Följande bilder är de senaste CentOS-HPC-avbildningarna som stöder ConnectX3-Pro kort:
>
> - OpenLogic:CentOS-HPC:7.6:7.6.2020062900
> - OpenLogic:CentOS-HPC:7_6gen2:7.6.2020062901
> - OpenLogic:CentOS-HPC:7.7:7.7.2020062600
> - OpenLogic:CentOS-HPC:7_7-gen2:7.7.2020062601
> - OpenLogic:CentOS-HPC:8_1:8.1.2020062400
> - OpenLogic:CentOS-HPC:8_1-gen2:8.1.2020062401
>

## <a name="install-grid-drivers-on-nv-or-nvv3-series-vms"></a>Installera GRID-drivrutiner på virtuella datorer i NV- eller NVv3-serien

Installera NVIDIA GRID-drivrutiner på virtuella datorer i NV- eller NVv3-serien genom att upprätta en SSH-anslutning till varje virtuell dator och följa stegen för din Linux-distribution. 

### <a name="ubuntu"></a>Ubuntu 

1. Kör kommandot `lspci`. Kontrollera att NVIDIA M60-kortet eller korten visas som PCI-enheter.

2. Installera uppdateringar.

   ```bash
   sudo apt-get update
   sudo apt-get upgrade -y
   sudo apt-get dist-upgrade -y
   sudo apt-get install build-essential ubuntu-desktop -y
   sudo apt-get install linux-azure -y
   ```
3. Inaktivera Nouveau-kerneldrivrutinen, som inte är kompatibel med NVIDIA-drivrutinen. (Använd endast NVIDIA-drivrutinen på virtuella NV- eller NVv2-datorer.) Det gör du genom att skapa en fil `/etc/modprobe.d` i med namnet med följande `nouveau.conf` innehåll:

   ```
   blacklist nouveau
   blacklist lbm-nouveau
   ```


4. Starta om den virtuella datorn och återanslut. Avsluta X-servern:

   ```bash
   sudo systemctl stop lightdm.service
   ```

5. Ladda ned och installera GRID-drivrutinen:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  
   chmod +x NVIDIA-Linux-x86_64-grid.run
   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. När du tillfrågas om du vill köra verktyget nvidia-xconfig för att uppdatera X-konfigurationsfilen väljer du **Ja.**

7. När installationen är klar kopierar du /etc/nvidia/gridd.conf.template till en ny fil gridd.conf på plats /etc/nvidia/

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
10. Starta om den virtuella datorn och verifiera installationen.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS eller Red Hat Enterprise Linux 

1. Uppdatera kerneln och DKMS (rekommenderas). Om du väljer att inte uppdatera kerneln ser du till att versionerna av `kernel-devel` och är lämpliga för din `dkms` kernel.
 
   ```bash  
   sudo yum update
   sudo yum install kernel-devel
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
   sudo yum install dkms
   sudo yum install hyperv-daemons
   ```

2. Inaktivera Nouveau-kerneldrivrutinen, som inte är kompatibel med NVIDIA-drivrutinen. (Använd endast NVIDIA-drivrutinen på VIRTUELLA NV- eller NV3-datorer.) Det gör du genom att skapa en fil `/etc/modprobe.d` i med namnet med följande `nouveau.conf` innehåll:

   ```
   blacklist nouveau
   blacklist lbm-nouveau
   ```

3. Starta om den virtuella datorn, återanslut och installera de senaste [Linux-integreringstjänsterna för Hyper-V och Azure](https://www.microsoft.com/download/details.aspx?id=55106). Kontrollera om LIS krävs genom att verifiera resultatet av lspci. Om alla GPU-enheter visas som förväntat (och dokumenteras ovan) krävs inte installation av LIS. 

   Hoppa över det här steget om du planerar att använda CentOS/RHEL 7.8 (eller senare versioner) eftersom LIS inte längre krävs för dessa versioner.

      ```bash
      wget https://aka.ms/lis
      tar xvzf lis
      cd LISISO

      sudo ./install.sh
      sudo reboot

      ```
 
4. Återanslut till den virtuella datorn och kör `lspci` kommandot . Kontrollera att NVIDIA M60-kortet eller korten visas som PCI-enheter.
 
5. Ladda ned och installera GRID-drivrutinen:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  
   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. När du tillfrågas om du vill köra verktyget nvidia-xconfig för att uppdatera X-konfigurationsfilen väljer du **Ja.**

7. När installationen är klar kopierar du /etc/nvidia/gridd.conf.template till en ny fil gridd.conf på plats /etc/nvidia/
  
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
10. Starta om den virtuella datorn och verifiera installationen.


### <a name="verify-driver-installation"></a>Verifiera drivrutinsinstallationen


Om du vill fråga GPU-enhetens tillstånd SSH till den virtuella datorn och köra [kommandoradsverktyget nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) installerat med drivrutinen. 

Om drivrutinen är installerad visas utdata som liknar följande. Observera att **GPU-Util visar** 0 % om du inte kör en GPU-arbetsbelastning på den virtuella datorn. Drivrutinsversionen och GPU-informationen kan vara annorlunda än den som visas.

![Skärmbild som visar utdata när gpu-enhetens tillstånd efterfrågas.](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11-server
Om du behöver en X11-server för fjärranslutningar till en virtuell NV- eller NVv2-dator rekommenderas [x11vnc](http://www.karlrunge.com/x11vnc/) eftersom det tillåter maskinvaruacceleration av grafik. M60-enhetens BusID måste läggas till manuellt i X11-konfigurationsfilen (vanligtvis `etc/X11/xorg.conf` ). Lägg till `"Device"` ett avsnitt som liknar följande:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Uppdatera dessutom avsnittet för `"Screen"` att använda den här enheten.
 
Du kan hitta decimal-BusID genom att köra

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
BusID kan ändras när en virtuell dator omallokeras eller startas om. Därför kanske du vill skapa ett skript för att uppdatera BusID i X11-konfigurationen när en virtuell dator startas om. Du kan till exempel skapa ett skript med namnet (eller ett annat namn som du `busidupdate.sh` väljer) med innehåll som liknar följande:

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

Skapa sedan en post för uppdateringsskriptet i `/etc/rc.d/rc3.d` så att skriptet anropas som rot vid start.

## <a name="troubleshooting"></a>Felsökning

* Du kan ange persistenceläge `nvidia-smi` med så att kommandots utdata blir snabbare när du behöver köra frågor mot kort. Om du vill ange persistenceläge kör du `nvidia-smi -pm 1` . Observera att lägesinställningen försvinner om den virtuella datorn startas om. Du kan alltid skripta den lägesinställning som ska köras vid start.
* Om du har uppdaterat NVIDIA CUDA-drivrutinerna till den senaste versionen och upptäcker att RDMA-anslutningen inte längre fungerar installerar du om [RDMA-drivrutinerna](#rdma-network-connectivity) för att återupprätta anslutningen. 
* Om en viss CentOS/RHEL OS-version (eller kernel) inte stöds för LIS under installationen av LIS, uppstår ett fel om att kernelversionen inte stöds. Rapportera det här felet tillsammans med operativsystem- och kernel-versionerna.

## <a name="next-steps"></a>Nästa steg

* Information om hur du avbildar en virtuell Linux-datoravbildning med dina installerade NVIDIA-drivrutiner finns i [Generalisera och avbilda en virtuell Linux-dator.](capture-image.md)
