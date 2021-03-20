---
title: Tabell lagrings resultat från Azure Stream Analytics
description: I den här artikeln beskrivs Azure Table Storage som utdata för Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 558ddf065d33a552034c5b129ea70bc144c494ca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98013897"
---
# <a name="table-storage-output-from-azure-stream-analytics"></a>Tabell lagrings resultat från Azure Stream Analytics

[Azure Table Storage](../storage/common/storage-introduction.md) erbjuder hög skalbar lagring med hög tillgänglighet, så att ett program kan skalas automatiskt för att möta användarnas behov. Table Storage är Microsofts NoSQL Key/Attribute-Arkiv, som du kan använda för strukturerade data med färre begränsningar i schemat. Azure Table Storage kan användas för att lagra data för beständig och effektiv hämtning.

I följande tabell visas egenskaps namnen och deras beskrivningar för att skapa ett tabell resultat.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias |Ett eget namn som används i frågor för att dirigera frågeresultatet till den här tabell lagringen. |
| Lagringskonto |Namnet på det lagrings konto där du ska skicka dina utdata. |
| Lagrings konto nyckel |Åtkomst nyckeln som är kopplad till lagrings kontot. |
| Tabellnamn |Namnet på tabellen. Tabellen skapas om den inte finns. |
| Partitionsnyckel |Namnet på kolumnen utdata som innehåller partitionsnyckel. Partitionsnyckel är en unik identifierare för partitionen i en tabell som utgör den första delen av en entitets primära nyckel. Det är ett sträng värde som kan vara upp till 1 KB stort. |
| Rad nyckel |Namnet på kolumnen utdata som innehåller rad nyckeln. Rad nyckeln är en unik identifierare för en entitet i en partition. Den utgör den andra delen av en entitets primära nyckel. Rad nyckeln är ett sträng värde som kan vara upp till 1 KB stort. |
| Batchstorlek |Antalet poster för en batch-åtgärd. Standardvärdet (100) är tillräckligt för de flesta jobb. Mer information om hur du ändrar den här inställningen finns i [specifikationen tabell åtgärds åtgärd](/java/api/com.microsoft.azure.storage.table.tablebatchoperation) . |

## <a name="partitioning"></a>Partitionering

Partitionsnyckel är valfri kolumn i kolumnen utdata. Antalet utgående skrivare följer inmatnings partitionering för [helt parallella frågor](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Batchstorlek för utdata

För maximal meddelande storlek, se [Azure Storage gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). Standardvärdet är 100 entiteter per enskild transaktion, men du kan konfigurera den till ett mindre värde om det behövs.

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](stream-analytics-quick-create-portal.md)
* [Snabb start: skapa ett Azure Stream Analytics jobb med hjälp av Azure CLI](quick-create-azure-cli.md)
* [Snabb start: skapa ett Azure Stream Analytics jobb med en ARM-mall](quick-create-azure-resource-manager.md)
* [Snabb start: skapa ett Stream Analytics jobb med Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Snabb start: skapa ett Azure Stream Analytics jobb med hjälp av Visual Studio](stream-analytics-quick-create-vs.md)
* [Snabb start: skapa ett Azure Stream Analytics jobb i Visual Studio Code](quick-create-visual-studio-code.md)