---
title: Konfigurera autentisering med webhook-prenumerant – Azure Event Grid IoT Edge | Microsoft Docs
description: Konfigurera autentisering för webhook-prenumerant
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: d78b6e80005e9533ccd5ebfaea853f35ae6004be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171660"
---
# <a name="configure-webhook-subscriber-authentication"></a>Konfigurera autentisering för webhook-prenumerant

Den här guiden innehåller exempel på möjliga webhook-Subscriber-konfigurationer för en Event Grid-modul. Som standard accepteras bara HTTPS-slutpunkter för webhook-prenumeranter. Event Grid-modulen kommer att nekas om prenumeranten presenterar ett självsignerat certifikat.

## <a name="allow-only-https-subscriber"></a>Tillåt endast HTTPS-prenumerant

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>Tillåt HTTPS-prenumerant med självsignerat certifikat

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Ange egenskapen `outbound__webhook__allowUnknownCA` till `true` endast i test miljöer som du vanligt vis använder självsignerade certifikat. För produktions arbets belastningar rekommenderar vi att de anges till **falskt**.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>Tillåt HTTPS-prenumerant men hoppa över certifikat verifiering

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=true",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>Ange egenskapen `outbound__webhook__skipServerCertValidation` till `true` endast i test miljöer eftersom du kanske inte presenterar ett certifikat som behöver autentiseras. För produktions arbets belastningar rekommenderar vi att de anges till **falskt**

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>Tillåt både HTTP och HTTPS med självsignerade certifikat

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=false",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Ange egenskapen `outbound__webhook__httpsOnly` som `false` endast i test miljöer som du kanske vill ha en http-prenumerant först. För produktions arbets belastningar rekommenderar vi att de anges till **Sant**
