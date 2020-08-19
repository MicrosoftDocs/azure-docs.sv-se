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
ms.date: 08/14/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: 068c2dc698e9f0b6d6f2f6486dff863c1343b178
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258282"
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
| Maximal storlek på ett enskilt dokument | 5 120 tecken som mäts av [StringInfo. LengthInTextElements](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements). Gäller även för Textanalys för hälso container. |
| Maximal storlek på hela begäran | 1 MB. Gäller även för Textanalys för hälso container. |

Det maximala antalet dokument som du kan skicka i en enskild begäran beror på vilken API-version och funktion du använder.

#### <a name="version-3"></a>[Version 3](#tab/version-3)

Följande gränser har ändrats i v3 för API: et. Om du överskrider gränserna nedan skapas en HTTP 400-felkod.


| Funktion | Maximalt antal dokument per begäran | 
|----------|-----------|
| Språkidentifiering | 1000 |
| Attitydanalys | 10 |
| Extrahering av nyckelfraser | 10 |
| Igenkänning av namngiven enhet | 5 |
| Entity Linking | 5 |
| Textanalys för hälso container | 1000 |
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

Begär Anden mäts för varje Textanalys funktion separat. Du kan till exempel skicka maximalt antal begär Anden för din pris nivå till varje funktion på samma tillfälle.  


## <a name="see-also"></a>Se även

* [Vad är API för textanalys](../overview.md)
* [Prisinformation](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
