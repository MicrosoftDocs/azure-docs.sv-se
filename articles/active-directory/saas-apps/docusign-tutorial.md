---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med DocuSign | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning (SSO) mellan Azure Active Directory och DocuSign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2021
ms.author: jeedes
ms.openlocfilehash: d8c94bfaeb3438ef54e745505e51442d6d391929
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643006"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med DocuSign

I den här självstudien får du lära dig hur du integrerar DocuSign med Microsoft Azure Active Directory (Azure AD). När du integrerar DocuSign med Azure AD kan du:

* Använd Azure AD för att kontrol lera vem som har åtkomst till DocuSign.
* Aktivera automatisk inloggning till DocuSign för dina användare via deras Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En DocuSign-prenumeration som är enkel inloggning aktive rad (SSO).

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö för att kontrol lera att:

* DocuSign stöder Service Provider **SP** -initierad SSO.

* DocuSign stöder **just-in-Time-** etablering av användare.

* DocuSign stöder [Automatisk användar etablering](./docusign-provisioning-tutorial.md).

## <a name="adding-docusign-from-the-gallery"></a>Lägga till DocuSign från galleriet

Om du vill konfigurera integreringen av DocuSign i Azure AD måste du lägga till DocuSign från galleriet i listan över hanterade SaaS-appar:

1. Logga in på Azure Portal med ett arbets-eller skol konto, eller genom att använda en personlig Microsoft-konto.
1. I navigerings fönstret till vänster väljer du tjänsten **Azure Active Directory** .
1. Gå till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **DocuSign** i sökrutan.
1. Välj **DocuSign** på panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-docusign"></a>Konfigurera och testa Azure AD SSO för DocuSign

Konfigurera och testa Azure AD SSO med DocuSign med hjälp av en test användare med namnet **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och motsvarande användare i DocuSign.

Utför följande steg för att konfigurera och testa Azure AD SSO med DocuSign:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso) så att användarna kan använda den här funktionen.
    1. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning i Azure AD med B. Simon.
    1. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) att aktivera B. Simon för att använda enkel inloggning i Azure AD.
