---
title: Förstå indata för Azure Stream Analytics
description: Den här artikeln beskriver begreppet indata i ett Azure Stream Analytics jobb, och jämför strömmande indata till referens data inmatning.
ms.service: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 1e5bf3e884e5f2c1a57cee08cbf3c22379dab080
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102436047"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Förstå indata för Azure Stream Analytics

Azure Stream Analytics-jobb ansluter till en eller flera data indata. Varje inmatning definierar en anslutning till en befintlig data källa. Stream Analytics accepterar inkommande data från flera olika typer av händelse källor, inklusive Event Hubs, IoT Hub och blob-lagring. Indata refereras till av namn i den strömmande SQL-fråga som du skriver för varje jobb. I frågan kan du koppla flera indata för att blanda data eller jämföra strömmande data med en sökning i referens data och skicka resultatet till utdata. 

Stream Analytics har förstklassig integrering med fyra typer av resurser som indata:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 
- [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 

De här ingångs resurserna kan leva i samma Azure-prenumeration som din Stream Analytics jobb eller från en annan prenumeration.

Du kan använda [Azure Portal](stream-analytics-quick-create-portal.md#configure-job-input),  [Azure PowerShell](/powershell/module/az.streamanalytics/New-azStreamAnalyticsInput), [.NET-API](/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), [REST API](/rest/api/streamanalytics/2016-03-01/inputs)och [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) för att skapa, redigera och testa Stream Analytics jobb indata.

## <a name="stream-and-reference-inputs"></a>Data ström-och referens indata
När data skickas till en data källa förbrukas den av Stream Analytics jobb och bearbetas i real tid. Indata är uppdelade i två typer: inkommande dataströmmar och inkommande referensdata.

### <a name="data-stream-input"></a>Data Ströms inmatning
En data ström är en obunden sekvens med händelser över tid. Stream Analytics-jobb måste innehålla minst en inkommande dataström. Event Hubs, IoT Hub, Azure Data Lake Storage Gen2 och Blob Storage stöds som data ström källor för data strömmar. Event Hubs används för att samla in händelse strömmar från flera enheter och tjänster. Dessa strömmar kan innehålla uppgifter för sociala media-aktiviteter, aktie handel eller data från sensorer. IoT Hub är optimerade för att samla in data från IoT-scenarier (Connected devices in Sakernas Internet).  Blob Storage kan användas som indatakälla för att mata in Mass data som en ström, till exempel loggfiler.  

Mer information om strömmande data inmatning finns i [strömma data som indata till Stream Analytics](stream-analytics-define-inputs.md)

### <a name="reference-data-input"></a>Referens data inmatning
Stream Analytics också stöd för indata som kallas *referens data*. Referens data är antingen helt statiska eller ändringar långsamma. Den används vanligt vis för att utföra korrelationer och sökningar. Du kan till exempel koppla data i data strömmens indata till data i referens data, ungefär som du skulle göra med en SQL-anslutning för att söka efter statiska värden. Azure Blob Storage, Azure Data Lake Storage Gen2 och Azure SQL Database stöds för närvarande som inmatnings källor för referens data. Referens data källans blobbar har en gräns på upp till 300 MB i storlek, beroende på frågan komplexitet och allokerade enheter för strömning (se avsnittet [storleks begränsning](stream-analytics-use-reference-data.md#size-limitation) i dokumentationen för referens data för mer information).

Mer information om indata från referenser finns i [använda referens data för sökningar i Stream Analytics](stream-analytics-use-reference-data.md)

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](stream-analytics-quick-create-portal.md)