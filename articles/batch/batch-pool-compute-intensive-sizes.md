---
title: Använd beräknings intensiva virtuella Azure-datorer med batch
description: Hur du kan dra nytta av virtuella datorer i HPC och GPU i Azure Batch pooler. Lär dig mer om OS-beroenden och se flera scenario exempel.
ms.topic: how-to
ms.date: 12/17/2018
ms.openlocfilehash: 016da7669c9e6a6586a53d379f9665c9ea048b64
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86147332"
---
# <a name="use-rdma-or-gpu-instances-in-batch-pools"></a>Använd RDMA-eller GPU-instanser i batch-pooler

Om du vill köra vissa batch-jobb kan du dra nytta av Azure VM-storlekar som har utformats för storskalig beräkning. Exempel:

* Om du vill köra [MPI-arbetsbelastningar](batch-mpi.md)med flera instanser väljer du H-serien eller andra storlekar som har ett nätverks gränssnitt för RDMA (Remote Direct Memory Access). Dessa storlekar ansluter till ett InfiniBand-nätverk för kommunikation mellan noder, vilket kan påskynda MPI-program. 

* För CUDA-program väljer du N-seriens storlekar som innehåller GPU-kort (Graphics Processing Unit) med NVIDIA Tesla.

Den här artikeln innehåller vägledning och exempel för att använda vissa av Azures specialiserade storlekar i batch-pooler. För specifikationer och bakgrund, se:

* Hög prestanda beräkning VM-storlekar ([Linux](../virtual-machines/sizes-hpc.md), [Windows](../virtual-machines/sizes-hpc.md)) 

* GPU-aktiverade VM-storlekar ([Linux](../virtual-machines/sizes-gpu.md), [Windows](../virtual-machines/sizes-gpu.md)) 

