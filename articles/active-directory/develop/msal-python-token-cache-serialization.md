---
title: Anpassad token cache-serialisering (MSAL python) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du serialiserar token-cachen för MSAL för python
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/13/2019
ms.author: rayluo
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 7aaf59377949101ba8dd5c9454b89229e925e859
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87846198"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>Anpassad token cache-serialisering i MSAL för python

I MSAL python anges en token för InMemory-cachen som finns kvar under sessionen, som standard när du skapar en instans av [ClientApplication](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication).

Serialisering av token-cachen, så att olika sessioner av din app kan komma åt den, anges inte "utanför lådan". Det beror på att MSAL python kan användas i appar som inte har åtkomst till fil systemet, t. ex. Web Apps. Om du vill ha en beständig token-cache i en MSAL python-app måste du tillhandahålla anpassad cachelagring av token.

Strategierna för att serialisera token-cachen varierar beroende på om du skriver ett offentligt klient program (skriv bord) eller ett konfidentiellt klient program (webbapp, webb-API eller daemon-app).

## <a name="token-cache-for-a-public-client-application"></a>Token cache för ett offentligt klient program

Offentliga klient program körs på en användares enhet och hanterar tokens för en enskild användare. I det här fallet kan du serialisera hela cacheminnet till en fil. Kom ihåg att tillhandahålla fil låsning om din app eller en annan app kan komma åt cachen samtidigt. Ett enkelt exempel på hur du serialiserar en token-cache till en fil utan att låsa finns i exemplet i referens dokumentationen för [SerializableTokenCache](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) -klassen.

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>Token-cache för en webbapp (konfidentiellt klient program)

För webbappar eller webb-API: er kan du använda sessionen eller en Redis-cache eller en databas för att lagra token-cachen. Det bör finnas en token cache per användare (per konto) så att du kan serialisera token-cachen per konto.

## <a name="next-steps"></a>Nästa steg

Se [MS-Identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) för ett exempel på hur du använder token-cachen för en Windows-eller Linux-webbapp eller webb-API. Exemplet är för en webbapp som anropar Microsoft Graph API.
