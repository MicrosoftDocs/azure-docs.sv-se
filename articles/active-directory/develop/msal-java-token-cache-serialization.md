---
title: Anpassad token cache-serialisering (MSAL4j)
titleSuffix: Microsoft identity platform
description: Lär dig hur du serialiserar token-cachen för MSAL för Java
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: e85cfb8c03111b889a9f95509f337d415c517163
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87312375"
---
# <a name="custom-token-cache-serialization-in-msal-for-java"></a>Anpassad token cache-serialisering i MSAL för Java

Om du vill bevara token-cachen mellan instanser av ditt program måste du anpassa serialiseringen. Java-klasser och gränssnitt som ingår i cachelagring av token är följande:

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html): gränssnitt som representerar cache för säkerhetstoken.
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html): gränssnitt som representerar körning av kod före och efter åtkomst. Du skulle @Override *BeforeCacheAccess* och *afterCacheAccess* med den logik som ansvarar för serialisering och deserialisering av cacheminnet.
- [ITokenCacheContext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html): gränssnitt som representerar kontexten där token cache används. 

Nedan visas en naïve-implementering av anpassad serialisering för serialisering/deserialisering för token. Kopiera och klistra inte in detta i en produktions miljö.

```Java
static class TokenPersistence implements ITokenCacheAccessAspect {
String data;

TokenPersistence(String data) {
        this.data = data;
}

@Override
public void beforeCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        iTokenCacheAccessContext.tokenCache().deserialize(data);
}

@Override
public void afterCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        data = iTokenCacheAccessContext.tokenCache().serialize();
}
```

```Java
// Loads cache from file
String dataToInitCache = readResource(this.getClass(), "/cache_data/serialized_cache.json");

ITokenCacheAccessAspect persistenceAspect = new TokenPersistence(dataToInitCache);

// By setting *TokenPersistence* on the PublicClientApplication, MSAL will call *beforeCacheAccess()* before accessing the cache and *afterCacheAccess()* after accessing the cache. 
PublicClientApplication app = 
PublicClientApplication.builder("my_client_id").setTokenCacheAccessAspect(persistenceAspect).build();
```

## <a name="learn-more"></a>Läs mer

Lär dig mer om [att hämta och ta bort konton från token-cachen med MSAL för Java](msal-java-get-remove-accounts-token-cache.md).
