---
title: 'Självstudie: Azure Active Directory integrering med 123FormBuilder SSO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och 123FormBuilder SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: jeedes
ms.openlocfilehash: 2fadfac3fe9e66c3a05e2cceed19def607ff72c3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88539291"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-123formbuilder-sso"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med 123FormBuilder SSO

I den här självstudien får du lära dig hur du integrerar 123FormBuilder SSO med Azure Active Directory (Azure AD). När du integrerar 123FormBuilder SSO med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till 123FormBuilder SSO.
* Gör det möjligt för användarna att logga in automatiskt till 123FormBuilder SSO med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* 123FormBuilder SSO-aktiverad prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* 123FormBuilder SSO stöder **SP-och IDP** -initierad SSO.
* 123FormBuilder SSO stöder **just-in-Time** User-etablering.
* När du har konfigurerat 123FormBuilder SSO kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-123formbuilder-sso-from-the-gallery"></a>Lägga till 123FormBuilder SSO från galleriet

Om du vill konfigurera integreringen av 123FormBuilder SSO i Azure AD måste du lägga till 123FormBuilder SSO från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **123FormBuilder SSO** i sökrutan.
1. Välj **123FORMBUILDER SSO** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-123formbuilder-sso"></a>Konfigurera och testa enkel inloggning med Azure AD för 123FormBuilder SSO

Konfigurera och testa Azure AD SSO med 123FormBuilder SSO med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i 123FormBuilder SSO.

Om du vill konfigurera och testa Azure AD SSO med 123FormBuilder SSO slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera 123FORMBUILDER SSO](#configure-123formbuilder-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa 123FORMBUILDER SSO test User](#create-123formbuilder-sso-test-user)** – om du vill ha en motsvarighet till B. Simon i 123FormBuilder SSO som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan **123FormBuilder SSO** application integration och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/metadata`


    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/acs`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/sso`

    > [!NOTE]
    > Dessa värden är inte verkliga. Du behöver uppdatera de här värdena från faktiska URL:er och identifierare. Detta förklaras senare i självstudien.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp XML för **federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera 123FORMBUILDER SSO** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till 123FormBuilder SSO.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **123FORMBUILDER SSO**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-123formbuilder-sso"></a>Konfigurera 123FormBuilder SSO

1. Om du vill konfigurera enkel inloggning på **123FORMBUILDER SSO** -sidan går du till [https://www.123formbuilder.com/form-2709121/](https://www.123formbuilder.com/form-2709121/) och utför följande steg:

    ![Konfigurera enkel inloggning](./media/123formbuilder-tutorial/submit.png) 

    a. I textrutan **E-post** skriver du e-postadressen för användaren: `B.Simon@Contoso.com`.

    b. Klicka på **Ladda upp** och bläddra till den nedladdade metadata-XML-filen som du har laddat ned från Azure-portalen.

    c. Klicka på **SUBMIT FORM** (Skicka formulär).

2. I **Microsoft Azure AD - Single sign-on - Configure App Settings** (Microsoft Azure AD – Enkel inloggning – Konfigurera appinställningar) utför du följande steg:

    ![Konfigurera enkel inloggning](./media/123formbuilder-tutorial/url3.png)

    a. Om du vill konfigurera programmet i **IDP-initierat läge** kopierar du värdet för **IDENTIFIER** (Identifierare) för din instans och klistrar in det i textrutan **Identifierare** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    b. Om du vill konfigurera programmet i **IDP-initierat läge** kopierar du värdet för **REPLY URL** (Svars-URL) för din instans och klistrar in det i textrutan **Svars-URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    c. Om du vill konfigurera programmet i **SP-initierat läge** kopierar du värdet för **SIGN ON URL** (Inloggnings-URL) för din instans och klistrar in det i textrutan **Inloggnings-URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

### <a name="create-123formbuilder-sso-test-user"></a>Skapa 123FormBuilder SSO test User

I det här avsnittet skapas en användare som kallas Britta Simon i 123FormBuilder SSO. 123FormBuilder SSO stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i 123FormBuilder SSO, skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på 123FormBuilder SSO-panelen på åtkomst panelen, bör du loggas in automatiskt på 123FormBuilder SSO som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova 123FormBuilder SSO med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
