---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med nintex Promapp | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och nintex Promapp.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/30/2020
ms.author: jeedes
ms.openlocfilehash: 3b4102b4e8dcbd5aa04e354cec0ab91bf139572c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553486"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nintex-promapp"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med nintex Promapp

I den här självstudien får du lära dig att integrera nintex-Promapp med Azure Active Directory (Azure AD). När du integrerar nintex-Promapp med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till nintex-Promapp.
* Gör det möjligt för användarna att logga in automatiskt till nintex Promapp med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Nintex Promapp-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Nintex Promapp stöder **SP-och IDP** -INITIERAd SSO
* Nintex Promapp stöder **just-in-Time** User-etablering

## <a name="adding-nintex-promapp-from-the-gallery"></a>Lägga till nintex Promapp från galleriet

Om du vill konfigurera integreringen av nintex-Promapp i Azure AD måste du lägga till nintex Promapp från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **nintex Promapp** i sökrutan.
1. Välj **nintex Promapp** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-nintex-promapp"></a>Konfigurera och testa enkel inloggning med Azure AD för nintex-Promapp

Konfigurera och testa Azure AD SSO med nintex Promapp med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i nintex Promapp.

Om du vill konfigurera och testa Azure AD SSO med nintex Promapp slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera nintex PROMAPP SSO](#configure-nintex-promapp-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa nintex Promapp test User](#create-nintex-promapp-test-user)** – om du vill ha en motsvarighet till B. Simon i nintex Promapp som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **nintex Promapp** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    1. I rutan **identifierare** anger du en URL i det här mönstret:

        ```https
        https://go.promapp.com/TENANTNAME/
        https://au.promapp.com/TENANTNAME/
        https://us.promapp.com/TENANTNAME/
        https://eu.promapp.com/TENANTNAME/
        https://ca.promapp.com/TENANTNAME/
        ```

       > [!NOTE]
       > Azure AD-integrering med nintex Promapp är för närvarande endast konfigurerad för autentisering som initierats av tjänsten. (Det vill säga till en nintex Promapp-URL initiera autentiseringsprocessen.) Men fältet **svars-URL** är ett obligatoriskt fält.

    1. I rutan **svars-URL** anger du en URL i följande mönster:

       `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate.aspx`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I rutan **inloggnings-URL** anger du en URL i det här mönstret: `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate`

    > [!NOTE]
    > Dessa värden är plats hållare. Du måste använda den faktiska identifieraren, svars-URL: en och inloggnings-URL: en. Kontakta [nintex Promapp support team](https://www.promapp.com/about-us/contact-us/) för att hämta värdena. Du kan också se de mönster som visas i dialog rutan **grundläggande SAML-konfiguration** i Azure Portal.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera nintex Promapp** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till nintex Promapp.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **nintex Promapp**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-nintex-promapp-sso"></a>Konfigurera nintex Promapp SSO

1. Logga in på din nintex Promapp-företags webbplats som administratör.

2. På menyn längst upp i fönstret väljer du **admin**:

    ![Välj administratör][12]

3. Välj **Konfigurera**:

    ![Välj Konfigurera][13]

4. Utför följande steg i dialog rutan **säkerhet** .

    ![Dialog rutan säkerhet][14]

    1. Klistra in **inloggnings-URL:** en som du kopierade från Azure Portal i rutan **SSO-login URL** .

    1. I listan **SSO-enkel inloggnings läge** väljer du **valfri**. Välj **Spara**.

       > [!NOTE]
       > Valfritt läge är endast för testning. När du är nöjd med konfigurationen väljer du **obligatorisk** i listan **SSO-enkel inloggnings läge** för att tvinga alla användare att autentisera med Azure AD.

    1. I anteckningar öppnar du det certifikat som du laddade ned i föregående avsnitt. Kopiera innehållet i certifikatet utan den första raden (**-----BEGIN certificate-----**) eller den sista raden (**-----END Certificate-----**). Klistra in certifikat innehållet i rutan **SSO-x. 509-certifikat** och välj sedan **Spara**.

### <a name="create-nintex-promapp-test-user"></a>Skapa nintex Promapp-test användare

I det här avsnittet skapas en användare som heter B. Simon i nintex Promapp. Nintex Promapp stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i nintex Promapp skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen nintex Promapp på åtkomst panelen, bör du loggas in automatiskt på nintex-Promapp som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova nintex Promapp med Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png