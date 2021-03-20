---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ef9efe389894af7c792e980922ca422e9d05929b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96026768"
---
I följande tabell beskrivs standard gränserna för Azures allmänna-syfte v1, v2, Blob Storage och Block-Blob Storage-konton. Den *inkommande* gränsen refererar till alla data som skickas till ett lagrings konto. *Utgående* gräns avser alla data som tas emot från ett lagrings konto.

> [!NOTE]
> Du kan begära högre kapacitet och ingress-gränser. Kontakta [Azure-supporten](https://azure.microsoft.com/support/faq/)om du vill begära en ökning.

| Resurs | Gräns |
| --- | --- |
| Antal lagrings konton per region per prenumeration, inklusive standard-och Premium Storage-konton.| 250 |
| Maximal kapacitet för lagrings konto | 5 PiB <sup>1</sup>|
| Maximalt antal BLOB-behållare, blobbar, fil resurser, tabeller, köer, entiteter eller meddelanden per lagrings konto | Obegränsad |
| Högsta begär ande frekvens<sup>1</sup> per lagrings konto | 20 000 begär Anden per sekund |
| Maximalt antal inkommande<sup>1</sup> per lagrings konto (USA, Europa regioner) | 10 Gbit/s |
| Maximalt antal inkommande<sup>1</sup> per lagrings konto (andra regioner än USA och Europa) | 5 Gbit/s om RA-GRS/GRS är aktiverat, 10 Gbit/s för LRS/ZRS<sup>2</sup> |
| Maximalt utgånget för General-Purpose v2-och Blob Storage-konton (alla regioner) | 50 Gbit/s |
| Högsta utgående trafik för generella v1-lagrings konton (amerikanska regioner) | 20 Gbit/s om RA-GRS/GRS är aktiverat, 30 Gbit/s för LRS/ZRS<sup>2</sup> |
| Maximalt utgående för allmänna v1-lagrings konton (icke-amerikanska regioner) | 10 Gbit/s om RA-GRS/GRS är aktiverat, 15 Gbit/s för LRS/ZRS<sup>2</sup> |
| Maximalt antal virtuella nätverks regler per lagrings konto | 200 |
| Maximalt antal IP-adress regler per lagrings konto | 200 |

<sup>1</sup> Azure Storage Standard konton stöder högre kapacitets gränser och högre gränser för ingångar efter begäran. Kontakta [Azure-supporten](https://azure.microsoft.com/support/faq/)om du vill begära en ökning av konto gränserna.

<sup>2</sup> om ditt lagrings konto har Läs behörighet aktiverat med Geo-redundant lagring (RA-GRS) eller geo-Zone-redundant lagring (ra-GZRS), är utgångs målen för den sekundära platsen identiska med de på den primära platsen. Mer information finns i [Azure Storage replikering](../articles/storage/common/storage-redundancy.md).

> [!NOTE]
> Microsoft rekommenderar att du använder ett allmänt-syfte v2-lagrings konto för de flesta scenarier. Du kan enkelt uppgradera ett allmänt v1-eller Azure Blob Storage-konto till ett allmänt-syfte v2-konto utan avbrott och utan att behöva kopiera data. Mer information finns i [Uppgradera till ett allmänt-syfte v2-lagrings konto](../articles/storage/common/storage-account-upgrade.md).

Alla lagrings konton som körs på en låg nätverks sto pol Ogin oavsett när de skapades. Mer information om Azure Storage platt nätverks arkitektur och om skalbarhet finns i [Microsoft Azure Storage: en moln lagrings tjänst med hög tillgänglighet med stark konsekvens](/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets).