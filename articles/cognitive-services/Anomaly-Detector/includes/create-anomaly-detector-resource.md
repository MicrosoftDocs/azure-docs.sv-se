---
title: Stöd för containrar
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 037b9746571678e24e80bd76e7e2ed173ab8eb90
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97677622"
---
## <a name="create-an-anomaly-detector-resource"></a>Skapa en resurs för avvikelse detektor

1. Logga in på <a href="https://portal.azure.com" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. Välj <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank">skapa avvikelse detektor <span class="docon docon-navigate-external x-hidden-focus"></span> </a> resurs.
1. Ange alla nödvändiga inställningar:

    |Inställningen|Värde|
    |--|--|
    |Namn|Önskat namn (2-64 tecken)|
    |Prenumeration|Välj lämplig prenumeration|
    |Plats|Välj valfri närliggande och tillgänglig plats|
    |Prisnivå|`F0` – 10 anrop per sekund, 20 000 transaktioner per månad. <br> Eller<br> `S0` – 80 anrop per sekund|
    |Resursgrupp|Välj en tillgänglig resurs grupp|

1. Klicka på **skapa** och vänta tills resursen har skapats. När den har skapats går du till resurs Sidan
1. Samla in konfigurerade `endpoint` och en API-nyckel:

    |Fliken nycklar och slut punkt i portalen|Inställningen|Värde|
    |--|--|--|
    |**Översikt**|Slutpunkt|Kopiera slut punkten. Det ser ut ungefär så här ` https://<your-resource-name>.cognitiveservices.azure.com/`|
    |**Nycklar**|API-nyckel|Kopia 1 av de två nycklarna. Det är en sträng med 32 alfanumeriska tecken utan blank steg eller bindestreck `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|



