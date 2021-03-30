---
title: 'Självstudie: Konfigurera PHS som säkerhets kopiering för AD FS i Azure AD Connect | Microsoft Docs'
description: Visar hur du aktiverar synkronisering av lösenordshash som en säkerhetskopia och för AD FS.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d9f59906c566d80344891c0796a85b0a4972e68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91313101"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>Självstudie: Konfigurera PHS som säkerhets kopiering för AD FS i Azure AD Connect

Följande självstudie vägleder dig genom att ställa in synkronisering av lösenordshash som en säkerhetskopiering och redundans för AD FS.  Det här dokumentet visar också hur du aktiverar synkronisering av lösenordshash som primär autentiseringsmetod om AD FS har misslyckats eller är otillgängligt.

>[!NOTE] 
>Även om dessa steg vanligt vis utförs under nöd situationer eller avbrott, rekommenderar vi att du testar de här stegen och kontrollerar dina procedurer innan ett avbrott uppstår.

>[!NOTE]
>I händelse av att du inte har åtkomst till Azure AD Connect Server eller om servern inte har åtkomst till Internet, kan du kontakta [Microsoft Support](https://support.microsoft.com/en-us/contactus/) för att hjälpa till med ändringarna i Azure AD-sidan.

## <a name="prerequisites"></a>Förutsättningar
Den här självstudien bygger på [självstudien: federera en enda AD-skogs miljö till molnet](tutorial-federation.md) och är ett krav som krävs innan du provar den här kursen.  Om du inte har slutfört den här kursen kan du göra det innan du provar att utföra stegen i det här dokumentet.

>[!IMPORTANT]
>Innan du växlar till PHS bör du skapa en säkerhets kopia av din AD FSs miljö.  Detta kan göras med hjälp av [AD FS Rapid Restore-verktyget](/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool#how-to-use-the-tool).

## <a name="enable-phs-in-azure-ad-connect"></a>Aktivera PHS i Azure AD Connect
Det första steget, nu när vi har en Azure AD Connect-miljö som använder federation, är att aktivera lösenordshashsynkronisering och låta Azure AD Connect synkronisera hashvärdena.

Gör följande:

1.  Dubbelklicka på ikonen för Azure AD Connect som skapades på skrivbordet
2.  Klicka på **Konfigurera**.
3.  På sidan Ytterligare uppgifter väljer du **Anpassa synkroniseringsalternativ** och klickar sedan på **Nästa**.
4.  Ange användarnamnet och lösenordet för din globala administratör.  Det här kontot skapades [här](tutorial-federation.md#create-a-global-administrator-in-azure-ad) i föregående självstudie.
5.  På skärmen **Anslut dina kataloger** klickar du på **Nästa**.
6.  På skärmen **Domän- och organisationsenhetsfiltrering** klickar du på **Nästa**.
7.  På skärmen **Valfria funktioner** kan du markera **Synkronisering av lösenordshash** och klicka på **Nästa**.
![Välj](media/tutorial-phs-backup/backup1.png)</br>
8.  Klicka på **Konfigurera** på skärmen **redo att konfigurera** .
9.  När konfigurationen är klar klickar du på **Avsluta**.
10. Klart!  Du är klar.  Synkronisering av lösenordshash utförs nu och kan användas som en säkerhetskopia om AD FS blir otillgänglig.

## <a name="switch-to-password-hash-synchronization"></a>Växla till synkronisering av lösenordshash
Nu ska vi visa hur du byter till synkronisering av lösenordshash. Innan du börjar bör du fundera på under vilka villkor du ska göra växlingen. Växla inte av tillfälliga skäl, som ett nätverksavbrott, ett mindre AD FS-problem eller ett problem som påverkar en delmängd av dina användare. Om du vill växla eftersom det tar för lång tid att lösa problemet kan du göra följande:

> [!IMPORTANT]
> Tänk på att det tar lite tid innan lösen ordets hash-värden synkroniseras med Azure AD.  Det innebär att det kan ta upp till 3 timmar innan synkroniseringarna har slutförts och innan du kan börja autentisera med hjälp av lösen ordets hash-värden.

1. Dubbelklicka på ikonen för Azure AD Connect som skapades på skrivbordet
2.  Klicka på **Konfigurera**.
3.  Välj **Ändra användarinloggning** och klicka på **Nästa**.
![Ändra](media/tutorial-phs-backup/backup2.png)</br>
4.  Ange användarnamnet och lösenordet för din globala administratör.  Det här kontot skapades [här](tutorial-federation.md#create-a-global-administrator-in-azure-ad) i föregående självstudie.
5.  På skärmen **Användarinloggning** väljer du **Synkronisering av lösenordshash** och markerar rutan **Konvertera inte användarkonton**.  
6.  Låt standardinställningen **Aktivera enkel inloggning** vara kvar och klicka på **Nästa**.
7.  På skärmen **Aktivera enkel inloggning** klickar ni på **Nästa**.
8.  Klicka på **Konfigurera** på skärmen **Klart att konfigurera**.
9.  När konfigurationen är klar klickar du på **Avsluta**.
10. Användare kan nu använda sina lösenord för att logga in på Azure och Azure-tjänster.

## <a name="test-signing-in-with-one-of-our-users"></a>Testa att logga in med någon av våra användare

1. Gå till [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Logga in med ett användarkonto som har skapats i vår nya klient.  Du måste logga in med följande format: (user@domain.onmicrosoft.com). Använd samma lösenord som användaren använder för att logga in lokalt.</br>
   ![Skärm bild som visar ett klart meddelande när du testar inloggningen. ](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="switch-back-to-federation"></a>Växla tillbaka till federationen
Nu visar vi dig hur du växlar tillbaka till federationen.  Gör detta genom att göra följande:

1.  Dubbelklicka på ikonen för Azure AD Connect som skapades på skrivbordet
2.  Klicka på **Konfigurera**.
3.  Välj **Ändra användarinloggning** och klicka på **Nästa**.
4.  Ange användarnamnet och lösenordet för din globala administratör.  Detta är det konto som skapades [här](tutorial-federation.md#create-a-global-administrator-in-azure-ad) i föregående självstudie.
5.  På **inloggnings** skärmen för användare väljer du **Federation med AD FS** och klickar på **Nästa**.  
6. På sidan med inloggningsuppgifterna för domänadministratören anger du användarnamnet contoso\Administrator och lösenordet och klickar på **Nästa.**
7. På sidan AD FS server grupp klickar du på **Nästa**.
8. På skärmen **Azure AD-domän** väljer du domänen i list rutan och klickar på **Nästa**.
9. Klicka på **Konfigurera** på skärmen **Klart att konfigurera**.
10. När konfigurationen är klar klickar du på **Nästa**.
![Konfigurera](media/tutorial-phs-backup/backup4.png)</br>
11. På skärmen **Verifiera Federations anslutning** klickar du på **Verifiera**.  Du kan behöva konfigurera DNS-poster (Lägg till A-och AAAA-poster) för att det ska kunna slutföras.
![Skärm bild som visar skärmen verifiera Federations anslutningar och knappen verifiera.](media/tutorial-phs-backup/backup5.png)</br>
12. Klicka på **Avsluta**.

## <a name="reset-the-ad-fs-and-azure-trust"></a>Återställa AD FS och Azure-förtroende
Nu måste vi återställa förtroendet mellan AD FS och Azure.

1.  Dubbelklicka på ikonen för Azure AD Connect som skapades på skrivbordet
2.  Klicka på **Konfigurera**.
3.  Välj **Hantera Federation** och klicka på **Nästa**.
4.  Välj **Återställ Azure AD-förtroende** och klicka på **Nästa**.
![Återställning](media/tutorial-phs-backup/backup6.png)</br>
5.  På skärmen **Anslut till Azure AD** anger du användar namn och lösen ord för din globala administratör.
6.  På skärmen **Anslut till AD FS** anger du användar namn och lösen ord för contoso\Administrator och klickar på **Nästa.**
7.  I fönstret **certifikat** klickar du på **Nästa**.

## <a name="test-signing-in-with-a-user"></a>Testa att logga in med en användare

1.  Gå till [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Logga in med ett användarkonto som har skapats i vår nya klientorganisation.  Du behöver logga in med följande format: (user@domain.onmicrosoft.com). Använd samma lösenord som användaren använder för att logga in lokalt.
![Verifiera](media/tutorial-password-hash-sync/verify1.png)

Du har nu har installerat en hybrididentitetsmiljö som du kan använda för att testa och bekanta dig med allt som Azure har att erbjuda.

## <a name="next-steps"></a>Nästa steg


- [Maskin vara och krav](how-to-connect-install-prerequisites.md) 
- [Standardinställningar](how-to-connect-install-express.md)
- [Hash-synkronisering av lösen ord](how-to-connect-password-hash-synchronization.md)