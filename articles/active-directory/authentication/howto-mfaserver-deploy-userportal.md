---
title: Användar Portal för Azure MFA Server – Azure Active Directory
description: Kom igång med Azure MFA och användarportalen.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61cefacc4147ab1eb105af9cb21365129dd1950e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742467"
---
# <a name="user-portal-for-the-azure-multi-factor-authentication-server"></a>Användarportal för Azure Multi-Factor Authentication-servern

Användarportalen är en IIS-webbplats där användarna kan registrera sig för Azure Multi-Factor Authentication (MFA) och hantera sina konton. En användare kan ändra sina telefonnummer, sin PIN-kod eller kringgå Azure Multi-Factor Authentication vid nästa inloggning.

Användarna loggar in på användarportalen med sitt vanliga användarnamn och lösenord och genomför antingen ett tvåstegsverifieringssamtal eller svarar på säkerhetsfrågor för att slutföra autentiseringen. Om användarregistrering tillåts, konfigurerar användarna sina telefonnummer och PIN-koder första gången de loggar in på användarportalen.

Administratörer av användarportalen kan konfigureras och beviljas behörighet att lägga till nya användare och uppdatera befintliga användare.

Beroende på din miljö kan du distribuera användarportalen på samma server som Azure Multi-Factor Authentication-servern eller på en annan Internet-ansluten server.

