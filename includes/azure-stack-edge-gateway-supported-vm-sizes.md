---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/09/2020
ms.author: alkohli
ms.openlocfilehash: d22d40b21671b148083b48efe9772f118dc3b292
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105582747"
---
Storleken på den virtuella datorn avgör hur mycket data bearbetnings resurser (t. ex. processor, GPU och minne) som görs tillgängliga för den virtuella datorn. Du bör skapa virtuella datorer med hjälp av en storlek på den virtuella datorn som är lämplig för arbets belastningen. Även om alla datorer ska köras på samma maskin vara, har dator storlekarna olika begränsningar för disk åtkomst. Detta kan hjälpa dig att hantera den totala disk åtkomsten över dina virtuella datorer. Om en arbets belastning ökar kan du också ändra storlek på en befintlig virtuell dator.

Följande virtuella datorer stöds för att skapas på din Azure Stack Edge-enhet.

### <a name="dv2-series"></a>Dv2-serien
|Storlek     |Virtuell processor     |Minne (GiB) | Resurs disk storlek (GiB)  | OS-diskens storlek (GiB) | Maximalt antal datadiskar | Maximalt antal nätverkskort |
|-------------------|----|----|-----|----|------|------------|
|**Standard_D1_v2** |1   |3.5 |50   |1000| 4    |2 |
|**Standard_D2_v2** |2   |7   |100  |1000| 8    |4 |
|**Standard_D3_v2** |4   |14  |200  |1000| 16  |4 |
|**Standard_D4_v2** |8   |28  |400  |1000| 32  |8 |
|**Standard_D5_v2** |16  |56  |800  |1000| 64  |8 |
|**Standard_D11_v2** |2   |14  |100  |1000| 8     |2 |
|**Standard_D12_v2** |4   |28  |200  |1000| 16   |4 |
|**Standard_D13_v2** |8   |56  |400  |1000| 32  |8 |

### <a name="dsv2-series"></a>DSv2-serien
|Storlek     |Virtuell processor     |Minne (GiB) |  Resurs disk storlek (GiB)  | OS-diskens storlek (GiB) | Maximalt antal datadiskar| Maximalt antal nätverkskort |
|--------------------|----|----|----|-----|------|-------------|
|**Standard_DS1_v2** |1   |3.5 |7  |4000  |1000 |4  |
|**Standard_DS2_v2** |2   |7   |14 |8000  |1000 |8  |
|**Standard_DS3_v2** |4   |14  |28 |16000 |1000 |16 |
|**Standard_DS4_v2** |8   |28  |56 |32000 |1000 |32 |
|**Standard_DS5_v2** |16  |56  |112|64000 |1000 |64 |
|**Standard_DS11_v2**|2   |14  |28 |8000  |1000 |4  |
|**Standard_DS12_v2**|4   |28  |56 |16000 |1000 |8  |
|**Standard_DS13_v2**|8   |56  |112|32000 |1000 |16 |


Mer information finns i [Dv2 och DSv2-serien](../articles/virtual-machines/dv2-dsv2-series.md#dv2-series).

### <a name="ncast4_v3-series-preview"></a>NCasT4_v3-serien (för hands version)

Dessa storlekar stöds för GPU-datorer på enheten och är optimerade för beräknings intensiva GPU-accelererade program. Den här serien fokuserar på arbets belastningar med en Tesla T4-GPU. 

|Storlek     |Virtuell processor     |Minne (GiB) | Resurs disk storlek (GiB)  |OS-diskens storlek (GiB)| GPU | GPU-minne (GiB) | Maximalt antal nätverkskort |
|---------------------|----|----|-----|-----|-------|--------------|---|
|**Standard_NC4as_T4_v3** |4   |28  |180   |1000|1 |16   |4 |
|**Standard_NC8as_T4_v3** |8   |56  |360   |1000|1 |16  |8 |

Mer information finns i [NCasT4_v3-serien](../articles/virtual-machines/nct4-v3-series.md).

### <a name="f-series"></a>F-serien

Dessa serier är optimerade för beräknings arbets belastningar och körs på Intel Xeon-processorer. 

| Storlek | vCPU | Minne: GiB |Resurs disk storlek (GiB) |OS-diskens storlek (GiB)|  Maximalt antal datadiskar | Maximalt antal nätverkskort |
|---|---|---|---|---|---|---|
| Standard_F1  | 1  | 2   |16      |1000| 4  |  2 |
| Standard_F2 | 2  | 4 |32      |1000| 8  |  4 |
| Standard_F4  | 4  | 8 |64   |1000| 16 |  4 |
| Standard_F8 | 8 | 16  |132    |1000| 32 |  8 |
| Standard_F16 | 16 | 32  |262   |1000| 64 |  8 |
| Standard_F1s | 1 | 2  | 4  |1000| 4 | 1 |
| Standard_F2s | 2 | 4 |8   |1000| 8 | 4 |
| Standard_F4s | 4 | 8 |16 |1000| 16 |  4 |
| Standard_F8s | 8 | 16 |32 |1000| 32 |  8 |
| Standard_F16s | 16 | 32 |64 |1000| 64 |  8 |

Mer information finns i [Fsv2-Series](../articles/virtual-machines/fsv2-series.md).

