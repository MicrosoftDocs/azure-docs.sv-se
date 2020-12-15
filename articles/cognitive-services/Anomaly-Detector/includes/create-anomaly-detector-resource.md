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
ms.openlocfilehash: d1add17c1c84d2a22d76aaa1f96aeca4db645ba7
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506270"
---
## <a name="create-an-anomaly-detector-resource"></a>Skapa en resurs för avvikelse detektor

1. Logga in på <a href="https://portal.azure.com" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. Välj <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank">skapa avvikelse detektor <span class="docon docon-navigate-external x-hidden-focus"></span> </a> resurs.
1. Ange alla nödvändiga inställningar:

    |Inställning|Värde|
    |--|--|
    |Namn|Önskat namn (2-64 tecken)|
    |Prenumeration|Välj lämplig prenumeration|
    |Plats|Välj valfri närliggande och tillgänglig plats|
    |Prisnivå|`F0` – 100 anrop per sekund, 20 000 transaktioner per månad. <br> Eller<br> `S0` – 80 anrop per sekund|
    |Resursgrupp|Välj en tillgänglig resurs grupp|

1. Klicka på **skapa** och vänta tills resursen har skapats. När den har skapats går du till resurs Sidan
1. Samla in konfigurerade `endpoint` och en API-nyckel:

    |Fliken nycklar och slut punkt i portalen|Inställning|Värde|
    |--|--|--|
    |**Översikt**|Slutpunkt|Kopiera slut punkten. Det ser ut ungefär så här ` https://<your-resource-name>.cognitiveservices.azure.com/`|
    |**Nycklar**|API-nyckel|Kopia 1 av de två nycklarna. Det är en sträng med 32 alfanumeriska tecken utan blank steg eller bindestreck `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|



