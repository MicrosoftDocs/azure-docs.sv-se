---
title: Skapa en Cognitive Services Textanalys-resurs
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en Cognitive Services Textanalys-resurs.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/09/2021
ms.author: aahi
ms.openlocfilehash: 5b6479d48a51ba962f2f6bfba16dac3b0886a9ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "101750705"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Skapa en Cognitive Services Textanalys-resurs

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Skapa en resurs** och gå sedan till **AI + maskininlärning** > **Textanalys**.
   Eller gå till [skapa textanalys](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Ange alla nödvändiga inställningar:

    |Inställning|Värde|
    |--|--|
    |Namn|Ange ett namn (2–64 tecken).|
    |Prenumeration|Välj lämplig prenumeration.|
    |Plats|Välj en närliggande plats.|
    |Prisnivå| Ange **S**, standardprisnivån.|
    |Resursgrupp|Välj en tillgänglig resursgrupp.|

1. Klicka på **Skapa** och vänta tills resursen har skapats. Webbläsaren omdirigeras automatiskt till den nyligen skapade resurssidan.
1. Samla in den konfigurerade `endpoint` och en API-nyckel:

    |Fliken resurs i portalen|Inställning|Värde|
    |--|--|--|
    |**Översikt**|Slutpunkt|Kopiera slut punkten. Det ser ut ungefär som `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0` .|
    |**Nycklar**|API-nyckel|Kopiera en av de två nycklarna. Det är en alfanumerisk sträng på 32 tecken utan blank steg eller bindestreck: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
