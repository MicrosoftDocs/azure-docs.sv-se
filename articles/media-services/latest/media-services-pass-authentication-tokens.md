---
title: Skicka autentiseringstoken till Media Services v3 | Microsoft Docs
description: Lär dig hur du skickar autentiseringstoken från klienten till Media Services v3 Key Delivery Service
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 590309ad392876ba3c5cb6d21abe777ab5a93efb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572541"
---
# <a name="pass-tokens-to-the-azure-media-services-v3-key-delivery-service"></a>Skicka token till Azure Media Services v3 Key Delivery Service

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Kunderna frågar ofta hur en spelare kan skicka token till den Azure Media Services nyckel leverans tjänsten för verifiering så att spelaren kan hämta nyckeln. Media Services stöder formatet simple web token (SWT) och JSON Web Token (JWT). Token-autentisering används för alla typer av nycklar, oavsett om du använder common Encryption eller Advanced Encryption Standard (AES) kryptering i systemet.

 Beroende på vilken spelare och plattform du riktar in dig på, kan du skicka token med spelaren på följande sätt:

## <a name="pass-a-token-through-the-http-authorization-header"></a>Skicka en token via HTTP Authorization-huvudet

> [!NOTE]
> Prefixet "Bearer" förväntas enligt OAuth 2,0-specifikationerna. Ange video källan genom att välja **AES (JWT-token)** eller **AES (SWT-token)**. Token skickas via Authorization-huvudet.

## <a name="pass-a-token-via-the-addition-of-a-url-query-parameter-with-tokentokenvalue"></a>Skicka en token genom att lägga till en URL-frågeparameter med token = tokenvalue.

> [!NOTE]
> Prefixet "Bearer" förväntas inte. Eftersom token skickas via en URL måste du skydda token-strängen. Här är en exempel kod i C# som visar hur du gör det:

```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
```

## <a name="pass-a-token-through-the-customdata-field"></a>Skicka en token via fältet CustomData

Det här alternativet används endast för PlayReady License Acquisition genom CustomData-fältet i PlayReady License Acquisition Challenge. I det här fallet måste token finnas i XML-dokumentet som beskrivs här:

```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
```

Lägg till din autentiseringstoken i token-elementet.
