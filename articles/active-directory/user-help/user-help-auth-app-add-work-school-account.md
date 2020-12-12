---
title: Lägg till ett arbets-eller skol konto i Microsoft Authenticator app – Azure AD
description: Lägg till ditt arbets-eller skol konto i Microsoft Authenticator-appen för att verifiera din identitet när du använder tvåstegsverifiering.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 04c9bc429d9663f7ac36b6ba8f40abf225eb71c6
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359123"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Lägg till ditt arbets-eller skol konto i Microsoft Authenticator-appen

Om din organisation använder tvåstegsverifiering kan du konfigurera ditt arbets-eller skol konto för att använda Microsoft Authenticator-appen som en av verifierings metoderna.

>[!Important]
>Innan du kan lägga till ditt konto måste du ladda ned och installera Microsoft Authenticator-appen. Om du inte har gjort det än följer du stegen i artikeln [Hämta och installera appen](user-help-auth-app-download-install.md) .

## <a name="add-your-work-or-school-account"></a>Lägga till ditt arbets- eller skolkonto

Du kan lägga till ditt arbets-eller skol konto i Microsoft Authenticator-appen genom att göra något av följande:

- Logga in på ditt arbets-eller skolkonto autentiseringsuppgifter (förhands granskning)
- Skanna en QR-kod

### <a name="sign-in-with-your-credentials"></a>Logga in med dina inloggningsuppgifter

>[!Note]
>Den här funktionen kan bara användas av användare vars administratörer har aktiverat telefonin loggning med hjälp av Authenticator-appen för dem.

För att lägga till ett konto genom att logga in på ditt arbets-eller skol konto med dina autentiseringsuppgifter:

1. Öppna appen Microsoft Authenticator och välj till **+** knappen och tryck på **Lägg till arbets-eller skol konto**. Välj **Logga in**.

1. Ange autentiseringsuppgifterna för ditt arbets-eller skol konto. Om du har ett tillfälligt åtkomst pass (tryck) kan du använda det för att logga in. I det här läget kan du eventuellt blockeras från att fortsätta med något av följande villkor:

   - Om du inte har tillräckligt med autentiseringsmetoder på ditt konto för att få en stark autentiseringstoken, kan du inte fortsätta att lägga till ett konto.

   - Om du får meddelandet `You might be signing in from a location that is restricted by your admin` blockeras du och behöver en administratör för att avblockera dig i [säkerhets information](https://mysignins.microsoft.com/security-info).

   - Om du inte är blockerad för telefonin loggning med hjälp av autentiseraren av administratören kan du gå igenom enhets registreringen för att komma igång med lösen ords lös inloggning och Azure Multi-Factor Authentication (MFA).

1. Nu kan du uppmanas att skanna en QR-kod från din organisation för att konfigurera ett lokalt Multi-Factor Authentication-konto i appen. Du behöver bara göra detta om din organisation använder en lokal MFA-Server.

1. På din enhet trycker du på kontot och kontrollerar i hel skärms läge att ditt konto är korrekt och att det finns en associerad, sexsiffriga verifierings kod. För ytterligare säkerhet ändras verifierings koden var 30: e sekund som hindrar någon från att använda en kod flera gånger.

## <a name="sign-in-with-a-qr-code"></a>Logga in med en QR-kod

Om du vill lägga till ett konto genom att skanna en QR-kod gör du följande:

1. På datorn går du till sidan **ytterligare säkerhets verifiering** .

   >[!Note]
   >Om du inte ser sidan **ytterligare säkerhets verifiering** är det möjligt att administratören har aktiverat säkerhets information (för hands versionen). Om så är fallet bör du följa anvisningarna i avsnittet [Konfigurera säkerhets information för att använda en Authenticator-app](security-info-setup-auth-app.md) . Om så inte är fallet måste du kontakta din organisations supportavdelning om du behöver hjälp. Mer information om säkerhets information finns i [Konfigurera din säkerhets information från en inloggnings tolk](security-info-setup-signin.md).

1. Markera kryss rutan bredvid Authenticator-appen och välj sedan **Konfigurera**. Sidan **Konfigurera mobilapp** visas.

   ![Skärm som tillhandahåller en QR-kod](./media/user-help-auth-app-add-work-school-account/auth-app-barcode.png)

1. Öppna appen Microsoft Authenticator, Välj plus ikonen ![ och välj plus ikonen på antingen iOS-eller Android-enheter ](media/user-help-auth-app-add-work-school-account/plus-icon.png) och välj **Lägg till konto** och välj sedan **arbets-eller skol konto,** följt av **skanna en QR-kod**.
   Om du inte har ett konto som har kon figurer ATS i Authenticator-appen visas en stor blå knapp med texten **Lägg till konto**.

Om du inte uppmanas att använda kameran för att skanna en QR-kod, kontrollerar du i telefonens inställningar att appen autentiserare har åtkomst till telefon kameran.

## <a name="next-steps"></a>Nästa steg

- När du har lagt till dina konton i appen kan du logga in med Authenticator-appen på din enhet. Mer information finns i [Logga in med appen](user-help-auth-app-sign-in.md).

- För enheter som kör iOS kan du också säkerhetskopiera dina kontoautentiseringsuppgifter och relaterade appinställningar, till exempel ordningen på dina konton, i molnet. Mer information finns i [säkerhets kopiering och återställning med Microsoft Authenticator app](user-help-auth-app-backup-recovery.md).
