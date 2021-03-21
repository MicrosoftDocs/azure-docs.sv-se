---
title: Batch-konton och Azure Storage konton
description: Lär dig mer om Azure Batch konton och hur de används av en utvecklings synpunkt.
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 83108a265f91c9feef2fab424f1819939c2d58c9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896756"
---
# <a name="batch-accounts-and-azure-storage-accounts"></a>Batch-konton och Azure Storage konton

Ett Azure Batch konto är en unikt identifierad entitet i batch-tjänsten. De flesta batch-lösningar använder [Azure Storage](../storage/index.yml) för att lagra resursfiler och utdatafiler, så varje batch-konto är vanligt vis kopplat till ett motsvarande lagrings konto.

## <a name="batch-accounts"></a>Batch-konton

All bearbetning och alla resurser är associerade med ett batch-konto. När ditt program gör en begäran mot batchtjänsten, autentiserar det begäran med hjälp av Azure Batch-kontonamnet, URL:en för kontot och antingen en åtkomstnyckel eller en Azure Active Directory-token.

Du kan köra flera batch-arbetsbelastningar i ett enda batch-konto. Du kan också distribuera dina arbets belastningar mellan batch-konton som finns i samma prenumeration, men som finns i olika Azure-regioner.

Du kan skapa ett batch-konto med hjälp av [Azure Portal](batch-account-create-portal.md) eller program mässigt, till exempel med [batch Management .net-biblioteket](batch-management-dotnet.md). När du skapar kontot kan du koppla ett Azure-lagringskonto för att lagra jobbrelaterade in- och utdata eller program.

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]


## <a name="azure-storage-accounts"></a>Azure Storage-konton

De flesta Batch-lösningar använder Azure Storage för lagring av resursfiler och utdatafiler. Till exempel brukar Batch-aktiviteterna (inklusive standardaktiviteter, startaktiviteter, jobbförberedelse- och jobbpubliceringsaktiviteter) definiera resursfiler som finns i ett lagringskonto. Lagrings konton lagrar också de data som bearbetas och alla utdata som genereras.

Batch stöder följande typer av Azure Storage-konton:

- GPv2-konton (General-purpose v2)
- GPv1-konton (General-purpose v1)
- Blob Storage-konton (stöds för närvarande för pooler i VM-konfigurationen)

Mer information om lagrings konton finns i [Översikt över Azure Storage-konto](../storage/common/storage-account-overview.md).

Du kan associera ett lagringskonto till Batch-kontot när du skapar Batch-kontot, eller senare. Betänk dina kostnads- och prestandakrav när du väljer lagringskonto. Till exempel har alternativen med GPv2- och blob storage-konto stöd för större [kapacitet och skalbarhet](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) jämfört med GPv1. (Kontakta Azure-supporten om du vill begära en ökning av en lagrings gräns.) Dessa konto alternativ kan förbättra prestanda för batch-lösningar som innehåller ett stort antal parallella aktiviteter som läser från eller skriver till lagrings kontot.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [noder och pooler](nodes-and-pools.md).
- Lär dig hur du skapar och hanterar batch-konton med hjälp av [Azure Portal](batch-account-create-portal.md) eller [batch Management .net](batch-management-dotnet.md).
- Lär dig hur du använder [privata slut punkter](private-connectivity.md) med Azure Batch-konton.
