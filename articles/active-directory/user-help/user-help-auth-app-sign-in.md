---
title: Logga in med Microsoft Authenticator app – Azure AD
description: Använd Microsoft Authenticator-appen för att logga in på ditt arbets-eller skol konto eller ditt personliga Microsoft-konto och andra konton än Microsoft, antingen med hjälp av tvåstegsverifiering eller telefonin loggning.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 03/12/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 0fd0aa19d52adb4bf06c9d21d4182e27497a531b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103471772"
---
# <a name="sign-in-to-your-accounts-using-the-microsoft-authenticator-app"></a>Logga in på dina konton med Microsoft Authenticator-appen

Microsoft Authenticator appen hjälper dig att logga in på dina konton om du använder tvåstegsverifiering. Med tvåfaktorsverifiering kan du komma åt dina konton säkrare, särskilt när du visar känslig information. Eftersom lösenord kan glömmas, stjälas eller komprometteras är tvåfaktorsverifiering ett ytterligare steg som hjälper dig att skydda ditt konto genom att göra det svårare för andra att logga in.

Du kan använda Microsoft Authenticator-appen på flera sätt, däribland följande:

- Tillhandahålla en uppmaning för en andra verifieringsmetod när du har loggat in med ditt användarnamn och lösenord.

- Tillhandahålla inloggning utan att kräva ett lösenord, med ditt användarnamn och din mobila enhet med ditt fingeravtryck, ett ansikte eller en PIN-kod.

  >[!Important]
  >Den här telefonens inloggnings metod fungerar bara med ditt arbets-eller skol konto och personliga Microsoft-konton. Dina konton som inte kommer från Microsoft kräver att du använder den vanliga verifierings processen med två faktorer.

## <a name="prerequisites"></a>Förutsättningar

Innan du kan använda Microsoft Authenticator-appen måste du:

 1. Hämta och installera Microsoft Authenticator-appen. Om du inte har gjort det ännu kan du läsa [Hämta och installera appen](user-help-auth-app-download-install.md).

 2. Lägg till ditt arbets-eller skol konto, personliga konton och tredje parts konton i Microsoft Authenticator-appen. Detaljerade anvisningar finns i [Lägg till ditt arbets-eller skol konto](user-help-auth-app-add-work-school-account.md), [Lägg till dina personliga konton](user-help-auth-app-add-personal-ms-account.md)och [Lägg till konton som inte kommer från Microsoft](user-help-auth-app-add-non-ms-account.md).

## <a name="turn-on-and-use-phone-sign-in-for-your-work-or-school-account"></a>Aktivera och Använd telefonin loggning för ditt arbets-eller skol konto

Telefonin loggning är en typ av tvåstegsverifiering. Du måste fortfarande verifiera din identitet genom att tillhandahålla en sak som du känner till, men med telefonin loggning kan du hoppa över att ange ditt konto lösen ord och utföra all din identitets verifiering på din mobila enhet.

Innan du kan aktivera telefonin loggning måste du aktivera tvåstegsverifiering. Mer information om hur du aktiverar tvåstegsverifiering för ett konto finns i [Lägg till ditt arbets-eller skol konto](user-help-auth-app-add-work-school-account.md) och [Lägg till dina personliga konton](user-help-auth-app-add-personal-ms-account.md).

Telefonin loggning är bara tillgängligt på iOS-och Android-enheter som kör Android 6,0 eller senare.

### <a name="turn-on-phone-sign-in"></a>Aktivera telefonin loggning

Öppna appen Microsoft Authenticator, gå till ditt arbets-eller skol konto och aktivera telefonin loggning.

- **När du trycker på panelen konto** visas en hel skärms visning av kontot. Om du ser att **telefonin loggning är aktiverat** , vilket innebär att du är helt konfigurerad för inloggning utan lösen ord. Om du ser **Aktivera telefonin loggning** trycker du på den för att aktivera telefonin loggning.
- **Om du redan har använt appen för tvåstegsverifiering** kan du trycka på panelen konto om du vill se en hel skärms visning av kontot. Tryck sedan på **Aktivera telefonin loggning** för att aktivera telefonin loggning.
- **Om du inte hittar ditt arbets-eller skol konto** på skärmen **konton** i appen, innebär det att du inte har lagt till det i appen ännu. Lägg till ditt arbets-eller skol konto genom att följa stegen i [hjälpen för Lägg till ditt arbets-eller skol konto](user-help-auth-app-add-work-school-account.md).

