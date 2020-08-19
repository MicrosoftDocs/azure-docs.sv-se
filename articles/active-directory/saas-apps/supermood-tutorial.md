---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med överstämning | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och överstämning.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/31/2019
ms.author: jeedes
ms.openlocfilehash: f30dea768e7b3a0376ebdf6c3d80e98eaba6f760
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88533415"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-supermood"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med överstämning

I den här självstudien får du lära dig att integrera överstämning med Azure Active Directory (Azure AD). När du integrerar överstämning med Azure AD kan du:

* Kontroll i Azure AD som har till gång till överstämning.
* Gör det möjligt för användarna att logga in automatiskt till överlappande med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Överstämnings aktive rad prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Överstämning stöder **SP-och IDP** -INITIERAd SSO
* Överstämning stöder **just-in-Time** User-etablering

## <a name="adding-supermood-from-the-gallery"></a>Lägga till överstämning från galleriet

Om du vill konfigurera en överblick i Azure AD måste du lägga till överstämning från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. Skriv **överstämning** i sökrutan i avsnittet **Lägg till från galleriet** .
1. Välj **överstämning** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-supermood"></a>Konfigurera och testa enkel inloggning med Azure AD för överstämning

Konfigurera och testa Azure AD SSO med överlappande med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i överstämningen.

Om du vill konfigurera och testa Azure AD SSO med överlappande, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera superstämnings-SSO](#configure-supermood-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa överstämnings test användare](#create-supermood-test-user)** – för att få en motsvarighet till B. Simon i överstämning som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan [Azure Portal](https://portal.azure.com/)går du till sidan för **överstämnings** program integrering och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    a. Markera **Ange ytterligare URL: er**.
    
    b. I text rutan för **relä tillstånd** anger du en URL: `https://supermood.co/auth/sso/saml20`

1. Klicka på **Ange ytterligare URL:er** och utför följande steg om du vill konfigurera programmet i **SP**-initierat läge:

    I rutan **Inloggnings-URL** anger du följande URL: `https://supermood.co/app/#!/loginv2`

1. Klicka på **Spara**.

1. Övertecknings program förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Förutom över, förväntar sig överstämnings program att fler attribut ska skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name | Källattribut|
    | ---------------| ------|
    | firstName | user.givenname |
    | lastName | user.surname |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **URL: en för appens Federations-metadata** och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att ge åtkomst till överstämning.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **överstämning**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-supermood-sso"></a>Konfigurera överstämnings-SSO

1. Gå till Supermood.co admin panel som säkerhets administratör.

1. Klicka på **mitt konto** (längst ned till vänster) och **enkel inloggning (SSO)**.

    ![Certifikatet Single](./media/supermood-tutorial/tutorial_supermood_single.png)

1. På **dina SAML 2,0-konfigurationer**klickar du på **Lägg till en SAML 2,0-konfiguration för en e-postdomän**.

    ![Certifikatet Lägg till](./media/supermood-tutorial/tutorial_supermood_add.png)

1. På **Lägg till en SAML 2,0-konfiguration för en e-postdomän**. i avsnittet utför du följande steg:

    ![Certifikatets SAML](./media/supermood-tutorial/tutorial_supermood_saml.png)

    a. Skriv din domän i text rutan **för den här identitets leverantörens e-postdomän** .

    b. I text rutan **Använd en metadata-URL** klistrar du in **URL: en för app Federation-Metadata** som du har kopierat från Azure Portal.

    c. Klicka på **Lägg till**.

### <a name="create-supermood-test-user"></a>Skapa överstämnings test användare

I det här avsnittet skapas en användare som kallas Britta Simon i överstämningen. Överstämning stöder just-in-Time-gruppetablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i överstämningen skapas en ny efter autentiseringen. Om du behöver skapa en användare manuellt kan du kontakta [överstämnings support teamet](mailto:hello@supermood.fr).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen överlappa på åtkomst panelen bör du loggas in automatiskt på den överlappande som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova överstämning med Azure AD](https://aad.portal.azure.com/)