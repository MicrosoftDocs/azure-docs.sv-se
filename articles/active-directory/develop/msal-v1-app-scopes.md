---
title: Omfattningar för v 1.0-appar (MSAL) | Azure
description: Läs om omfattningarna för ett v 1.0-program med hjälp av Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: b35b39d7072b22d9cc3f7b4f4ef8886431b06f69
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98754666"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Omfattningar för ett webb-API som accepterar v 1.0-token

OAuth2-behörigheter är behörighets omfattningar som en Azure Active Directory (Azure AD) för utvecklare (v 1.0) webb-API (resurs) exponerar för klient program. Dessa behörighets omfattningar kan beviljas till klient program under medgivande. Se avsnittet om `oauth2Permissions` [referens för Azure Active Directory program manifest](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Omfattningar för att begära åtkomst till specifika OAuth2-behörigheter för ett v 1.0-program

För att hämta tokens för specifika omfång av ett v 1.0-program (till exempel Microsoft Graph-API, som är https://graph.microsoft.com) , skapar du omfång genom att sammanfoga ett önskat resurs-ID med önskad OAuth2-behörighet för resursen.

Till exempel för att komma åt användaren för en v 1.0-webb-API där app-ID-URI `ResourceId` : n är:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

För att läsa och skriva med MSAL.NET Azure AD med hjälp av Microsoft Graph API (https: \/ /Graph.Microsoft.com/), måste du skapa en lista över omfattningar som visas i följande exempel:

```csharp
string ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.microsoft.com/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Om du vill skriva den omfattning som motsvarar Azure Resource Manager API (https: \/ /Management.Core.Windows.net/) måste du begära följande omfång (Observera de två snedstrecken):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Du måste använda två snedstreck eftersom Azure Resource Manager-API: et förväntar sig ett snedstreck i sitt Audience-anspråk (AUD) och det finns ett snedstreck för att avgränsa API-namnet från omfånget.

Den logik som används av Azure AD är följande:

- För ADAL-slutpunkten (Azure AD v 1.0) med en v 1.0-åtkomsttoken (endast möjlig), AUD = Resource
- För MSAL (Microsoft Identity Platform) som efterfrågar en åtkomsttoken för en resurs som accepterar v 2.0-token `aud=resource.AppId`
- För MSAL (v 2.0-slutpunkt) som efterfrågar en åtkomsttoken för en resurs som accepterar en v 1.0-åtkomsttoken (vilket är fallet ovan), parsar Azure AD den önskade mål gruppen från det begärda omfånget genom att ta allt före det sista snedstrecket och använda det som resurs-ID. Om https: \/ /Database.Windows.net förväntar sig en mål grupp av "https: \/ /Database.Windows.net/" måste du begära ett omfånget "https: \/ /Database.Windows.net//.default". Se även GitHub problem [#747: resurs-URL: en avslutande snedstreck utelämnas, vilket orsakade SQL-auth-fel](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Omfattningar för att begära åtkomst till alla behörigheter för ett v 1.0-program

Om du vill hämta en token för alla statiska omfattningar i ett v 1.0-program lägger du till ". default" i app-ID-URI: n för API: et:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-a-client-credential-flowdaemon-app"></a>Omfattningar som ska begäras för ett flöde/en daemon-app för klientens autentiseringsuppgifter

Om det gäller klientens Credential-flöde, är det också den omfattning som passas `/.default` . Detta säger till Azure AD: "alla behörigheter på program nivå som administratören har samtyckt till i program registreringen.
