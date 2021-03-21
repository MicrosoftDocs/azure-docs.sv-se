---
title: Vanliga frågor och svar om Azure AD Multi-Factor Authentication – Azure Active Directory
description: Vanliga frågor och svar om Azure AD Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/14/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: a58fbf874339bf287ce1af62e1edb39ec6227d99
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257110"
---
# <a name="frequently-asked-questions-about-azure-ad-multi-factor-authentication"></a>Vanliga frågor om Azure AD Multi-Factor Authentication

I det här avsnittet besvaras vanliga frågor om Azure AD Multi-Factor Authentication och tjänsten Multi-Factor Authentication. Den är uppdelad i frågor om tjänsten i allmänhet, fakturerings modeller, användar upplevelser och fel sökning.

> [!IMPORTANT]
> Från och med den 1 juli 2019 kommer Microsoft inte längre att erbjuda MFA Server för nya distributioner. Nya kunder som vill kräva Multi-Factor Authentication från sina användare bör använda molnbaserad Azure AD-Multi-Factor Authentication. Befintliga kunder som har aktiverat MFA Server tidigare än 1 juli kommer att kunna ladda ned den senaste versionen, framtida uppdateringar och generera autentiseringsuppgifter för aktivering som vanligt.
>
> Informationen som delas nedan för Azure-Multi-Factor Authentication-server gäller bara för användare som redan har MFA-servern igång.
>
> Konsumtions-baserad licens är inte längre tillgänglig för nya kunder från och med den 1 september 2018.
> Från och med 1 september 2018 kan nya auth providers inte längre skapas. Befintliga auth-providers kan fortsätta att användas och uppdateras. Multi-Factor Authentication kommer att fortsätta vara en tillgänglig funktion i Azure AD Premium licenser.

## <a name="general"></a>Allmänt

