---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Boomi | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Boomi.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/07/2020
ms.author: jeedes
ms.openlocfilehash: 8f6c815bf6b8be6d280dcdc60401f7a91e171e66
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88542767"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Boomi

I den här självstudien får du lära dig hur du integrerar Boomi med Azure Active Directory (Azure AD). När du integrerar Boomi med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Boomi.
* Gör det möjligt för användarna att logga in automatiskt till Boomi med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Boomi för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Boomi stöder **IDP**-initierad enkel inloggning
* När du har konfigurerat Boomi kan du framtvinga sessionsnycklar som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-boomi-from-the-gallery"></a>Lägga till Boomi från galleriet

För att konfigurera integreringen av Boomi till Azure AD behöver du lägga till Boomi från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Boomi** i sökrutan.
1. Välj **Boomi** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-boomi"></a>Konfigurera och testa enkel inloggning med Azure AD för Boomi

Konfigurera och testa Azure AD SSO med Boomi med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Boomi.

Om du vill konfigurera och testa Azure AD SSO med Boomi, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera BOOMI SSO](#configure-boomi-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa Boomi test User](#create-boomi-test-user)** -om du vill ha en motsvarighet till B. Simon i Boomi som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Boomi** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du har en **metadata-fil för Service Provider** och vill konfigurera i **IDP** initierat läge, utför du följande steg:

    a. Klicka på **Ladda upp metadatafil**.

    ![Ladda upp metadatafil](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![välj metadatafil](common/browse-upload-metadata.png)

    c. När metadatafilen har laddats upp, fylls **ID** och **svars-URL** -värden automatiskt i avsnittet grundläggande SAML-konfiguration.

    ![image](common/idp-intiated.png)

    > [!Note]
    > Du hämtar **metadata-filen för tjänst leverantören** från avsnittet **Konfigurera Boomi SSO** , som beskrivs senare i självstudien. Om värdena **Identifierare** och **Svars-URL** inte fylls i automatiskt fyller du i värdena manuellt enligt dina krav.

1. Boomi-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig Boomi-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name |  Källattribut|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Boomi** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Boomi.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I programlistan väljer du **Boomi**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-boomi-sso"></a>Konfigurera Boomi SSO

1. Logga in på din Boomi-företags webbplats som administratör i ett annat webbläsarfönster.

1. Gå till **Företagsnamn** och sedan till **Konfigurera**.

1. Klicka på fliken **SSO Options** (SSO-alternativ) och utför stegen nedan.

    ![Konfigurera enkel inloggning på appsidan](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Markera kryssrutan **Enable SAML Single Sign-On** (Aktivera enkel inloggning med SAML).

    b. Klicka på **Importera** för att ladda upp det nedladdade certifikatet från Azure AD till **Certifikat för identitetsprovider**.

    c. I textrutan **Inloggnings-URL för identitetsprovider** anger du värdet för **inloggnings-URL** från konfigurationsfönstret för Azure AD-programmet.

    d. För **Federations-ID-plats**väljer du **Federations-ID: t i FEDERATION_ID** alternativ knapp för attribut.

    e. Kopiera URL: en för **AtomSphere metadata**, gå till **URL:** en för metadata via valfri webbläsare och spara utdata till en fil. Ladda upp **URL: en för metadata** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    f. Klicka på knappen **Spara**.

### <a name="create-boomi-test-user"></a>Skapa Boomi-testanvändare

För att Azure AD-användare ska kunna logga in på Boomi måste de tillhandahållas i Boomi. När det gäller Boomi är etablering en manuell aktivitet.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Gör följande för att etablera ett användarkonto:

1. Logga in på din Boomi-företags webbplats som administratör.

1. När du loggat in går du till **Användarhantering** och sedan till **Användare**.

    ![Användare](./media/boomi-tutorial/tutorial_boomi_001.png "Användare")

1. Klicka på **+**  ikonen och dialog rutan **Lägg till/underhålla användar roller** öppnas.

    ![Användare](./media/boomi-tutorial/tutorial_boomi_002.png "Användare")

    ![Användare](./media/boomi-tutorial/tutorial_boomi_003.png "Användare")

    a. I textrutan **User e-mail address** (E-post för användare) skriver du användarens e-postadress som B.Simon@contoso.com.

    b. I text rutan **förnamn** skriver du det första namnet på användaren, t. ex. B.

    c. I text rutan **efter namn** skriver du det senaste namnet på användaren som Simon.

    d. Ange användarens **Federations-ID**. Varje användare ha ett Federation-ID som unikt identifierar användare i kontot.

    e. Tilldela rollen **Standardanvändare** till användaren. Tilldela inte administratörs rollen eftersom detta ger dem normal åtkomst till atmosfär samt enkel inloggning.

    f. Klicka på **OK**.

    > [!NOTE]
    > Användaren får inget e-postmeddelande med ett välkomst meddelande som innehåller ett lösen ord som kan användas för att logga in på AtomSphere-kontot eftersom lösen ordet hanteras via identitets leverantören. Du kan använda andra verktyg eller API:er för Boomi-kontoskapande som tillhandahålls av Boomi för att etablera AAD-användarkonton.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Boomi-panelen i åtkomstpanelen bör du automatiskt loggas in på Boomi som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Prova Boomi med Azure AD](https://aad.portal.azure.com/)