> [!NOTE]
> Microsoft stöder inte en kombination av enhets registrering och certifikatbaserad autentisering i autentiserad iOS. I stället måste användaren registrera enheten manuellt via verifierings inställningar innan du loggar in.

När du har aktiverat telefonin loggning kan du bara logga in med Microsoft Authenticator-appen. Gör så här:

1. Logga in på ditt arbets-eller skol konto.

    När du har skrivit ditt användar namn visas en **Godkänn inloggnings** skärm som visar dig ett tvåsiffrigt nummer och du uppmanas att logga in via Microsoft Authenticator-appen. Om du inte vill använda den här inloggnings metoden kan du välja **Använd ditt lösen ord i stället** och logga in med ditt lösen ord.

    ![Godkänn inloggnings rutan på datorn](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in.png)

2. Öppna meddelandet eller appen Microsoft Authenticator på enheten och tryck sedan på det nummer som motsvarar det nummer som visas på datorns **Godkänn inloggnings** skärm.

    ![Godkänn inloggnings rutan på enheten](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in-numbers.png)

3. Välj **Godkänn** om du känner igen inloggningsförsöket. Annars väljer du **Neka**.

4. Använd telefonens PIN-kod eller bio metriska nyckel för att slutföra autentiseringen.

## <a name="turn-on-and-use-phone-sign-in-for-your-personal-microsoft-accounts"></a>Aktivera och Använd telefonin loggning för dina personliga Microsoft-konton

Du kan aktivera telefonin loggning för din personliga Microsoft-konto, till exempel det konto som du använder för att logga in på Outlook.com, Xbox eller Skype.

>[!NOTE]
>För att skydda ditt konto kräver Microsoft Authenticator-appen en PIN-kod eller ett biometric-lås på enheten. Om du håller telefonen olåst måste du konfigurera ett säkerhets lås innan du aktiverar telefonin loggning.

### <a name="turn-on-phone-sign-in"></a>Aktivera telefonin loggning 

Öppna appen Microsoft Authenticator, gå till ditt arbets-eller skol konto och aktivera telefonin loggning.

- **När du trycker på panelen konto** visas en hel skärms visning av kontot. Om du ser att **telefonin loggning är aktiverat** , vilket innebär att du är helt konfigurerad för inloggning utan lösen ord. Om du ser **Aktivera telefonin loggning** trycker du på den för att aktivera telefonin loggning.
- **Om du redan använder appen för tvåstegsverifiering** kan du trycka på panelen konto om du vill se en hel skärms visning av kontot. Tryck sedan på **Aktivera telefonin loggning** för att aktivera telefonin loggning.
- **Om du inte hittar ditt konto** på skärmen **konton** i appen, innebär det att du inte har lagt till det i appen ännu. Lägg till din personliga Microsoft-konto genom att följa anvisningarna i artikeln [Lägg till personliga Microsoft-konton](user-help-auth-app-add-personal-ms-account.md) .

### <a name="sign-in-to-your-account-using-phone-sign-in"></a>Logga in på ditt konto med telefonin loggning

1. Gå till din personliga Microsoft-konto inloggnings sida. i stället för att skriva ditt lösen ord väljer du länken **Använd appen Microsoft Authenticator i stället** .

    Microsoft skickar ett meddelande till din telefon.

2. Godkänn meddelandet.

## <a name="sign-in-using-two-factor-verification-for-your-account"></a>Logga in med tvåstegsverifiering för ditt konto

Med den vanliga autentiseringsmetoden med två faktorer måste du ange ditt användar namn och lösen ord i enheten som du loggar in på och sedan välja om Microsoft Authenticator appen får ett meddelande eller om du vill kopiera verifierings koden från Authenticator-appen. På en Android-enhet hittar du verifierings koderna på skärmen **konton** . På en iOS-enhet hittar du dessa verifierings koder på skärmen **konton** eller i hel skärms läge för ett konto beroende på typ av konto. Du aktiverar tvåstegsverifiering för ditt konto när du lägger till kontot i Microsoft Authenticator-appen.

>[!Note]
>Om du inte ser ditt arbets-eller skol konto eller ditt personliga konto på skärmen **konton** i Microsoft Authenticator-appen, innebär det att du inte har lagt till kontot i Microsoft Authenticator-appen. Information om hur du lägger till ditt konto finns i [Lägg till ditt arbets-eller skol konto](user-help-auth-app-add-work-school-account.md) eller [Lägg till dina personliga konton](user-help-auth-app-add-personal-ms-account.md).

De steg som krävs för att logga in på ditt arbete eller din skola eller ditt personliga konto med hjälp av olika metoder för tvåstegsverifiering, finns i [Logga in med tvåstegsverifiering eller säkerhets information](user-help-sign-in.md).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

