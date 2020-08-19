---
title: 'Självstudie: Azure Active Directory integrering med GitHub | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och GitHub.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2020
ms.author: jeedes
ms.openlocfilehash: 7153a1279785cfe79d23b2b5ba843ec9f5cd4965
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88550754"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med GitHub

I den här självstudien får du lära dig hur du integrerar GitHub med Azure Active Directory (Azure AD). När du integrerar GitHub med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till din GitHub företags moln organisation.
* Hantera åtkomst till din GitHub företags moln organisation på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med GitHub behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En GitHub-organisation som har skapats i [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) och kräver [GitHub Enterprise-faktureringsplanen](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* GitHub stöder **IDP**-initierad enkel inloggning

* GitHub stöder [ **automatiserad** användar etablering (organisatoriska inbjudningar)](github-provisioning-tutorial.md)
* När du har konfigurerat GitHub kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-github-from-the-gallery"></a>Lägga till GitHub från galleriet

För att konfigurera integreringen av GitHub till Azure AD behöver du lägga till GitHub från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **GitHub** i sökrutan.
1. Välj **GitHub** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-github"></a>Konfigurera och testa enkel inloggning med Azure AD för GitHub

Konfigurera och testa Azure AD SSO med GitHub med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i GitHub.

Om du vill konfigurera och testa Azure AD SSO med GitHub, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera GITHUB SSO](#configure-github-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa GitHub test User](#create-github-test-user)** -om du vill ha en motsvarighet till B. Simon i GitHub som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **GitHub** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

   a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://github.com/orgs/<Organization ID>/sso`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://github.com/orgs/<Organization ID>`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://github.com/orgs/<Organization ID>/saml/consume`


    > [!NOTE]
    > Observera att detta inte är de verkliga värdena. Du måste uppdatera dessa värden med URL: en för faktisk inloggning, identifierare och svars-URL. Här föreslår vi att du använder det unika värdet för strängen i identifieraren. Gå till GitHub-administrationsavsnittet för att hämta dessa värden.

5. GitHub-programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. Följande skärm bild visar en lista över standardattribut, där som **unik användar identifierare (namn-ID)** mappas med **User. UserPrincipalName**. GitHub-programmet förväntar sig att **unika användar-ID: n (namn-ID)** ska mappas med **User. mail**, så du måste redigera mappningen av attributet genom att klicka på ikonen **Redigera** och ändra attributet mappning.

    ![image](common/edit-attribute.png)

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera GitHub** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till GitHub.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I programlistan väljer du **GitHub**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML-kontrollen, i dialog rutan **Välj en roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

    ![användarroll](./media/github-tutorial/user-role.png)

    > [!NOTE]
    > **Välj ett roll** alternativ kommer att inaktive ras och standard rollen är användare för den valda användaren.

7. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-github-sso"></a>Konfigurera GitHub SSO

1. Logga in på din GitHub-organisations webbplats som administratör i ett annat webbläsarfönster.

2. Gå till **Inställningar** och klicka på **Säkerhet**

    ![Inställningar](./media/github-tutorial/security.png)

3. Markera rutan **Aktivera SAML-autentisering**. Då visas konfigurationsfälten för enkel inloggning. utför följande steg:

    ![Inställningar](./media/github-tutorial/saml-sso.png)

    a. Kopiera **URL-värde för enkel inloggning** och klistra in värdet i text rutan för **inloggnings-URL** i den **grundläggande SAML-konfigurationen** i Azure Portal.
    
    b. Kopiera **intygets URL-** värde och klistra in det här värdet i text rutan **SVARs-URL** i den **grundläggande SAML-konfigurationen** i Azure Portal.

4. Konfigurera följande fält:

    ![Inställningar](./media/github-tutorial/configure.png)

    a. I textrutan **Inloggnings-URL** klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

    b. I textrutan **Issuer** (Utfärdare) klistrar du in värdet för den **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    c. Öppna det nedladdade certifikatet från Azure-portalen i Anteckningar och klistra in innehållet i textrutan **Offentligt certifikat**.

    d. Klicka på ikonen **Redigera** för att redigera **Signature Method** (Signaturmetod) och **Digest Method** (Hashmetod) från **RSA-SHA1** och **SHA1** till **RSA-SHA256** och **SHA256** enligt nedan.
    
    e. Uppdatera **intygs mottagar tjänstens URL (svars-URL)** från standard-URL: en, så att den URL: en i GitHub matchar URL: en i Azure App Registration.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. Klicka på **Testa SAML-konfiguration** för att bekräfta att inga verifieringsfel eller andra fel inträffade under enkel inloggning.

    ![Inställningar](./media/github-tutorial/test.png)

6. Klicka på **Spara**

> [!NOTE]
> Enkel inloggning i GitHub autentiserar till en specifik organisation i GitHub och ersätter inte autentisering av själva GitHub. Om användarens github.com-session har upphört att gälla kan det därför hända att du blir ombedd att autentisera med ID/lösenord för GitHub under processen för enkel inloggning.

### <a name="create-github-test-user"></a>Skapa GitHub-testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i GitHub. GitHub stöder automatisk användaretablering, vilket är aktiverat som standard. Du hittar mer information [här](github-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du behöver skapa användare manuellt så gör du följande:**

1. Logga in på din GitHub-företagsplats som administratör.

2. Klicka på **Personer**.

    ![People](./media/github-tutorial/people.png "People")

3. Klicka på **Invite member** (Bjud in medlem).

    ![Bjud in användare](./media/github-tutorial/invite-member.png "Bjud in användare")

4. På dialogrutan **Invite member** (Bjud in medlem) utför du följande steg:

    a. I textrutan **E-post** anger du e-postadressen för Britta Simon-kontot.

    ![Bjud in personer](./media/github-tutorial/email-box.png "Bjud in personer")

    b. Klicka på **Skicka inbjudan**.

    ![Bjud in personer](./media/github-tutorial/send-invitation.png "Bjud in personer")

    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på GitHub-panelen i åtkomstpanelen bör du automatiskt loggas in på GitHub som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova GitHub med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
