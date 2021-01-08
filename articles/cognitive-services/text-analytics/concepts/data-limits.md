---
title: Data begränsningar för API för textanalys
titleSuffix: Azure Cognitive Services
description: Data begränsningar för API för textanalys från Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 11/19/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: 2adca03a820d02731bca252dee99c76debc85e2e
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028139"
---
# <a name="data-and-rate-limits-for-the-text-analytics-api"></a>Data-och hastighets begränsningar för API för textanalys
<a name="data-limits"></a>

Använd den här artikeln för att hitta gränserna för storlek och frekvenser som du kan skicka data till API för textanalys. 

## <a name="data-limits"></a>Databegränsningar

> [!NOTE]
> * Om du behöver analysera större dokument än gränsen tillåter kan du dela upp texten i mindre text segment innan du skickar dem till API: et. 
> * Ett dokument är en enskild sträng med text tecken.  

| Gräns | Värde |
|------------------------|---------------|
| Maximal storlek på ett enskilt dokument | 5 120 tecken som mäts av [StringInfo. LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). Gäller även Textanalys för hälso tillstånd. |
| Maximal storlek för ett enskilt dokument ( `/analyze` slut punkt)  | 125K tecken som mäts av [StringInfo. LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). Gäller inte Textanalys för hälso tillstånd. |
| Maximal storlek på hela begäran | 1 MB. Gäller även Textanalys för hälso tillstånd. |


Om ett dokument överskrider tecken gränsen kommer API: et att beter sig på olika sätt beroende på vilken slut punkt du använder:

* `/analyze` Endpoint
  * API: et avvisar hela begäran och returnerar ett `400 bad request` fel om något dokument i det överskrider den maximala storleken.
* Alla andra slut punkter:  
  * API: et bearbetar inte ett dokument som överskrider den maximala storleken och returnerar ett ogiltigt dokument fel för det. Om en API-begäran har flera dokument fortsätter API: et att bearbeta dem om de ligger inom gränsen för tecken.

Det maximala antalet dokument som du kan skicka i en enskild begäran beror på API-versionen och funktionen som du använder, vilket beskrivs i tabellen nedan.

#### <a name="version-3"></a>[Version 3](#tab/version-3)

Följande begränsningar gäller för det aktuella v3-API: et. Om du överskrider gränserna nedan skapas en HTTP 400-felkod.


| Funktion | Maximalt antal dokument per begäran | 
|----------|-----------|
| Språkidentifiering | 1000 |
| Attitydanalys | 10 |
| Åsikts utvinning | 10 |
| Extrahering av nyckelfraser | 10 |
| Igenkänning av namngiven enhet | 5 |
| Entity Linking | 5 |
| Textanalys för hälsa  | 10 för det webbaserade API: t 1000 för containern. |
| Analysera slut punkt | 25 för alla åtgärder. |

#### <a name="version-2"></a>[Version 2](#tab/version-2)

| Funktion | Maximalt antal dokument per begäran | 
|----------|-----------|
| Språkidentifiering | 1000 |
| Attitydanalys | 1000 |
| Extrahering av nyckelfraser | 1000 |
| Igenkänning av namngiven enhet | 1000 |
| Entity Linking | 1000 |

---

## <a name="rate-limits"></a>Hastighetsbegränsningar

Din hastighets gräns varierar beroende på [pris nivå](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/). Dessa begränsningar är desamma för båda versionerna av API: et. Dessa hastighets begränsningar gäller inte för Textanalys för hälso containern, som inte har någon angiven hastighets gräns.

| Nivå          | Förfrågningar per sekund | Begär Anden per minut |
|---------------|---------------------|---------------------|
| S/multi-service | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

Begär ande priser mäts för varje Textanalys funktion separat. Du kan skicka maximalt antal begär Anden för din pris nivå till varje funktion på samma tid. Om du till exempel befinner dig på `S` nivån och skickar 1000-begäranden samtidigt kan du inte skicka ytterligare en begäran om 59 sekunder.


## <a name="see-also"></a>Se även

* [Vad är API för textanalys](../overview.md)
* [Prisinformation](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
