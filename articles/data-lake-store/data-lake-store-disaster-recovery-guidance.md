---
title: Vägledning om haveri beredskap för Azure Data Lake Storage Gen1 | Microsoft Docs
description: Lär dig mer om hur du skyddar dina data ytterligare från hela regionens avbrott eller oavsiktliga borttagningar utöver den lokalt redundanta lagringen av Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: twooley
ms.openlocfilehash: 48136f8d9172c3674e849e24efca4ae5070f83ab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92109127"
---
# <a name="high-availability-and-disaster-recovery-guidance-for-data-lake-storage-gen1"></a>Vägledning för hög tillgänglighet och katastrof återställning för Data Lake Storage Gen1

Data Lake Storage Gen1 tillhandahåller lokalt redundant lagring (LRS). Data i ditt Data Lake Storage Gen1-konto är därför elastiska mot tillfälliga maskin varu haverier inom ett Data Center genom automatiska repliker. Detta säkerställer hållbarhet och hög tillgänglighet, som uppfyller Data Lake Storage Gen1 service avtal. Den här artikeln innehåller vägledning om hur du ytterligare skyddar dina data från ovanliga avbrott i regioner eller oavsiktliga borttagningar.

## <a name="disaster-recovery-guidance"></a>Vägledning om haveriberedskap

Det är viktigt att du förbereder en katastrof återställnings plan. Läs informationen i den här artikeln och dessa ytterligare resurser som hjälper dig att skapa ett eget abonnemang.

* [Haveriberedskap och hög tillgänglighet för Azure-program](/azure/architecture/framework/resiliency/backup-and-recovery)
* [Azure-återhämtning, tekniska riktlinjer](/azure/architecture/framework/resiliency/overview)

### <a name="best-practice-recommendations"></a>Rekommendationer för bästa praxis

Vi rekommenderar att du kopierar viktiga data till ett annat Data Lake Storage Gen1 konto i en annan region med en frekvens som är anpassad till behoven hos din katastrof återställnings plan. Det finns flera olika metoder för att kopiera data, inklusive [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md)eller [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). Azure Data Factory är användbart för att skapa och distribuera pipelines för datarörlighet regelbundet.

Om ett regionalt avbrott inträffar kan du sedan komma åt dina data i den region där data kopierades. Du kan övervaka [Azure Service Health-instrumentpanelen](https://azure.microsoft.com/status/) för att fastställa statusen för Azure-tjänsten över hela världen.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Skadade data eller vägledning för återställning av oavsiktligt borttagna data

Medan Data Lake Storage Gen1 ger data återhämtning via automatiserade repliker, förhindrar detta inte att ditt program (eller utvecklare/användare) skadar data eller tar bort den av misstag.

För att förhindra oavsiktlig borttagning rekommenderar vi att du först ställer in rätt åtkomst principer för ditt Data Lake Storage Gen1-konto. Detta omfattar att använda [Azure Resource](../azure-resource-manager/management/lock-resources.md) Locks för att låsa viktiga resurser och tillämpa åtkomst kontroll på konto-och filnivå med hjälp av de tillgängliga [data Lake Storage gen1 säkerhetsfunktionerna](data-lake-store-security-overview.md). Vi rekommenderar också att du regelbundet skapar kopior av dina viktiga data med [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) eller [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) i ett annat data Lake Storage gen1 konto, mapp eller Azure-prenumeration. Detta kan användas för att återställa från skadade data eller en borttagning. Azure Data Factory är användbart för att skapa och distribuera pipelines för datarörlighet regelbundet.

Du kan också aktivera [diagnostikloggning](data-lake-store-diagnostic-logs.md) för ett data Lake Storage gen1 konto för att samla in gransknings historiken för data åtkomst. Gransknings historiken innehåller information om vem som kan ha tagit bort eller uppdaterat en fil.

## <a name="next-steps"></a>Nästa steg

* [Kom igång med Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)