* [Hur hanterar Azure Multi-Factor Authentication-server användar data?](#how-does-azure-multi-factor-authentication-server-handle-user-data)
* [Vilka SMS-koder används för att skicka SMS-meddelanden till mina användare?](#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

### <a name="how-does-azure-multi-factor-authentication-server-handle-user-data"></a>Hur hanterar Azure Multi-Factor Authentication-server användar data?

Med Multi-Factor Authentication-server lagras användar data endast på lokala servrar. Inga beständiga användardata lagras i molnet. När användaren utför tvåstegsverifiering kan Multi-Factor Authentication-server skicka data till Azure AD Multi-Factor Authentication moln tjänsten för autentisering. Kommunikation mellan Multi-Factor Authentication-server och Multi-Factor Authentication moln tjänsten använder Secure Sockets Layer (SSL) eller Transport Layer Security (TLS) över port 443 utgående.

När autentiseringsbegäranden skickas till moln tjänsten samlas data in för autentiserings-och användnings rapporter. Följande data fält ingår i två stegs verifierings loggar:

* **Unikt ID** (antingen användar namn eller lokalt Multi-Factor Authentication-Server-ID)
* För- **och efter namn** (valfritt)
* **E-postadress** (valfritt)
* **Telefonnummer** (när du använder ett röst samtal eller SMS-autentisering)
* **Enhets-token** (när du använder autentisering med mobil program)
* **Autentiseringsläge**
* **Autentiseringsresultat**
* **Multi-Factor Authentication-server namn**
* **Multi-Factor Authentication-server IP**
* **Klientens IP-adress** (om tillgänglig)

De valfria fälten kan konfigureras i Multi-Factor Authentication-server.

Verifierings resultatet (lyckades eller nekas), och orsaken till att den nekades, lagras med autentiseringsdata. Dessa data är tillgängliga i autentiserings-och användnings rapporter.

Mer information finns i [data placering och kund information för Azure AD Multi-Factor Authentication](concept-mfa-data-residency.md).

### <a name="what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users"></a>Vilka SMS-koder används för att skicka SMS-meddelanden till mina användare?

I USA använder vi följande SMS-kort koder:

* *97671*
* *69829*
* *51789*
* *99399*

I Kanada använder vi följande SMS-kort koder:

* *759731*
* *673801*

Det finns ingen garanti för konsekvent SMS-eller Voice-baserad Multi-Factor Authentication meddelande leverans med samma nummer. Vi kan när som helst lägga till eller ta bort korta koder när vi gör väg justeringar för att förbättra SMS-leveranser.

Vi har inte stöd för korta koder för länder eller regioner utöver USA och Kanada.

## <a name="billing"></a>Fakturering

De flesta fakturerings frågor kan besvaras genom att referera till antingen [sidan Multi-Factor Authentication priser](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) eller dokumentationen för [Azure AD Multi-Factor Authentication-versioner och förbruknings planer](concept-mfa-licensing.md).

* [Debiteras min organisation för att skicka telefonsamtal och textmeddelanden som används för autentisering?](#is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication)
* [Debiteras fakturerings modellen per användare för alla aktiverade användare eller bara de som utförde tvåstegsverifiering?](#does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification)
* [Hur fungerar Multi-Factor Authentication fakturering?](#how-does-multi-factor-authentication-billing-work)
* [Finns det en kostnads fri version av Azure AD Multi-Factor Authentication?](#is-there-a-free-version-of-azure-ad-multi-factor-authentication)
* [Kan min organisation växla mellan användnings modeller per användare och per autentisering när som helst?](#can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time)
* [Kan min organisation växla mellan förbruknings faktureringar och prenumerationer (en licens baserad modell) när som helst?](#can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time)
* [Måste min organisation använda och synkronisera identiteter för att använda Azure AD Multi-Factor Authentication?](#does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-ad-multi-factor-authentication)

### <a name="is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication"></a>Debiteras min organisation för att skicka telefonsamtal och textmeddelanden som används för autentisering?

Nej, du debiteras inte för enskilda telefonsamtal eller SMS-meddelanden som skickas till användare via Azure AD Multi-Factor Authentication. Om du använder en MFA-Provider per autentisering debiteras du för varje autentisering, men inte för den metod som används.

Dina användare kan debiteras för telefonsamtal eller textmeddelanden som de får, enligt deras personliga telefon tjänst.

### <a name="does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification"></a>Debiteras fakturerings modellen per användare för alla aktiverade användare eller bara de som utförde tvåstegsverifiering?

Faktureringen baseras på antalet användare som kon figurer ATS för att använda Multi-Factor Authentication, oavsett om de utförde tvåstegsverifiering i månaden.

### <a name="how-does-multi-factor-authentication-billing-work"></a>Hur fungerar Multi-Factor Authentication fakturering?

När du skapar en MFA-Provider per användare eller per autentisering faktureras din organisations Azure-prenumeration varje månad baserat på användning. Den här fakturerings modellen liknar hur Azure-fakturor används för att använda virtuella datorer och Web Apps.

När du köper en prenumeration för Azure AD Multi-Factor Authentication betalar din organisation endast den årliga licens avgiften för varje användare. MFA-licenser och Microsoft 365, Azure AD Premium eller Enterprise Mobility + Security paket faktureras på det här sättet.

Mer information finns i [så här skaffar du Azure AD-Multi-Factor Authentication](concept-mfa-licensing.md).

### <a name="is-there-a-free-version-of-azure-ad-multi-factor-authentication"></a>Finns det en kostnads fri version av Azure AD Multi-Factor Authentication?

Säkerhets inställningar kan aktive ras på Azure AD Free nivå. Med säkerhets inställningar har alla användare Aktiver ATS för Multi-Factor Authentication med hjälp av Microsoft Authenticator-appen. Det finns ingen möjlighet att använda textmeddelanden eller telefon verifiering med säkerhets inställningar, bara Microsoft Authenticator-appen.

Mer information finns i [Vad är säkerhets inställningar?](../fundamentals/concept-fundamentals-security-defaults.md)

### <a name="can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time"></a>Kan min organisation växla mellan användnings modeller per användare och per autentisering när som helst?

Om din organisation köper MFA som en fristående tjänst med förbruknings-baserad fakturering väljer du en fakturerings modell när du skapar en MFA-Provider. Du kan inte ändra fakturerings modellen när en MFA-provider har skapats. 

Om MFA-providern *inte* är länkad till en Azure AD-klient, eller om du länkar den nya MFA-providern till en annan Azure AD-klient, så överförs inte användar inställningar och konfigurations alternativ. Dessutom måste befintliga Azure MFA-servrar återaktiveras med hjälp av aktiveringsautentiseringsuppgifter som skapas via den nya MFA-providern. Återaktiveringen av MFA-servrarna för att koppla dem till den nya MFA-providern påverkar inte autentiseringen via telefonsamtal eller SMS, men mobilappsaviseringarna slutar fungera för alla användare tills de återaktiverar mobilappen.

Lär dig mer om MFA-leverantörer i [komma igång med en Azure Multi-Factor Auth-provider](concept-mfa-authprovider.md).

### <a name="can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time"></a>Kan min organisation växla mellan förbruknings faktureringar och prenumerationer (en licens baserad modell) när som helst?

I vissa fall Ja.

Om din katalog har en Azure Multi-Factor Authentication *-Provider per användare* kan du lägga till MFA-licenser. Användare med licenser räknas inte i förbruknings åtgången per användare. Användare utan licenser kan fortfarande aktive ras för MFA via MFA-providern. Om du köper och tilldelar licenser för alla användare som kon figurer ATS att använda Multi-Factor Authentication kan du ta bort Azure Multi-Factor Authentication-providern. Du kan alltid skapa en annan MFA-Provider per användare om du har fler användare än licenser i framtiden.

Om din katalog har en Azure Multi-Factor Authentication *-Provider per autentisering* , faktureras du alltid för varje autentisering, så länge MFA-providern är länkad till din prenumeration. Du kan tilldela MFA-licenser till användare, men du kommer fortfarande att faktureras för varje tvåstegsverifiering, oavsett om det kommer från någon med en MFA-licens som tilldelats eller inte.

### <a name="does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-ad-multi-factor-authentication"></a>Måste min organisation använda och synkronisera identiteter för att använda Azure AD Multi-Factor Authentication?

Om din organisation använder en förbruknings-baserad fakturerings modell är Azure Active Directory valfritt, men krävs inte. Om MFA-providern inte är länkad till en Azure AD-klient kan du bara Distribuera Azure Multi-Factor Authentication-server lokalt.

Azure Active Directory krävs för licens modellen eftersom licenser läggs till i Azure AD-klienten när du köper och tilldelar dem till användare i katalogen.

## <a name="manage-and-support-user-accounts"></a>Hantera och stöd användarkonton

* [Vad ska jag säga till att mina användare gör om de inte får något svar på sin telefon?](#what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone)
* [Vad ska jag göra om en av mina användare inte kan komma åt sitt konto?](#what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account)
* [Vad ska jag göra om en av mina användare förlorar en telefon som använder applösenord?](#what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords)
* [Vad händer om en användare inte kan logga in på icke-webbläsarbaserade appar?](#what-if-a-user-cant-sign-in-to-non-browser-apps)
* [Mina användare säger att de ibland inte tar emot textmeddelandet eller att verifierings tiden är slut.](#my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out)
* [Kan jag ändra hur lång tid mina användare måste ange verifierings koden från ett SMS innan systemet nått tids gränsen?](#can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out)
* [Kan jag använda maskinvaru-token med Azure Multi-Factor Authentication-server?](#can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server)
* [Kan jag använda Azure Multi-Factor Authentication-server för att skydda Terminal Services?](#can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services)
* [Jag konfigurerade anroparens ID i MFA Server, men mina användare får fortfarande Multi-Factor Authentication anrop från en anonym anropare.](#i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller)
* [Varför uppmanas mina användare att registrera sin säkerhets information?](#why-are-my-users-being-prompted-to-register-their-security-information)

### <a name="what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone"></a>Vad ska jag säga till att mina användare gör om de inte får något svar på sin telefon?

Låt användarna försöka upp till fem gånger om 5 minuter för att få ett telefonsamtal eller SMS för autentisering. Microsoft använder flera leverantörer för att leverera samtal och SMS-meddelanden. Om den här metoden inte fungerar öppnar du ett support ärende för fel sökning.

Säkerhetsappar från tredje part kan också blockera verifierings kodens textmeddelande eller telefonsamtal. Om du använder en säkerhets app från tredje part kan du försöka inaktivera skyddet och sedan begära en annan MFA-verifierings kod.

Om stegen ovan inte fungerar kontrollerar du om användarna har kon figurer ATS för mer än en verifieringsmetod. Försök att logga in igen, men Välj en annan verifieringsmetod på inloggnings sidan.

Mer information finns i [fel söknings guiden för slutanvändare](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

### <a name="what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account"></a>Vad ska jag göra om en av mina användare inte kan komma åt sitt konto?

Du kan återställa användarens konto genom att låta dem gå igenom registrerings processen igen. Läs mer om hur du [hanterar användar-och enhets inställningar med Azure AD Multi-Factor Authentication i molnet](howto-mfa-userdevicesettings.md).

### <a name="what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords"></a>Vad ska jag göra om en av mina användare förlorar en telefon som använder applösenord?

Ta bort alla användares applösenord för att förhindra obehörig åtkomst. När användaren har en ersättnings enhet kan de återskapa lösen orden. Läs mer om hur du [hanterar användar-och enhets inställningar med Azure AD Multi-Factor Authentication i molnet](howto-mfa-userdevicesettings.md).

### <a name="what-if-a-user-cant-sign-in-to-non-browser-apps"></a>Vad händer om en användare inte kan logga in på icke-webbläsarbaserade appar?

Om din organisation fortfarande använder äldre klienter och du har [tillåtit användningen av applösenord](howto-mfa-app-passwords.md), kan användarna inte logga in på dessa äldre klienter med sitt användar namn och lösen ord. De måste i stället [Konfigurera applösenord](../user-help/multi-factor-authentication-end-user-app-passwords.md). Användarna måste ta bort sin inloggnings information, starta om appen och sedan logga in med sitt användar namn och *applösenord* i stället för det vanliga lösen ordet.

Om din organisation inte har äldre klienter bör du inte tillåta att användarna skapar applösenord.

> [!NOTE]
> **Modern autentisering för Office 2013-klienter**
>
> Applösenord krävs bara för appar som inte stöder modern autentisering. Office 2013-klienter stöder moderna autentiseringsprotokoll, men måste konfigureras. Modern autentisering är tillgänglig för alla kunder som kör uppdateringen från mars 2015 eller senare för Office 2013. Mer information finns i blogg inlägget [uppdaterat Office 365 modern Authentication](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/).

### <a name="my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out"></a>Mina användare säger att de ibland inte tar emot textmeddelandet eller att verifierings tiden är slut.

Leverans av SMS-meddelanden är inte garanterad eftersom det finns okontrollerade faktorer som kan påverka tjänstens tillförlitlighet. Dessa faktorer är till exempel målets land eller region, mobil telefon operatören och signal styrkan.

Säkerhetsappar från tredje part kan också blockera verifierings kodens textmeddelande eller telefonsamtal. Om du använder en säkerhets app från tredje part kan du försöka inaktivera skyddet och sedan begära en annan MFA-verifierings kod.

Om användarna ofta har problem med att ta emot textmeddelanden på ett tillförlitligt sätt kan du be dem att använda Microsoft Authenticator appen eller Telefonsamtals metoden i stället. Microsoft Authenticator kan ta emot meddelanden både via mobil nät och Wi-Fi anslutningar. Dessutom kan mobilappen generera verifierings koder även om enheten inte har någon signal alls. Microsoft Authenticator-appen finns för [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [iOS](https://go.microsoft.com/fwlink/?Linkid=825073) och [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).

### <a name="can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out"></a>Kan jag ändra hur lång tid mina användare måste ange verifierings koden från ett SMS innan systemet nått tids gränsen?

I vissa fall Ja.

För enkelriktat SMS med Azure MFA server v 7.0 eller högre kan du konfigurera timeout-inställningen genom att ange en register nyckel. När MFA Cloud Service skickar textmeddelandet returneras verifierings koden (eller eng ång slö sen ordet) till MFA-servern. MFA-servern lagrar koden i minnet i 300 sekunder som standard. Om användaren inte anger koden innan 300 sekunder har passerat, nekas autentiseringen. Använd de här stegen för att ändra standardinställningen för timeout-inställningen:

1. Gå till `HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor`.
2. Skapa en **DWORD** -registernyckeln med namnet *pfsvc_pendingSmsTimeoutSeconds* och ange tiden i sekunder som du vill att Azure MFA-servern ska lagra eng ång slö sen ord.

>[!TIP]
>
> Om du har flera MFA-servrar vet bara den som bearbetade den ursprungliga autentiseringsbegäran den verifierings kod som skickades till användaren. När användaren anger koden måste autentiseringsbegäran som verifieras skickas till samma server. Om kod verifieringen skickas till en annan server nekas autentiseringen.

Om användarna inte svarar på SMS inom den angivna tids gränsen, nekas deras autentisering.

För enkelriktat SMS med Azure AD MFA i molnet (inklusive AD FS adapter eller nätverks princip Server tillägget) kan du inte konfigurera tids inställningen för timeout. Azure AD lagrar verifierings koden i 180 sekunder.

### <a name="can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server"></a>Kan jag använda maskinvaru-token med Azure Multi-Factor Authentication-server?

Om du använder Azure Multi-Factor Authentication-server kan du importera tredjeparts-tidsbaserad (ed), eng ång slö sen ord (TOTP mobilapp)-token och sedan använda dem för tvåstegsverifiering.

Du kan använda ActiveIdentity-token som är OATH TOTP mobilapp-tokens om du använder den hemliga nyckeln i en CSV-fil och importera till Azure Multi-Factor Authentication-server. Du kan använda OATH-token med Active Directory Federation Services (AD FS) (ADFS), formulärbaserad autentisering i IIS (Internet Information Server) och Remote Authentication Dial-In User Service (RADIUS) så länge klient systemet kan acceptera användarindata.

Du kan importera TOTP mobilapp-token från tredje part med följande format:  

- Portabel symmetrisk nyckel behållare (PSKC)
- CSV om filen innehåller ett serie nummer, en hemlig nyckel i Base 32-format och ett tidsintervall

### <a name="can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services"></a>Kan jag använda Azure Multi-Factor Authentication-server för att skydda Terminal Services?

Ja, men om du använder Windows Server 2012 R2 eller senare kan du bara skydda Terminal Services med hjälp av Fjärrskrivbordsgateway (RD Gateway).

Säkerhets ändringar i Windows Server 2012 R2 har ändrat hur Azure Multi-Factor Authentication-server ansluter till säkerhets paketet Local Security Authority (LSA) i Windows Server 2012 och tidigare versioner. För versioner av Terminal Services i Windows Server 2012 eller tidigare kan du [skydda ett program med Windows-autentisering](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Om du använder Windows Server 2012 R2 behöver du RD Gateway.

### <a name="i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller"></a>Jag konfigurerade anroparens ID i MFA Server, men mina användare får fortfarande Multi-Factor Authentication anrop från en anonym anropare.

När Multi-Factor Authentication anrop görs via det offentliga telefonnätet, kan de ibland dirigeras via en operatör som inte har stöd för uppringarens ID. På grund av det här beteendet för bärvåg är uppringarens ID inte garanterat, trots att Multi-Factor Authentication systemet alltid skickar det.

### <a name="why-are-my-users-being-prompted-to-register-their-security-information"></a>Varför uppmanas mina användare att registrera sin säkerhets information?

Det finns flera orsaker till att användarna uppmanas att registrera sina säkerhets uppgifter:

- Användaren har Aktiver ATS för MFA av deras administratör i Azure AD, men har inte registrerat någon säkerhets information för sitt konto ännu.
- Användaren har Aktiver ATS för lösen ords återställning via självbetjäning i Azure AD. Säkerhets informationen hjälper dem att återställa sina lösen ord i framtiden om de glömmer bort det.
- Användaren kom åt ett program som har en princip för villkorlig åtkomst för att kräva MFA och som inte tidigare har registrerats för MFA.
- Användaren registrerar en enhet med Azure AD (inklusive Azure AD Join) och din organisation kräver MFA för enhets registrering, men användaren har inte redan registrerats för MFA.
- Användaren skapar Windows Hello för företag i Windows 10 (vilket kräver MFA) och inte tidigare har registrerats för MFA.
- Organisationen har skapat och aktiverat en MFA-registrerings princip som har tillämpats på användaren.
- Användaren har redan registrerats för MFA, men valde en verifierings metod som en administratör har inaktiverat. Användaren måste därför gå igenom MFA-registreringen igen för att välja en ny standard verifierings metod.

## <a name="errors"></a>Fel

* [Vad ska användarna göra om de ser att det inte finns något fel meddelande om att autentiseringsbegäran inte gäller för ett aktiverat konto när de använder meddelanden i mobilappen?](#what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications)
* [Vad ska användarna göra om de ser ett 0x800434D4L fel meddelande vid inloggning till ett program som inte är ett webb läsar program?](#what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application)

### <a name="what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications"></a>Vad ska användarna göra om de ser att det inte finns något fel meddelande om att autentiseringsbegäran inte gäller för ett aktiverat konto när de använder meddelanden i mobilappen?

Be användaren att slutföra följande procedur för att ta bort kontot från Microsoft Authenticator och sedan lägga till det igen:

1. Gå till [Azure Portal profil](https://account.activedirectory.windowsazure.com/profile/) och logga in med ett organisations konto.
2. Välj **ytterligare säkerhets verifiering**.
3. Ta bort det befintliga kontot från Microsoft Authenticator-appen.
4. Klicka på **Konfigurera** och följ sedan anvisningarna för att konfigurera om Microsoft Authenticator.

### <a name="what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application"></a>Vad ska användarna göra om de ser ett 0x800434D4L fel meddelande vid inloggning till ett program som inte är ett webb läsar program?

*0x800434D4L* -felet uppstår när du försöker logga in till ett program som inte är en webbläsare, som är installerat på en lokal dator, som inte fungerar med konton som kräver tvåstegsverifiering.

En lösning för det här felet är att ha separata användar konton för administratörs-och icke-admin-åtgärder. Senare kan du länka post lådor mellan ditt administratörs konto och kontot för icke-administratörer så att du kan logga in i Outlook med kontot som inte är administratör. Om du vill ha mer information om den här lösningen kan du lära dig att [ge en administratör möjlighet att öppna och visa innehållet i en användares post låda](https://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Nästa steg

Om din fråga inte besvaras här finns följande support alternativ:

* Sök i [Microsoft Support Knowledge Base](https://support.microsoft.com) efter lösningar på vanliga tekniska problem.
* Sök efter och bläddra efter tekniska frågor och svar från communityn eller Ställ din egen fråga i [Azure Active Directory Q&A](/answers/topics/azure-active-directory.html).
* Kontakta Microsoft Professional via [Azure Multi-Factor Authentication-Server Support](https://support.microsoft.com/oas/default.aspx?prid=14947). När du kontaktar oss är det bra om du kan inkludera så mycket information om problemet som möjligt. Information som du kan tillhandahålla inkluderar sidan där du såg felet, den speciella felkoden, det ID som identifierades och ID: t för den användare som såg felet.
* Om du är en äldre PhoneFactor-kund och har frågor eller behöver hjälp med att återställa ett lösen ord, använder du [phonefactorsupport@microsoft.com](mailto:phonefactorsupport@microsoft.com) e-postadressen för att öppna ett support ärende.
