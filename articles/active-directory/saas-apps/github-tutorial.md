---
title: 'Självstudie: Azure Active Directory integration med en GitHub företags moln organisation | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och en GitHub företags moln organisation.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: c87f66e48aa7b39a7f3fc5f9b8572dd6925abbc0
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732168"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-a-github-enterprise-cloud-organization"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med en GitHub företags moln organisation

I den här självstudien får du lära dig hur du integrerar en GitHub företags moln **organisation** med Azure Active Directory (Azure AD). När du integrerar en GitHub företags moln organisation med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till din GitHub företags moln organisation.
* Hantera åtkomst till din GitHub företags moln organisation på en central plats – Azure Portal.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med en GitHub företags moln organisation behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En GitHub-organisation som har skapats i [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) och kräver [GitHub Enterprise-faktureringsplanen](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* GitHub stöder **IDP**-initierad enkel inloggning

* GitHub stöder [ **automatiserad** användar etablering (organisatoriska inbjudningar)](github-provisioning-tutorial.md)


## <a name="adding-github-from-the-gallery"></a>Lägga till GitHub från galleriet

För att konfigurera integreringen av GitHub till Azure AD behöver du lägga till GitHub från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **GitHub** i sökrutan.
1. Välj **GitHub Enterprise Cloud-Organization** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-github"></a>Konfigurera och testa Azure AD SSO för GitHub

Konfigurera och testa Azure AD SSO med GitHub med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i GitHub.

Utför följande steg för att konfigurera och testa Azure AD SSO med GitHub:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera GITHUB SSO](#configure-github-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa GitHub test User](#create-github-test-user)** -om du vill ha en motsvarighet till B. Simon i GitHub som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **GitHub** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

   a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://github.com/orgs/<Organization ID>/sso`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://github.com/orgs/<Organization ID>`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://github.com/orgs/<Organization ID>/saml/consume`


    > [!NOTE]
    > Observera att detta inte är de verkliga värdena. Du måste uppdatera dessa värden med URL: en för faktisk inloggning, identifierare och svars-URL. Här föreslår vi att du använder det unika värdet för strängen i identifieraren. Gå till GitHub-administrationsavsnittet för att hämta dessa värden.

5. GitHub-programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. Följande skärm bild visar en lista över standardattribut, där som **unik användar identifierare (namn-ID)** mappas med **User. UserPrincipalName**. GitHub-programmet förväntar sig att **unika användar-ID: n (namn-ID)** ska mappas med **User. mail**, så du måste redigera mappningen av attributet genom att klicka på ikonen **Redigera** och ändra attributet mappning.

    ![Skärm bild som visar avsnittet "användarattribut" med ikonen "redigera" markerad.](common/edit-attribute.png)

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera GitHub** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Ett exempel är `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till GitHub.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I programlistan väljer du **GitHub**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.

    ![användarroll](./media/github-tutorial/user-role.png)

7. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-github-sso"></a>Konfigurera GitHub SSO

1. Logga in på din GitHub-organisations webbplats som administratör i ett annat webbläsarfönster.

2. Navigera till **Inställningar** och klicka på **säkerhet**.

    ![Skärm bild som visar GitHub "organisations inställningar" med "säkerhet" valt.](./media/github-tutorial/security.png)

3. Markera kryss rutan **Aktivera SAML-autentisering** och Visa konfigurations fälten för enkel inloggning genom att utföra följande steg:

    ![Skärm bild som visar avsnittet "S A M L enkel inloggning" med text rutorna "Aktivera S A M L" med text rutorna för U R L markerade.](./media/github-tutorial/saml-sso.png)

    a. Kopiera **URL-värde för enkel inloggning** och klistra in värdet i text rutan för **inloggnings-URL** i den **grundläggande SAML-konfigurationen** i Azure Portal.
    
    b. Kopiera **intygets URL-** värde och klistra in det här värdet i text rutan **SVARs-URL** i den **grundläggande SAML-konfigurationen** i Azure Portal.

4. Konfigurera följande fält:

    ![Skärm bild som visar text rutorna "inloggnings-URL", "Issuer" och "offentligt certifikat".](./media/github-tutorial/configure.png)

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

    ![Skärm bild som visar GitHub-webbplatsen där personer har valts.](./media/github-tutorial/people.png "People")

3. Klicka på **Invite member** (Bjud in medlem).

    ![Bjud in användare](./media/github-tutorial/invite-member.png "Bjud in användare")

4. På dialogrutan **Invite member** (Bjud in medlem) utför du följande steg:

    a. I textrutan **E-post** anger du e-postadressen för Britta Simon-kontot.

    ![Bjud in personer](./media/github-tutorial/email-box.png "Bjud in personer")

    b. Klicka på **Skicka inbjudan**.

    ![Skärm bild som visar dialog sidan "Bjud in medlem" med "medlem" valt och knappen "Skicka inbjudan" vald.](./media/github-tutorial/send-invitation.png "Bjud in personer")

    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till GitHub-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till GitHub-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen GitHub i Mina appar omdirigeras det till GitHub-inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat GitHub kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)