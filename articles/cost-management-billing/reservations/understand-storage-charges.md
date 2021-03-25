---
title: Förstå hur reservations rabatter tillämpas på Azure Storage-tjänster | Microsoft Docs
description: Lär dig mer om hur reserverade kapacitets rabatter tillämpas på Azure Blob Storage, Azure Files och Azure Data Lake Storage Gen2 resurser.
author: tamram
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: tamram
ms.openlocfilehash: f18097f0da658b3a485b79f5279e9ecc54a41ea0
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024038"
---
# <a name="understand-how-reservation-discounts-are-applied-to-azure-storage-services"></a>Förstå hur reservations rabatter tillämpas på Azure Storage-tjänster 
Med Azure Storage-tjänster kan du spara pengar på lagrings kostnader genom att reservera kapacitet. Azure Blob Storage, Azure Files och Azure Data Lake för lagring av Gen 2 support reserv instanser. När du har köpt reserverad kapacitet tillämpas reservations rabatten automatiskt på de lagrings resurser som matchar villkoren i reservationen. Reservationsrabatten gäller endast för lagringskapacitet. Bandbredd och förfrågningsfrekvens debiteras till Betala per användning-priser.

Mer information om Azure Blob Storage och Azure Data Lake Storage gen 2 reserverad kapacitet finns i [optimera kostnader för Blob Storage med reserverad kapacitet](../../storage/blobs/storage-blob-reserved-capacity.md). Mer information om Azure Files reserverad kapacitet finns i [optimera kostnader för Azure Files med reserverad kapacitet](../../storage/files/files-reserve-capacity.md).

Information om Azure Blob Storage reservations priser finns i avsnittet om priser för [Block-Blob](https://azure.microsoft.com/pricing/details/storage/blobs/) och [Azure Data Lake Storage gen 2-priser](https://azure.microsoft.com/pricing/details/storage/data-lake/). Information om Azure Files Storage reservation-prissättning finns i [Azure Files prissättning](https://azure.microsoft.com/pricing/details/storage/files).

## <a name="how-the-reservation-discount-is-applied"></a>Så tillämpas reservationsrabatten
Den reserverade kapacitets rabatten tillämpas på lagrings resurser som stöds per timme.

Rabatten för reserverad kapacitet är rabatten "Använd-IT-eller-förlorad-IT". Om du inte har några block blobbar, Azure-filresurser eller Azure Data Lake Storage Gen2 resurser som uppfyller villkoren i reservationen för en specifik timme, förlorar du en reservations kvantitet för den timmen. Det går inte att föra vidare oanvända reserverade timmar.

När du tar bort en resurs tillämpas reservationsrabatten automatiskt på en annan matchande resurs i det angivna omfånget. Om det inte finns några matchande resurser i det angivna omfånget går de reserverade timmarna förlorade.

## <a name="discount-examples"></a>Exempel på rabatter
I följande exempel visas hur den reserverade kapacitets rabatten gäller, beroende på distributionerna.

Anta att du har köpt 100-TiB av reserverad kapacitet i USA, västra 2-regionen för ett års period. Din reservation är för lokalt redundant lagring (LRS) Blob Storage i frekvent åtkomst nivå.

Anta att kostnaden i den här exempelreservationen är 18 540 USD. Du kan välja att betala hela beloppet direkt eller betala i fasta månatliga avbetalningar på 1 545 USD per månad under de kommande tolv månaderna.

Anta för dessa exempel att du har registrerat dig för en månatlig reservationsbetalningsplan. Följande scenarier beskriver vad som händer om du underutnyttjar eller överutnyttjar din reserverade kapacitet.

### <a name="underusing-your-capacity"></a>Underutnyttja kapaciteten
Anta att du under en viss timme inom reservations perioden bara använde 80 TiB av din 100 TiB-reserverade kapacitet. Det återstående 20-TiB gäller inte för den timmen och överför inte.

### <a name="overusing-your-capacity"></a>Överutnyttja kapaciteten
Anta att du har använt 101 TiB lagrings kapacitet under en viss timme inom reservations perioden. Reservations rabatten gäller för 100 TiB av dina data och återstående 1 TiB debiteras enligt priserna för betala per användning för den timmen. Om din användning ändras till 100 TiB i nästa timme, omfattas all användning av reservationen.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss
Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- [Optimera kostnader för blobblagring med reserverad kapacitet](../../storage/blobs/storage-blob-reserved-capacity.md)
- [Optimera kostnader för Azure Files med reserverad kapacitet](../../storage/files/files-reserve-capacity.md)
- [Vad är Azure-reservationer?](save-compute-costs-reservations.md)
