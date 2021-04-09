---
title: Loggnings fel och undantag i MSAL för Java
titleSuffix: Microsoft identity platform
description: Lär dig hur du loggar fel och undantag i MSAL för Java
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: d3fa13a6751b2d8acf1fc99aecbf174f1823fb0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954926"
---
# <a name="logging-in-msal-for-java"></a>Loggning i MSAL för Java

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-java-logging"></a>MSAL för Java-loggning

Med MSAL för Java kan du använda det loggnings bibliotek som du redan använder med din app, så länge det är kompatibelt med SLF4J. MSAL for Java använder [fasad för enkel loggning för Java](http://www.slf4j.org/) (SLF4J) som en enkel fasad eller abstraktion för olika loggnings ramverk, till exempel [Java. util. logging](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html), [logback](http://logback.qos.ch/) och [log4j](https://logging.apache.org/log4j/2.x/). SLF4J gör att användaren kan ansluta till det önskade loggnings ramverket vid distributions tillfället.

Om du till exempel vill använda logback som loggnings ramverk i ditt program lägger du till logback-beroendet till maven Pom-filen för ditt program:

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

Lägg sedan till konfigurations filen logback:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

SLF4J binder automatiskt till logback vid distributions tillfället. MSAL-loggar skrivs till-konsolen.

Instruktioner för hur du binder till andra loggnings ramverk finns i [hand boken för SLF4J](http://www.slf4j.org/manual.html).

### <a name="personal-and-organization-information"></a>Personlig information och organisationsinformation

Som standard samlar MSAL inte in eller loggar personliga eller organisatoriska data. I följande exempel är loggning av personliga eller organisatoriska data inaktiverat som standard:

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

Aktivera personlig och organisatorisk data loggning genom att ställa in `logPii()` på klient programs Builder. Om du aktiverar personlig eller organisatorisk data loggning måste din app ta ansvar för säker hantering av mycket känsliga data och som uppfyller alla myndighets krav.

I följande exempel är loggning av personliga eller organisatoriska data aktiverade:

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="next-steps"></a>Nästa steg

Fler kod exempel finns i [kod exempel för Microsoft Identity Platform](sample-v2-code.md).