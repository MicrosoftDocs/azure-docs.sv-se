---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med skärm utsändning-O-Matic | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och skärm utsändning-O-Matic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/15/2019
ms.author: jeedes
ms.openlocfilehash: e56684c30d243e0b7848355234fef896ccd220ce
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543303"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-screencast-o-matic"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med skärm utsändning-O-Matic

I den här självstudien får du lära dig att integrera skärm utsändning-O-Matic med Azure Active Directory (Azure AD). När du integrerar skärm utsändning-O-Matic med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till skärm utsändning-O-Matic.
* Gör det möjligt för användarna att logga in automatiskt till skärm utsändning-O-Matic med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Skärm utsändning – O-Matic för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Skärm utsändning-O-Matic stöder **SP** -INITIERAd SSO
* Skärm utsändning-O-Matic stöder **just-in-Time** User-etablering

## <a name="adding-screencast-o-matic-from-the-gallery"></a>Lägga till skärm utsändning-O-Matic från galleriet

Om du vill konfigurera integrationen av skärm utsändning-O-Matic i Azure AD måste du lägga till skärm utsändning-O-Matic från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **skärm utsändning-O-Matic** i rutan Sök.
1. Välj **skärm utsändning-O-Matic** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-screencast-o-matic"></a>Konfigurera och testa enkel inloggning med Azure AD för skärm utsändning-O-Matic

Konfigurera och testa Azure AD SSO med skärm utsändning-O-Matic med en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i skärm utsändning-O-Matic.

Om du vill konfigurera och testa Azure AD SSO med skärm utsändning-O-Matic, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera skärm utsändning-O-Matic SSO](#configure-screencast-o-matic-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa skärm utsändning – o-Matic test User](#create-screencast-o-matic-test-user)** – om du vill ha en motsvarighet till B. Simon i skärm utsändning-O-Matic som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan skärm bilds **-O-Matic** program integrering letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://screencast-o-matic.com/<InstanceName>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta skärm bilds fönstret [-O-Matic](mailto:support@screencast-o-matic.com) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera skärm bilds-O-Matic** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till skärm utsändning-O-Matic.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **skärm utsändning-O-Matic**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-screencast-o-matic-sso"></a>Konfigurera skärm utsändning – O-Matic SSO

1. Om du vill automatisera konfigurationen i skärm bilds-O-Matic måste du installera **webb läsar tillägget Mina appar med säker inloggning** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

1. När du har lagt till tillägg i webbläsaren, klickar du på **Konfigurera skärm utsändning-o-Matic** för att dirigera dig till programmet skärm utsändning-o-Matic. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på skärm utsändning-O-Matic. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-11.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Om du vill konfigurera skärm läsaren-O-Matic manuellt öppnar du ett nytt webbläsarfönster och loggar in på din skärm bild för O-Matic företags plats som administratör och utför följande steg:

1. Klicka på **prenumerationen**.

    ![Prenumerationen](./media/screencast-tutorial/tutorial_screencast_sub.png)

1. Under avsnittet **åtkomst sida** klickar du på **installation**.

    ![Åtkomst](./media/screencast-tutorial/tutorial_screencast_setup.png)

1. Utför följande steg på **sidan Konfigurera åtkomst**.

1. Under avsnittet **åtkomst-URL** , anger du ditt instancename i den angivna text rutan.

    ![Åtkomst](./media/screencast-tutorial/tutorial_screencast_access.png)

1. Välj **Kräv domän användare** under avsnittet **SAML-användar begränsning (valfritt)** .

1. Under **överför IDP metadata XML-fil**klickar du på **Välj fil** för att ladda upp de metadata som du har laddat ned från Azure Portal.

1. Klicka på **OK**.

    ![Åtkomst](./media/screencast-tutorial/tutorial_screencast_save.png)

### <a name="create-screencast-o-matic-test-user"></a>Skapa skärm utsändning – O-Matic test användare

I det här avsnittet skapas en användare som heter Britta Simon i skärm utsändning-O-Matic. Skärm utsändning-O-Matic stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i skärm bilds-O-Matic skapas en ny efter autentiseringen. Om du behöver skapa en användare manuellt kan du kontakta [skärm bilds-O-O-Matic klient support team](mailto:support@screencast-o-matic.com).

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på ikonen skärm bilds-O-Matic på åtkomst panelen, bör du loggas in automatiskt på skärm bilden-O-Matic som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova skärm utsändning-O-Matic med Azure AD](https://aad.portal.azure.com/)