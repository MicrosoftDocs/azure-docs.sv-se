---
title: 'Självstudie: Azure Active Directory integrering med Palo-nätverk – bländare | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Palo-nätverk – bländare.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 7b4c8ab55a9b2afc9c1a215236fa1276a630a5cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97963629"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Självstudie: Azure Active Directory integrering med Palo-nätverk – bländare

I den här självstudien får du lära dig att integrera Palo-nätverk-bländare med Azure Active Directory (Azure AD).
Integrering av Palo-nätverk – bländare med Azure AD ger följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Palo-nätverk – bländare.
* Du kan göra det möjligt för användarna att logga in automatiskt till Palo-bländare (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Palo-nätverk-bländare behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Palo-nätverk – bländare med enkel inloggning aktive rad öppnings prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Palo-nätverk – bländare stöder **SP** -och **IDP** -initierad SSO

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>Lägga till Palo-nätverk – bländare från galleriet

Om du vill konfigurera integrationen av Palo-nätverk – bländare i Azure AD måste du lägga till Palo-nätverk – bländare från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **Palo-nätverk – bländare** i sökrutan.
1. Välj **Palo-nätverk – bländare** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurera och testa Azure AD SSO

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Palo-nätverk – bländare som baseras på en test användare som kallas **B. Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Palo-nätverk-bländare upprättas.

Utför följande steg för att konfigurera och testa enkel inloggning i Azure AD med Palo-nätverk – bländare:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
2. **[Konfigurera Palo-nätverk – bländare SSO](#configure-palo-alto-networks---aperture-sso)** – om du vill konfigurera enskilda Sign-On inställningar på program sidan.
    * **[Skapa Palo-nätverk-bländare-test användare](#create-palo-alto-networks---aperture-test-user)** – om du vill ha en motsvarighet till Britta Simon i Paload nätverk-bländare som är länkad till Azure AD-representation av användare.
3. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan **Palo-nätverk-bländare-** program integration och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Skärm bild som visar "Basic S A M L-konfiguration" med text rutorna "identifierare" och "svara U R L" och "Spara"-åtgärden markerat.](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Palo-nätverk-bländare-domän och URL enkel inloggning information SP](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Palo-nätverk – bländare-klientens support team](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera Palo-nätverk – bländare** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Palo-nätverk – bländare.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Palo-nätverk-bländare**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-palo-alto-networks---aperture-sso"></a>Konfigurera Palo-nätverk – bländare SSO

1. Logga in på Palo-nätverk-bländare som administratör i ett annat webbläsarfönster.

2. Klicka på **Inställningar** på den översta meny raden.

    ![Fliken Inställningar](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

3. Navigera till **program** avsnittet Klicka på **autentisering** formulär menyns vänstra sida.

    ![Fliken auth](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
4. Utför följande steg på sidan **autentisering** :
    
    ![Fliken autentisering](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. Markera kryss rutan **aktivera enkel inloggning (SSP-providers som stöds är okta, en inloggning)** från fältet **för enkel inloggning** .

    b. I text rutan **ID för identitets leverantör** klistrar du in värdet för **Azure AD-identifieraren**, som du har kopierat från Azure Portal.

    c. Klicka på **Välj fil** för att ladda upp det hämtade certifikatet från Azure AD i certifikat fältet för **identitets leverantören** .

    d. I text rutan **URL för identitetsprovider** , klistrar du in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal.

    e. Läs informationen om IdP från avsnittet om **bländare** och hämta certifikatet från **nyckel fältet bländare** .

    f. Klicka på **Spara**.


### <a name="create-palo-alto-networks---aperture-test-user"></a>Skapa Palo-nätverk – bländare-test användare

I det här avsnittet skapar du en användare med namnet Britta Simon i Palo-nätverk-bländare. Arbeta med [Palo-nätverk – bländare-klientens support team](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) för att lägga till användarna i Palo-nätverkets bländare-plattform. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Palo-nätverk-bländare-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till Palo-nätverk – bländarens inloggnings-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt till Palo-nätverk-bländare som du konfigurerade SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen Palo-nätverk – bländare i Mina appar, om det kon figurer ATS i SP-läge, kommer du att omdirigeras till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt till Palo-nätverket-bländaren som du konfigurerade SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Palo-nätverk – bländare kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).