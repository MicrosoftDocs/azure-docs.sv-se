---
title: Konfigurations fel koder-Azure Stream Analytics
description: Felsök Azure Stream Analytics problem med konfigurations fel koder.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 6137ceeb0d86b1531adab910175ddbc4722ef858
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019524"
---
# <a name="azure-stream-analytics-configuration-error-codes"></a>Fel koder för Azure Stream Analytics konfiguration

Du kan använda aktivitets loggar och resurs loggar för att felsöka oväntade beteenden från ditt Azure Stream Analytics-jobb. Den här artikeln innehåller en beskrivning av varje konfigurations fel kod. Konfigurations fel är relaterade till din jobb konfiguration eller konfigurationer för indata och utdata.

## <a name="eventhubunauthorizedaccess"></a>EventHubUnauthorizedAccess

* **Orsak**: Event Hub utlöste ett *otillåtet åtkomst* fel.

## <a name="eventhubreceiverepochconflict"></a>EventHubReceiverEpochConflict

* **Orsak**: det finns fler än en Event Hub-mottagare med olika värde värden.
* **Rekommendation**: kontrol lera att *Service Bus Explorer* eller ett *EventProcessorHost* -program inte är anslutet medan Stream Analytics jobbet körs.

## <a name="eventhubreceiverquotaexceeded"></a>EventHubReceiverQuotaExceeded

* **Orsak**: Stream Analytics kan inte ansluta till en partition eftersom det maximala antalet tillåtna mottagare per partition i en konsument grupp har nåtts.
* **Rekommendation**: se till att andra Stream Analytics jobb eller Service Bus Explorer inte använder samma konsument grupp.

## <a name="eventhuboutputthrottled"></a>EventHubOutputThrottled

* **Orsak**: ett fel uppstod när data skrevs till händelsehubben på grund av begränsning.
* **Rekommendation**: om detta sker konsekvent uppgraderar du data flödet.

## <a name="eventhuboutputinvalidconnectionconfig"></a>EventHubOutputInvalidConnectionConfig

* **Orsak**: den angivna anslutnings konfigurationen är felaktig.
* **Rekommendation**: korrigera konfigurationen och starta om jobbet.

## <a name="eventhuboutputinvalidhostname"></a>EventHubOutputInvalidHostname

* **Orsak**: det går inte att hitta händelse nav värden.
* **Rekommendation**: kontrol lera att det angivna värd namnet är korrekt.

## <a name="eventhuboutputunexpectedpartitioncount"></a>EventHubOutputUnexpectedPartitionCount

* **Orsak**: eventhub-avsändaren påträffade ett oväntat antal EventHub-partitioner.
* **Rekommendation**: starta om Stream Analytics jobb om EventHub-partitionens antal har ändrats.

## <a name="cosmosdbpartitionkeynotfound"></a>CosmosDBPartitionKeyNotFound

* **Orsak**: Stream Analytics inte att hitta partitionsnyckel för en viss Cosmos DB samling i databasen.
* **Rekommendation**: se till att en giltig partitionsnyckel har angetts för samlingen i Cosmos dB.

## <a name="cosmosdbinvalidpartitionkeycolumn"></a>CosmosDBInvalidPartitionKeyColumn

* **Orsak**: utlöses när en partitionsnyckel varken är en lövnod eller på den översta nivån.

## <a name="cosmosdbinvalididcolumn"></a>CosmosDBInvalidIdColumn

* **Orsak**: frågans utdata får inte innehålla kolumn \[ -ID: t] om en annan kolumn har valts som primär nyckel egenskap.

## <a name="cosmosdbdatabasenotfound"></a>CosmosDBDatabaseNotFound

* **Orsak**: Stream Analytics kan inte hitta en CosmosDB-databas.

## <a name="cosmosdbcollectionnotfound"></a>CosmosDBCollectionNotFound

* **Orsak**: Stream Analytics kan inte hitta en viss Cosmos DB samling i en databas.

## <a name="cosmosdboutputwritethrottling"></a>CosmosDBOutputWriteThrottling

* **Orsak**: ett fel uppstod när data skrevs på grund av begränsning av Cosmos dB.
* **Rekommendation**: uppgradera prestanda nivån för samlingen och justera databasens prestanda.

## <a name="sqldatabaseconnectionstringerror"></a>SQLDatabaseConnectionStringError

* **Orsak**: ett autentiseringsfel uppstod i Stream Analyticss jobbet.
* **Rekommendation**: kontrol lera att den SQL Database anslutnings strängen är korrekt.

## <a name="sqldatabasemanagedidentityauthenticationerror"></a>SQLDatabaseManagedIdentityAuthenticationError

* **Orsak**: ett autentiseringsfel uppstod i Stream Analyticss jobbet. 
* **Rekommendation**: kontrol lera att konto namnet är korrekt konfigurerat och att jobbets hanterade identitet har åtkomst till SQL Database.

## <a name="sqldatabaseoutputnotableerror"></a>SQLDatabaseOutputNoTableError

* **Orsak**: Stream Analytics kan inte hitta schema information för en viss tabell.

## <a name="sqldwoutputinvalidserviceedition"></a>SQLDWOutputInvalidServiceEdition

* **Orsak**: SQL Database stöds inte.
* **Rekommendation**: Använd dedikerad SQL-pool.

## <a name="next-steps"></a>Nästa steg

* [Felsöka indataanslutningar](stream-analytics-troubleshoot-input.md)
* [Felsöka Azure Stream Analytics utdata](stream-analytics-troubleshoot-output.md)
* [Felsöka Azure Stream Analytics frågor](stream-analytics-troubleshoot-query.md)
* [Felsöka Azure Stream Analytics med hjälp av resurs loggar](stream-analytics-job-diagnostic-logs.md)
* [Azure Stream Analytics data fel](data-errors.md)
