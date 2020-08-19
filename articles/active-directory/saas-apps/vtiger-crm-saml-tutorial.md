---
title: 'Självstudie: Azure Active Directory integrering med vtiger CRM (SAML) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och vtiger CRM (SAML).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.openlocfilehash: 7eb2b4e2f51750cfc6ec3bddcbcf313a8e52d759
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88531291"
---
# <a name="tutorial-integrate-vtiger-crm-saml-with-azure-active-directory"></a>Självstudie: integrera vtiger CRM (SAML) med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar vtiger CRM (SAML) med Azure Active Directory (Azure AD). När du integrerar vtiger CRM (SAML) med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till vtiger CRM (SAML).
* Gör det möjligt för användarna att logga in automatiskt till vtiger CRM (SAML) med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få en månads kostnads fri utvärderings version [här](https://azure.microsoft.com/pricing/free-trial/).
* Vtiger CRM (SAML) enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. 

* Vtiger CRM (SAML) stöder **SP** -INITIERAd SSO
* Vtiger CRM (SAML) stöder **just-in-Time** User-etablering

## <a name="adding-vtiger-crm-saml-from-the-gallery"></a>Lägga till vtiger CRM (SAML) från galleriet

Om du vill konfigurera integreringen av vtiger CRM (SAML) i Azure AD måste du lägga till vtiger CRM (SAML) från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **VTIGER CRM (SAML)** i sökrutan.
1. Välj **VTIGER CRM (SAML)** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med vtiger CRM (SAML) med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i vtiger CRM (SAML).

Om du vill konfigurera och testa Azure AD SSO med vtiger CRM (SAML) slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera VTIGER CRM (SAML) SSO](#configure-vtiger-crm-saml-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa VTIGER CRM (SAML)-test användare](#create-vtiger-crm-saml-test-user)** – om du vill ha en motsvarighet till Britta Simon i vtiger CRM (SAML) som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **VTIGER CRM (SAML)** -program integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: 

   - `https://<customer_instance>.od1.vtiger.com`
   - `https://<customer_instance>.od2.vtiger.com`
   - `https://<customer_instance>.od1.vtiger.ws`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<customer_instance>.od1.vtiger.com/sso/saml?acs`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [VTIGER CRM (SAML)-klient support teamet](mailto:support@vtiger.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

   ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera VTIGER CRM (SAML)** kopierar du lämpliga URL: er baserat på ditt krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-vtiger-crm-saml-sso"></a>Konfigurera vtiger CRM (SAML) SSO

Om du vill konfigurera enkel inloggning på **VTIGER CRM (SAML)** , måste du skicka det hämtade **certifikatet (base64)** och lämpliga kopierade url: er från Azure Portal till [support teamet för vtiger CRM (SAML)](mailto:support@vtiger.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till vtiger CRM (SAML).

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **VTIGER CRM (SAML)**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-vtiger-crm-saml-test-user"></a>Skapa test användare av vtiger CRM (SAML)

I det här avsnittet skapas en användare som kallas Britta Simon i vtiger CRM (SAML). Vtiger CRM (SAML) stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i vtiger CRM (SAML) skapas en ny efter autentiseringen.

### <a name="test-sso"></a>Testa SSO

När du väljer vtiger CRM-panelen (SAML) i åtkomst panelen, bör du loggas in automatiskt på vtiger CRM (SAML) som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)