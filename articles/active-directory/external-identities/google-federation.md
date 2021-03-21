---
title: Lägg till Google som en identitets leverantör för B2B – Azure AD
description: Federera med Google så att gäst användare kan logga in på dina Azure AD-appar med egna Gmail-konton.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a397c17821d16594ccfb48175a8a141cb9f390b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687829"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Lägg till Google som en identitets leverantör för B2B-gäst användare

Genom att konfigurera Federation med Google kan du tillåta att inbjudna användare loggar in på dina delade appar och resurser med egna Gmail-konton, utan att behöva skapa Microsoft-konton.

När du har lagt till Google som ett av programmets inloggnings alternativ på **inloggnings** sidan kan en användare bara ange det e-postmeddelande som de använder för att logga in på Google, eller välja **inloggnings alternativ** och välja **Logga in med Google**. I båda fallen omdirigeras de till Google-inloggnings sidan för autentisering.

![Inloggnings alternativ för Google-användare](media/google-federation/sign-in-with-google-overview.png)

> [!NOTE]
> Google Federation är särskilt utformat för Gmail-användare. Använd [direkt Federation](direct-federation.md)för att federera med G Suite-domäner.

> [!IMPORTANT]
> Från och med den **4 januari 2021** är Google [inaktuell WebView-inloggning support](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Om du använder Google Federation eller självbetjänings registrering med Gmail bör du [testa dina verksamhetsbaserade interna program för kompatibilitet](google-federation.md#deprecation-of-webview-sign-in-support).

## <a name="what-is-the-experience-for-the-google-user"></a>Vad är upplevelsen av Google User?

När en Google-användare löser din inbjudan varierar deras upplevelse beroende på om de redan är inloggade på Google:

- Gäst användare som inte är inloggade på Google uppmanas att göra det.
- Gäst användare som redan är inloggade på Google uppmanas att välja det konto de vill använda. De måste välja det konto som du använde för att bjuda in dem.

Gäst användare som ser ett "sidhuvud för lång"-fel kan ta bort sina cookies eller öppna ett privat eller Incognito-fönster och försöka logga in igen.

![Skärm bild som visar Google-inloggnings sidan.](media/google-federation/google-sign-in.png)

## <a name="sign-in-endpoints"></a>Inloggnings slut punkter

Google gäst användare kan nu logga in till din klient-eller Microsoft-appar från första part genom att använda en [gemensam slut punkt](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint) (med andra ord en allmän App-URL som inte innehåller din klient kontext). Under inloggnings processen väljer gäst användaren **inloggnings alternativ** och väljer sedan logga in i **en organisation**. Användaren skriver sedan namnet på din organisation och fortsätter att logga in med sina Google-autentiseringsuppgifter.

Google gäst användare kan också använda program slut punkter som innehåller din klient information, till exempel:

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

Du kan också ge Google gäst användare en direkt länk till ett program eller en resurs genom att inkludera din klient information, till exempel `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>` .

## <a name="deprecation-of-webview-sign-in-support"></a>Utfasning av webbvyer-inloggnings stöd

Från och med 4 januari 2021 är Google [föråldrad inbäddad WebView-inloggnings support](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Om du använder Google Federation eller [självbetjänings registrering med Gmail](identity-providers.md)bör du testa dina verksamhetsbaserade interna program för kompatibilitet. Om dina appar innehåller WebView-innehåll som kräver autentisering kan inte Google Gmail-användare autentisera sig. Här följer några kända scenarier som påverkar Gmail-användare:

- Windows-appar som använder inbäddad webbvy eller WebAccountManager (WAM) i äldre versioner av Windows.
- Andra inbyggda appar som du har utvecklat som använder ett inbäddat webb läsar ramverk för autentisering.

Den här ändringen påverkar inte:

- Windows-appar som använder inbäddad webbvy eller WebAccountManager (WAM) i de senaste versionerna av Windows
- Microsoft iOS-appar
- G Suite-identiteter, till exempel när du använder SAML-baserad [direkt Federation](direct-federation.md) med G Suite

Vi fortsätter att testa olika plattformar och scenarier och kommer att uppdatera den här artikeln.
### <a name="to-test-your-apps-for-compatibility"></a>Testa appar för kompatibilitet

1. Följ [Googles vägledning](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html) för att avgöra om dina appar påverkas.
2. Använd Fiddler eller något annat test verktyg och mata in ett sidhuvud under inloggningen och Använd en Google external-identitet för att testa inloggningen:

   1. Lägg till Google-Accounts-check-OAuth-login: true till dina HTTP-begärandehuvuden när begär Anden skickas till accounts.google.com.
   1. Försök att logga in i appen genom att ange en Gmail-adress på accounts.google.com-inloggnings sidan.
   1. Om inloggningen Miss lyckas och ett fel meddelande visas, till exempel "den här webbläsaren eller appen är kanske inte säker", kommer dina Google external-identiteter att blockeras från att logga in.

3. Lös problemet genom att göra något av följande:

   - Om din Windows-app använder inbäddad webbvy eller WebAccountManager (WAM) på en äldre version av Windows, uppdatera till den senaste versionen av Windows.
   - Ändra dina appar så att de använder systemets webbläsare för inloggning. Mer information finns i avsnittet [Embedded vs system Web UI](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui) i MSAL.net-dokumentationen.  


## <a name="step-1-configure-a-google-developer-project"></a>Steg 1: Konfigurera ett Google Developer-projekt
Börja med att skapa ett nytt projekt i Google Developer-konsolen för att hämta ett klient-ID och en klient hemlighet som du senare kan lägga till i Azure Active Directory (Azure AD). 
1. Gå till Google-API: erna på https://console.developers.google.com och logga in med ditt Google-konto. Vi rekommenderar att du använder ett delat team Google-konto.
2. Godkänn villkoren för tjänsten om du uppmanas att göra det.
3. Skapa ett nytt projekt: Välj projekt listan i det övre vänstra hörnet på sidan och välj sedan **nytt projekt** på sidan **Välj ett projekt** .
4. På sidan **nytt projekt** , ge projektet ett namn (till exempel **Azure AD B2B**) och välj sedan **skapa**: 
   
   ![Skärm bild som visar en ny projekt sida.](media/google-federation/google-new-project.png)

4. På sidan **API: er & tjänster** väljer du **Visa** under det nya projektet.

5. Välj **gå till API: översikt** på API-kortet. **Skärmen Välj OAuth-medgivande**.

6. Välj **extern** och välj sedan **skapa**. 

7. På **skärmen OAuth-godkännande** anger du ett **program namn**:

   ![Skärm bild som visar skärmen för Google OAuth-godkännande.](media/google-federation/google-oauth-consent-screen.png)

8. Bläddra till avsnittet **godkända domäner** och ange **microsoftonline.com**:

   ![Skärm bild som visar avsnittet godkända domäner.](media/google-federation/google-oauth-authorized-domains.PNG)

9. Välj **Spara**.

10. Välj **Autentiseringsuppgifter**. På menyn **skapa autentiseringsuppgifter** väljer du **OAuth-klient-ID**:

    ![Skärm bild som visar menyn Google-API: er skapa autentiseringsuppgifter.](media/google-federation/google-api-credentials.png)

11. Under **program typ** väljer du **webb program**. Ge programmet ett lämpligt namn, t. ex. **Azure AD B2B**. Ange följande URI: er under **auktoriserade omdirigerings-URI**: er:
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>(där `<tenant ID>` är ditt klient-ID)
   
    > [!NOTE]
    > Du hittar klient-ID: t genom att gå till [Azure Portal](https://portal.azure.com). Under **Azure Active Directory** väljer du **Egenskaper** och kopierar **klient-ID: t**.

    ![Skärm bild som visar avsnittet behöriga omdirigerings-URI: er.](media/google-federation/google-create-oauth-client-id.png)

12. Välj **Skapa**. Kopiera klient-ID och klient hemlighet. Du ska använda dem när du lägger till identitets leverantören i Azure Portal.

    ![Skärm bild som visar OAuth-klient-ID och klient hemlighet.](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Steg 2: konfigurera Google Federation i Azure AD 
Du kommer nu att ange Google-klient-ID och klient hemlighet. Du kan använda Azure Portal eller PowerShell för att göra det. Se till att testa din Google Federation-konfiguration genom att bjuda in dig själv. Använd en Gmail-adress och försök att lösa in inbjudan med ditt inbjudna Google-konto. 

**Konfigurera Google Federation i Azure Portal** 
1. Gå till [Azure-portalen](https://portal.azure.com). I den vänstra rutan väljer du **Azure Active Directory**. 
2. Välj **externa identiteter**.
3. Välj **alla identitets leverantörer** och välj sedan **Google** -knappen.
4. Ange klient-ID och klient hemlighet som du fick tidigare. Välj **Spara**: 

   ![Skärm bild som visar sidan Lägg till Google Identity Provider.](media/google-federation/google-identity-provider.png)

**Konfigurera Google Federation med PowerShell**
1. Installera den senaste versionen av Azure AD PowerShell för Graph module ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Kör det här kommandot: `Connect-AzureAD`
3. Logga in med det hanterade globala administratörs kontot vid inloggnings meddelandet.  
4. Kör följande kommando: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId <client ID> -ClientSecret <client secret>`
 
   > [!NOTE]
   > Använd klient-ID och klient hemlighet från den app som du skapade i "steg 1: Konfigurera ett Google Developer-projekt". Mer information finns i [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview). 
 
## <a name="how-do-i-remove-google-federation"></a>Hur gör jag för att ta bort Google Federation?
Du kan ta bort din Google Federation-installation. Om du gör det kommer Google gäst användare som redan har löst sin inbjudan att kunna logga in. Men du kan ge dem åtkomst till dina resurser igen genom att ta bort dem från katalogen och bjuda in dem igen. 
 
**Ta bort Google Federation i Azure AD-portalen**
1. Gå till [Azure-portalen](https://portal.azure.com). I den vänstra rutan väljer du **Azure Active Directory**. 
2. Välj **externa identiteter**.
3. Välj **alla identitets leverantörer**.
4. På **Google** -raden väljer du knappen med tre punkter (**...**) och väljer sedan **ta bort**. 
   
   ![Skärm bild som visar knappen Ta bort för den sociala identitets leverantören.](media/google-federation/google-social-identity-providers.png)

1. Välj **Ja** för att bekräfta borttagningen. 

**Ta bort Google Federation med PowerShell** 
1. Installera den senaste versionen av Azure AD PowerShell för Graph module ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Kör `Connect-AzureAD`.  
4. Logga in med det hanterade globala administratörs kontot i inloggnings meddelandet.  
5. Ange följande kommando:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Mer information finns i [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview).