> [!NOTE]
> Vissa VM-storlekar kanske inte är tillgängliga i de regioner där du skapar dina batch-konton. Om du vill kontrol lera att en storlek är tillgänglig går du till [produkter som är tillgängliga efter region](https://azure.microsoft.com/regions/services/) och [väljer en VM-storlek för en batch-pool](batch-pool-vm-sizes.md).

## <a name="dependencies"></a>Beroenden

RDMA-eller GPU-funktionerna för beräknings intensiva storlekar i batch stöds bara i vissa operativ system. (Listan över operativ system som stöds är en delmängd av de som stöds för virtuella datorer som skapats i dessa storlekar.) Beroende på hur du skapar en batch-pool kan du behöva installera eller konfigurera ytterligare driv rutiner eller annan program vara på noderna. Följande tabeller sammanfattar dessa beroenden. Mer information finns i länkade artiklar. Alternativ för att konfigurera batch-pooler finns längre fram i den här artikeln.

### <a name="linux-pools---virtual-machine-configuration"></a>Linux-pooler – konfiguration av virtuell dator

| Storlek | Funktion | Operativsystem | Programvara som krävs | Inställningar för pool |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Ubuntu 16,04 LTS eller<br/>CentOS-baserat HPC<br/>(Azure Marketplace) | Intel MPI 5<br/><br/>Linux RDMA-drivrutiner | Aktivera kommunikation mellan noder, inaktivera körning av samtidiga aktiviteter |
| [NC, NCv2, NCv3, NDv2-serien](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla GPU (varierar per serie) | Ubuntu 16,04 LTS eller<br/>CentOS 7,3 eller 7,4<br/>(Azure Marketplace) | NVIDIA CUDA-eller CUDA Toolkit-drivrutiner | Ej tillämpligt | 
| [NV, NVv2-serien](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16,04 LTS eller<br/>CentOS 7,3<br/>(Azure Marketplace) | NVIDIA GRID-drivrutiner | Ej tillämpligt |

<sup>*</sup>RDMA-kompatibla N-seriens storlekar inkluderar också NVIDIA Tesla GPU: er

### <a name="windows-pools---virtual-machine-configuration"></a>Windows-pooler – konfiguration av virtuell dator

| Storlek | Funktion | Operativsystem | Programvara som krävs | Inställningar för pool |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/windows/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Windows Server 2016, 2012 R2 eller<br/>2012 (Azure Marketplace) | Microsoft MPI 2012 R2 eller senare, eller<br/> Intel MPI 5<br/><br/>Windows RDMA-drivrutiner | Aktivera kommunikation mellan noder, inaktivera körning av samtidiga aktiviteter |
| [NC, NCv2, NCv3, ND, NDv2-serien](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla GPU (varierar per serie) | Windows Server 2016 eller <br/>2012 R2 (Azure Marketplace) | NVIDIA CUDA-eller CUDA Toolkit-drivrutiner| Ej tillämpligt | 
| [NV, NVv2-serien](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 eller<br/>2012 R2 (Azure Marketplace) | NVIDIA GRID-drivrutiner | Ej tillämpligt |

<sup>*</sup>RDMA-kompatibla N-seriens storlekar inkluderar också NVIDIA Tesla GPU: er

### <a name="windows-pools---cloud-services-configuration"></a>Windows-pooler – Cloud Services-konfiguration

> [!NOTE]
> N-seriens storlekar stöds inte i batch-pooler med moln tjänst konfigurationen.
>

| Storlek | Funktion | Operativsystem | Programvara som krävs | Inställningar för pool |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md) | RDMA | Windows Server 2016, 2012 R2, 2012 eller<br/>2008 R2 (gäst operativ system familj) | Microsoft MPI 2012 R2 eller senare, eller<br/>Intel MPI 5<br/><br/>Windows RDMA-drivrutiner | Aktivera kommunikation mellan noder,<br/> Inaktivera körning av samtidiga aktiviteter |

## <a name="pool-configuration-options"></a>Konfigurations alternativ för pool

Om du vill konfigurera en specialiserad VM-storlek för batch-poolen har du flera alternativ för att installera nödvändig program vara eller driv rutiner:

* För pooler i konfigurationen för den virtuella datorn väljer du en förkonfigurerad VM-avbildning för [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) med driv rutiner och program vara förinstallerad. Exempel: 

  * [CentOS-baserad 7,4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) – innehåller RDMA-drivrutiner och Intel MPI 5,1

  * [Data science Virtual Machine](../machine-learning/data-science-virtual-machine/overview.md) för Linux eller Windows – innehåller NVIDIA CUDA-drivrutiner

  * Linux-avbildningar för arbets belastningar för batch-behållare som också innehåller GPU-och RDMA-driv rutiner:

    * [CentOS (med GPU-och RDMA-drivrutiner) för Azure Batch behållar pooler](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.centos-container-rdma?tab=Overview)

    * [Ubuntu Server (med GPU-och RDMA-drivrutiner) för Azure Batch behållar pooler](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.ubuntu-server-container-rdma?tab=Overview)

* Skapa en [anpassad Windows-eller Linux VM-avbildning](batch-sig-images.md) där du har installerat driv rutiner, program eller andra inställningar som krävs för den virtuella dator storleken. 

* Skapa ett batch- [programpaket](batch-application-packages.md) från en zippad driv rutin eller ett installations program och konfigurera batch för att distribuera paketet till pool-noder och installera en gång när varje nod skapas. Om programpaketet till exempel är ett installations program skapar du en kommando rad för [Start uppgift](jobs-and-tasks.md#start-task) för att tyst installera appen på alla noder i poolen. Överväg att använda ett programpaket och en uppgift för att starta en pool om din arbets belastning är beroende av en viss driv rutins version.

  > [!NOTE] 
  > Start aktiviteten måste köras med utökade (admin) behörigheter och måste vänta på framgång. Tids krävande aktiviteter ökar tiden för att etablera en batch-pool.
  >

* [Batch-Shipyard](https://github.com/Azure/batch-shipyard) konfigurerar automatiskt GPU-och RDMA-drivrutinerna så att de fungerar transparent med arbets belastningar i behållare på Azure Batch. Batch-Shipyard är helt driven med konfigurationsfiler. Det finns många exempel på recept konfigurationer som möjliggör GPU-och RDMA-arbetsbelastningar, till exempel [CNTK GPU-recept](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) som förkonfigurerar GPU-drivrutiner för virtuella datorer i N-serien och läser in Microsoft Cognitive Toolkit program vara som en Docker-avbildning.


## <a name="example-nvidia-gpu-drivers-on-windows-nc-vm-pool"></a>Exempel: NVIDIA GPU-drivrutiner på Windows NC VM-pool

Om du vill köra CUDA-program på en pool med Windows NC-noder måste du installera NVDIA GPU-drivrutiner. Följande exempel steg använder ett programpaket för att installera NVIDIA GPU-drivrutinerna. Du kan välja det här alternativet om din arbets belastning är beroende av en speciell driv rutins version av GPU.

1. Hämta ett installations paket för GPU-drivrutinerna på Windows Server 2016 från [NVIDIA-webbplatsen](https://www.nvidia.com/Download/index.aspx) , till exempel [version 411,82](https://us.download.nvidia.com/Windows/Quadro_Certified/411.82/411.82-tesla-desktop-winserver2016-international.exe). Spara filen lokalt med ett kort namn som *GPUDriverSetup.exe*.
2. Skapa en zip-fil för paketet.
3. Ladda upp paketet till ditt batch-konto. Anvisningar finns i vägledningen för [programpaket](batch-application-packages.md) . Ange ett program-ID, till exempel *GPUDriver*, och en version som *411,82*.
1. Med hjälp av batch-API: erna eller Azure Portal skapar du en pool i konfigurationen för den virtuella datorn med önskat antal noder och skalning. I följande tabell visas exempel inställningar för att installera NVIDIA GPU-drivrutinerna tyst med en start aktivitet:

| Inställning | Värde |
| ---- | ----- | 
| **Avbildnings typ** | Marketplace (Linux/Windows) |
| **Utgivare** | MicrosoftWindowsServer |
| **Erbjudande** | WindowsServer |
| **SKU** | 2016 – Data Center |
| **Node-storlek** | FÖR nc6 standard |
| **Referenser för programpaket** | GPUDriver, version 411,82 |
| **Starta uppgift aktive rad** | Sant<br>**Kommando rad** - `cmd /c "%AZ_BATCH_APP_PACKAGE_GPUDriver#411.82%\\GPUDriverSetup.exe /s"`<br/>**Användare identitet** -pool-autouser, admin<br/>**Vänta på framgång** – sant

## <a name="example-nvidia-gpu-drivers-on-a-linux-nc-vm-pool"></a>Exempel: NVIDIA GPU-drivrutiner i en Linux NC VM-pool

Om du vill köra CUDA-program på en pool med Linux NC-noder måste du installera nödvändiga NVIDIA Tesla GPU-drivrutiner från CUDA Toolkit. Följande exempel steg skapar och distribuerar en anpassad Ubuntu 16,04 LTS-avbildning med GPU-drivrutinerna:

1. Distribuera en virtuell dator med Azure NC-serien som kör Ubuntu 16,04 LTS. Skapa till exempel den virtuella datorn i regionen USA, södra centrala. 
2. Lägg till [tillägget nVidia GPU-drivrutiner](../virtual-machines/extensions/hpccompute-gpu-linux.md
) på den virtuella datorn med hjälp av Azure Portal, en klient dator som ansluter till Azure-prenumerationen eller Azure Cloud Shell. Du kan också följa stegen för att ansluta till den virtuella datorn och [Installera CUDA-drivrutiner](../virtual-machines/linux/n-series-driver-setup.md) manuellt.
3. Följ stegen för att skapa en [bild för ett delat bild galleri](batch-sig-images.md) för batch.
4. Skapa ett batch-konto i en region som stöder NC-VM: ar.
5. Med hjälp av batch-API: erna eller Azure Portal skapar du en pool [med hjälp av den anpassade avbildningen](batch-sig-images.md) och med önskat antal noder och skalning. I följande tabell visas exempel på poolinställningarna för avbildningen:

| Inställning | Värde |
| ---- | ---- |
| **Avbildnings typ** | Anpassad avbildning |
| **Anpassad bild** | *Namn på avbildningen* |
| **Node agent-SKU** | batch. Node. Ubuntu 16,04 |
| **Node-storlek** | FÖR nc6 standard |

## <a name="example-microsoft-mpi-on-a-windows-h16r-vm-pool"></a>Exempel: Microsoft MPI på en Windows H16r VM-pool

Om du vill köra Windows MPI-program på en pool med virtuella Azure H16r-noder måste du konfigurera HpcVmDrivers-tillägget och installera [Microsoft MPI](/message-passing-interface/microsoft-mpi). Här följer exempel steg för att distribuera en anpassad Windows Server 2016-avbildning med nödvändiga driv rutiner och program vara:

1. Distribuera en virtuell Azure H16r-dator som kör Windows Server 2016. Skapa till exempel den virtuella datorn i regionen USA, västra. 
2. Lägg till HpcVmDrivers-tillägget till den virtuella datorn genom att [köra ett Azure PowerShell kommando](../virtual-machines/sizes-hpc.md) från en klient dator som ansluter till din Azure-prenumeration eller med Azure Cloud Shell. 
1. Upprätta en fjärr skrivbords anslutning till den virtuella datorn.
1. Hämta [installations paketet](https://www.microsoft.com/download/details.aspx?id=57467) (MSMpiSetup.exe) för den senaste versionen av Microsoft MPI och installera Microsoft MPI.
1. Följ stegen för att skapa en [bild för ett delat bild galleri](batch-sig-images.md) för batch.
1. Med hjälp av batch-API: erna eller Azure Portal skapar du en pool [med hjälp av det delade avbildnings galleriet](batch-sig-images.md) och med önskat antal noder och skalning. I följande tabell visas exempel på poolinställningarna för avbildningen:

| Inställning | Värde |
| ---- | ---- |
| **Avbildnings typ** | Anpassad avbildning |
| **Anpassad bild** | *Namn på avbildningen* |
| **Node agent-SKU** | batch. Node. Windows amd64 |
| **Node-storlek** | H16r standard |
| **Kommunikation mellan noder är aktiverat** | Sant |
| **Maximalt antal aktiviteter per nod** | 1 |

## <a name="example-intel-mpi-on-a-linux-h16r-vm-pool"></a>Exempel: Intel MPI på en Linux H16r VM-pool

För att köra MPI-program på en pool med Linux H-serien noder, är ett alternativ att använda den [CentOS-baserade 7,4 HPC-](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) avbildningen från Azure Marketplace. Linux RDMA-drivrutiner och Intel MPI förinstalleras. Den här avbildningen har även stöd för arbets belastningar med Docker-behållare.

Med hjälp av batch-API: erna eller Azure Portal skapar du en pool med hjälp av den här avbildningen och med önskat antal noder och skalning. I följande tabell visas exempel på poolinställningarna:

| Inställning | Värde |
| ---- | ---- |
| **Avbildnings typ** | Marketplace (Linux/Windows) |
| **Utgivare** | OpenLogic |
| **Erbjudande** | CentOS – HPC |
| **SKU** | 7,4 |
| **Node-storlek** | H16r standard |
| **Kommunikation mellan noder är aktiverat** | Sant |
| **Maximalt antal aktiviteter per nod** | 1 |

## <a name="next-steps"></a>Nästa steg

* Information om hur du kör MPI-jobb på en Azure Batch pool finns i [Windows](batch-mpi.md) -eller [Linux](/archive/blogs/windowshpc/introducing-mpi-support-for-linux-on-azure-batch) -exempel.

* Exempel på GPU-arbetsbelastningar i batch finns i [batch-Shipyard](https://github.com/Azure/batch-shipyard/) recept.
