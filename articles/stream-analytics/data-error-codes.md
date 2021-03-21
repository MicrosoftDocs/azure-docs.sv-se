---
title: Data fel koder – Azure Stream Analytics
description: Felsök Azure Stream Analytics problem med felkoder för data.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 33e617c8c3589d76d649dd2ea2236a4247b12500
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98016498"
---
# <a name="azure-stream-analytics-data-error-codes"></a>Fel koder för Azure Stream Analytics data

Du kan använda aktivitets loggar och resurs loggar för att felsöka oväntade beteenden från ditt Azure Stream Analytics-jobb. Den här artikeln visar beskrivningen för varje data fel fel kod. Data fel inträffar när det finns felaktiga data i data strömmen, till exempel ett oväntat post schema.

## <a name="inputdeserializationerror"></a>InputDeserializationError

* **Orsak**: det uppstod ett fel vid deserialiseringen av indata.

## <a name="inputeventtimestampnotfound"></a>InputEventTimestampNotFound

* **Orsak**: Stream Analytics kan inte hämta en tidsstämpel för resursen. 

## <a name="inputeventtimestampbyovervaluenotfound"></a>InputEventTimestampByOverValueNotFound

* **Orsak**: Stream Analytics kan inte hämta värdet `TIMESTAMP BY OVER COLUMN` .

## <a name="inputeventlatebeyondthreshold"></a>InputEventLateBeyondThreshold

* **Orsak**: en inmatad händelse skickades senare än konfigurerad tolerans.

## <a name="inputeventearlybeyondthreshold"></a>InputEventEarlyBeyondThreshold

* **Orsak**: en indatamängds tidpunkt för inloggning är tidigare än tröskeln för program tids stämpling i indatamängden.

## <a name="azurefunctionmessagesizeexceeded"></a>AzureFunctionMessageSizeExceeded

* **Orsak**: meddelandets utdata till Azure Functions överskrider storleks gränsen.

## <a name="eventhuboutputrecordexceedssizelimit"></a>EventHubOutputRecordExceedsSizeLimit

* **Orsak**: en utmatnings post överskrider den maximala storleks gränsen vid skrivning till Event Hub.

## <a name="cosmosdboutputinvalidid"></a>CosmosDBOutputInvalidId

* **Orsak**: värdet eller typen för en viss kolumn är ogiltig.
* **Rekommendation**: Ange unika icke-tomma strängar som inte är längre än 255 tecken.

## <a name="cosmosdboutputinvalididcharacter"></a>CosmosDBOutputInvalidIdCharacter

* **Orsak**: dokument-ID: t för den utgående posten innehåller ett ogiltigt Character.

## <a name="cosmosdboutputmissingid"></a>CosmosDBOutputMissingId

* **Orsak**: utmatnings posten innehåller inte kolumn \[ -ID: t] som används som primär nyckel egenskap.

## <a name="cosmosdboutputmissingidcolumn"></a>CosmosDBOutputMissingIdColumn

* **Orsak**: utdata-posten innehåller inte egenskapen dokument-ID. 
* **Rekommendation**: se till att frågeresultatet innehåller kolumnen med en unik icke-tom sträng med färre än 255 tecken.

## <a name="cosmosdboutputmissingpartitionkey"></a>CosmosDBOutputMissingPartitionKey

* **Orsak**: Utdataparametern saknar den kolumn som ska användas som egenskap för partitionsnyckel.

## <a name="cosmosdboutputsinglerecordtoolarge"></a>CosmosDBOutputSingleRecordTooLarge

* **Orsak**: en enskild post som skrivs till Cosmos DB är för stor.

## <a name="sqldatabaseoutputdataerror"></a>SQLDatabaseOutputDataError

* **Orsak**: Stream Analytics kan inte skriva händelser till SQL Database på grund av problem med data.

## <a name="next-steps"></a>Nästa steg

* [Felsöka indataanslutningar](stream-analytics-troubleshoot-input.md)
* [Felsöka Azure Stream Analytics utdata](stream-analytics-troubleshoot-output.md)
* [Felsöka Azure Stream Analytics frågor](stream-analytics-troubleshoot-query.md)
* [Felsöka Azure Stream Analytics med hjälp av resurs loggar](stream-analytics-job-diagnostic-logs.md)
* [Azure Stream Analytics data fel](data-errors.md)
