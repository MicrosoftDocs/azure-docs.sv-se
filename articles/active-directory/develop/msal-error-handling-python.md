---
title: Hantera fel och undantag i MSAL för Python
titleSuffix: Microsoft identity platform
description: Lär dig hur du hanterar fel och undantag, utmanings krav för villkorlig åtkomst och nya försök i MSAL för python-program.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, rayluo
ms.custom: aaddev
ms.openlocfilehash: 3046787393d38ed60b54236f33acc065db90321d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98761084"
---
# <a name="handle-errors-and-exceptions-in-msal-for-python"></a>Hantera fel och undantag i MSAL för Python

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-python"></a>Fel hantering i MSAL för python

I MSAL för python förmedlas de flesta fel som ett retur värde från API-anropet. Felet visas som en ord lista som innehåller JSON-svaret från Microsoft Identity Platform.

* Ett lyckat svar innehåller `"access_token"` nyckeln. Svars formatet definieras av OAuth2-protokollet. Mer information finns i [5,1 svar](https://tools.ietf.org/html/rfc6749#section-5.1)
* Ett fel svar innehåller `"error"` vanligt vis `"error_description"` . Svars formatet definieras av OAuth2-protokollet. Mer information finns i [5,2-fel svar](https://tools.ietf.org/html/rfc6749#section-5.2)

När ett fel returneras `"error_description"` innehåller nyckeln ett läsbart meddelande, vilket i sin tur vanligt vis innehåller en felkod för Microsoft Identity Platform. Mer information om de olika fel koderna finns i [fel koder för autentisering och auktorisering](./reference-aadsts-error-codes.md).

I MSAL för python är undantag sällsynt eftersom de flesta fel hanteras genom att returnera ett felvärde. `ValueError`Undantaget uppstår bara när det är problem med hur du försöker använda biblioteket, till exempel när API-parameter (s) har fel format.

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Nästa steg

Överväg att aktivera [loggning i MSAL för python](msal-logging-python.md) för att hjälpa dig att diagnostisera och felsöka problem.
