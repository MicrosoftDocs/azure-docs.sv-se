---
title: Azure Stack Edge Mini R-gränser | Microsoft Docs
description: Beskriver system begränsningar och rekommenderade storlekar för Azure Stack Edge Mini R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 6f01d62d1d11f5ff90661482ffd2db112657eee5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96467848"
---
# <a name="azure-stack-edge-mini-r-limits"></a>Azure Stack Edge Mini R-gränser


Tänk på följande begränsningar när du distribuerar och använder din Azure Stack Edge Mini R-lösning.

## <a name="azure-stack-edge-service-limits"></a>Gränser för Azure Stack Edge-tjänsten

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-mini-r-device-limits"></a>Gränser för Azure Stack Edge Mini R-enheter

I följande tabell beskrivs gränserna för den Azure Stack Edge Mini R-enheten.

| Beskrivning | Gräns|
|---|---:|
|Nej. av filer per enhet | 100 000 000 <!--check with devs-->|
|Nej. av resurser per behållare | 1|
|Maximalt antal. av resurs slut punkter och REST-slutpunkter per enhet| 24 |
|Maximalt antal. av skiktade lagrings konton per enhet| 24|
|Maximal fil storlek som skrivs till en resurs| 500 GB|
|Maximalt antal resurs grupper per enhet| 800|

## <a name="azure-storage-limits"></a>Azure Storage-gränser

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Varningar för datauppladdning

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Storleks gränser för Azure Storage-konto och objekt storlek

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Storleks gränser för Azure-objekt

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Nästa steg

- [Förbered för att distribuera Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md)
