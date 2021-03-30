---
title: Notification Hubs TLS-uppdateringar
description: Läs mer om stöd för TLS i Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 04/29/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 87a3627d7820f9f456ac08e2f20b70af961f817e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87084253"
---
# <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

För att säkerställa en högre säkerhets nivå kommer Notification Hubs att inaktivera stöd för TLS-versionerna 1,0 och 1,1 den **31 December 2020** (från och med 30 april 2020). Dessa äldre protokoll ger svag kryptering och är sårbara för BEAST-och POODLE-attacker. Program som distribueras till enheter som kör Android version 5 eller senare, eller iOS version 5 eller senare, påverkas inte av den här ändringen eftersom operativ systemen har stöd för TLS 1,2 och klienten och servern förhandlar den högsta ömsesidigt stödda versionen av protokollet vid anslutning.

Vi rekommenderar att du läser igenom alla dina program som använder Azure Notification Hubs för att säkerställa att de använder de mest tillämpliga biblioteken och TLS-stackar som stöder TLS 1,2.

## <a name="update-apps"></a>Uppdatera appar

Du kan se till att dina iOS-appar använder TLS 1,2 med hjälp av säkerhetsfunktionen i Apple som kallas app Transport Security (ATS). ATS kan inte användas för SDK: er som är äldre än iOS 9,0 eller macOS 10,11, och du kan läsa mer om det från [Apples dokumentation](https://developer.apple.com/documentation/security/preventing_insecure_network_connections).

För Android-program som använder SSLSocket-instanser ställer du in aktiverade protokoll på varje SSLSocket-instans som anges i [setEnabledProtocols](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D)).

Med hjälp av tabellen på sidan för [kompatibilitet med TLS-protokoll](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) kan du mappa operativ system med kompatibla TLS-versioner.

Mer information finns i Översikt över [stödet för TLS-protokoll i Windows](/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows).

## <a name="next-steps"></a>Nästa steg

- [Översikt över Notification Hubs](notification-hubs-push-notification-overview.md)
