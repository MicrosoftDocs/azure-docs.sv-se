---
title: Logga in med autentisering med ett arbets- eller skolkonto – Azure AD
description: Lär dig hur du loggar in på ditt arbets- eller skolkonto med hjälp av de olika tvåfaktorsverifieringsmetoderna.
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
ms.openlocfilehash: b373288eb5cd47f99bdbb25f961e1330a708d7d1
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739196"
---
# <a name="sign-in-to-your-work-or-school-account-using-your-two-factor-verification-method"></a>Logga in på ditt arbets- eller skolkonto med hjälp av din tvåfaktorsverifieringsmetod

> [!NOTE]
> Syftet med den här artikeln är att gå igenom en typisk inloggningsupplevelse. Om du behöver hjälp med att logga in eller felsöka problem kan du gå [till Ha problem med Azure AD Multi-Factor Authentication.](multi-factor-authentication-end-user-troubleshoot.md)

## <a name="what-will-your-sign-in-experience-be"></a>Hur kommer inloggningen att se ut?
Inloggningen varierar beroende på vad du väljer att använda som andra faktor: ett telefonsamtal, en autentiseringsapp eller sms. Välj det alternativ som bäst beskriver vad du gör:

| Hur loggar du in? |
| --- |
| [Med ett telefonsamtal till min mobiltelefon eller kontorstelefon](#signing-in-with-a-phone-call) |
| [Med ett sms till min mobiltelefon](#signing-in-with-a-text-message)
| [Med meddelanden från Microsoft Authenticator appen](#to-sign-in-with-a-notification-from-the-microsoft-authenticator-app) |
| Med verifieringskoder från Microsoft Authenticator appen |
| [Med en alternativ metod, eftersom jag inte kan använda den metod jag föredrar just nu](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Logga in med ett telefonsamtal
Följande information beskriver tvåstegsverifieringsupplevelsen med ett samtal till din mobil- eller kontorstelefon.

1. Logga in på ett program eller en tjänst, till exempel Microsoft 365 med ditt användarnamn och lösenord.  
2. Microsoft anropar dig.  
3. Svara i telefonen och tryck på #-tangenten.  

## <a name="signing-in-with-a-text-message"></a>Logga in med ett SMS
Följande information beskriver tvåstegsverifieringen med ett SMS till din mobiltelefon:

1. Logga in på ett program eller en tjänst, till exempel Microsoft 365 med ditt användarnamn och lösenord.
2. Microsoft skickar ett SMS som innehåller en nummerkod.
3. Ange koden i rutan på inloggningssidan.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Logga in med Microsoft Authenticator appen
Följande information beskriver hur du använder Microsoft Authenticator för tvåstegsverifieringar. Det finns två olika sätt att använda appen. Du kan ta emot push-meddelanden på enheten eller öppna appen för att få en verifieringskod.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Logga in med ett meddelande från Microsoft Authenticator appen
1. Logga in på ett program eller en tjänst, till exempel Microsoft 365 med ditt användarnamn och lösenord.
2. Microsoft skickar ett meddelande till Microsoft Authenticator på din enhet.

   ![Microsoft skickar ett meddelande](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Öppna meddelandet på din telefon och välj **knappen** Verifiera. Om ditt företag kräver en PIN-kod anger du den här.
4. Du bör nu vara inloggad.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Logga in med en verifieringskod med Microsoft Authenticator appen

Om du använder Microsoft Authenticator för att hämta verifieringskoder visas ett nummer under ditt kontonamn när du öppnar appen. Det här talet ändras var 30:e sekund så att du inte använder samma tal två gånger. När du tillfrågas om en verifieringskod öppnar du appen och använder det nummer som visas för tillfället.

1. Logga in på ett program eller en tjänst, till exempel Microsoft 365 med ditt användarnamn och lösenord.
2. Microsoft uppmanar dig att ange en verifieringskod.

   ![Ange verifieringskod](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Öppna Microsoft Authenticator på telefonen och ange koden i rutan där du loggar in.

## <a name="signing-in-with-an-alternate-method"></a>Logga in med en alternativ metod
Ibland har du inte den telefon eller enhet som du har ställt in som önskad verifieringsmetod. Det är därför vi rekommenderar att du säkerhetskopiera metoder för ditt konto. I följande avsnitt visas hur du loggar in med en alternativ metod när din primära metod kanske inte är tillgänglig.

1. Logga in på ett program eller en tjänst, till exempel Microsoft 365 med ditt användarnamn och lösenord.
2. Välj **Använd ett annat verifieringsalternativ.** Du ser olika verifieringsalternativ baserat på hur många du har ställt in.
3. Välj en alternativ metod och logga in.

   ![Använd alternativ metod](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Nästa steg
- Om du har problem med att logga in med tvåstegsverifiering kan du läsa mer i [Ha problem med Azure AD Multi-Factor Authentication.](multi-factor-authentication-end-user-troubleshoot.md)

- Lär dig hur [du hanterar dina tvåstegsverifieringsinställningar.](multi-factor-authentication-end-user-manage-settings.md)

- Ta reda på hur [du kommer igång med Microsoft Authenticator-appen](user-help-auth-app-download-install.md) så att du kan använda meddelanden för att logga in i stället för sms och telefonsamtal.
