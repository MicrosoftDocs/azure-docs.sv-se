---
title: Anropa ett webb-API från en daemon-app | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du skapar en daemon-app som anropar ett webb-API.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: bd0d53049c68843a6fd2cb6128c473d7c4f8d639
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582799"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Daemon-app som anropar webb-API: er – anropa ett webb-API från appen

.NET daemon-appar kan anropa ett webb-API. .NET daemon-appar kan också anropa flera förauktoriserade webb-API: er.

## <a name="calling-a-web-api-from-a-daemon-application"></a>Anropa ett webb-API från ett daemon-program

Så här använder du token för att anropa ett API:

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="java"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

---

## <a name="calling-several-apis"></a>Anropa flera API: er

Webb-API: er som du anropar måste vara för hands godkända för daemon-appar. Det finns inget stegvist godkännande med daemon-appar. (Det finns inga användar åtgärder.) Klient organisationens administratör måste ge sitt medgivande i förväg för programmet och alla API-behörigheter. Om du vill anropa flera API: er kan du hämta en token för varje resurs, varje tid som anropar `AcquireTokenForClient` . MSAL kommer att använda Application token-cache för att undvika onödiga tjänst anrop.

## <a name="next-steps"></a>Nästa steg

# <a name="net"></a>[.NET](#tab/dotnet)

Gå vidare till nästa artikel i det här scenariot, [Flytta till produktion](./scenario-daemon-production.md?tabs=dotnet).

# <a name="python"></a>[Python](#tab/python)

Gå vidare till nästa artikel i det här scenariot, [Flytta till produktion](./scenario-daemon-production.md?tabs=python).

# <a name="java"></a>[Java](#tab/java)

Gå vidare till nästa artikel i det här scenariot, [Flytta till produktion](./scenario-daemon-production.md?tabs=java).

---
