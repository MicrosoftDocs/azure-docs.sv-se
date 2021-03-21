---
title: Logga in med autentisering med ett arbets-eller skol konto – Azure AD
description: Lär dig hur du loggar in på ditt arbets-eller skol konto med hjälp av de olika metoderna för att verifiera två faktorer.
services: active-directory
author: curtand
manager: daveba
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/02/2017
ms.author: curtand
ms.reviewer: librown
ms.custom: end-user, seo-update-azuread-jan
ms.openlocfilehash: 2f27dd7daf310e425b09db7905ad2f7c37fcff81
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94834174"
---
# <a name="sign-in-to-your-work-or-school-account-using-your-two-factor-verification-method"></a>Logga in på ditt arbets-eller skol konto med hjälp av en metod för tvåstegsverifiering

> [!NOTE]
> Syftet med den här artikeln är att gå igenom en typisk inloggnings upplevelse. Om du behöver hjälp med att logga in, eller om du vill felsöka problem, kan du läsa få [problem med Azure AD Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

## <a name="what-will-your-sign-in-experience-be"></a>Vad är inloggnings upplevelsen?
Inloggnings upplevelsen varierar beroende på vad du väljer att använda som den andra faktorn: ett telefonsamtal, en webbapp eller text. Välj det alternativ som bäst beskriver vad du gör:

| Hur loggar du in? |
| --- |
| [Med ett telefonsamtal till min mobil-eller arbets telefon](#signing-in-with-a-phone-call) |
| [Med en text till min mobil telefon](#signing-in-with-a-text-message)
| [Med meddelanden från Microsoft Authenticator-appen](#to-sign-in-with-a-notification-from-the-microsoft-authenticator-app) |
| Med verifierings koder från Microsoft Authenticator-appen |
| [Med en alternativ metod, eftersom jag inte kan använda min önskade metod just nu](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Logga in med ett telefonsamtal
Följande information beskriver hur du kan kontrol lera två steg med ett samtal till din mobil-eller arbets telefon.

1. Logga in på ett program eller en tjänst som Microsoft 365 med ditt användar namn och lösen ord.  
2. Microsoft anropar dig.  
3. Besvara telefonen och tryck på # Key.  

## <a name="signing-in-with-a-text-message"></a>Logga in med ett SMS
Följande information beskriver hur du verifierar två steg med ett SMS till din mobil telefon:

1. Logga in på ett program eller en tjänst som Microsoft 365 med ditt användar namn och lösen ord.
2. Microsoft skickar ett SMS till ett textmeddelande som innehåller en siffer kod.
3. Ange koden i rutan som visas på inloggnings sidan.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Logga in med Microsoft Authenticator-appen
Följande information beskriver hur du använder Microsoft Authenticator-appen för tvåstegsverifiering. Det finns två olika sätt att använda appen. Du kan ta emot push-meddelanden på enheten, eller så kan du öppna appen för att få en verifierings kod.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Logga in med ett meddelande från appen Microsoft Authenticator
1. Logga in på ett program eller en tjänst som Microsoft 365 med ditt användar namn och lösen ord.
2. Microsoft skickar ett meddelande till Microsoft Authenticator-appen på din enhet.

   ![Microsoft skickar ett meddelande](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Öppna meddelandet på din telefon och välj **Verifiera** nyckel. Om ditt företag kräver en PIN-kod anger du den här.
4. Du bör nu vara inloggad.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Logga in med en verifierings kod med Microsoft Authenticator-appen

Om du använder appen Microsoft Authenticator för att hämta verifierings koderna visas ett nummer under ditt konto namn när du öppnar appen. Det här talet ändras var 30: e sekund så att du inte använder samma nummer två gånger. När du uppmanas att ange en verifierings kod öppnar du appen och använder det där numret visas för tillfället.

1. Logga in på ett program eller en tjänst som Microsoft 365 med ditt användar namn och lösen ord.
2. Microsoft uppmanas att ange en verifierings kod.

   ![Ange verifierings kod](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Öppna appen Microsoft Authenticator på din telefon och ange koden i rutan där du loggar in.

## <a name="signing-in-with-an-alternate-method"></a>Logga in med en alternativ metod
Ibland har du inte den telefon eller enhet som du har angett som önskad verifieringsmetod. Den här situationen rekommenderar vi att du konfigurerar säkerhets kopierings metoder för ditt konto. I följande avsnitt visas hur du loggar in med en alternativ metod när din primära metod inte är tillgänglig.

1. Logga in på ett program eller en tjänst som Microsoft 365 med ditt användar namn och lösen ord.
2. Välj **Använd ett annat verifierings alternativ**. Du ser olika verifierings alternativ beroende på hur många installations program du har.
3. Välj en alternativ metod och logga in.

   ![Använd alternativ metod](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Nästa steg
- Om du har problem med att logga in med tvåstegsverifiering kan du få mer information om att [ha problem med Azure AD-Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

- Lär dig hur du [hanterar dina verifierings inställningar i två steg](multi-factor-authentication-end-user-manage-settings.md).

- Ta reda på hur du [kommer igång med Microsoft Authenticator-appen](user-help-auth-app-download-install.md) så att du kan använda aviseringar för att logga in i stället för texter och telefonsamtal.
