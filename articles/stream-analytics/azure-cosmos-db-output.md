---
title: Azure Cosmos DB utdata från Azure Stream Analytics
description: I den här artikeln beskrivs hur du matar ut data från Azure Stream Analytics till Azure Cosmos DB.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 9382ac8dc71d1ccb3a85dc0a7a027c8e99296cc6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98016585"
---
# <a name="azure-cosmos-db-output-from-azure-stream-analytics"></a>Azure Cosmos DB utdata från Azure Stream Analytics

[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) är en globalt distribuerad databas tjänst som erbjuder obegränsad elastisk skalning runt om i världen, frågan och automatisk indexering över schema oberoende data modeller. Mer information om Azure Cosmos DB container alternativ för Stream Analytics finns i artikeln [Stream Analytics med Azure Cosmos DB som utdata](stream-analytics-documentdb-output.md) .

Azure Cosmos DB utdata från Stream Analytics är för närvarande inte tillgängligt i regionerna Azure Kina 21Vianet och Azure Tyskland (T-Systems International).

> [!Note]
> Azure Stream Analytics stöder endast anslutning till Azure Cosmos DB med hjälp av SQL-API: et.
> Andra Azure Cosmos DB API: er stöds inte ännu. Om du pekar Azure Stream Analytics till de Azure Cosmos DB konton som skapats med andra API: er, kanske data inte lagras korrekt.

I följande tabell beskrivs egenskaperna för att skapa en Azure Cosmos DB utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias | Ett alias för att referera till utdata i din Stream Analytics fråga. |
| Kanalmottagare | Azure Cosmos DB. |
| Importalternativ | Välj antingen **välj Cosmos dB från din prenumeration** eller **Ange Cosmos DB inställningar manuellt**.
| Konto-ID | Azure Cosmos DB kontots namn eller slut punkts-URI. |
| Kontonyckel | Den delade åtkomst nyckeln för det Azure Cosmos DB kontot. |
| Databas | Namnet på Azure Cosmos DBs databasen. |
| Containerns namn | Behållar namnet som ska användas, vilket måste finnas i Cosmos DB. Exempel:  <br /><ul><li> En behållar _: en_ behållare med namnet "behållar" måste finnas.</li>|
| Dokument-ID |Valfritt. Namnet på fältet i utmatnings händelser som används för att ange den primära nyckel som infognings-eller uppdaterings åtgärder baseras på.

## <a name="partitioning"></a>Partitionering

Partitionsnyckel baseras på PARTITION BY-satsen i frågan. Antalet utgående skrivare följer inmatnings partitionering för [helt parallella frågor](stream-analytics-scale-jobs.md). Stream Analytics konverterar nyckeln för Cosmos DBs startpartition till en sträng. Om du till exempel har en partitionsnyckel med värdet 1 av typen bigint, konverteras den till "1" av typen sträng. Den här konverteringen sker alltid oavsett om egenskapen partition har skrivits till Cosmos DB.

## <a name="output-batch-size"></a>Batchstorlek för utdata

För maximal meddelande storlek, se [Azure Cosmos dB gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits). Batchstorlek och skriv frekvens justeras dynamiskt baserat på Azure Cosmos DB svar. Det finns inga identifierade begränsningar från Stream Analytics.

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](stream-analytics-quick-create-portal.md)
* [Snabb start: skapa ett Azure Stream Analytics jobb med hjälp av Azure CLI](quick-create-azure-cli.md)
* [Snabb start: skapa ett Azure Stream Analytics jobb med en ARM-mall](quick-create-azure-resource-manager.md)
* [Snabb start: skapa ett Stream Analytics jobb med Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Snabb start: skapa ett Azure Stream Analytics jobb med hjälp av Visual Studio](stream-analytics-quick-create-vs.md)
* [Snabb start: skapa ett Azure Stream Analytics jobb i Visual Studio Code](quick-create-visual-studio-code.md)
