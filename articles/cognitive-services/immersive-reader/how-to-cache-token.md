---
title: Cachelagra autentiseringstoken
titleSuffix: Azure Cognitive Services
description: I den här artikeln visas hur du cachelagrar autentiseringstoken.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 01/14/2020
ms.author: metang
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: e79ae3914e32038e2823fb37e3eee658c95e0003
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102608775"
---
# <a name="how-to-cache-the-authentication-token"></a>Så här cachelagrar du autentiseringstoken

Den här artikeln visar hur du cachelagrar autentiseringstoken för att förbättra programmets prestanda.

## <a name="using-aspnet"></a>Använda ASP.NET

Importera NuGet-paketet **Microsoft. IdentityModel. clients. ActiveDirectory** , som används för att hämta en token. Använd sedan följande kod för att hämta en `AuthenticationResult` med hjälp av de autentiseringsmetoder som du fick när du [skapade den fördjupade läsaren](./how-to-create-immersive-reader.md).

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

`AuthenticationResult`Objektet har en `AccessToken` egenskap som är den faktiska token som du kommer att använda när du startar den fördjupade läsaren med hjälp av SDK. Den har också en `ExpiresOn` egenskap som anger när token upphör att gälla. Innan du startar den fördjupade läsaren kan du kontrol lera om token har upphört att gälla och bara hämta en ny token om den har upphört att gälla.

## <a name="using-nodejs"></a>Använda Node.JS

Lägg till NPM-paketet för [**begäran**](https://www.npmjs.com/package/request) i projektet. Använd följande kod för att hämta en token med hjälp av de autentiseringsmetoder som du fick när du [skapade den fördjupade läsaren](./how-to-create-immersive-reader.md).

```javascript
router.get('/token', function(req, res) {
    request.post(
        {
            headers: { 'content-type': 'application/x-www-form-urlencoded' },
            url: `https://login.windows.net/${TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: CLIENT_ID,
                client_secret: CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, json) {
            const result = JSON.parse(json);
            return res.send({
                access_token: result.access_token,
                expires_on: result.expires_on
            });
        }
    );
});
```

`expires_on`Egenskapen är det datum och den tid då token upphör att gälla, uttryckt i antal sekunder sedan den 1 januari 1970 UTC. Använd det här värdet för att avgöra om din token har upphört att gälla innan du försöker hämta ett nytt.

```javascript
async function getToken() {
    if (Date.now() / 1000 > CREDENTIALS.expires_on) {
        CREDENTIALS = await refreshCredentials();
    }
    return CREDENTIALS.access_token;
}
```

## <a name="next-steps"></a>Nästa steg

* Utforska [SDK-referensen för avancerad läsare](./reference.md)