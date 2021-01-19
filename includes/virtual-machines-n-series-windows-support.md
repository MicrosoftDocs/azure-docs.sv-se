---
title: ta med fil
description: ta med fil
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 7a6962a0fa1374edb5a9f43641a0adf398708acf
ms.sourcegitcommit: 9d9221ba4bfdf8d8294cf56e12344ed05be82843
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2021
ms.locfileid: "98570832"
---
## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla-drivrutiner (CUDA)

NVIDIA Tesla-drivrutiner (CUDA) för NC, NCv2, NCv3, NCasT4_v3, ND och NDv2-seriens virtuella datorer (tillval för NV-serien) stöds bara på de operativ system som anges i följande tabell. Driv rutins nedladdnings länkar är aktuella vid tidpunkten för publiceringen. De senaste drivrutinerna finns på [NVIDIA:s](https://www.nvidia.com/) webbplats.

> [!TIP]
> Som ett alternativ till manuell CUDA av driv rutins installation på en virtuell Windows Server-dator kan du distribuera en Azure [data science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) -avbildning. DSVM-versionerna för Windows Server 2016 förinstallera NVIDIA CUDA-drivrutiner, nätverks biblioteket CUDA djup neurala och andra verktyg.


| Operativsystem | Drivrutinen |
| -------- |------------- |
| Windows Server 2019 | [451,82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (. exe) |
| Windows Server 2016 | [451,82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (. exe) |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID-drivrutiner

Microsoft distribuerar NVIDIA GRID-drivrutiner installations program för virtuella datorer i NV-och NVv3-serien som används som virtuella arbets stationer eller för virtuella program. Installera endast dessa RUTNÄTs driv rutiner på virtuella datorer i Azure NV-serien, bara på de operativ system som anges i följande tabell. Dessa driv rutiner inkluderar licensiering för virtuella GPU-program i Azure i Azure. Du behöver inte konfigurera en program licens Server för NVIDIA vGPU.

RUTNÄTs driv rutiner som distribueras om av Azure fungerar inte på virtuella datorer som inte är NV-filer som NCv2, NCv3, ND och NDv2-seriens virtuella datorer. Det enda undantaget är NCas_T4_V3 VM-serien där RUTNÄTs driv rutinerna ska aktivera grafik funktionerna som liknar NV-serien.

NC-Series med NVIDIA K80-GPU: er stöder inte rutnät/grafik program.  

Observera att NVIDIA-tillägget alltid kommer att installera den senaste driv rutinen. Vi tillhandahåller länkar till den tidigare versionen här för kunder, som har beroende av en äldre version.

För Windows Server 2019, Windows Server 2016 och Windows 10 (upp till build 2004):
- [GRID 11,3 (452,77)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe)
- [GRID 11,2 (452,57)](https://download.microsoft.com/download/1/b/1/1b15516a-de49-4ba4-8651-6abda4f7fb82/452.57_grid_win10_server2016_server2019_64bit_international.exe) (. exe) 

För Windows Server 2012 R2: 
- [GRID 11,3 (452,77)](https://download.microsoft.com/download/5/4/3/54323644-3c84-4aa1-97ec-35491f94c866/452.77_grid_server2012R2_64bit_azure_swl.exe) (. exe) 
- [GRID 11,0 (451,48)](https://download.microsoft.com/download/f/7/2/f729e28b-57b8-4141-b577-38d2390973ef/451.48_grid_server2012R2_64bit_international.exe) (. exe)

En fullständig lista över alla tidigare NVIDIA GRID driv rutins länkar finns på [GitHub](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json)