| Fråga | Lösning |
| -------- | -------- |
| Hur loggar jag in med min telefon säkrare än att ange ett lösen ord? | Idag loggar de flesta användare in på webbplatser eller appar med hjälp av användar namn och lösen ord. Lösen ord kan tyvärr förloras, stulits eller gissas från hackare.<br><br>När du har konfigurerat Microsoft Authenticator-appen skapar den en nyckel på din telefon för att låsa upp ditt konto som skyddas av telefonens PIN-kod eller bio metriska lås. Den här nyckeln används sedan för att bevisa din identitet vid inloggning.<br><br>**Viktigt**<br>Dina data används bara för att skydda din nyckel lokalt. Den skickas aldrig till eller lagras i molnet. |
| Ersätter telefonin loggningen tvåstegsverifiering? Bör jag stänga av den? | Telefonin loggning är en typ av två steg-verifiering där båda stegen sker på den mobila enheten. Du bör ha två stegs verifiering aktiverat för att ge ytterligare säkerhet för ditt konto. |
| Måste jag godkänna två meddelanden om jag vill att tvåstegsverifiering ska vara aktiverat för mitt konto? | Nej. Att logga in på din Microsoft-konto att använda din telefon räknas också som tvåstegsverifiering, så inga andra godkännanden krävs. |
| Vad händer om jag tappar bort min telefon eller inte? Hur gör jag för att åtkomst till mitt konto? | Du kan alltid välja länken Använd ett lösen ord i stället på inloggnings sidan för att växla tillbaka till med ditt lösen ord. Men om du använder tvåstegsverifiering behöver du fortfarande använda en andra metod för att verifiera din identitet.<br><br>**Viktigt**<br>Vi rekommenderar starkt att du ser till att du har mer än en, uppdaterad, verifieringsmetod som är kopplad till ditt konto.<br><br>Du kan hantera dina verifierings metoder för personliga konton från sidan [säkerhets inställningar](https://account.live.com/proofs/manage) . För arbets-eller skol konton kan du gå till din organisations [ytterligare säkerhets verifierings](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1) sida eller sidan **skydda ditt konto** om administratören har aktiverat säkerhets information. Mer information om säkerhets information finns i [Översikt över säkerhets information (för hands version)](./security-info-setup-signin.md).<br><br>Om du inte kan hantera dina verifierings metoder måste du kontakta administratören. |
| Vill du Hur gör jag för att sluta använda den här funktionen och gå tillbaka till att använda mitt lösen ord? | För personliga konton väljer du länken **Använd ett lösen ord i stället** vid inloggning. Ditt senaste val sparas och erbjuds som standard nästa gången du loggar in. Om du vill gå tillbaka till Använd telefonin loggning väljer du länken **Använd en app i stället** under Logga in.<br><br>För arbets-eller skol konton måste du antingen avregistrera enheten från sidan **Inställningar** i Microsoft Authenticator-appen eller inaktivera enheten från avsnittet **enheter & aktivitet** i din profil. Mer information om hur du inaktiverar din enhet från din profil finns i [Uppdatera din profil och konto information från portalen Mina appar](./my-account-portal-devices-page.md#disable-a-device). |
| Varför kan jag inte använda mer än ett arbets-eller skol konto för telefonin loggning? | En telefon måste vara registrerad på ett arbets-eller skol konto. Om du vill aktivera telefonin loggning för ett annat arbets-eller skol konto måste du avregistrera ditt konto från den här enheten via sidan **Inställningar** . |
| Kan jag logga in på min dator med min telefon? | För datorn rekommenderar vi att du loggar in med Windows Hello i Windows 10. Med Windows Hello kan du använda ditt ansikte, finger avtryck eller PIN-kod för att logga in. |

## <a name="next-steps"></a>Nästa steg

- Om du har problem med att hämta din verifierings kod för din personliga Microsoft-konto kan du läsa avsnittet **fel sökning av verifierings kod problem** i artikeln [Microsoft-konto säkerhets information & verifierings koder](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) .

- Om du har fler vanliga frågor om appen kan du läsa [Microsoft Authenticator vanliga](user-help-auth-app-faq.md) frågor och svar

- Om du vill ha mer information om tvåstegsverifiering kan du läsa Konfigurera [mitt konto för](multi-factor-authentication-end-user-first-time.md) tvåstegsverifiering

- Om du vill ha mer information om säkerhets information, se [Översikt över säkerhets information (för hands version)](./security-info-setup-signin.md)