---
title: Konfigurera Läs upp
titleSuffix: Azure Cognitive Services
description: I den här artikeln visas hur du konfigurerar de olika alternativen för Läs högt.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 8b45fe07b4bd42059199197bc5b99f20f6b2a8cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102608724"
---
# <a name="how-to-configure-read-aloud"></a>Så här konfigurerar du Läs upp

Den här artikeln visar hur du konfigurerar de olika alternativen för Läs högt i den fördjupade läsaren.

## <a name="automatically-start-read-aloud"></a>Automatisk start av läsa upp

`options`Parametern innehåller alla flaggor som kan användas för att konfigurera Läs högt. Ange `autoplay` till `true` om du vill aktivera automatisk start av läsa upp efter att den fördjupade läsaren har startats.

```typescript
const options = {
    readAloudOptions: {
        autoplay: true
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

> [!NOTE]
> På grund av begränsningar i webbläsaren stöds inte automatisk uppspelning i Safari.

## <a name="configure-the-voice"></a>Konfigurera rösten

Ange `voice` antingen `male` eller `female` . Alla språk stöder inte båda rösterna. Mer information finns på sidan [språk stöd](./language-support.md) .

```typescript
const options = {
    readAloudOptions: {
        voice: 'female'
    }
};
```

## <a name="configure-playback-speed"></a>Konfigurera uppspelnings hastighet

Ange `speed` ett tal mellan `0.5` (50%) och `2.5` (250%) sin. Värden utanför intervallet kommer att få clamped till antingen 0,5 eller 2,5.

```typescript
const options = {
    readAloudOptions: {
        speed: 1.5
    }
};
```

## <a name="next-steps"></a>Nästa steg

* Utforska [SDK-referensen för avancerad läsare](./reference.md)