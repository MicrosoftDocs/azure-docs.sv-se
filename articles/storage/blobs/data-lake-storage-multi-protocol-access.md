---
title: Åtkomst till flera protokoll på Azure Data Lake Storage | Microsoft Docs
description: 'Använd BLOB-API: er och program som använder BLOB-API: er med Azure Data Lake Storage Gen2.'
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e3997fc215637175165402a926bffc6ac8d02771
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "77914866"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Åtkomst till flera protokoll på Azure Data Lake Storage

BLOB-API: er fungerar nu med konton som har ett hierarkiskt namn område. Detta låser upp eko systemet för verktyg, program och tjänster, samt flera Blob Storage-funktioner till konton som har ett hierarkiskt namn område.

Tills nyligen har du kanske haft för att ha haft separata lagrings lösningar för objekt lagring och analys lagring. Det beror på att Azure Data Lake Storage Gen2 har begränsat stöd för eko systemet. Det hade också begränsad åtkomst till Blob Service funktioner som diagnostisk loggning. En fragmenterad lagrings lösning är svår att underhålla eftersom du måste flytta data mellan konton för att utföra olika scenarier. Du behöver inte längre göra det.

Med åtkomst med flera protokoll på Data Lake Storage kan du arbeta med dina data med hjälp av eko systemets verktyg, program och tjänster. Detta inkluderar även verktyg och program från tredje part. Du kan peka dem på konton som har ett hierarkiskt namn område utan att behöva ändra dem. Dessa program fungerar *som de är* även om de anropar BLOB-API: er, eftersom BLOB-API: er nu kan köras på data i konton som har ett hierarkiskt namn område.

Blob Storage-funktioner som [diagnostisk loggning](../common/storage-analytics-logging.md), [åtkomst nivåer](storage-blob-storage-tiers.md)och principer för [hantering av BLOB-lagrings livs cykeln](storage-lifecycle-management-concepts.md) fungerar nu med konton som har ett hierarkiskt namn område. Därför kan du aktivera hierarkiska namn rymder på Blob Storage-kontona utan att förlora åtkomsten till dessa viktiga funktioner. 

> [!NOTE]
> Åtkomst till flera protokoll på Data Lake Storage är allmänt tillgänglig och är tillgänglig i alla regioner. Vissa Azure-tjänster eller Blob Storage-funktioner som aktive ras av åtkomst till flera protokoll är fortfarande i för hands version.  De här artiklarna sammanfattar det aktuella stödet för Blob Storage-funktioner och Azure Service integrationer. 
>
> [Blob Storage funktioner som är tillgängliga i Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
>
>[Azure-tjänster som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Så här fungerar åtkomst till flera protokoll på data Lake Storage

BLOB-API: er och Data Lake Storage Gen2-API: er kan köras på samma data i lagrings konton som har ett hierarkiskt namn område. Data Lake Storage Gen2 dirigerar BLOB-API: er via det hierarkiska namn området så att du kan få fördelarna med de första klasserna i klass katalog och POSIX-kompatibla åtkomst kontrol listor (ACL: er). 

![Åtkomst till flera protokoll på Data Lake Storage konceptuell](./media/data-lake-storage-interop/interop-concept.png) 

Befintliga verktyg och program som använder BLOB API får dessa fördelar automatiskt. Utvecklare behöver inte ändra dem. Data Lake Storage Gen2 konsekvent tillämpar katalog-och fil nivå åtkomst kontrol listor oavsett vilket protokoll som verktyg och program använder för att komma åt data. 

## <a name="see-also"></a>Se även

- [Blob Storage funktioner som är tillgängliga i Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Azure-tjänster som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Plattformar med öppen källkod som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Kända problem med Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)




