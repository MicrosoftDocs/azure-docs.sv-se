---
title: 'API: er för SaaS-utförande på Microsofts kommersiella marknads plats'
description: 'En introduktion till uppfyllande API: er som gör att du kan integrera dina SaaS-erbjudanden i Microsoft AppSource och Azure Marketplace.'
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/18/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 2e6381afb19018822f6f37171a5ca4b3d929b42e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88037532"
---
# <a name="saas-fulfillment-apis-in-the-microsoft-commercial-marketplace"></a>API: er för SaaS-utförande på Microsofts kommersiella marknads plats

API: erna för SaaS-utförande möjliggör utgivare, som även kallas oberoende program varu leverantörer (ISV) för att publicera och sälja sina SaaS-program i Microsoft AppSource, Azure Marketplace och Azure Portal. Dessa API: er gör det möjligt för ISV-program att delta i alla Commerce-aktiverade kanaler: direkt, partner lampa (åter försäljare) och fält indikator.  Att integrera med dessa API: er är ett krav för att skapa och publicera ett SaaS erbjudande i Partner Center.

ISV: er måste implementera följande API-flöden genom att lägga till i sin SaaS-tjänst kod för att bibehålla samma prenumerations status för både ISV: er och Microsoft:

* Landnings sid flöde: Microsoft meddelar utgivaren att utgivarens SaaS-erbjudande har köpts av en kund på Marketplace.
* Aktiverings flöde: Publisher meddelar Microsoft att ett nyligen köpt SaaS-konto har kon figurer ATS på utgivarens sida.
* Uppdaterings flöde: ändring av inköps plan och/eller antal köpta platser.
* Pausa och återställa flöde: om du avbryter det köpta SaaS-erbjudandet om kundens betalnings metod inte längre är giltig. Det inaktiverade erbjudandet kan återställas när problemet med betalnings metoden är löst.
* Webhook-flöden: Microsoft meddelar utgivare om SaaS prenumerations ändringar och uppsägning som utlösts av kunden från Microsoft.

För annullering av den köpta SaaS-prenumerationen är integreringen valfritt, eftersom den kan utföras av kunden från Microsoft.

Korrekt integrering med SaaS-API: er är avgörande för att säkerställa att

* de slutanvändare som köpte utgivarens SaaS-erbjudande debiteras korrekt av Microsoft.
* slutanvändarna får rätt att köpa, konfigurera, använda och hantera SaaS-prenumerationer som köpts i Marketplace.

Dessa API: er gör att utgivarens erbjudanden kan delta i alla Commerce-aktiverade kanaler:

* direct
* partner lampa (åter försäljare, CSP)
* indikator för fält

I scenariot för åter försäljare (CSP) köper en KRYPTOGRAFIPROVIDER SaaS-erbjudandet för slut kunden. En kund förväntas använda SaaS-erbjudandet, men CSP: n är den entitet som gör följande:

* fakturera kunden
* ändra prenumerations planer/belopp för köpta platser
* prenumerationen avbryts

Utgivaren behöver inte implementera något av API-anropen på ett annat sätt för det här scenariot.

För ytterligare information om CSP, se https://partner.microsoft.com/licensing .

>[!Warning]
>Den aktuella versionen av detta API är version 2, som ska användas för alla nya SaaS-erbjudanden. Version 1 av API: et är inaktuell och underhålls för att stödja befintliga erbjudanden.

>[!Note]
>API: erna för SaaS-utförande är endast avsedda att anropas från en backend-tjänst för utgivaren. Integrering med API: er direkt från utgivarens webb sida stöds inte. Endast tjänst-till-tjänst-autentiseringsschema ska användas.

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det, registrera ditt SaaS-program i [Azure Portal](https://ms.portal.azure.com) enligt beskrivningen i [Registrera ett Azure AD-program](./pc-saas-registration.md).  Sedan använder du den mest aktuella versionen av det här gränssnittet för utveckling: [SaaS uppfyllelse API version 2](./pc-saas-fulfillment-api-v2.md).
