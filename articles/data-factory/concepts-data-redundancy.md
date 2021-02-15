---
title: Dataredundans i Azure Data Factory | Microsoft Docs
description: Lär dig mer om meta-dataredundans i Azure Data Factory
author: nabhishek
ms.reviewer: abnarain
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: abnarain
ms.openlocfilehash: 9d1c22b9ac6912f99838733a4326cb4082f49a6c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369704"
---
# <a name="azure-data-factory-data-redundancy"></a>**Azure Data Factory dataredundans**

Azure Data Factory data innehåller metadata (pipeline, data uppsättningar, länkade tjänster, integrations körning och utlösare) och övervaknings data (pipeline, utlösare och aktivitets körningar). 

I alla regioner (förutom södra Brasilien och Sydostasien), lagras och replikeras Azure Data Factory data i den [kopplade regionen](../best-practices-availability-paired-regions.md#azure-regional-pairs) för att skydda mot metadata förlust. Under regionala Data Center fel kan Microsoft initiera en regional redundansväxling av Azure Data Factory-instansen. I de flesta fall krävs ingen åtgärd på din sida. När den Microsoft-hanterade redundansväxlingen har slutförts kommer du att kunna komma åt din Azure Data Factory i området för redundans. 

På grund av krav på data placering i Brasilien, och Sydostasien, lagras Azure Data Factory data endast i den [lokala regionen](../storage/common/storage-redundancy.md#locally-redundant-storage). För Sydostasien lagras alla data i Singapore. För södra Brasilien lagras alla data i Brasilien. När regionen förloras på grund av en betydande katastrof kommer Microsoft inte att kunna återställa dina Azure Data Factory data.  

> [!NOTE]
> Microsoft-hanterad redundans gäller inte för integration runtime med egen värd (SHIR) eftersom den här infrastrukturen vanligt vis hanteras av kund. Om SHIR har kon figurer ATS på den virtuella Azure-datorn är rekommendationen att använda [Azure Site Recovery](../site-recovery/site-recovery-overview.md) för att hantera den [virtuella Azure-redundansväxlingen](../site-recovery/azure-to-azure-architecture.md) till en annan region.



## <a name="using-source-control-in-azure-data-factory"></a>**Använda käll kontroll i Azure Data Factory**

För att säkerställa att du kan spåra och granska ändringar som gjorts i dina Azure Data Factory-metadata, bör du överväga att konfigurera käll kontroll för Azure Data Factory. Du kan också få åtkomst till dina JSON-filer för pipelines, data uppsättningar, länkade tjänster och utlösare. Med Azure Data Factory kan du arbeta med olika git-lagringsplatser (Azure DevOps och GitHub). 

 Lär dig hur du konfigurerar [käll kontroll i Azure Data Factory](./source-control.md). 

> [!NOTE]
> I händelse av en katastrof (förlust av region) kan en ny data fabrik tillhandahållas manuellt eller på ett automatiserat sätt. När den nya data fabriken har skapats kan du återställa pipelines, data uppsättningar och länkade tjänster JSON från den befintliga git-lagringsplatsen. 



## <a name="data-stores"></a>**Datalager**

Med Azure Data Factory kan du flytta data mellan data lager som finns lokalt och i molnet. För att säkerställa affärs kontinuiteten med dina data lager bör du läsa rekommendationer för affärs kontinuitet för var och en av dessa data lager. 

 

## <a name="see-also"></a>Se även

- [Regionala Azure-par](../best-practices-availability-paired-regions.md)
- [Data placering i Azure](https://azure.microsoft.com/global-infrastructure/data-residency/)