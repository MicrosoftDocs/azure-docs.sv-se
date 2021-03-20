---
title: Konfigurera klientautentisering av inkommande anrop – Azure Event Grid IoT Edge | Microsoft Docs
description: Läs om de möjliga konfigurationerna för klientautentisering för Event Grid-modulen.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 9525da4204e270d033f0c2354318bd71874eaf54
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91290780"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>Konfigurera klientautentisering av inkommande samtal

Den här guiden innehåller exempel på konfigurationer av möjliga klientautentisering för Event Grid-modulen. Event Grid-modulen stöder två typer av klientautentisering:

* Signaturer för delad åtkomst (SAS)
* Certifikatbaserad

Se [säkerhets-och autentiserings](security-authentication.md) guide för alla möjliga konfigurationer.

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Aktivera certifikatbaserad klientautentisering, inga självsignerade certifikat

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Aktivera certifikatbaserad klientautentisering, Tillåt självsignerade certifikat

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>Ange egenskapen **inbound__clientAuth__clientCert__allowUnknownCA** till **Sant** endast i test miljöer som du vanligt vis använder självsignerade certifikat. För produktions arbets belastningar rekommenderar vi att du ställer in den här egenskapen på **false** och certifikat från en certifikat utfärdare (ca).

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Aktivera certifikatbaserad autentisering och SAS-baserad klientautentisering

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=true",
    "inbound__clientAuth__sasKeys__key1=<some-secret1-here>",
    "inbound__clientAuth__sasKeys__key2=<some-secret2-here>",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>SAS-baserad klientautentisering tillåter en icke-IoT Edge-modul för hanterings-och körnings åtgärder som förutsätter att API-portarna är tillgängliga utanför IoT Edge nätverket.
