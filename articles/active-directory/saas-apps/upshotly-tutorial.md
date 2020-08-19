---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med avbildat | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och avbildar.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 1/7/2020
ms.author: jeedes
ms.openlocfilehash: 1dbcd2bd997872820e87c9d8c2e239c4324e9ce6
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88532877"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-upshotly"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med avbild

I den här självstudien får du lära dig att integrera direkt med Azure Active Directory (Azure AD). När du integrerar i intagningen med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till.
* Gör det möjligt för dina användare att vara automatiskt inloggade för att gå över till deras Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Prenumerationen för enkel inloggning har Aktiver ATS (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Har stöd för **SP-och IDP** -INITIERAd SSO

## <a name="adding-upshotly-from-the-gallery"></a>Lägga till nedtagning från galleriet

Om du vill konfigurera integreringen av i Azure AD måste du lägga till dem från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** **, skriver du** in i sökrutan.
1. Välj **upptagningen** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-upshotly"></a>Konfigurera och testa enkel inloggning med Azure AD för att gå över

Konfigurera och testa Azure AD SSO med att använda en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i bild.

Om du vill konfigurera och testa Azure AD SSO med avbildat, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera uppbildad SSO](#configure-upshotly-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa ett avbildat test av användaren](#create-upshotly-test-user)** för att få en motsvarighet till B. Simon som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan **för program integrering** i [Azure Portal](https://portal.azure.com/)går du till sidan **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, är programmet förkonfigurerat och de nödvändiga URL: erna redan har fyllts i i förväg med Azure. Användaren måste spara konfigurationen genom att klicka på knappen **Spara** .

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://app.upshotly.com/api/sso/login/<companyID>`

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera värdena med den faktiska inloggnings-URL: en. Du får det **companyID** -värde som beskrivs senare i självstudien. Kontakta [klient support teamet](mailto:support@upshotly.com) för frågor. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. På sidan **Konfigurera upptagningen** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till ett steg.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du i **bild**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-upshotly-sso"></a>Konfigurera uppbildad SSO

1. Logga in på företagets plats som administratör i ett annat webbläsarfönster.

1. Klicka på **användar profilen** och gå till **admin > SSO** och utför följande steg:

    ![Konfiguration uppskjutits](./media/upshotly-tutorial/config1.png)

    a. Kopiera **företags-ID-** värdet och Använd det här **företags-ID-** värdet för att ersätta **företags-ID-** värdet i **inloggnings-URL:** en i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    b. Öppna hämtade **XML-metadata XML** från Azure Portal till anteckningar, kopiera innehållet i XML för metadata och klistra in det i text rutan **XML-metadata** .

### <a name="create-upshotly-test-user"></a>Skapa en avbildad test användare

I det här avsnittet skapar du en användare som heter B. Simon i ett avbildat gräns moln. Arbeta med ett [inskjutit klient support team](mailto:support@upshotly.com) för att lägga till användarna i den avbildade gräns moln plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen i åtkomst panelen bör du loggas in automatiskt på den bild som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Försök att göra en avbild med Azure AD](https://aad.portal.azure.com/)