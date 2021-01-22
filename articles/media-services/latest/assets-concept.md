---
title: Tillgångar
titleSuffix: Azure Media Services
description: Lär dig mer om vilka till gångar som är och hur de används av Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: f6f4cfcc07496179908c8349f4282a7633379cd3
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98695836"
---
# <a name="assets-in-azure-media-services-v3"></a>Till gångar i Azure Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

I Azure Media Services är en [till gång](/rest/api/media/assets) ett Core-begrepp. Det är här du matar in media (till exempel via överföring eller Live-inmatning), utdata-medium (från ett jobb) och publicerar media från (för strömning). 

En till gång mappas till en BLOB-behållare i [Azure Storage-kontot](storage-account-concept.md) och filerna i till gången lagras som block-blobbar i den behållaren. Till gångar innehåller information om digitala filer som lagras i Azure Storage (inklusive video, ljud, bilder, miniatyr samlingar, text spår och filer med dold textning).

Media Services stöder BLOB-nivåer när kontot använder General-Purpose v2-lagring (GPv2). Med GPv2 kan du flytta filer till låg frekvent [lagring eller Arkiv lag](../../storage/blobs/storage-blob-storage-tiers.md)ring. **Arkiv** lag ring är lämpligt för att arkivera källfiler när de inte längre behövs (till exempel efter att de har kodats).

**Arkiv** lag rings nivån rekommenderas endast för mycket stora källfiler som redan har kodats och kodnings jobbets utdata lades till i en utgående BLOB-behållare. De blobbar i behållaren för utdata som du vill associera med en till gång och som används för att **strömma eller analysera** innehållet måste finnas på en frekvent eller låg **frekvent lagrings** nivå.

## <a name="naming"></a>Namngivning 

### <a name="assets"></a>Tillgångar

Till gångens namn måste vara unika. Media Services v3-resurs namn (till exempel till gångar, jobb, transformeringar) lyder under Azure Resource Manager namngivnings begränsningar. Mer information finns i [namngivnings konventioner](media-services-apis-overview.md#naming-conventions).

### <a name="blobs"></a>Blobar

Namnen på filer/blobbar i en till gång måste följa både BLOB- [namnets krav](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) och [kraven för NTFS-namn](/windows/win32/fileio/naming-a-file). Orsaken till dessa krav är att filerna kan kopieras från Blob Storage till en lokal NTFS-disk för bearbetning.

## <a name="next-steps"></a>Nästa steg

[Översikt över Media Services](media-services-overview.md)

## <a name="see-also"></a>Se även

[Skillnader mellan Media Services v2 och v3](migrate-v-2-v-3-migration-introduction.md)
