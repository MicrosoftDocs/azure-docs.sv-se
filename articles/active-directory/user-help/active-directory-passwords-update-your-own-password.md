---
title: Återställ ditt lösen ord med hjälp av säkerhets information – Azure Active Directory | Microsoft Docs
description: Återställa ditt eget lösen ord om du glömmer det, med hjälp av din säkerhets information och tvåstegsverifiering.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 09/10/2020
ms.author: curtand
ms.openlocfilehash: c47d573dab3e72a956f6f0b9d09a1dafadf0225c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91827761"
---
# <a name="reset-your-work-or-school-password-using-security-info"></a>Återställa ditt arbets-eller skol lösen ord med hjälp av säkerhets information

Om du har glömt lösen ordet till ditt arbets-eller skol konto, fick du aldrig ett lösen ord från din organisation eller har låsts av ditt konto, kan du använda din säkerhets information och din mobila enhet för att återställa lösen ordet till ditt arbets-eller skol konto. Administratören måste aktivera den här funktionen så att du kan ställa in din information och [återställa ditt eget lösen ord](./active-directory-passwords-reset-register.md).

Om du känner till ditt lösen ord och vill ändra det kan du läsa avsnittet [ändra lösen ords steg](#how-to-change-your-password) i den här artikeln.

>[!Important]
>Den här artikeln är avsedd för användare som försöker använda återställning av lösen ord för ett bortglömt eller okänt arbets-eller skol konto. Om du är administratör för att få information om hur du aktiverar lösen ords återställning via självbetjäning för dina anställda eller andra användare kan du läsa mer i [Distribuera Azure AD självbetjäning återställning av lösen ord och andra artiklar](../authentication/howto-sspr-deployment.md).

## <a name="how-to-reset-or-unlock-your-password-for-a-work-or-school-account"></a>Återställa eller låsa upp lösen ordet för ett arbets-eller skol konto

Om du inte kan komma åt ditt Azure Active Directory-konto (Azure AD) kan det bero på något av följande:

- Ditt lösen ord fungerar inte och du vill återställa det, eller

- Du känner till ditt lösen ord, men ditt konto är utelåst och du måste låsa upp det.

### <a name="to-reset-your-password-and-get-back-into-your-account"></a>Så här återställer du ditt lösen ord och återgår till ditt konto

1. Välj **glömt mitt lösen ord** på skärmen **Ange lösen ord** .

2. I fönstret **kom tillbaka till ditt konto** skriver du **användar-ID:** t för arbets-eller skol användaren (till exempel din e-postadress), visar att du inte är en robot genom att ange de tecken som visas på skärmen och sedan välja **Nästa**.

   ![Gå tillbaka till konto skärmen](media/security-info/security-info-back-into-acct.png)

   >[!NOTE]
   >Om administratören inte har aktiverat möjligheten att återställa ditt eget lösen ord, ser du en **Kontakta din administratörs** länk i stället för att **gå tillbaka till konto** skärmen. Med den här länken kan du kontakta administratören om du vill återställa lösen ordet via e-post eller en webb Portal.

3. Välj en av följande metoder för att verifiera din identitet och ändra lösen ordet. Beroende på hur din administratör har konfigurerat din organisation kan du behöva gå igenom den här processen en andra gång och lägga till information för ett andra verifierings steg.

    ![Kom tillbaka till ditt konto, verifierings steg #1](media/security-info/security-info-back-into-acct2.png)

    >[!NOTE]
    >Beroende på hur din administratör har konfigurerat din organisation kanske vissa av dessa verifierings alternativ inte är tillgängliga. Du måste ha konfigurerat din mobila enhet tidigare för verifiering med minst en av dessa metoder.<br><br>Dessutom kan det nya lösen ordet behöva uppfylla vissa styrke krav. Starka lösen ord har vanligt vis 8 till 16 tecken, inklusive versaler och gemener, minst en siffra och minst ett specialtecken.

- **Återställ ditt lösen ord med hjälp av en e-postadress.** Skickar ett e-postmeddelande till den e-postadress som du tidigare har konfigurerat i tvåstegsverifiering eller säkerhets information. Om din administratör har aktiverat säkerhets informationen kan du hitta mer information om hur du konfigurerar en e-postadress i artikeln [Konfigurera säkerhets information för att använda e-post (för hands version)](security-info-setup-email.md) . Om du ännu inte använder säkerhets information kan du hitta mer information om hur du konfigurerar en e-postadress i artikeln [Konfigurera mitt konto för](multi-factor-authentication-end-user-first-time.md) tvåstegsverifiering. 

    1. Välj **e-posta min alternativa e-postadress** och välj sedan **e-post**.

    2. Skriv verifierings koden från e-postmeddelandet i rutan och välj sedan **Nästa**.

    3. Skriv och bekräfta ditt nya lösen ord och välj sedan **Slutför**.

- **Återställ ditt lösen ord med ett textmeddelande.** Skickar ett SMS till det telefonnummer som du tidigare angav i säkerhets information. Om din administratör har aktiverat säkerhets informationen kan du hitta mer information om hur du konfigurerar textmeddelanden i artikeln [Konfigurera säkerhets information för att använda text meddelanden (för hands version)](security-info-setup-text-msg.md) . Om du ännu inte använder säkerhets information kan du hitta mer information om hur du konfigurerar textmeddelanden i artikeln [Konfigurera mitt konto för](multi-factor-authentication-end-user-first-time.md) tvåstegsverifiering.

    1. Välj **text min mobil telefon**, skriv ditt telefonnummer och välj sedan **text**.

    2. Skriv verifierings koden från textmeddelandet i rutan och välj sedan **Nästa**.

    3. Skriv och bekräfta ditt nya lösen ord och välj sedan **Slutför**.

- **Återställ ditt lösen ord med hjälp av ett telefonnummer.** Placerar ett automatiskt röst samtal till det telefonnummer som du tidigare angav i säkerhets information. Om din administratör har aktiverat säkerhets informationen kan du hitta mer information om hur du konfigurerar ett telefonnummer i artikeln [Konfigurera säkerhets information för att använda ett telefonsamtal (för hands version)](security-info-setup-phone-number.md) . Om du ännu inte använder säkerhets information kan du hitta mer information om hur du konfigurerar ett telefonnummer i artikeln [Konfigurera mitt konto för](multi-factor-authentication-end-user-first-time.md) tvåstegsverifiering.

    1. Välj **Ring min mobil telefon**, ange ditt telefonnummer och välj sedan **anropa**.

    2. Besvara telefonsamtalet och följ anvisningarna för att verifiera din identitet och välj sedan **Nästa**.

    3. Skriv och bekräfta ditt nya lösen ord och välj sedan **Slutför**.

- **Återställ ditt lösen ord med hjälp av säkerhets frågor.** Visar en lista med säkerhets frågor som du ställer in i säkerhets information. Om din administratör har aktiverat säkerhets informationen kan du hitta mer information om hur du konfigurerar dina säkerhets frågor i artikeln [Konfigurera säkerhets information för att använda fördefinierade säkerhets frågor (för hands version)](security-info-setup-questions.md) . Om du ännu inte använder säkerhets information kan du hitta mer information om att ställa in säkerhets frågor i artikeln [Konfigurera mitt konto för](multi-factor-authentication-end-user-first-time.md) tvåstegsverifiering.

    1. Välj **svara på mina säkerhets frågor**, svara på frågorna och välj sedan **Nästa**.

    2. Skriv och bekräfta ditt nya lösen ord och välj sedan **Slutför**.

- **Återställ ditt lösen ord med hjälp av ett meddelande från din Authenticator-app.** Skickar ett meddelande om godkännande till Authenticator-appen. Om din administratör har aktiverat säkerhets informationen kan du hitta mer information om hur du konfigurerar en Authenticator-app för att skicka ett meddelande i artikeln [Konfigurera säkerhets information för att använda en autentiseringstjänst (förhands granskning)](security-info-setup-auth-app.md) . Om du ännu inte använder säkerhets information kan du hitta mer information om hur du konfigurerar en Authenticator-app för att skicka ett meddelande i artikeln [Konfigurera mitt konto för](multi-factor-authentication-end-user-first-time.md) tvåstegsverifiering.

    1. Välj **Godkänn ett meddelande i min Authenticator-app** och välj sedan **Skicka meddelande**.

    2. Godkänn inloggningen från din Authenticator-app.

    3. Skriv och bekräfta ditt nya lösen ord och välj sedan **Slutför**.

- **Återställ ditt lösen ord med hjälp av en kod från din Authenticator-app.** Accepterar en slumpmässig kod från din webbapp. Om din administratör har aktiverat säkerhets informationen kan du hitta mer information om hur du konfigurerar en Authenticator-app för att tillhandahålla en kod i artikeln [Konfigurera säkerhets information för att använda en autentiseringstjänst (förhands granskning)](security-info-setup-auth-app.md) . Om du ännu inte använder säkerhets information kan du hitta mer information om hur du konfigurerar en Authenticator-app för att tillhandahålla en kod i artikeln [Konfigurera mitt konto för tvåstegsverifiering](multi-factor-authentication-end-user-first-time.md) .

  1. Välj **Ange en kod från min Authenticator-app** och välj sedan **Skicka meddelande**.

  2. Öppna din Authenticator-app, skriv verifierings koden för ditt konto i rutan och välj sedan **Nästa**.

  3. Skriv och bekräfta ditt nya lösen ord och välj sedan **Slutför**.

  4. När du får meddelandet att ditt lösen ord har återställts kan du logga in på ditt konto med ditt nya lösen ord.

     Om du fortfarande inte kan komma åt ditt konto bör du kontakta din organisations administratör för mer hjälp.

När du har återställt lösen ordet kan du få en bekräftelse via e-post som kommer från ett konto, till exempel Microsoft på uppdrag av \<*your_organization*> . Om du får ett liknande e-postmeddelande, men du inte nyligen återställt ditt lösen ord, måste du kontakta din organisations administratör omedelbart.

## <a name="how-to-change-your-password"></a>Så ändrar du ditt lösenord

Om du bara vill ändra ditt lösen ord kan du göra det via Office 365-portalen, portalen för Mina appar eller inloggnings sidan för Windows 10.

### <a name="to-change-your-password-using-the-office-365-portal"></a>Ändra lösen ordet med hjälp av Office 365-portalen

Använd den här metoden om du normalt kommer åt dina appar via Office-portalen:

1. Logga in på ditt [Office 365-konto](https://portal.office.com)med ditt befintliga lösen ord.

2. Välj din profil längst upp till höger och välj sedan **Visa konto**.

3. Välj **säkerhets & sekretess**  >  **lösen ord**.

4. Skriv ditt gamla lösen ord, skapa och bekräfta ditt nya lösen ord och välj sedan **Skicka**.

### <a name="to-change-your-password-from-the-my-apps-portal"></a>Ändra ditt lösen ord från portalen Mina appar

Använd den här metoden om du normalt kommer åt dina appar från portalen Mina appar:

1. Logga in på [Mina Apps-portalen](https://myapps.microsoft.com/)med ditt befintliga lösen ord.

2. Välj din profil längst upp till höger och välj sedan **profil**.

3. Välj **ändra lösen ord**.

4. Skriv ditt gamla lösen ord, skapa och bekräfta ditt nya lösen ord och välj sedan **Skicka**.

### <a name="to-change-your-password-at-windows-sign-in"></a>Ändra lösen ordet på Windows-inloggning

Om din administratör har aktiverat funktionen kan du se en länk för att **återställa lösen ordet** på inloggnings skärmen för Windows 7, Windows 8, Windows 8,1 eller Windows 10.

1. Välj länken **Återställ lösen ord** för att starta processen för lösen ords återställning utan att behöva använda den normala webbaserade upplevelsen.

2. Bekräfta ditt användar-ID och välj **Nästa**.

3. Välj och bekräfta en kontakt metod för verifiering. Om det behövs väljer du ett andra verifierings alternativ som skiljer sig från ditt föregående och fyller i nödvändig information.

4. Skriv och bekräfta ditt nya lösen ord på sidan **skapa ett nytt lösen ord** och välj sedan **Nästa**.

    Starka lösen ord har vanligt vis 8 till 16 tecken, inklusive versaler och gemener, minst en siffra och minst ett specialtecken.

5. När du får meddelandet att ditt lösen ord har återställts kan du välja **Slutför**.

    Om du fortfarande inte kan komma åt ditt konto bör du kontakta din organisations administratör för mer hjälp.

## <a name="common-problems-and-their-solutions"></a>Vanliga problem och lösningar

Här följer några vanliga fel och deras lösningar:

|Problem|Beskrivning|Lösning|
| --- | --- | --- |
|Jag får ett fel när jag försöker ändra mitt lösen ord. |Ditt lösen ord har ett ord, en fras eller ett mönster som gör ditt lösen ord lätt att gissa.| Försök igen med ett starkare lösen ord.|
|När jag har angett mitt användar-ID går jag till en sida med texten "kontakta administratören".|Microsoft har fastställt att lösen ordet för ditt användar konto hanteras av administratören i en lokal miljö. Det innebär att du inte kan återställa ditt lösen ord från länken "det går inte att komma åt ditt konto". |Kontakta administratören om du vill ha mer hjälp.|
|När jag har angett mitt användar-ID visas ett fel meddelande om att ditt konto inte har Aktiver ATS för lösen ords återställning.|Administratören har inte konfigurerat ditt konto så att du kan återställa ditt eget lösen ord.|Administratören har inte aktiverat lösen ords återställning för din organisation från länken "det går inte att komma åt ditt konto" eller har inte licensierat dig till att använda funktionen.<br><br> Om du vill återställa lösen ordet måste du välja länken "kontakta en administratör" för att skicka ett e-postmeddelande till företagets administratör och meddela dem att du vill återställa lösen ordet.|
|När jag har angett mitt användar-ID visas ett fel meddelande om att vi inte kunde verifiera ditt konto.|Det gick inte att verifiera din konto information i inloggnings processen.|Det finns två orsaker till att du kan se det här meddelandet.<br><br>1. din administratör har aktiverat lösen ords återställning för din organisation, men du har inte registrerat dig för att använda tjänsten. Registrera dig för lösen ords återställning se en av följande artiklar, baserat på din verifieringsmetod: [Konfigurera säkerhets information för att använda en Authenticator-app (för hands version)](security-info-setup-auth-app.md), [Konfigurera säkerhets information för att använda ett telefonsamtal (](security-info-setup-phone-number.md)förhands granskning), konfigurera säkerhets information för att använda [textmeddelanden](security-info-setup-text-msg.md)(förhands granskning), konfigurera säkerhets information för att [använda](security-info-setup-questions.md) [e-post (för](security-info-setup-email.md)hands version)<br><br>2. administratören har inte aktiverat lösen ords återställning för din organisation. I så fall måste du välja länken "kontakta en administratör" för att skicka ett e-postmeddelande till administratören och be att återställa lösen ordet.|

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om säkerhets information i [översikts artikeln om säkerhets information (för hands version)](./security-info-setup-signin.md) .

- Om du försöker komma tillbaka till ett personligt konto, till exempel Xbox, hotmail.com eller outlook.com, kan du prova förslagen i [när du inte kan logga in på din Microsoft-konto-artikel](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
