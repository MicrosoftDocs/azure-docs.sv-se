---
title: Microsoft Authenticator tillgänglighet och begränsningar för Android i Kina | Microsoft Docs
description: Lär dig mer om hur du hämtar Microsoft Authenticator app-Availability i Kina
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/27/2021
ms.author: curtand
ms.openlocfilehash: d499417414e560bdaa3fcd0a12255946aca413d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99054849"
---
# <a name="microsoft-authenticator-for-android-in-the-public-cloud-in-china"></a>Microsoft Authenticator för Android i det offentliga molnet i Kina

Microsoft Authenticator-appen för Android finns tillgänglig för hämtning i Kina. Google Play Butik är inte tillgänglig i Kina, så appen måste laddas ned från andra Marketplace för kinesiska appar. Microsoft Authenticator-appen för Android är för närvarande tillgänglig i följande butiker i Kina:

- [Lenovo](https://www.lenovomm.com/appdetail/com.azure.authenticator/20197724)
- [Huawei](https://appgallery.cloud.huawei.com/uowap/index.html#/detailApp/C100262999?source=appshare&subsource=C100262999&shareTo=weixin&locale=zh_CN)
- [Samsung Galaxy-butik](http://apps.samsung.com/appquery/appDetail.as?appId=com.azure.authenticator)

Den senaste versionen av appen är i Google Play Butik, men vi uppdaterar appen på alla andra app-butiker så snabbt som möjligt. Eftersom det inte finns något anpassat Android-programpaket (APK) distribuerat till någon App Store kan appen sömlöst uppdateras från någon av följande platser:

- Den lagrings plats som den hämtades från
- Google Play Butik om användaren korsar regioner

## <a name="limitations"></a>Begränsningar

Microsoft Authenticator-appen för Android använder Googles Firebase Cloud Messaging system och Google Play-tjänster för att ta emot push-meddelanden. Eftersom ingen av tjänsterna är tillgänglig i Kina finns det vissa begränsningar i appens funktioner:

- Registreringen av Authenticator-appen som en Multi-Factor Authentication-metod (MFA) som använder push-meddelanden fungerar inte.

- Det går inte att konfigurera [telefonin loggning](../authentication/howto-authentication-sms-signin.md) . Användaren måste konfigurera appen som en MFA-metod med push-meddelanden, som för närvarande inte fungerar.

Om en användare tidigare har hanterat för att konfigurera telefonin loggning eller Multi-Factor Authentication med appen, kan de utföra en manuell kontroll av aviserings begär anden i appen och använda den för identitets verifiering.

## <a name="multi-factor-authentication-workaround"></a>Multi-Factor Authentication-lösning

I stället för att använda push-meddelanden för Multi-Factor Authentication kan användarna [Konfigurera sin Authenticator-app för att ta emot verifierings koder](multi-factor-authentication-setup-auth-app.md#set-up-the-microsoft-authenticator-app-to-use-verification-codes) på deras enheter som de kan använda för MFA för att verifiera sin identitet. De här verifierings koderna är giltiga i 30 sekunder och använder dem, administratörerna måste göra det möjligt för administratörer att utföra verifiering med tidsbaserad One-Time verifierings koder för lösen ord (TOTP mobilapp).

## <a name="availability"></a>Tillgänglighet

Microsoft Authenticator funktion | Tillgänglighet i Kina
------------------------------- | ---------------------
MFA-registrering med push-meddelanden | Inga
Redan befintligt MFA-konto verifierar identitet med push-meddelanden | Inga
Befintligt MFA-konto som utför manuell kontroll av meddelanden | Ja
MFA-registrering/-autentisering med TOTP mobilapp/verifierings koder | Ja
Registrering av telefonin loggning | Inga
Befintlig telefonin loggning med push-meddelanden | Inga
Befintlig telefonin loggning genom att utföra manuell kontroll av autentiseringsbegäranden | Ja
Stöd för Authenticator-appen för personliga Microsoft-konton | Nej

## <a name="next-steps"></a>Nästa steg

- [Ladda ned och installera appen Microsoft Authenticator](user-help-auth-app-download-install.md)
