---
title: 'Självstudie: Azure Active Directory integrering med SharePoint lokalt | Microsoft Docs'
description: Läs hur du konfigurerar enkel inloggning mellan Azure Active Directory och lokal SharePoint.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: miguego
ms.openlocfilehash: 996668751ee93d14b18e399035fd345f32c58fbe
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552181"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-sharepoint-on-premises"></a>Självstudie: Azure Active Directory integration med enkel inloggning med SharePoint lokalt

I den här självstudien får du lära dig hur du integrerar lokal SharePoint med Azure Active Directory (Azure AD). När du integrerar SharePoint lokalt med Azure AD kan du:

* Kontrol lera vem som har åtkomst till SharePoint lokalt i Azure AD.
* Gör det möjligt för användarna att logga in automatiskt till SharePoint lokalt med deras Azure AD-konton.
* Hantera dina konton i Azure Portal.

Mer information om SaaS-appar (Software as a Service) med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SharePoint lokalt behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En SharePoint 2013-Server grupp eller senare.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning (SSO) för Azure AD i en test miljö. Användare från Azure AD kan komma åt din SharePoint lokalt.

## <a name="create-enterprise-applications-in-the-azure-portal"></a>Skapa företags program i Azure Portal

Om du vill konfigurera integrering av lokal SharePoint i Azure AD så behöver du lägga till lokal SharePoint från galleriet i din lista över hanterade SaaS-appar.

Så här lägger du till SharePoint lokalt från galleriet:

1. Välj **Azure Active Directory**i rutan till vänster i rutan [Azure Portal](https://portal.azure.com).

   > [!NOTE]
   > Om elementet inte är tillgängligt kan du också öppna det via länken **alla tjänster** längst upp i fönstret längst till vänster. I följande översikt finns **Azure Active Directory** länken i avsnittet **identitet** . Du kan också söka efter den med hjälp av filter rutan.

1. Gå till **företags program**och välj sedan **alla program**.

1. Lägg till ett nytt program genom att välja **Nytt program** längst upp i dialogrutan.

1. Ange **SharePoint lokalt**i rutan Sök. Välj **SharePoint lokalt** i resultat fönstret.

    <kbd>![Lokal SharePoint i resultatlistan](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. Ange ett namn för din lokala SharePoint-instans och välj **Lägg** till för att lägga till programmet.

1. I det nya företags programmet väljer du **Egenskaper**och kontrollerar värdet för **användar tilldelning krävs?**.

   <kbd>![Krävs användar tilldelning? granska](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

   I det här scenariot anges värdet **Nej**.

## <a name="configure-and-test-azure-ad"></a>Konfigurera och testa Azure AD

I det här avsnittet konfigurerar du Azure AD SSO med SharePoint lokalt. För att SSO ska fungera upprättar du en länk relation mellan en Azure AD-användare och den relaterade användaren i SharePoint lokalt.

Om du vill konfigurera och testa Azure AD SSO med SharePoint lokalt, fyller du i dessa Bygg stenar:

- [Konfigurera enkel inloggning i Azure AD](#configure-azure-ad-single-sign-on) så att användarna kan använda den här funktionen.
- [Konfigurera SharePoint lokalt](#configure-sharepoint-on-premises) för att konfigurera SSO-inställningar på program sidan.
- [Skapa en Azure AD test-användare i Azure Portal](#create-an-azure-ad-test-user-in-the-azure-portal) för att skapa en ny användare i Azure AD för SSO.
- [Skapa en Azure AD-säkerhetsgrupp i Azure Portal](#create-an-azure-ad-security-group-in-the-azure-portal) för att skapa en ny säkerhets grupp i Azure AD för SSO.
- Ge behörighet till en Azure AD-användare genom att [bevilja behörigheter till ett Azure AD-konto i SharePoint lokalt](#grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises) .
- Bevilja behörighet till en Azure [AD-grupp i SharePoint lokalt](#grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises) för att ge behörighet till en Azure AD-grupp.
- [Bevilja åtkomst till ett gäst konto till SharePoint lokalt i Azure Portal](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal) för att ge behörighet till ett gäst konto i Azure AD för SharePoint lokalt.
- [Konfigurera den betrodda identitets leverantören för flera webb program](#configure-the-trusted-identity-provider-for-multiple-web-applications) att använda samma betrodda identitets leverantör för flera webb program.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du Azure AD SSO i Azure Portal.

Så här konfigurerar du Azure AD SSO med SharePoint lokalt:

1. I [Azure Portal](https://portal.azure.com/)väljer du **Azure Active Directory**  >  **företags program**. Välj det tidigare skapade företags program namnet och välj **enkel inloggning**.

1. I dialog rutan **Välj metod för enkel inloggning** väljer du **SAML** -läget för att aktivera SSO.
 
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du ikonen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

1. I avsnittet **grundläggande SAML-konfiguration** följer du dessa steg:

    ![SSO-information för SharePoint lokal domän och URL-adresser](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. I rutan **identifierare** anger du en URL med hjälp av det här mönstret: `urn:<sharepointFarmName>:<federationName>` .

    1. I rutan **svars-URL** anger du en URL med hjälp av det här mönstret: `https://<YourSharePointSiteURL>/_trust/` .

    1. I rutan **inloggnings-URL** anger du en URL med hjälp av det här mönstret: `https://<YourSharePointSiteURL>/` .
    1. Välj **Spara**.

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL: en, identifierare och svars-URL.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du **Hämta** för att ladda ned **certifikatet (base64)** från de angivna alternativen utifrån dina behov och sparar dem på din dator.

    ![Länken Hämta certifikat](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

1. I avsnittet **Konfigurera SharePoint lokalt** kopierar du lämpliga URL: er baserat på ditt krav:
    
    - **Inloggnings-URL**
    
        Kopiera inloggnings-URL: en och Ersätt **/SAML2** i slutet med **/wsfed** så att den ser ut https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed . (URL: en är inte korrekt.)

    - **Azure AD-identifierare**
    - **Utloggnings-URL**

    > [!NOTE]
    > Den här URL: en kan inte användas i SharePoint. Du måste ersätta **/SAML2** med **/wsfed**. Det lokala SharePoint-programmet använder en SAML 1,1-token, så Azure AD förväntar sig en WS-begäran från SharePoint-servern. Efter autentiseringen utfärdar den SAML 1,1-token.

### <a name="configure-sharepoint-on-premises"></a>Konfigurera SharePoint lokalt

1. Skapa en ny betrodd identitets leverantör i SharePoint Server 2016.

    Logga in på SharePoint-servern och öppna hanterings gränssnittet för SharePoint. Fyll i värdena:
    - **$Realm** är ID-värdet från avsnittet SharePoint-lokal domän och URL: er i Azure Portal.
    - **$wsfedurl** är URL: en för SSO-tjänsten.
    - **$filepath** är sökvägen till den fil som du har laddat ned certifikat filen från Azure Portal.

    Kör följande kommandon för att konfigurera en ny betrodd identitets leverantör.

    > [!TIP]
    > Om PowerShell är nytt för dig eller om du vill veta mer om hur PowerShell fungerar, se [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps).


    ```
    $realm = "urn:sharepoint:sps201x"
    $wsfedurl="https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed"
    $filepath="C:\temp\SharePoint 2019 OnPrem.cer"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map1 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "Azure AD SharePoint server 201x" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map1,$map2 -SignInUrl $wsfedurl -IdentifierClaim $map1.InputClaimType
    ```
1. Aktivera den betrodda identitets leverantören för ditt program.

    1. I **Central administration**, gå till **Hantera webb program** och välj det webb program som du vill skydda med Azure AD.

    1. Välj **autentiseringsproviders** i menyfliksområdet och välj den zon som du vill använda.

    1. Välj **betrodd identitetsprovider**och välj den identifierande provider som du precis registrerade med namnet *AzureAD*.

    1. Välj **OK**.

    ![Konfigurera din autentiseringsprovider](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Skapa en Azure AD test-användare i Azure Portal

Syftet med det här avsnittet är att skapa en test användare i Azure Portal.

1. Välj **Azure Active Directory**i rutan till vänster i rutan Azure Portal. I fönstret **Hantera** väljer **du användare**.

1. Välj **alla användare**  >  **ny användare** överst på skärmen.

1. Välj **skapa användare**och följ de här stegen i användar egenskaperna. Du kanske kan skapa användare i din Azure AD med hjälp av ditt klient certifikat eller en verifierad domän. 

    1. I rutan **namn** anger du användar namnet. Vi använde **TestUser**.
  
    1. I rutan **Användarnamn** anger du `TestUser@yourcompanydomain.extension`. Det här exemplet visar `TestUser@contoso.com` .

       ![Dialogrutan Användare](./media/sharepoint-on-premises-tutorial/user-properties.png)

    1. Markera kryss rutan **Visa lösen ord** och skriv sedan ned värdet som visas i rutan **lösen ord** .

    1. Välj **Skapa**.

    1. Du kan nu dela platsen med TestUser@contoso.com och ge den här användaren åtkomst till den.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Skapa en Azure AD-säkerhetsgrupp i Azure Portal

1. Välj **Azure Active Directory**  >  **grupper**.

1. Välj **ny grupp**.

1. Fyll i rutorna **grupp typ**, **grupp namn**, **grupp Beskrivning**och **medlemskaps typ** . Välj pilarna för att välja medlemmar och Sök sedan efter eller Välj de medlemmar som du vill lägga till i gruppen. Välj **Välj** för att lägga till de valda medlemmarna och välj sedan **skapa**.

![Skapa en Azure AD-säkerhetsgrupp](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises"></a>Bevilja behörigheter till ett Azure AD-konto i SharePoint lokalt

Om du vill bevilja åtkomst till en Azure AD-användare i SharePoint lokalt, dela webbplats samlingen eller Lägg till Azure AD-användaren till en av webbplats samlingens grupper. Användare kan nu logga in på SharePoint-201x med hjälp av identiteter från Azure AD, men det finns fortfarande möjligheter att förbättra användar upplevelsen. Om du till exempel söker efter en användare så visas flera sökresultat i personväljaren. Det finns ett Sök Resultat för var och en av de anspråks typer som skapas i anspråks mappningen. Om du vill välja en användare med hjälp av person väljaren måste du ange användar namnet exakt och välja resultatet för **namn** anspråk.

![Anspråkssökresultat](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

Det finns ingen validering av de värden som du söker efter, vilket kan leda till fel stavningar eller användare av misstag väljer fel anspråks typ. Den här situationen kan hindra användare från att få åtkomst till resurser.

En lösning med öppen källkod som heter [AzureCP](https://yvand.github.io/AzureCP/) tillhandahåller en anpassad anspråks leverantör för SharePoint 2013, 2016 och 2019 för att åtgärda det här scenariot med person väljaren. Den använder Microsoft Graph-API: et för att lösa vad användare anger och utför verifiering. Mer information finns i [AzureCP](https://yvand.github.io/AzureCP/).

  > [!NOTE]
  > Utan AzureCP kan du lägga till grupper genom att lägga till Azure AD-gruppens ID, men den här metoden är inte användarvänlig och tillförlitlig. Så här ser det ut:
  > 
  >![Lägga till en Azure AD-grupp i en SharePoint-grupp](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises"></a>Bevilja behörighet till en Azure AD-grupp i SharePoint lokalt

Om du vill tilldela Azure AD-säkerhetsgrupper till SharePoint lokalt, måste du använda en anpassad anspråks leverantör för SharePoint Server. I det här exemplet används AzureCP.

 > [!NOTE]
 > AzureCP är inte en Microsoft-produkt och stöds inte av Microsoft Support. Information om hur du hämtar, installerar och konfigurerar AzureCP i den lokala SharePoint-servergruppen finns på [AzureCP](https://yvand.github.io/AzureCP/) -webbplatsen. 

1. Konfigurera AzureCP i den lokala SharePoint-gruppen eller en annan lösning för anspråks leverantörer. Information om hur du konfigurerar AzureCP finns på den här [AzureCP](https://yvand.github.io/AzureCP/Register-App-In-AAD.html) -webbplatsen.

1. I Azure Portal väljer du **Azure Active Directory**  >  **företags program**. Välj det tidigare skapade företags program namnet och välj **enkel inloggning**.

1. På sidan **Konfigurera enkel inloggning med SAML** redigerar du avsnittet **användar attribut &-anspråk** .

1. Välj **Lägg till ett grupp anspråk**.

1. Välj vilka grupper som är associerade med användaren som ska returneras i anspråket. I det här fallet väljer du **alla grupper**. I avsnittet **källattribut** väljer du **grupp-ID** och väljer **Spara**.

Om du vill bevilja åtkomst till Azure AD-säkerhetsgruppen i SharePoint lokalt, dela webbplats samlingen eller Lägg till Azure AD-säkerhetsgruppen i en av webbplats samlingens grupper.

1. Bläddra till **SharePoint**-webbplatssamling. Under **plats inställningar** för webbplats samlingen väljer du **personer och grupper**. 

1. Välj SharePoint-gruppen och välj sedan **ny**  >  **Lägg till användare i den här gruppen**. När du skriver namnet på din grupp visar person väljaren Azure AD-säkerhetsgruppen.

    ![Lägga till en Azure AD-grupp i en SharePoint-grupp](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>Bevilja åtkomst till ett gäst konto till SharePoint lokalt i Azure Portal

Du kan bevilja åtkomst till din SharePoint-webbplats till ett gäst konto på ett konsekvent sätt eftersom UPN nu ändras. Användaren visas till exempel `jdoe@outlook.com` som `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com` . Om du vill dela din webbplats med externa användare måste du lägga till vissa ändringar i avsnittet **användarattribut &-anspråk** i Azure Portal.

1. I Azure Portal väljer du **Azure Active Directory**  >  **företags program**. Välj det tidigare skapade företags program namnet och välj **enkel inloggning**.

1. På sidan **Konfigurera enkel inloggning med SAML** redigerar du avsnittet **användar attribut &-anspråk** .

1. I den **obligatoriska anspråks** zonen väljer du **unik användar identifierare (namn-ID)**.

1. Ändra egenskapen **Source Attribute** till värdet **User. Localuserprincipalname**och välj **Spara**.

    ![Användarattribut & anspråk initialt](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. Använd menyfliksområdet och gå tillbaka till **SAML-baserad inloggning**. Nu ser avsnittet **användarattribut &-anspråk** ut så här: 

    ![Användarattribut & anspråk slutligt](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)

    > [!NOTE]
    > Det krävs inget efter namn och angivet namn i den här konfigurationen.

1. I rutan Azure Portal väljer du **Azure Active Directory** i rutan längst till vänster och väljer sedan **användare**.

1. Välj **ny gäst användare**.

1. Välj alternativet **Bjud in användare** . Fyll i användar egenskaperna och välj **Bjud in**.

1. Du kan nu dela platsen med MyGuestAccount@outlook.com och ge den här användaren åtkomst till den.

    ![Dela en webbplats med ett gäst konto](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configure-the-trusted-identity-provider-for-multiple-web-applications"></a>Konfigurera den betrodda identitets leverantören för flera webb program

Konfigurationen fungerar för ett enda webb program, men ytterligare konfiguration behövs om du tänker använda samma betrodda identitets leverantör för flera webb program. Anta till exempel att du har utökat ett webb program till att använda URL: en `https://sales.contoso.com` och att du nu vill autentisera användare `https://marketing.contoso.com` . Det gör du genom att uppdatera identitets leverantören så att den följer parametern WReply och uppdatera program registreringen i Azure AD för att lägga till en svars-URL.

1. I Azure Portal väljer du **Azure Active Directory**  >  **företags program**. Välj det tidigare skapade företags program namnet och välj **enkel inloggning**.

1. På sidan **Konfigurera enkel inloggning med SAML** redigerar du **grundläggande SAML-konfiguration**.

    ![Grundläggande SAML-konfiguration](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

1. För **svars-URL: en (intyg om mottagar tjänstens URL)** lägger du till URL: en för ytterligare webb program och väljer **Spara**.

    ![Redigera den grundläggande SAML-konfigurationen](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

1. Öppna hanterings gränssnittet för SharePoint-201x på SharePoint-servern och kör följande kommandon. Använd namnet på den betrodda identitets-token som du använde tidigare.
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
1. I **Central administration**går du till webb programmet och aktiverar den befintliga betrodda identitets leverantören.

Du kanske har andra scenarier där du vill ge åtkomst till din lokala SharePoint-instans för dina interna användare. I det här scenariot måste du distribuera Microsoft Azure Active Directory Connect för att kunna synkronisera dina lokala användare med Azure AD. Den här installationen beskrivs i en annan artikel.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)
- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Vad är hybrid identitet med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