> [!IMPORTANT]
> Från den 1 juli 2019 erbjuder Microsoft inte längre MFA-Server för nya distributioner. Nya kunder som vill kräva Multi-Factor Authentication (MFA) vid inloggnings händelser bör använda molnbaserad Azure AD-Multi-Factor Authentication.
>
> Information om hur du kommer igång med molnbaserad MFA finns i [Självstudier: skydda användar inloggnings händelser med Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Befintliga kunder som aktiverade MFA server före den 1 juli 2019 kan ladda ned den senaste versionen, framtida uppdateringar och generera autentiseringsuppgifter för aktivering som vanligt.

![Inloggnings sida för MFA Server-användargrupp](./media/howto-mfaserver-deploy-userportal/portal.png)

> [!NOTE]
> Användarportalen är endast tillgänglig med Multi-Factor Authentication-servern. Om du använder Multi-Factor Authentication i molnet ska du hänvisa användarna till [Set up your account for two-step verification](../user-help/multi-factor-authentication-end-user-first-time.md) (Konfigurera ditt konto för tvåstegsverifiering) eller [Manage your settings for two-step verification](../user-help/multi-factor-authentication-end-user-manage-settings.md) (Hantera dina inställningar för tvåstegsverifiering).

## <a name="install-the-web-service-sdk"></a>Installera webbtjänst-SDK

Om webbtjänst-SDK för Azure Multi-Factor Authentication **inte** redan är installerat på Azure Multi-Factor Authentication-servern (MFA) slutför du stegen nedan för båda scenarierna.

1. Öppna Multi-Factor Authentication-serverkonsolen.
2. Gå till **Webbtjänst-SDK** och välj **Installera Webbtjänst-SDK:n**.
3. Slutför installationen med hjälp av standardinställningarna om du inte behöver ändra dem av någon anledning.
4. Bind ett TLS/SSL-certifikat till platsen i IIS.

Om du har frågor om hur du konfigurerar ett TLS/SSL-certifikat på en IIS-server kan du läsa artikeln [så här konfigurerar du SSL i IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis).

Webbtjänst-SDK måste skyddas med ett TLS/SSL-certifikat. Ett självsignerat certifikat är lämpligt för detta ändamål. Importera certifikatet till arkivet "betrodda rot certifikat utfärdare" för det lokala dator kontot på webb servern för användar portalen så att det litar på certifikatet när TLS-anslutningen initieras.

![MFA-serverkonfiguration för webbtjänst-SDK](./media/howto-mfaserver-deploy-userportal/sdk.png)

## <a name="deploy-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>Distribuera användarportalen på samma server som Azure Multi-Factor Authentication-servern

Följande krav krävs för att installera användar portalen på **samma server** som Azure-Multi-Factor Authentication-Server:

* IIS, inklusive ASP.NET och IIS 6 metabase-kompatibilitet (för IIS 7 eller senare)
* Ett konto med administratörsrättigheter för datorn och domänen om tillämpligt. Kontot måste ha behörighet att skapa Active Directory-säkerhetsgrupper.
* Skydda användar portalen med ett TLS/SSL-certifikat.
* Skydda Azure Multi-Factor Authentication-webbtjänst-SDK med ett TLS/SSL-certifikat.

Följ anvisningarna nedan om du vill distribuera användarportalen:

1. Öppna Azure Multi-Factor Authentication-serverkonsolen, klicka på ikonen **Användarportal** i vänster meny och sedan på **Installera användarportal**.
2. Slutför installationen med hjälp av standardinställningarna om du inte behöver ändra dem av någon anledning.
3. Bind ett TLS/SSL-certifikat till platsen i IIS

   > [!NOTE]
   > Detta TLS/SSL-certifikat är vanligt vis ett offentligt signerat TLS/SSL-certifikat.

4. Öppna en webbläsare från valfri dator och gå till URL: en där användar portalen installerades (exempel: `https://mfa.contoso.com/MultiFactorAuth` ). Se till att inga certifikatvarningar eller fel visas.

![Installation av MFA-serveranvändarportal](./media/howto-mfaserver-deploy-userportal/install.png)

Om du har frågor om hur du konfigurerar ett TLS/SSL-certifikat på en IIS-server kan du läsa artikeln [så här konfigurerar du SSL i IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis).

## <a name="deploy-the-user-portal-on-a-separate-server"></a>Distribuera användarportalen på en separat server

Om servern där Azure Multi-Factor Authentication-server körs inte är Internet-ansluten ska du installera användarportalen på en **separat, Internet-ansluten server**.

Om din organisation använder appen Microsoft Authenticator som en av verifieringsmetoderna och vill distribuera användarportalen på sin egen server ska du uppfylla följande krav:

* Använd v6.0 eller senare för Azure Multi-Factor Authentication Server.
* Installera användarportalen på en Internetriktad webbserver som kör Microsoft® Internet Information Services (IIS) 6.x eller senare.
* Om du använder IIS 6.x, kontrollerar du att ASP.NET v2.0.50727 är installerat, registrerat och inställt på **Tillåten**.
* När du använder IIS 7.x eller högre, IIS, inklusive grundläggande autentisering, ASP.NET och IIS 6 metabase-kompatibilitet.
* Skydda användar portalen med ett TLS/SSL-certifikat.
* Skydda Azure Multi-Factor Authentication-webbtjänst-SDK med ett TLS/SSL-certifikat.
* Se till att användar portalen kan ansluta till Azure Multi-Factor Authentication Web Service SDK över TLS/SSL.
* Se till att användarportalen kan autentisera till webbtjänst-SDK:n för Azure Multi-Factor Authentication med autentiseringsuppgifterna för ett tjänstkonto som är medlem i säkerhetsgruppen ”PhoneFactor Admins”. Det här kontot och gruppen ska finnas i Active Directory om Azure Multi-Factor Authentication-servern körs på en domänansluten server. Det här tjänstkontot och gruppen finns lokalt på Azure Multi-Factor Authentication-servern om den inte är ansluten till en domän.

Om du installerar användarportalen på en annan server än Azure Multi-Factor Authentication-servern följer du dessa steg:

1. **På MFA-servern** går du till installationssökvägen (exempel: C:\Program\Multi-Factor Authentication Server) och kopierar filen **MultiFactorAuthenticationUserPortalSetup64** till en plats som är tillgänglig för den Internet-anslutna servern där du installerar den.
2. Kör installationsfilen **MultiFactorAuthenticationUserPortalSetup64** på den Internet-anslutna webbservern som administratör, ändra platsen om du vill och ändra namnet på den virtuella katalogen till ett kort namn om du så önskar.
3. Bind ett TLS/SSL-certifikat till platsen i IIS.

   > [!NOTE]
   > Detta TLS/SSL-certifikat är vanligt vis ett offentligt signerat TLS/SSL-certifikat.

4. Bläddra till **C:\inetpub\wwwroot\MultiFactorAuth**
5. Redigera Web.Config-filen i Anteckningar

    * Leta reda på nyckeln **"USE_WEB_SERVICE_SDK"** och ändra **värde="false"** till **värde="true"**
    * Leta rätt på nycklarna **"WEB_SERVICE_SDK_AUTHENTICATION_USERNAME"** och ändra **värde=""** till **värde="DOMAIN\User"** där DOMAIN\User är ett tjänstkonto som är en del av gruppen "PhoneFactor Admins".
    * Leta reda på nyckeln **"WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD"** och ändra **värde=""** till **värde="Password"** där Password är lösenordet för tjänstkontot som angavs på föregående rad.
    * Hitta värdet `https://www.contoso.com/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx` och ändra URL-platshållaren till URL:en för webbtjänst-SDK:n vi installerade i steg 2.
    * Spara filen Web.Config och stäng Anteckningar.

6. Öppna en webbläsare från valfri dator och gå till URL: en där användar portalen installerades (exempel: `https://mfa.contoso.com/MultiFactorAuth` ). Se till att inga certifikatvarningar eller fel visas.

Om du har frågor om hur du konfigurerar ett TLS/SSL-certifikat på en IIS-server kan du läsa artikeln [så här konfigurerar du SSL i IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis).

## <a name="configure-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Konfigurera inställningarna för användarportalen i Azure Multi-Factor Authentication-servern

Nu när användarportalen har installerats måste du konfigurera Azure Multi-Factor Authentication-servern så att den fungerar med portalen.

1. I Multi-Factor Authentication-serverkonsolen klickar du på ikonen **Användarportal**. På inställningar-fliken, anger du URL:en för användarportalen i textrutan **Användarportal-URL**. Den här URL:en infogas i e-postmeddelanden som skickas till användare när de importeras till Azure Multi-Factor Authentication-servern om e-postfunktionen har aktiverats.
2. Välj de inställningar som du vill använda på användarportalen. Om användarna till exempel tillåts styra sin autentiseringsmetod, kontrollera att **Tillåt användare välja metod** har markerats, med de metoder som de får välja mellan.
3. Definiera vem som ska vara administratörer på fliken **Administratörer** . Du kan skapa detaljerade administrativa behörigheter med kryss rutorna och list rutorna i rutorna Lägg till/redigera.

Valfri konfiguration:

- **Säkerhetsfrågor** - definiera godkända säkerhetsfrågor för din miljö och det språk som de visas i.
- **Slutförda sessioner** – Konfigurera användarportalintegrering med en formulärbaserad webbplats som använder MFA.
- **Betrodda IP-adresser** – Tillåt användare att hoppa över MFA vid autentisering från en lista över betrodda IP-adresser eller intervall.

![Konfiguration av MFA-serveranvändarportal](./media/howto-mfaserver-deploy-userportal/config.png)

Azure Multi-Factor Authentication-servern tillhandahåller flera alternativ för användarportalen. Följande tabell innehåller en lista över dessa alternativ och en förklaring av hur de används.

| Inställningar för användarportalen | Beskrivning |
|:--- |:--- |
| URL till användarportalen | Ange URL:en till den plats där portalen finns. |
| Primär autentisering | Ange vilken typ av autentisering som ska användas för inloggning på portalen. Du kan välja mellan Windows-, RADIUS- eller LDAP-autentisering. |
| Tillåt användare att logga in | Låt användare ange ett användarnamn och lösenord på inloggningssidan för användarportalen. Om det här alternativet inte är valt är rutorna nedtonade. |
| Tillåt användarregistrering | Låt en användare registrera sig i Multi-Factor Authentication genom att ta dem till en installationsskärm där de uppmanas att ange ytterligare information, till exempel telefonnummer. Inställningen Fråga efter reservtelefonnummer gör att användaren kan ange ett sekundärt telefonnummer. Fråga efter tredje parts OATH-token gör att användaren kan ange en OATH-token från en tredje part. |
| Tillåt användare att starta en engångsförbikoppling | Tillåt användare att starta en engångsförbikoppling. Om en användare ställer in det här alternativet börjar det gälla nästa gång användaren loggar in. Inställningen Fråga efter antal förbikopplingssekunder visar en ruta där användaren kan ändra standardinställningen på 300 sekunder. Annars är engångsförbikopplingen bara giltig i 300 sekunder. |
| Tillåt användare att välja metod | Låt användare ange sin primära kontaktmetod. Den här metoden kan vara ett telefonsamtal, ett textmeddelande, en mobilapp eller en OATH-token. |
| Tillåt användare att välja språk | Låt användare ändra språket som används för telefonsamtalet, textmeddelandet, mobilappen eller OATH-token. |
| Tillåt användare att aktivera mobilapp | Låt användare generera en aktiveringskod för att slutföra aktiveringen för mobilappar som används med servern.  Du kan också ange antalet enheter som de kan aktivera appen på, mellan 1 och 10. |
| Använd säkerhetsfrågor som reserv | Tillåt säkerhetsfrågor om tvåstegsverifiering misslyckas. Du kan ange antalet säkerhetsfrågor som måste besvaras korrekt. |
| Tillåt användare att koppla en utomstående OATH-token | Låt användare ange en OATH-token från en tredje part. |
| Använd OATH-token som reserv | Tillåt användning av en OATH-token om tvåstegsverifiering misslyckas. Du kan också ange tidsgränsen för sessionen i antal minuter. |
| Aktivera loggning | Aktivera loggning på användarportalen. Loggfilerna finns på: C:\Program Files\Multi-Factor Authentication Server\Logs. |

> [!IMPORTANT]
> Från och med mars 2019 är Telefonsamtals alternativen inte tillgängliga för MFA-Server användare i kostnads fria/utvärderings versioner av Azure AD-klienter. SMS-meddelanden påverkas inte av den här ändringen. Telefonsamtalet fortsätter att vara tillgängligt för användare i betalda Azure AD-klienter. Den här ändringen påverkar endast kostnads fria/utvärderings versioner av Azure AD.

De här inställningarna blir synliga för användaren när de är aktiverade och användaren är inloggad på användarportalen.

![Hantera MFA Server-kontot med hjälp av användar portalen](./media/howto-mfaserver-deploy-userportal/portalsettings.png)

### <a name="self-service-user-enrollment"></a>Användarregistrering via självbetjäning

Om du vill att användarna ska logga in och registrera måste du markera alternativen **Tillåt användare att logga in** och **Tillåt användar registrering** på fliken Inställningar. Kom ihåg att de inställningar du väljer påverkar användarens inloggnings upplevelse.

När en användare till exempel loggar in på användarportalen för första gången, tas de sedan till inställningssidan för Azure Multi-Factor Authentication. Beroende på hur du har konfigurerat Azure Multi-Factor Authentication kan användaren välja autentiseringsmetod.

Om användaren väljer autentiseringsmetoden Röstsamtal eller om användaren har förkonfigurerats att använda den metoden så uppmanas han eller hon att ange sitt primära telefonnummer och sin anknytning om en sådan krävs. Användaren kanske också kan ange ett reservtelefonnummer.

![Registrera primära och sekundära telefonnummer](./media/howto-mfaserver-deploy-userportal/backupphone.png)

Om användaren måste ange en PIN-kod vid autentiseringen uppmanas han eller hon att skapa en PIN-kod. Efter att ha angett sitt telefonnummer och PIN-kod (om en sådan krävs), klickar användaren på knappen **Ring mig nu för att autentisera**. Azure Multi-Factor Authentication utför en verifiering av telefonsamtal till användarens primära telefonnummer. Användaren måste besvara samtalet och ange sin PIN-kod (om en sådan krävs) och trycka på # för att gå vidare till nästa steg i självregistreringsprocessen.

Om användaren väljer autentiseringsmetoden för SMS-meddelanden eller om användaren har förkonfigurerats att använda den metoden uppmanas han eller hon att ange sitt mobiltelefonnummer. Om användaren måste ange en PIN-kod vid autentiseringen uppmanas han eller hon också att ange en PIN-kod.  Efter att de angett sitt telefonnummer och PIN-kod (om en sådan krävs), klickar användaren på knappen **Skicka mig ett textmeddelande för att autentisera**. Azure Multi-Factor Authentication utför en SMS-verifiering till användarens mobil telefon. Användaren får SMS:et med ett engångslösenord (OTP) och svarar på meddelandet med OTP:t och sin PIN-kod (om sådan krävs).

![Användar Portal verifiering med SMS](./media/howto-mfaserver-deploy-userportal/text.png)

Om användaren väljer autentiseringsmetoden Mobilapp eller om användaren har förkonfigurerats att använda den metoden uppmanas han eller hon att installera Microsoft Authenticator-appen på sin enhet och att generera en aktiveringskod. Efter installation, klickar användaren på knappen Generera aktiveringskod.

> [!NOTE]
> Användaren måste aktivera push-meddelanden för sin enhet för att kunna använda Microsoft Authenticator-appen.

Nu visas en aktiveringskod, en URL och en streckkodsbild på sidan. Om användaren måste ange en PIN-kod vid autentiseringen uppmanas han eller hon också att ange en PIN-kod. Användaren anger aktiveringskoden och URL:en i Microsoft Authenticator-appen eller använder streckkodsläsaren för att skanna streckkodsbilden och klickar sedan på knappen Aktivera.

När aktiveringen slutförts, klickar användaren på knappen **Autentisera mig nu**. Azure Multi-Factor Authentication utför en verifiering till användarens mobilapp. Användaren måste ange sin PIN-kod (om en sådan krävs) och trycka på knappen Autentisera i mobilappen för att gå vidare till nästa steg i självregistreringsprocessen.

Om administratörerna har konfigurerat Azure Multi-Factor Authentication Server att kräva svar på säkerhetsfrågor visas sidan Säkerhetsfrågor. Användaren måste välja fyra säkerhetsfrågor och ge svar på de frågor som han eller hon har valt.

![Säkerhetsfrågor för användarportalen](./media/howto-mfaserver-deploy-userportal/secq.png)

Användarens självregistreringen är nu klar och användaren loggas in på användarportalen. Användare kan logga in på användarportalen igen när som helst för att ändra sina telefonnummer, PIN-koder, autentiseringsmetoder och säkerhetsfrågor om det är tillåtet av administratören att byta metod.

## <a name="next-steps"></a>Nästa steg

- [Distribuera webbtjänsten Azure Multi-Factor Authentication Server Mobile App](howto-mfaserver-deploy-mobileapp.md)