1. [Konfigurera DOCUSIGN SSO](#configure-docusign-sso) för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. [Skapa en DocuSign-test användare](#create-docusign-test-user) för att generera en motsvarighet till B. Simon i DocuSign som är länkad till Azure AD-representation av användaren.
1. [Testa SSO](#test-sso) för att verifiera att konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera Azure AD SSO i Azure Portal:

1. I Azure Portal går du till sidan för program integrering i **DocuSign** , letar reda på avsnittet **Hantera** och väljer sedan **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** följer du dessa steg:

    a. I text rutan för **inloggnings-URL** anger du en URL med hjälp av följande mönster:

    `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. I text rutan **identifierare (enhets-ID)** anger du en URL med hjälp av följande mönster:

    `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    c. I text rutan **svars-URL** anger du någon av följande URL-mönster:
    
    | Svars-URL |
    |-------------|
    | Produktions |
    | `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/<IDPID>` |
    | `https://<subdomain>.docusign.net/SAML/` |
    | Frågor och svar-instans:|
    | `https://<SUBDOMAIN>.docusign.com/organizations/saml2` |

    > [!NOTE]
    > Dessa hakparenteser är plats hållare. Ersätt dem med värdena i den faktiska inloggnings-URL: en, identifierare och svars-URL. Informationen beskrivs i avsnittet "Visa SAML 2,0-slutpunkter" senare i den här självstudien.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **certifikat (base64)**. Välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera DocuSign** kopierar du lämplig URL (eller URL: er) baserat på dina krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare med namnet B. Simon i Azure Portal.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Överst på skärmen väljer du **ny användare**.
1. I **användar** egenskaperna följer du de här stegen:
   1. I fältet **namn** anger du **B. Simon**.  
   1. I fältet **användar namn** anger du `<username>@<companydomain>.<extension>` . Exempel: `B.Simon@contoso.com`.
   1. Markera kryss rutan **Visa lösen ord** och anteckna värdet som visas i rutan **lösen ord** .
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet beviljar du B. Simon-åtkomst till DocuSign så att den här användaren kan använda enkel inloggning i Azure.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **DocuSign**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan **användare** och trycker sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. I dialog rutan **Lägg till tilldelning** väljer du knappen **tilldela** .

## <a name="configure-docusign-sso"></a>Konfigurera DocuSign SSO

1. Om du vill automatisera konfigurationen i DocuSign måste du installera webb läsar tillägget Mina appar med säker inloggning genom att välja **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren väljer du **Setup DocuSign**. Du är dirigerad till DocuSign-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på DocuSign. Webb läsar tillägget konfigurerar automatiskt programmet och automatiserar steg 3 till 5.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera DocuSign manuellt öppnar du ett nytt webbläsarfönster och loggar in på din DocuSign-företags webbplats som administratör.

4. I det övre högra hörnet på sidan väljer du profil logo typ och väljer sedan **gå till administratör**.
  
    ![Gå till administratör under profil][51]

5. På sidan domän lösningar väljer du **domäner**.

    ![Domän lösningar/domäner][50]

6. I avsnittet **domäner** väljer du **anspråks domän**.

    ![Anspråk domän alternativ][52]

7. I dialog rutan Ange **en domän** i dialog rutan **domän namn** anger du din företags domän och väljer sedan **anspråk**. Kontrol lera att du verifierar domänen och att dess status är aktiv.

    ![Anspråks dialog rutan för domän/domän namn][53]

8. På sidan domän lösningar väljer du **identitets leverantörer**.
  
    ![Alternativet identitets leverantörer][54]

9. I avsnittet **identitets leverantörer** väljer du **Lägg till identitets leverantör**.

    ![Alternativet Lägg till identitetsprovider][55]

10. Följ dessa steg på sidan **Inställningar för identitetsprovider** :

    ![Fält för identitets leverantörs inställningar][56]

    a. I rutan **namn** anger du ett unikt namn för din konfiguration. Använd inte blank steg.

    b. I **rutan utfärdande av identitets leverantör** klistrar du in värdet för **Azure AD-identifieraren** , som du kopierade från Azure Portal.

    c. I rutan **inloggnings webb adress för identitets leverantör** klistrar du in värdet för **inloggnings-URL** , som du kopierade från Azure Portal.

    d. I rutan **Logga in URL för identitets leverantör** klistrar du in värdet för **URL för utloggning** som du kopierade från Azure Portal.

    e. Välj **signera authn-begäran**.

    f. För **Skicka authn-begäran av** väljer du **post**.

    ex. För **Skicka utloggnings förfrågan från** väljer du **Hämta**.

    h. I avsnittet **Mappning av anpassade attribut** väljer du **Lägg till ny mappning**.

       ![GRÄNSSNITT för mappning av anpassade attribut][62]

    i. Välj det fält som du vill mappa till Azure AD-anspråket. I det här exemplet mappas **EmailAddress** -anspråket till värdet `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` . Det är standard anspråks namnet från Azure AD för e-postanspråket. Välj **Spara**.

       ![Mappnings fält för anpassat attribut][57]

       > [!NOTE]
       > Använd rätt **användar identifierare** för att mappa användaren från Azure AD till DocuSign användar mappning. Välj fältet korrekt och ange lämpligt värde baserat på organisations inställningarna.

    j. I avsnittet **certifikat för identitetsprovider** väljer du **Lägg till certifikat**, laddar upp det certifikat som du laddade ned från Azure AD Portal och väljer **Spara**.

       ![Certifikat för identitets leverantör/Lägg till certifikat][58]

    k. I avsnittet **identitets leverantörer** väljer du **åtgärder** och väljer sedan **slut punkter**.

       ![Identitets leverantörer/slut punkter][59]

    l. I avsnittet **Visa SAML 2,0-slutpunkter** i administrations portalen för DocuSign följer du de här stegen:

       ![Visa SAML 2,0-slutpunkter][60]
       
       1. Kopiera **URL: en för service providerns utfärdare** och klistra in den i rutan **identifierare** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.
       
       1. Kopiera **URL: en till tjänste leverantörens intyg om försäkrans konsument tjänst** och klistra in den i rutan **svars-URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.
       
       1. Kopiera **inloggnings-URL: en för tjänst leverantören** och klistra in den i rutan för **INLOGGNINGs-URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal. I slutet av **inloggnings-URL: en för tjänst leverantören** får du IDPID-värdet.

       1. Välj **Stäng**.

### <a name="create-docusign-test-user"></a>Skapa DocuSign test användare

I det här avsnittet skapas en användare som heter B. Simon i DocuSign. DocuSign stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i DocuSign skapas en ny efter autentiseringen.

> [!Note]
> Kontakta [DocuSign support-teamet](https://support.docusign.com/)om du behöver skapa en användare manuellt.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

1. Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till DocuSign-inloggnings-URL där du kan starta inloggnings flödet. 

2. Gå till DocuSign-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

3. Du kan använda Microsoft Mina appar. När du klickar på panelen DocuSign i Mina appar, bör du loggas in automatiskt på den DocuSign som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat DocuSign kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial-docusign-18.png
[51]: ./media/docusign-tutorial/tutorial-docusign-21.png
[52]: ./media/docusign-tutorial/tutorial-docusign-22.png
[53]: ./media/docusign-tutorial/tutorial-docusign-23.png
[54]: ./media/docusign-tutorial/tutorial-docusign-19.png
[55]: ./media/docusign-tutorial/tutorial-docusign-20.png
[56]: ./media/docusign-tutorial/tutorial-docusign-24.png
[57]: ./media/docusign-tutorial/tutorial-docusign-25.png
[58]: ./media/docusign-tutorial/tutorial-docusign-26.png
[59]: ./media/docusign-tutorial/tutorial-docusign-27.png
[60]: ./media/docusign-tutorial/tutorial-docusign-28.png
[61]: ./media/docusign-tutorial/tutorial-docusign-29.png
[62]: ./media/docusign-tutorial/tutorial-docusign-30.png