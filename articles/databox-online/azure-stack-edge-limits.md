---
title: Azure Stack gräns Pro-gränser | Microsoft Docs
description: Lär dig mer om gränser och rekommenderade storlekar när du distribuerar och använder Azure Stack Edge Pro, inklusive tjänst gränser, enhets gränser och lagrings gränser.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/12/2020
ms.author: alkohli
ms.openlocfilehash: f1bb5662b48765c08ec58d1f7fa9d341de4937bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91992755"
---
# <a name="azure-stack-edge-pro-limits"></a>Azure Stack Edge Pro – begränsningar

Tänk på följande begränsningar när du distribuerar och använder din Microsoft Azure Stack Edge Pro-lösning. 

## <a name="azure-stack-edge-service-limits"></a>Gränser för Azure Stack Edge-tjänsten

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-device-limits"></a>Gränser för Azure Stack gräns enheter

I följande tabell beskrivs gränserna för den Azure Stack Edge Pro-enheten. 

I följande tabell beskrivs gränserna för Azure Stack Edge-enheten.

| Beskrivning | Värde |
|---|---|
|Nej. av filer per enhet |100 000 000 |
|Nej. av resurser per behållare |1 |
|Maximalt antal. av resurs slut punkter och REST-slutpunkter per enhet| 24 |
|Maximalt antal. av skiktade lagrings konton per enhet| 24|
|Maximal fil storlek som skrivs till en resurs| 5 TB |
|Maximalt antal resurs grupper per enhet| 800 |

## <a name="azure-storage-limits"></a>Azure Storage-gränser

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Varningar för datauppladdning

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Storleks gränser för Azure Storage-konto och objekt storlek

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Storleks gränser för Azure-objekt

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Nästa steg

- [Förbered för att distribuera Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md)
