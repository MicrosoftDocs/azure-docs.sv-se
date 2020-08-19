---
title: 'Självstudie: Azure Active Directory integration med Ungerboeck-programvara | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Ungerboeck-programvara.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jeedes
ms.openlocfilehash: 0999cbcb53edad8c496dc2f6dbd27471c93f3e4c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88532814"
---
# <a name="tutorial-integrate-ungerboeck-software-with-azure-active-directory"></a>Självstudie: integrera Ungerboeck-program med Azure Active Directory

I den här självstudien får du lära dig att integrera Ungerboeck-program med Azure Active Directory (Azure AD). När du integrerar Ungerboeck-program med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Ungerboeck-programvaran.
* Gör det möjligt för användarna att logga in automatiskt för att Ungerboeck program vara med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få en månads kostnads fri utvärderings version [här](https://azure.microsoft.com/pricing/free-trial/).
* Ungerboeck program vara för enkel inloggning (SSO) med aktiverade prenumerationer.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. Ungerboeck-programvaran stöder **SP** -initierad SSO.

## <a name="adding-ungerboeck-software-from-the-gallery"></a>Lägga till Ungerboeck-programvara från galleriet

Om du vill konfigurera integreringen av Ungerboeck-programvaran i Azure AD måste du lägga till Ungerboeck-program från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Ungerboeck Software** i sökrutan.
1. Välj **Ungerboeck-program** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Ungerboeck-programvara med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Ungerboeck-programvaran.

Om du vill konfigurera och testa Azure AD SSO med Ungerboeck-programvaran slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
2. **[Konfigurera Ungerboeck Software SSO](#configure-ungerboeck-software-sso)** för att konfigurera SSO-inställningarna på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning i Azure AD med B. Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
5. **[Skapa Ungerboeck Software test User](#create-ungerboeck-software-test-user)** för att ha en motsvarighet till B. Simon i Ungerboeck-programvaran som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för **program** integrering i Ungerboeck och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **grundläggande SAML-konfiguration** anger du värden för följande fält:

    1. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.ungerboeck.com/prod`

    1. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: 
    
    *  **För produktions miljö**:

       - `https://<SUBDOMAIN>.ungerboeck.com/prod`
       - `https://<SUBDOMAIN>.ungerboeck.net/prod`
       - `https://<SUBDOMAIN>.ungerboeck.io/prod`

   * **För test miljö**:

     - `https://<SUBDOMAIN>.ungerboeck.com/test`
     - `https://<SUBDOMAIN>.ungerboeck.net/test`
     - `https://<SUBDOMAIN>.ungerboeck.io/test`

   > [!NOTE]
   > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL: en och identifieraren som beskrivs senare i avsnittet **Konfigurera Ungerboeck program vara enkel inloggning** i självstudien.

1. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

1. I avsnittet **SAML-signerings certifikat** kopierar du **tumavtrycket** och sparar det på din dator.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

1. I avsnittet **Konfigurera Ungerboeck-programvara** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-ungerboeck-software-sso"></a>Konfigurera Ungerboeck Software SSO

Om du vill konfigurera enkel inloggning på **Ungerboeck Software** -sidan måste du skicka **tumavtrycket** och lämpliga kopierade url: er från Azure Portal till [Ungerboeck Software support-teamet](mailto:Rhonda.Jannings@ungerboeck.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Ungerboeck-programvaran.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Ungerboeck-programvara**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-ungerboeck-software-test-user"></a>Skapa Ungerboeck program vara test användare

I det här avsnittet skapar du en användare som heter B. Simon i Ungerboeck-programvaran. Arbeta med [Ungerboeck Software support team](mailto:Rhonda.Jannings@ungerboeck.com) för att lägga till användare i Ungerboeck-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-sso"></a>Testa SSO

När du väljer program panelen Ungerboeck på åtkomst panelen, bör du loggas in automatiskt till Ungerboeck-programvaran som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)