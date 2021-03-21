---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med synlig | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och synligt.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: jeedes
ms.openlocfilehash: de41e1ef021ea3da2b3a5bef4268469aab448a21
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92637946"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-visibly"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med synlig

I den här självstudien får du lära dig att integrera synligheten med Azure Active Directory (Azure AD). När du integrerar synligheten med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till synligt.
* Gör det möjligt för användarna att bli inloggade automatiskt så att de blir synliga för deras Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Enkel inloggning (SSO) med enkel inloggning aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Synligt stöder **SP** -INITIERAd SSO

* När du har konfigurerat synligt kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-visibly-from-the-gallery"></a>Lägga till synlig från galleriet

Om du vill konfigurera integreringen av synligt i Azure AD måste du lägga till synlig från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du in **synligt** i sökrutan.
1. Välj **synligt** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-visibly"></a>Konfigurera och testa Azure AD SSO för synligt

Konfigurera och testa Azure AD SSO med synligt med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i synligt.

Om du vill konfigurera och testa Azure AD SSO med synligt, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera](#configure-visibly-sso)** enkel inloggning för att konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa synligt test användare](#create-visibly-test-user)** – om du vill ha en motsvarighet till B. Simon i synligt som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan **synlig** program integrering och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I text rutan **inloggnings-URL** anger du URL: en: `https://app.visibly.io/`

    b. Skriv webb adressen i text rutan **svars-URL** : `https://api.visibly.io/api/v1/verifyResponse`


1. Det anpassade programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade attribut mappningar i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Förutom över, förväntar sig ett synligt program att få fler attribut att skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.
    
    | Name |  Källattribut|
    | ----------- | --------- |
    | city | User. City |
    | lastName | user.surname |
    | state | User. State |
    | avdelning | user.department |
    | e-post | user.mail |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera synligt** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till synligt.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. Välj **synlig** i listan program.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-visibly-sso"></a>Konfigurera synlig enkel inloggning

1. Logga in på ett synligt sätt med dina autentiseringsuppgifter.

1. Navigera till **inställnings** alternativet på navigerings menyn.

    ![Skärm bild som visar alternativet Inställningar markerat.](./media/visibly-tutorial/settings.png)

1. Klicka på **integreringar** i inställningar.

    ![Skärm bild som visar de integreringar som valts på menyn Inställningar.](./media/visibly-tutorial/integrations.png)

1. I **integreringarna** väljer du **SSO**.

    ![Skärm bild som visar S O valt från Integreringarna.](./media/visibly-tutorial/sso.png)

1. Utför följande steg på följande sida.

    ![Skärm bild som visar S S O-integrerings sida där du kan ange de värden som beskrivs.](./media/visibly-tutorial/configuration.png)

    a. I text rutan **entitets-ID** klistrar du in det **entitets-ID-** värde som du har kopierat från Azure Portal.

    b. I text rutan **SSO-URL** klistrar du in värdet för **inloggnings-URL:** en som du har kopierat från Azure Portal.

    c. Ange ett giltigt namn i text rutan för **SSO-namn** .

    d. Öppna det nedladdade **certifikatet (base64)** från Azure Portal i anteckningar och klistra in innehållet i text rutan **certifikat** eller också kan du ladda upp **certifikatet** genom att välja **Ladda upp certifikatet**.

    e. Klicka på **Spara**

### <a name="create-visibly-test-user"></a>Skapa en synlig test användare

I det här avsnittet skapas en användare som heter B. Simon i synligt. Synligt stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i det synliga objektet skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen synligt i åtkomst panelen, bör du loggas in automatiskt till det som är synligt för enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](./tutorial-list.md)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)

- [Prova synligt med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)