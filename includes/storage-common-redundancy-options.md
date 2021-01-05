---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/14/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 65729934ea7c4037d6857aec10b14cdddd616368
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/29/2020
ms.locfileid: "97805672"
---
Alternativ för redundans för ett lagrings konto är:

* Lokalt redundant lagring (LRS): en enkel, låg kostnads redundans strategi. Data kopieras synkront tre gånger inom en enda fysisk plats i den primära regionen.
* Zone-redundant lagring (ZRS): redundans för scenarier som kräver hög tillgänglighet. Data kopieras synkront över tre tillgänglighets zoner i Azure i den primära regionen.
* Geo-redundant lagring (GRS): Cross-regional redundans för att skydda mot regionala avbrott. Data kopieras synkront tre gånger i den primära regionen och kopieras sedan asynkront till den sekundära regionen. För Läs åtkomst till data i den sekundära regionen aktiverar du Geo-redundant lagring med Läs behörighet (RA-GRS).
* Geo-Zone-redundant lagring (GZRS) (för hands version): redundans för scenarier som kräver både hög tillgänglighet och maximal hållbarhet. Data kopieras synkront över tre tillgänglighets zoner i Azure i den primära regionen och kopieras sedan asynkront till den sekundära regionen. För Läs åtkomst till data i den sekundära regionen aktiverar du Läs åtkomst geo-Zone-redundant lagring (RA-GZRS).

Mer information om alternativ för redundans i Azure Storage finns [Azure Storage redundans](../articles/storage/common/storage-redundancy.md).
