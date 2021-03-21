---
title: Fel koder för extern tillgänglighet-Azure Stream Analytics
description: Felsök Azure Stream Analytics problem med fel koder för extern tillgänglighet.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 71625c4b81fc0795c376a89397e98659f4c72ff0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020561"
---
# <a name="azure-stream-analytics-external-availability-error-codes"></a>Fel koder för extern tillgänglighet för Azure Stream Analytics

Du kan använda aktivitets loggar och resurs loggar för att felsöka oväntade beteenden från ditt Azure Stream Analytics-jobb. Den här artikeln visar beskrivningen för varje extern tillgänglighets fel kod. Externa tillgänglighets fel inträffar när en beroende tjänst inte är tillgänglig.

## <a name="externalserviceunavailable"></a>ExternalServiceUnavailable

* **Orsak**: tjänsten är inte tillgänglig för tillfället.
* **Rekommendation**: Stream Analytics fortsätter att försöka komma åt tjänsten.

## <a name="eventhubmessagingerror"></a>EventHubMessagingError

* **Orsak**: Stream Analytics påträffade fel vid kommunikation med EventHub. 


## <a name="next-steps"></a>Nästa steg

* [Felsöka indataanslutningar](stream-analytics-troubleshoot-input.md)
* [Felsöka Azure Stream Analytics utdata](stream-analytics-troubleshoot-output.md)
* [Felsöka Azure Stream Analytics frågor](stream-analytics-troubleshoot-query.md)
* [Felsöka Azure Stream Analytics med hjälp av resurs loggar](stream-analytics-job-diagnostic-logs.md)
* [Azure Stream Analytics data fel](data-errors.md)
