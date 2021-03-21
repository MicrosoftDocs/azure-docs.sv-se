---
title: Vanliga problem med kontots två Factor Authentication – Azure AD
description: Lösningar för några av de vanligaste två faktorerna verifierings problem och ditt arbets-eller skol konto.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 09/01/2020
ms.author: curtand
ms.reviewer: kexia
ms.custom: contperf-fy21q1
ms.openlocfilehash: 0cb5e3463eda5d77bbbdefe223954105fd125c71
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98033990"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>Vanliga problem med tvåstegsverifiering och ditt arbets- eller skolkonto

Det finns några vanliga vanliga verifierings problem på två faktorer som verkar oftare än vad som helst av oss. Vi har samlat ihop den här artikeln för att beskriva korrigeringar för de vanligaste problemen.

Din Azure Active Directory (Azure AD)-organisation kan aktivera tvåstegsverifiering för ditt konto. När tvåstegsverifiering är på kräver ditt konto inloggning en kombination av följande data:

- Ditt användar namn
- Ditt lösenord
- En mobil enhet eller telefon

Tvåstegsverifiering är säkrare än bara ett lösen ord, eftersom tvåstegsverifiering kräver något som du _känner_ till och något du _har_. Ingen hackare har din fysiska telefon.

>[!Important]
>Om du är administratör kan du hitta mer information om hur du konfigurerar och hanterar din Azure AD-miljö i [Azure AD-dokumentationen](../index.yml).

