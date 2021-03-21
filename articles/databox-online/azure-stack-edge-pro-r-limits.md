---
title: Azure Stack Edge Pro R-gränser | Microsoft Docs
description: Beskriver system begränsningar och rekommenderade storlekar för Azure Stack Edge Pro R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: dfff3bdd716c54a6c83dbc9fec63c794c1fba85b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96467629"
---
# <a name="azure-stack-edge-pro-r-limits"></a>Azure Stack Edge Pro R-gränser

Tänk på följande begränsningar när du distribuerar och använder din Azure Stack Edge Pro R-lösning.

## <a name="azure-stack-edge-pro-r-service-limits"></a>Gränser för Azure Stack Edge Pro R-tjänsten

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-pro-r-device-limits"></a>Gränser för Azure Stack Edge Pro R-enhet

I följande tabell beskrivs gränserna för den Azure Stack Edge Pro R-enheten.

| Beskrivning | Värde |
|---|---|
|Nej. av filer per enhet |100 000 000 |
|Nej. av resurser per behållare |1 |
|Maximalt antal. av resurs slut punkter och REST-slutpunkter per enhet| 24 |
|Maximalt antal. av skiktade lagrings konton per enhet| 24|
|Maximal fil storlek som skrivs till en resurs| 5 TB |
|Maximalt antal resurs grupper per enhet| 800 |

## <a name="azure-storage-limits"></a>Azure Storage-gränser

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Varningar för datauppladdning

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Storleks gränser för Azure Storage-konto och objekt storlek

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Storleks gränser för Azure-objekt

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Nästa steg

- [Förbered för att distribuera Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md)
