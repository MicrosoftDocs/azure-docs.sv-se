---
title: Azure N-serien NVIDIA GPU-drivrutins installation för Windows
description: Konfigurera NVIDIA GPU-drivrutiner för virtuella datorer i N-serien som kör Windows Server eller Windows i Azure
author: vikancha-MSFT
manager: jkabat
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.collection: windows
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: vikancha
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 05487db1d0522634eda606dcb2ba22876fc341db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557445"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-windows"></a>Installera NVIDIA GPU-drivrutiner för virtuella datorer i N-serien som kör Windows 

Om du vill dra nytta av GPU-funktionerna i Azure N-seriens virtuella datorer som backas upp av NVIDIA GPU: er måste du installera NVIDIA GPU-drivrutiner. [NVidia GPU-drivrutinen](../extensions/hpccompute-gpu-windows.md) installerar lämpliga NVIDIA-CUDA eller rutnäts driv rutiner på en virtuell dator i N-serien. Installera eller hantera tillägget med hjälp av Azure Portal eller verktyg som Azure PowerShell eller Azure Resource Manager mallar. Mer information om vilka operativ system och distributions steg som stöds finns i [dokumentationen för NVIDIA GPU-drivrutins tillägget](../extensions/hpccompute-gpu-windows.md) .

Om du väljer att installera NVIDIA GPU-drivrutiner manuellt, innehåller den här artikeln stöd för operativ system, driv rutiner och installations-och verifierings steg. Det finns även information om manuell konfiguration av driv rutiner för [virtuella Linux-datorer](../linux/n-series-driver-setup.md).

Grundläggande specifikationer, lagrings kapacitet och disk information finns i storlekar för [GPU Windows VM](../sizes-gpu.md?toc=/azure/virtual-machines/windows/toc.json). 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Driv rutins installation

1. Anslut via fjärr skrivbord till varje virtuell dator i N-serien.

2. Hämta, extrahera och installera driv rutinen som stöds för ditt Windows-operativsystem.

Efter installationen av RUTNÄTs driv rutinen på en virtuell dator krävs en omstart. Efter installationen av CUDA-drivrutinen krävs ingen omstart.

## <a name="verify-driver-installation"></a>Verifiera installation av driv rutin

Observera att Nvidia-kontrollpanelen endast är tillgänglig med installationen av RUTNÄTs driv rutinen. Om du har installerat CUDA-drivrutiner visas inte Nvidia-kontrollpanelen på kontroll panelen.

Du kan kontrol lera driv rutins installationen i Enhetshanteraren. I följande exempel visas lyckad konfiguration av Tesla K80-kortet på en virtuell Azure NC-dator.

![Egenskaper för GPU-drivrutin](./media/n-series-driver-setup/GPU_driver_properties.png)

Kör kommando rads verktyget [NVIDIA-SMI](https://developer.nvidia.com/nvidia-system-management-interface) installerat med driv rutinen för att fråga GPU-enhetens tillstånd.

1. Öppna en kommando tolk och ändra till katalogen **C:\Program Files\NVIDIA Corporation\NVSMI** .

2. Kör `nvidia-smi`. Om driv rutinen är installerad visas utdata som liknar följande. **GPU-util** visar **0%** om du för närvarande kör en GPU-ARBETSBELASTNING på den virtuella datorn. Driv rutins versionen och GPU-informationen kan skilja sig från de som visas.

![Status för NVIDIA-enhet](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>RDMA-nätverksanslutning

RDMA-nätverksanslutning kan aktive ras på virtuella datorer med RDMA-kapacitet, till exempel NC24r som distribuerats i samma tillgänglighets uppsättning eller i en enda placerings grupp i en skalnings uppsättning för virtuella datorer. HpcVmDrivers-tillägget måste läggas till för att installera Windows nätverks enhets driv rutiner som aktiverar RDMA-anslutning. Om du vill lägga till VM-tillägget till en virtuell dator med RDMA-serien använder du [Azure PowerShell](/powershell/azure/) -cmdletar för Azure Resource Manager.

Så här installerar du det senaste version 1,1 HpcVMDrivers-tillägget på en befintlig RDMA-kompatibel virtuell dator med namnet myVM i regionen Västra USA:
  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Mer information finns i [tillägg och funktioner för virtuella datorer för Windows](../extensions/features-windows.md).

RDMA-nätverket stöder MPI-trafik (Message Passing Interface) för program som körs med [Microsoft MPI](/message-passing-interface/microsoft-mpi) eller Intel MPI 5. x. 


## <a name="next-steps"></a>Nästa steg

* Utvecklare som skapar GPU-accelererade program för NVIDIA Tesla-GPU: er kan också hämta och installera det senaste [CUDA Toolkit](https://developer.nvidia.com/cuda-downloads). Mer information finns i [installations guiden för CUDA](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).
