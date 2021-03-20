---
title: Azure Data Box Gateway gränser | Microsoft Docs
description: Beskriver system begränsningar och rekommenderade storlekar för Microsoft Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 15b01f92fe0d39d099c10c7c086790a4dbb91379
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96582659"
---
# <a name="azure-data-box-gateway-limits"></a>Azure Data Box Gateway gränser

Tänk på följande begränsningar när du distribuerar och använder Microsoft Azure Data Box Gateway-lösningen.

## <a name="data-box-gateway-service-limits"></a>Data Box Gateway tjänst gränser

[!INCLUDE [data-box-gateway-service-limits](../../includes/data-box-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Data Box Gateway enhets gränser

I följande tabell beskrivs gränserna för den Data Box Gateway enheten.

| Beskrivning | Värde |
|---|---|
|Nej. av filer per enhet |100 000 000 <br> För varje 25 000 000-filer som läggs till (med max gränsen på 100 000 000), ska du lägga till 2 TB disk utrymme, 8 GB RAM och 4 kärnor av processor. |
|Nej. av resurser per enhet |24 |
|Nej. av resurser per Azure Storage-behållare |1 |
|Maximal fil storlek som skrivs till en resurs|För en virtuell enhet på 2 TB är den maximala fil storleken 500 GB. <br> Den maximala fil storleken ökar med data disk storleken i föregående förhållande tills den når högst 5 TB. |

## <a name="azure-storage-limits"></a>Azure Storage-gränser

[!INCLUDE [data-box-gateway-storage-limits](../../includes/data-box-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Varningar för datauppladdning

[!INCLUDE [data-box-gateway-storage-data-upload-caveats](../../includes/data-box-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Storleks gränser för Azure Storage-konto och objekt storlek

[!INCLUDE [data-box-gateway-storage-acct-limits](../../includes/data-box-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Storleks gränser för Azure-objekt

[!INCLUDE [data-box-gateway-storage-object-limits](../../includes/data-box-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Nästa steg

- [Förbereda distributionen av Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
