---
title: 'ML Studio (klassisk): Hantera Azure AI Gallery data – Azure'
description: Du kan exportera och ta bort användar data från en produkt från Azure AI Gallery med hjälp av gränssnitts-eller AI-galleriets katalog-API. Den här artikeln visar hur du gör.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 05/25/2018
ms.reviewer: mldocs
ms.openlocfilehash: 3461b8026079d089bacca9f12aa94e13f5cb2874
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519243"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Visa och ta bort användar data i produkten från Azure AI Gallery



Du kan visa och ta bort användar data från Azure AI Gallery med hjälp av gränssnitts-eller AI-galleriets katalog-API. Den här artikeln beskriver hur du gör.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Visa dina data i AI-galleriet med användar gränssnittet

Du kan visa objekt som du har publicerat via användar gränssnittet för Azure AI Gallery webbplats. Användare kan visa både offentliga och ej listade lösningar, projekt, experiment och andra publicerade objekt:

1.    Logga in på [Azure AI Gallery](https://gallery.azure.ai/).
2.    Klicka på profil bilden i det övre högra hörnet och sedan på konto namnet för att läsa in din profil sida.
3.    På sidan profil visas alla objekt som har publicerats i galleriet, inklusive poster som inte finns i listan.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Använd AI-galleriets katalog-API för att visa dina data

Du kan visa data som samlas in via program mässigt via AI-galleriets katalog-API, som är tillgängligt på https://catalog.cortanaanalytics.com/entities . Om du vill visa data behöver du ditt författar-ID. Om du vill visa enheter som inte finns i listan via katalog-API: et behöver du en åtkomsttoken.

Katalog svar returneras i JSON-format.

### <a name="get-an-author-id"></a>Hämta ett författar-ID
Författar-ID baseras på den e-postadress som används vid publicering till Azure AI Gallery. Den ändras inte:

1. Logga in på [Azure AI Gallery](https://gallery.azure.ai/).
2. Klicka på profil bilden i det övre högra hörnet och sedan på konto namnet för att läsa in din profil sida.
3. URL: en i adress fältet visar det alfanumeriska ID: t `authorId=` . Till exempel för URL: \
    `https://gallery.azure.ai/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

    Författar-ID: \
    `99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>Hämta din åtkomsttoken

Du behöver en åtkomsttoken för att visa enheter som inte finns i listan via katalog-API. Utan en åtkomsttoken kan användarna fortfarande Visa offentliga entiteter och annan användar information.

För att få en åtkomsttoken måste du kontrol lera `DataLabAccessToken` rubriken för en HTTP-begäran som webbläsaren gör till katalog-API: et när du är inloggad:

1.    Logga in på [Azure AI Gallery](https://gallery.azure.ai/).
2.    Klicka på profil bilden i det övre högra hörnet och sedan på konto namnet för att läsa in din profil sida.
3.    Öppna webbläsarens Utvecklarverktyg fönstret genom att trycka på F12, Välj fliken nätverk och uppdatera sidan. 
4. Filtrera begär anden i sträng *katalogen* genom att skriva i text rutan filter.
5.    I begär anden till URL: `https://catalog.cortanaanalytics.com/entities` en, letar du upp en get-begäran och väljer fliken *sidhuvud* . Rulla ned till avsnittet *begärandehuvuden* .
6.    Under rubriken `DataLabAccessToken` finns den alfanumeriska token. Dela inte denna token om du vill skydda dina data.

### <a name="view-user-information"></a>Visa användar information
Använd det författar-ID som du fick i föregående steg, Visa information i en användares profil genom att ersätta `[AuthorId]` följande URL:

`https://catalog.cortanaanalytics.com/users/[AuthorID]`

Till exempel följande URL-begäran:

`https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

Returnerar ett svar som:

```json
{"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}
```

### <a name="view-public-entities"></a>Visa offentliga entiteter

Katalog-API: t lagrar information om publicerade entiteter till Azure AI Gallery som du också kan visa direkt på [AI-galleriets webbplats](https://gallery.azure.ai/). 

Om du vill visa publicerade entiteter går du till följande URL och ersätter `[AuthorId]` med det författar-ID som hämtades i [Hämta ett författar-ID](#get-an-author-id) ovan.

`https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'`

Exempel:

`https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'`

### <a name="view-unlisted-and-public-entities"></a>Visa enheter som inte finns i listan och offentliga entiteter

Den här frågan visar endast offentliga entiteter. Om du vill visa alla entiteter, inklusive List rutor, anger du den åtkomsttoken som hämtades från föregående avsnitt.

1.    Använd ett verktyg som [Postman](https://www.getpostman.com)och skapa en HTTP GET-begäran till katalog-URL: en enligt beskrivningen i [Hämta din](#get-your-access-token)åtkomsttoken.
2.    Skapa ett HTTP-begärandehuvuden `DataLabAccessToken` som kallas, med värdet inställt på åtkomsttoken.
3.    Skicka HTTP-begäran.

> [!TIP]
> Om entiteter som inte finns i listan inte visas i svar från katalog-API: et, kan användaren ha en ogiltig eller utgången åtkomsttoken. Logga ut från Azure AI Gallery och upprepa sedan stegen i [Hämta](#get-your-access-token) åtkomsttoken för att förnya token. 
