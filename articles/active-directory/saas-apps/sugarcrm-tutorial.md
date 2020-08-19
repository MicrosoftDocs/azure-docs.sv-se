---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med socker i CRM | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och socker CRM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.openlocfilehash: cd1be43bd8984df8fef690e595ec99f4b112c3c1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88533721"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sugar-crm"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med socker i CRM

I den här självstudien får du lära dig att integrera socker CRM med Azure Active Directory (Azure AD). När du integrerar socker i Azure med Azure AD kan du:

* Kontroll i Azure AD som har till gång till socker-CRM.
* Gör det möjligt för användarna att logga in automatiskt till socker till Azure med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En prenumeration med enkel inloggning (SSO) för den här funktionen i CRM.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Socker i CRM stöder **SP** -INITIERAd SSO

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-sugar-crm-from-the-gallery"></a>Lägga till socker i CRM från galleriet

Om du vill konfigurera en integrering av socker-CRM i Azure AD måste du lägga till socker-CRM från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **socker** i rutan Sök.
1. Välj **socker CRM** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sugar-crm"></a>Konfigurera och testa enkel inloggning med Azure AD för socker-CRM

Konfigurera och testa Azure AD SSO med socker CRM med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i socker CRM.

Om du vill konfigurera och testa Azure AD SSO med socker CRM slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera socker](#configure-sugar-crm-sso)** för enkel inloggning för att konfigurera enkel inloggnings inställningar på program sidan.
    1. **[Skapa test användare av socker i CRM](#create-sugar-crm-test-user)** för att få en motsvarighet till B. Simon i socker CRM som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **socker CRM** och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster:

    - `https://<companyname>.sugarondemand.com`
    - `https://<companyname>.trial.sugarcrm`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    - `https://<companyname>.sugarondemand.com/<companyname>`
    - `https://<companyname>.trial.sugarcrm.com/<companyname>`
    - `https://<companyname>.trial.sugarcrm.eu/<companyname>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med en faktisk inloggnings-URL och svars-URL. Kontakta [socker support teamet](https://support.sugarcrm.com/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera socker CRM** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till socker CRM.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **socker CRM**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-sugar-crm-sso"></a>Konfigurera socker för enkel inloggning i CRM

1. Logga in på företagets företags webbplats som administratör i ett annat webbläsarfönster.

1. Gå till **admin**.

    ![Administratör](./media/sugarcrm-tutorial/ic795888.png "Administratör")

1. I avsnittet **Administration** klickar du på **lösen ords hantering**.

    ![Administration](./media/sugarcrm-tutorial/ic795889.png "Administration")

1. Välj **Aktivera SAML-autentisering**.

    ![Administration](./media/sugarcrm-tutorial/ic795890.png "Administration")

1. Gör följande i avsnittet **SAML-autentisering**:

    ![SAML-autentisering](./media/sugarcrm-tutorial/ic795891.png "SAML-autentisering")  

    a. I text rutan **inloggnings-URL** klistrar du in värdet för **inloggnings-URL: en**som du har kopierat från Azure Portal.
  
    b. I text rutan **service nivå mål** klistrar du in värdet för **URL för utloggning**som du har kopierat från Azure Portal.
  
    c. Öppna ditt bas-64-kodade certifikat i anteckningar, kopiera innehållet i det till Urklipp och klistra sedan in hela certifikatet i text rutan för **X. 509-certifikat** .
  
    d. Klicka på **Spara**.

### <a name="create-sugar-crm-test-user"></a>Skapa test användare av socker i CRM

För att Azure AD-användare ska kunna logga in på socker i Azure måste de tillhandahållas till socker-CRM. När det gäller socker CRM är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din företags webbplats för ditt **socker** som administratör.

1. Gå till **admin**.

    ![Administratör](./media/sugarcrm-tutorial/ic795888.png "Administratör")

1. I avsnittet **Administration** klickar du på **användar hantering**.

    ![Administration](./media/sugarcrm-tutorial/ic795893.png "Administration")

1. Gå till **användare \> Skapa ny användare**.

    ![Skapa ny användare](./media/sugarcrm-tutorial/ic795894.png "Skapa ny användare")

1. Utför följande steg på fliken **användar profil** :

    ![Ny användare](./media/sugarcrm-tutorial/ic795895.png "Ny användare")

    * Ange **användar namn**, **efter namn**och **e-postadress** för en giltig Azure Active Directory användare i de relaterade text rutorna.
  
1. Som **status**väljer du **aktiv**.

1. Utför följande steg på fliken lösen ord:

    ![Ny användare](./media/sugarcrm-tutorial/ic795896.png "Ny användare")

    a. Skriv in lösen ordet i den relaterade text rutan.

    b. Klicka på **Spara**.

> [!NOTE]
> Du kan använda andra verktyg för att skapa Azure-användarkonton eller API: er från socker för Azure för att etablera Azure AD-användarkonton.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på fliken socker-CRM i åtkomst panelen, bör du loggas in automatiskt på den socker-CRM som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova socker i CRM med Azure AD](https://aad.portal.azure.com/)