Det här innehållet kan hjälpa dig med ditt arbets-eller skol konto, vilket är det konto som du fått av din organisation (till exempel dritan@contoso.com ). Om du har problem med tvåstegsverifiering på en personlig Microsoft-konto, vilket är ett konto som du har konfigurerat för dig själv (till exempel danielle@outlook.com ), se [Aktivera eller inaktivera tvåstegsverifiering för din Microsoft-konto](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-dont-have-my-mobile-device-with-me"></a>Jag har inte min mobila enhet

Det sker. Du har lämnat din mobila enhet hemma och nu kan du inte använda din telefon för att kontrol lera vem du är. Du kanske tidigare har lagt till en alternativ metod för att logga in på ditt konto, t. ex. via din arbets telefon. I så fall kan du använda den här alternativa metoden nu. Om du aldrig har lagt till en alternativ verifierings metod kan du kontakta din organisations supportavdelning om du behöver hjälp.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Logga in på ditt arbets-eller skol konto med en annan verifieringsmetod

1. Logga in på ditt konto, men Välj länken för att logga in på **ett annat sätt** på sidan med **verifiering av två faktorer** .

    ![Ändra verifierings metod för inloggning](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    Om du inte ser länken **Logga in på ett annat sätt** , innebär det att du inte har ställt in några andra verifierings metoder. Du måste kontakta administratören om du behöver hjälp med att logga in på ditt konto.

2. Välj den alternativa verifierings metoden och fortsätt med processen med tvåstegsverifiering.

## <a name="i-cant-turn-two-factor-verification-off"></a>Det går inte att aktivera tvåstegsverifiering

- Om du använder tvåstegsverifiering med ett personligt konto för en Microsoft-tjänst, till exempel alain@outlook.com , kan du [Aktivera och inaktivera funktionen](https://account.live.com/proofs/Manage).

- Om du använder tvåstegsverifiering med ditt arbets-eller skol konto innebär det förmodligen att din organisation har valt att du måste använda den här extra säkerhetsfunktionen. Det finns inget sätt för dig att individuellt stänga av den.

Om du inte kan stänga av tvåstegsverifiering kan det också bero på de säkerhets inställningar som har tillämpats på organisations nivå. Mer information om säkerhets inställningar finns i [Vad är säkerhets inställningar?](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="my-device-was-lost-or-stolen"></a>Enheten har tappats bort eller blivit stulen

Om du har förlorat eller haft din mobila enhet stulen kan du göra något av följande:

- Logga in med en annan metod.
- Be din organisations supportavdelning att rensa dina inställningar.

Vi rekommenderar starkt att du låter din organisations supportavdelning att veta om din telefon har försvunnit eller blivit stulen. Supportavdelningen kan göra lämpliga uppdateringar av ditt konto. När inställningarna har rensats uppmanas du att [Registrera dig för tvåstegsverifiering](multi-factor-authentication-end-user-first-time.md) nästa gången du loggar in.

## <a name="im-not-receiving-the-verification-code-sent-to-my-mobile-device"></a>Jag får inte verifierings koden som skickas till min mobila enhet

Att inte ta emot verifierings koden är ett vanligt problem. Problemet är vanligt vis relaterat till din mobila enhet och dess inställningar. Här är några åtgärder som du kan prova.

Prova detta | Information om vägledning
--------- | ------------
Använd Microsoft Authenticator-appen eller verifierings koderna | Du får "du har nått vår gräns för verifierings anrop" eller "du har nått vår gräns för text verifierings koder" fel meddelanden under inloggningen. <br/><br/>Microsoft kan begränsa upprepade autentiseringsförsök som utförs av samma användare under en kort tids period. Den här begränsningen gäller inte för Microsoft Authenticator eller verifierings koden. Om du har nått dessa gränser kan du använda Authenticator-appen, verifierings koden eller försöka logga in igen om några minuter. <br/><br/> Vi har tyvärr problem med att verifiera ditt konto. fel meddelande visas under inloggningen. <br/><br/> Microsoft kan begränsa eller blockera röst-eller SMS-autentiseringsförsök som utförs av samma användare, telefonnummer eller organisation på grund av ett stort antal misslyckade röst-eller SMS-autentiseringsförsök. Om du får det här felet kan du prova en annan metod, till exempel autentiserare eller verifierings kod, eller kontakta administratören för support.
Starta om din mobila enhet | Ibland behöver enheten bara en uppdatering. När du startar om enheten avslutas alla bakgrunds processer och-tjänster. Omstarten stänger också av enhetens kärn komponenter. Alla tjänster och komponenter uppdateras när du startar om enheten.
Kontrol lera att din säkerhets information är korrekt | Kontrol lera att informationen om säkerhets verifierings metoden är korrekt, särskilt dina telefonnummer. Om du anger fel telefonnummer hamnar alla aviseringar till det felaktiga numret. Den användaren är lyckligt vis inte att kunna göra något med aviseringarna, men du kan inte heller logga in på ditt konto. Om du vill kontrol lera att informationen är korrekt läser du anvisningarna i artikeln [Hantera inställningar för metod för att hantera dina inställningar](multi-factor-authentication-end-user-manage-settings.md) .
Kontrol lera att dina meddelanden är aktiverade | Se till att dina meddelanden är aktiverade på den mobila enheten. Se till att följande meddelande lägen är tillåtna: <br/><br/> &bull; Telefonsamtal <br/> &bull; Din app för autentisering <br/> &bull; Din app för SMS-meddelanden <br/><br/> Se till att dessa lägen skapar en avisering som är _synlig_ på enheten.
Kontrol lera att du har en enhets signal och Internet anslutning | Se till att dina telefonsamtal och textmeddelanden går igenom till din mobila enhet. Låt en vän ringa dig och skicka ett SMS till dig för att se till att du får båda. Om du inte får samtal eller text kontrollerar du först för att se till att din mobila enhet är påslagen. Om enheten är påslagen, men du fortfarande inte får samtalet eller texten, finns det förmodligen ett problem med nätverket. Du måste prata med din Provider. Om du ofta har problem med signalen rekommenderar vi att du installerar och använder Microsoft Authenticator- [appen](user-help-auth-app-download-install.md) på din mobila enhet. Authenticator-appen kan generera slumpmässiga säkerhets koder för inloggning, utan att det krävs någon cell signal eller Internet anslutning.
Inaktivera Stör ej | Kontrol lera att du inte har aktiverat funktionen **stör ej** på den mobila enheten. När den här funktionen är aktive rad är aviseringar inte tillåtna för att varna dig på din mobila enhet. Information om hur du inaktiverar den här funktionen finns i hand boken för den mobila enheten.
Avblockera telefonnummer | I USA kommer röst samtal från Microsoft att komma från följande nummer: + 1 (866) 539 4191, + 1 (855) 330 8653 och + 1 (877) 668 6536.
Kontrol lera dina inställningar för batteri | Om du ställer in batteri optimeringen så att mindre ofta använda appar från återstående aktiva i bakgrunden påverkas förmodligen ditt meddelande system. Försök att stänga av batteri optimering för både din webbapp och din app för meddelanden. Försök sedan att logga in på ditt konto igen.
Inaktivera säkerhetsappar från tredje part | Vissa Phone Security-appar blockerar SMS och telefonsamtal från irriterande okända anropare. En säkerhetsappen kan förhindra att din telefon tar emot verifierings koden. Försök att inaktivera säkerhets program från tredje part på din telefon och begär sedan att en annan verifierings kod ska skickas.

## <a name="im-not-being-prompted-for-my-second-verification-information"></a>Jag uppmanas inte att ange min andra verifierings information

Du loggar in på ditt arbets-eller skol konto genom att använda ditt användar namn och lösen ord. Härnäst ska du uppmanas att ange ytterligare information om säkerhets verifieringen. Om du inte tillfrågas kanske du inte har konfigurerat din enhet än. Din mobila enhet måste konfigureras så att den fungerar med din speciella ytterligare säkerhets verifierings metod.

Du kanske inte har konfigurerat din enhet ännu. Din mobila enhet måste konfigureras för att fungera med din speciella ytterligare säkerhets verifierings metod. Anvisningar om hur du gör din mobila enhet tillgänglig för användning med din verifierings metod finns i [Hantera inställningar för verifierings metoden i två](multi-factor-authentication-end-user-manage-settings.md)steg. Om du vet att du inte har konfigurerat din enhet eller ditt konto än kan du följa stegen i artikeln [Konfigurera mitt konto för](multi-factor-authentication-end-user-first-time.md) tvåstegsverifiering.

## <a name="i-have-a-new-phone-number-and-i-want-to-add-it"></a>Jag har ett nytt telefonnummer och jag vill lägga till det

Om du har ett nytt telefonnummer måste du uppdatera information om säkerhets verifierings metoden. Detta gör att verifierings frågorna kan gå till rätt plats. Om du vill uppdatera din verifieringsmetod följer du stegen i artikeln **Lägg till eller ändra ditt telefonnummer** i artikeln [Hantera inställningar för metod](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) för att kontrol lera din enhet.

## <a name="i-have-a-new-mobile-device-and-i-want-to-add-it"></a>Jag har en ny mobil enhet och jag vill lägga till den

Om du har en ny mobil enhet måste du konfigurera den så att den fungerar med tvåstegsverifiering. Detta är en lösning med flera steg:

1. Konfigurera enheten så att den fungerar med ditt konto genom att följa stegen i artikeln [Konfigurera mitt konto för](multi-factor-authentication-end-user-first-time.md) tvåstegsverifiering.

1. Uppdatera ditt konto och din enhets information på sidan **ytterligare säkerhets verifiering** . Utför uppdateringen genom att ta bort den gamla enheten och lägga till den nya. Mer information finns i artikeln [Hantera inställnings metoden för tvåfaktorautentisering](multi-factor-authentication-end-user-manage-settings.md) .

Valfria steg:

- Installera Microsoft Authenticator-appen på din mobila enhet genom att följa stegen i artikeln [Hämta och installera Microsoft Authenticator app](user-help-auth-app-download-install.md) .

- Aktivera tvåstegsverifiering för dina betrodda enheter genom att följa stegen i artikeln **Aktivera två faktorers verifiering på en betrodd enhet** i avsnittet [Hantera inställningar för metod](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) med tvåstegsverifiering.

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Jag har problem med att logga in på min mobila enhet under resa

Det kan vara svårare att använda en metod som är relaterad till mobil enhet, t. ex. textmeddelanden, medan du är på en internationell plats. Det är också möjligt att din mobila enhet kan medföra att du debiteras för nätverks växling. I den här situationen rekommenderar vi att du använder Microsoft Authenticator-appen, med alternativet att ansluta till en Wi-Fi aktiv punkt. Mer information om hur du konfigurerar Microsoft Authenticator-appen på din mobila enhet finns i artikeln [Hämta och installera Microsoft Authenticator app](user-help-auth-app-download-install.md) .

## <a name="i-cant-get-my-app-passwords-to-work"></a>Jag kan inte få mina applösenord att fungera

Applösenord ersätter ditt normala lösen ord för äldre Skriv bords program som inte stöder tvåstegsverifiering. Kontrol lera först att du har angett lösen ordet korrekt. Om det inte löser problemet kan du försöka skapa ett nytt applösenord för appen. Gör detta genom att följa anvisningarna i artikeln **skapa och ta bort applösenord med hjälp av mina apps-portalen** i artikeln [Hantera applösenord för programbegäran](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) .

## <a name="i-cant-turn-off-two-factor-verification"></a>Det går inte att stänga av tvåstegsverifiering

Om du använder tvåstegsverifiering med ditt arbets-eller skol konto (till exempel alain@contoso.com ) innebär det förmodligen att organisationen har beslutat att du måste använda den här extra säkerhetsfunktionen. Eftersom din organisation har valt att du måste använda den här funktionen kan du inaktivera den på ett och samma sätt. Om du däremot använder tvåstegsverifiering med ett personligt konto, kan du till exempel alain@outlook.com Aktivera och inaktivera funktionen. Instruktioner för hur du styr tvåstegsverifiering för dina personliga konton finns i [Aktivera eller inaktivera tvåstegsverifiering på Microsoft-konto](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

Om du inte kan stänga av tvåstegsverifiering kan det också bero på de säkerhets inställningar som har tillämpats på organisations nivå. Mer information om säkerhets inställningar finns i [Vad är säkerhets inställningar?](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Jag hittade inget svar på mitt problem

Om du har provat de här stegen men fortfarande har problem, kan du kontakta din organisations supportavdelning om du behöver hjälp.

## <a name="related-articles"></a>Relaterade artiklar

- [Hantera dina inställningar för verifierings metoden med två faktorer](multi-factor-authentication-end-user-manage-settings.md)

- [Konfigurera mitt konto för tvåstegsverifiering](multi-factor-authentication-end-user-first-time.md)

- [Vanliga frågor och svar om Microsoft Authenticator-appen](user-help-auth-app-faq.md)
