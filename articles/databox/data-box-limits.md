---
title: Azure Data Box gränser | Microsoft Docs
description: Beskriver system begränsningar och rekommenderade storlekar för Microsoft Azure Data Box-enhet komponenter och anslutningar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 01/05/2021
ms.author: alkohli
ms.openlocfilehash: 97d8da86565db73aa9a3866f39f793aaf0905470
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900169"
---
# <a name="azure-data-box-limits"></a>Azure Data Box gränser

Tänk på följande begränsningar när du distribuerar och använder Microsoft Azure Data Box-enhet. I följande tabell beskrivs de här gränserna för Data Box-enhet.

## <a name="data-box-service-limits"></a>Data Box-enhet tjänst gränser

[!INCLUDE [data-box-service-limits](../../includes/data-box-service-limits.md)]

## <a name="data-box-limits"></a>Data Box-enhet gränser

- Data Box-enhet kan lagra högst 500 000 000 filer för både import och export.
- Data Box-enhet stöder högst 512 behållare eller resurser i molnet. Katalogerna på den översta nivån i användar resursen blir behållare eller Azure-filresurser i molnet. 
- Data Box-enhet användnings kapaciteten kan vara mindre än 80 TB på grund av användningen av ReFS-metadatalagret.
- Data Box-enhet stöder högst 10 klient anslutningar i taget på en NFS-resurs.

## <a name="azure-storage-limits"></a>Azure Storage-gränser

[!INCLUDE [data-box-storage-limits](../../includes/data-box-storage-limits.md)]

## <a name="data-upload-caveats"></a>Varningar för datauppladdning


### <a name="for-import-order"></a>För import ordning

Data Box-enhet varningar för en import order:

[!INCLUDE [data-box-data-upload-caveats](../../includes/data-box-data-upload-caveats.md)]

### <a name="for-export-order"></a>För export ordning

Data Box-enhets varningar för en export order är:

- Data Box-enhet är en Windows-baserad enhet som inte stöder Skift läges känsliga fil namn. Du kan till exempel ha två olika filer i Azure med namn som bara skiljer sig i skift läge. Använd inte Data Box-enhet för att exportera sådana filer eftersom filerna kommer att skrivas över på enheten.
- Om du har dubbla Taggar i indatafiler eller taggar som refererar till samma data, kan Data Box-enhet export hoppa över eller skriva över filerna. Antalet filer och data storlekar som Azure Portal visar kan skilja sig från den faktiska storleken på data på enheten. 
- Data Box-enhet exporterar data till Windows-baserat system via SMB och begränsas av SMB-begränsningar för filer och mappar. Filer och mappar med namn som inte stöds exporteras inte.
- Det finns en 1:1-mappning från prefixet till container.
- Den största fil storleken är 1024 tecken. Fil namn som överskrider den här längden exporteras inte.
- Dubbla prefix i *XML-* filen (uppladdat vid skapande av order) exporteras. Duplicerade prefix ignoreras inte.
- Sid-blobbar och behållar namn är Skift läges känsliga. Om SKIFT läget inte stämmer hittas inte blobben och/eller behållaren.
 

## <a name="azure-storage-account-size-limits"></a>Storleks gränser för Azure Storage-kontot

[!INCLUDE [data-box-storage-account-size-limits](../../includes/data-box-storage-account-size-limits.md)]

## <a name="azure-object-size-limits"></a>Storleks gränser för Azure-objekt

[!INCLUDE [data-box-object-size-limits](../../includes/data-box-object-size-limits.md)]

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Konventioner för Azure Block Blob, Page blob och namngivning av filer

[!INCLUDE [data-box-naming-conventions](../../includes/data-box-naming-conventions.md)]
