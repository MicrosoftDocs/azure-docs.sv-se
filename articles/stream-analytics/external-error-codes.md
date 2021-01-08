---
title: Externa felkoder – Azure Stream Analytics
description: Felsök Azure Stream Analytics problem med externa felkoder.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 9f55a715b11b126ea340e665e008d7245e578190
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016396"
---
# <a name="azure-stream-analytics-external-error-codes"></a>Azure Stream Analytics externa fel koder

Du kan använda aktivitets loggar och resurs loggar för att felsöka oväntade beteenden från ditt Azure Stream Analytics-jobb. Den här artikeln innehåller en beskrivning av varje extern felkod. Externa fel är allmänna fel som utlösts av en uppströms-eller underordnad tjänst som Stream Analytics inte kan särskilja som datafel, konfigurations fel eller externt tillgänglighets fel.

## <a name="adapterinitializationerror"></a>AdapterInitializationError

* **Orsak**: ett fel uppstod när ett nätverkskort initierades.

## <a name="adapterfailedtowriteevents"></a>AdapterFailedToWriteEvents

* **Orsak**: ett fel uppstod när data skrevs till ett nätverkskort.

## <a name="azurefunctionhttperror"></a>AzureFunctionHttpError

* **Orsak**: ett HTTP-fel returnerades från Azure Functions.

## <a name="azurefunctionfailedtosendmessage"></a>AzureFunctionFailedToSendMessage

* **Orsak**: Stream Analytics kunde inte skriva händelser till Azure-funktionen.

## <a name="azurefunctionredirecterror"></a>AzureFunctionRedirectError

* **Orsak**: ett omdirigerings fel uppstod när du försöker Azure Functions.

## <a name="azurefunctionclienterror"></a>AzureFunctionClientError

* **Orsak**: det uppstod ett klient fel vid Azure Functions.

## <a name="azurefunctionservererror"></a>AzureFunctionServerError

* **Orsak**: ett Server fel uppstod vid Azure Functions.

## <a name="azurefunctionhttptimeouterror"></a>AzureFunctionHttpTimeOutError

* **Orsak**: det gick inte att skriva till Azure Functions eftersom http-begäran överskred tids gränsen. 
* **Rekommendation**: kontrol lera Azure Functions loggar för eventuella fördröjningar.

## <a name="eventhubargumenterror"></a>EventHubArgumentError

* **Orsak**: offset-värden är ogiltiga. Detta kan bero på en redundansväxling.
* **Rekommendation**: starta om Stream Analytics-jobbet från den senaste utdata-tiden.

## <a name="eventhubfailedtowriteevents"></a>EventHubFailedToWriteEvents

* **Orsak**: ett fel uppstod när data skickades till händelsehubben.

## <a name="cosmosdbconnectionfailureaftermaxretries"></a>CosmosDBConnectionFailureAfterMaxRetries

* **Orsak**: Stream Analytics kunde inte ansluta till ett Cosmos DB-konto efter det maximala antalet återförsök.

## <a name="cosmosdbfailureaftermaxretries"></a>CosmosDBFailureAfterMaxRetries

* **Orsak**: Stream Analytics kunde inte fråga Cosmos DB databasen och samlingen efter det maximala antalet återförsök.

## <a name="cosmosdbfailedtocreatestoredprocedure"></a>CosmosDBFailedToCreateStoredProcedure

* **Orsak**: CosmosDB kan inte skapa en lagrad procedur efter flera återförsök.

## <a name="cosmosdboutputrequesttimeout"></a>CosmosDBOutputRequestTimeout

* **Orsak**: den lagrade proceduren upsert returnerade ett fel. 

## <a name="sqldatabaseoutputinitializationerror"></a>SQLDatabaseOutputInitializationError

* **Orsak**: Stream Analytics kan inte initiera SQL Database utdata.

## <a name="sqldatabaseoutputwriteerror"></a>SQLDatabaseOutputWriteError

* **Orsak**: Stream Analytics kan inte skriva händelser till SQL Database utdata.

## <a name="sqldwoutputinitializationerror"></a>SQLDWOutputInitializationError

* **Orsak**: ett fel uppstod vid initiering av en dedikerad SQL-pool.

## <a name="sqldwoutputwriteerror"></a>SQLDWOutputWriteError

* **Orsak**: ett fel uppstod när utdata skrevs till en dedikerad SQL-pool.

## <a name="next-steps"></a>Nästa steg

* [Felsöka indataanslutningar](stream-analytics-troubleshoot-input.md)
* [Felsöka Azure Stream Analytics utdata](stream-analytics-troubleshoot-output.md)
* [Felsöka Azure Stream Analytics frågor](stream-analytics-troubleshoot-query.md)
* [Felsöka Azure Stream Analytics med hjälp av resurs loggar](stream-analytics-job-diagnostic-logs.md)
* [Azure Stream Analytics data fel](data-errors.md)
