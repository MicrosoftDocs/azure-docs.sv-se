---
title: Ange cookie-princip för avancerad läsare
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du ställer in cookie-principen för den fördjupade läsaren.
services: cognitive-services
author: nitinme
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: 8b0a1f4a948aa6fec565130acb5267476a1d4401
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048654"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Ange cookie-principen för den fördjupade läsaren

Den fördjupade läsaren kommer att inaktivera cookie-användning som standard. Om du aktiverar cookie-användning kan den fördjupade läsaren använda cookies för att underhålla användar inställningar och spåra funktions användning. Om du aktiverar användning av cookies i den fördjupade läsaren bör du ta hänsyn till kraven i EU: s policy för cookie-efterlevnad. Det är värd programmets ansvar för att erhålla nödvändiga användar medgivande i enlighet med EU: s policy för cookie-kompatibilitet.

Cookie-principen kan ställas in via [alternativen](../reference.md#options)för avancerad läsare.

## <a name="enable-cookie-usage"></a>Aktivera användning av cookie

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>Inaktivera cookie-användning

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>Nästa steg

* Visa [Node.js snabb start](../quickstarts/client-libraries.md?pivots=programming-language-nodejs) för att se vad mer du kan göra med avancerad läsar-SDK med hjälp av Node.js
* Visa [Android-självstudien](../how-to-launch-immersive-reader.md) för att se vad mer du kan göra med SDK: n för avancerad läsare med Java eller Kotlin för Android
* Visa [iOS-självstudien](../how-to-launch-immersive-reader.md) för att se vad mer du kan göra med SDK: n för avancerad läsare med Swift för iOS
* Visa [python-självstudien](../how-to-launch-immersive-reader.md) för att se vad mer du kan göra med avancerad läsar-SDK med hjälp av python
* Utforska SDK: [n för avancerad läsare](https://github.com/microsoft/immersive-reader-sdk) och [Avancerad läsare SDK-referens](../reference.md)