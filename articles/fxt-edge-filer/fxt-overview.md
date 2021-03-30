---
title: Översikt över Microsoft Azure FXT Edge-filer
description: Beskriver Azure FXT Edge-filer hybrid Storage cache, en aktiv lösning för arkiv-och fil åtkomst Accelerator för data behandling med höga prestanda
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 72308bf64246a8041bd40fa557280014a13c95eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92218790"
---
# <a name="what-is-azure-fxt-edge-filer-hybrid-storage-cache"></a>Vad är Azure FXT Edge-filer hybrid Storage cache?

Azure FXT Edge-filer är en hybrid lagrings enhet för cachelagring som ger snabb fil åtkomst och aktivt Arkiv för HPC-uppgifter (data behandling med höga prestanda).

Det fungerar med flera data källor, oavsett om de lagras i ett lokalt Data Center, via en fjärr anslutning eller i molnet. Azure FXT Edge-filer kan tillhandahålla ett enhetligt namn område för data i olika lagrings system.

Tre eller flera FXT Edge-enheter fungerar tillsammans som ett klustrat fil system för att tillhandahålla cache. Kontakta din Microsoft-representant om du vill ha mer information om att köpa nödvändig maskin vara.

Läs mer i produkt information och data bladet på [Azure FXT Edge](https://azure.microsoft.com/services/fxt-edge-filer/)-filer.

## <a name="use-cases"></a>Användningsfall

Azure FXT Edge-filer förbättrar produktiviteten bäst för arbets flöden som dessa:

* Läs – tungt arbets flöde för fil åtkomst
* NFSv3-eller SMB2-protokoll
* Beräknings grupper på 1000 till 100 000 CPU-kärnor

### <a name="nas-optimization-and-scaling"></a>NAS-optimering och skalning

Du kan använda Azure FXT Edge-cacheminnet för att få smidig åtkomst till befintliga NetApp-och Dell EMC Isilon NAS-system. Du kan också lägga till Azure Blob eller annan moln lagring för att tillhandahålla skalbarhet utan att behöva arbeta om data åtkomst processer på klient sidan.

### <a name="wan-caching"></a>WAN-cachelagring

Azure FXT Edge-filer kan användas för att stödja snabb fil åtkomst från privilegierade användare när de data som de behöver lagras någon annan stans. Ge åtkomst samtidigt som du behåller säkerhets kopieringen och andra data hanterings system i ett centraliserat Data Center.

### <a name="active-archive-in-azure-blob"></a>Aktivt Arkiv i Azure-Blob

Utöka ditt data Center till moln lagring med Azure FXT Edge-filer som åtkomst punkt.

## <a name="features"></a>Funktioner

Två maskin varu modeller är tillgängliga.

| Modell | DYNAMISKA | NVMe SSD | Nätverksportar |
|-------|------|----------|---------------|
| FXT 6600 | 1536 GB | 25,6 TB | 6 x 25 GB/10 GB + 2 x 1 GB |
| FXT 6400 | 768 GB | 12,8 TB | 6 x 25 GB/10 GB + 2 x 1 GB |

## <a name="next-steps"></a>Nästa steg

* Fortsätt att lära dig om Azure FXT Edge-filer genom att läsa [specifikationerna](fxt-specs.md) eller [själv studie kursen](fxt-install.md).
* Lär dig att köpa Azure FXT Edge-filer på [produkt sidan för Azure FXT Edge](https://azure.microsoft.com/services/fxt-edge-filer/)-filer.
