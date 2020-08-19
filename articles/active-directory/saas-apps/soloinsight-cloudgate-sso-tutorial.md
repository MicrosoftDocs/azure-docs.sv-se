---
title: 'Självstudie: Azure Active Directory integrering med Soloinsight-CloudGate SSO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Soloinsight-CloudGate SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: jeedes
ms.openlocfilehash: 5ac0f0777ea341036950550e19c5d8e7fb71a91f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545071"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>Självstudie: integrera Soloinsight-CloudGate SSO med Azure Active Directory

I den här självstudien får du lära dig att integrera Soloinsight-CloudGate SSO med Azure Active Directory (Azure AD). När du integrerar Soloinsight-CloudGate SSO med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Soloinsight-CloudGate SSO.
* Gör det möjligt för användarna att logga in automatiskt till Soloinsight-CloudGate SSO med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få en månads kostnads fri utvärderings version [här](https://azure.microsoft.com/pricing/free-trial/).
* Soloinsight-CloudGate SSO-prenumeration (SSO) med enkel inloggning.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. Soloinsight-CloudGate SSO stöder **SP** -initierad SSO.

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Lägga till Soloinsight-CloudGate SSO från galleriet

För att konfigurera integreringen av Soloinsight-CloudGate SSO med Azure AD måste du lägga till Soloinsight-CloudGate SSO från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **SOLOINSIGHT-CloudGate SSO** i sökrutan.
1. Välj **Soloinsight-CLOUDGATE SSO** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Soloinsight-CloudGate SSO med hjälp av en test användare som kallas **Britta Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i Soloinsight-CloudGate SSO.

Om du vill konfigurera och testa Azure AD SSO med Soloinsight-CloudGate SSO, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
2. **[Konfigurera Soloinsight-CLOUDGATE SSO](#configure-soloinsight-cloudgate-sso)** för att konfigurera SSO-inställningarna på program sidan.
3. **[Skapa en Azure AD test-användare](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera Britta Simon att använda enkel inloggning i Azure AD.
5. **[Skapa Soloinsight-CLOUDGATE SSO-test](#create-soloinsight-cloudgate-sso-test-user)** för att få en motsvarighet till Britta Simon i Soloinsight-CloudGate SSO som är länkat till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **SOLOINSIGHT-CloudGate SSO-** programintegration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **grundläggande SAML-konfiguration** anger du värden för följande fält:

    1. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.sigateway.com/login`

    1. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.sigateway.com/process/sso`

   > [!NOTE]
   > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL:en och identifieraren på det sätt som beskrivs senare i avsnittet **Konfigurera enkel inloggning för Soloinsight-CloudGate SSO** i självstudien.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

   ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Soloinsight-CLOUDGATE SSO** kopierar du lämpliga URL: er baserat på ditt krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-soloinsight-cloudgate-sso"></a>Konfigurera Soloinsight-CloudGate SSO

1. Om du vill automatisera konfigurationen i Soloinsight-CloudGate SSO måste du installera **webb läsar tillägget Mina appar med säker inloggning** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren, klickar du på **Setup Soloinsight-CLOUDGATE SSO** så att du dirigerar dig till Soloinsight-CloudGate SSO-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Soloinsight-CloudGate SSO. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-8.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera Soloinsight-CloudGate SSO manuellt öppnar du ett nytt webbläsarfönster och loggar in på din Soloinsight-CloudGate SSO-företags plats som administratör och utför följande steg:

4. Om du vill hämta de värden som ska klistras in i Azure Portal när du konfigurerar Basic SAML loggar du in på CloudGate-webbportalen med dina autentiseringsuppgifter och går sedan till SSO-inställningarna som finns på följande sökväg **start>Administration>Systeminställningar>allmänt**.

    ![Inställningar för enkel inloggning med CloudGate](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

5. **URL för SAML-konsument**

    * Kopiera länkarna som är tillgängliga mot **SAML-konsument-URL:** en och fälten för **omdirigerings-URL** och klistra in dem i avsnittet Azure Portal **grundläggande SAML-konfiguration** för **identifierare (enhets-ID)** och **svars-URL** -fält.

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

6. **SAML-signeringscertifikat**

    * Gå till källan till certifikat filen (base64) som hämtades från Azure Portal listor över SAML-signerings certifikat och högerklicka på den. Välj alternativet **Redigera med Notepad++** i listan. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Kopiera innehållet i certifikatfilen (Base64) i Notepad++.

        ![Certifikatkopia](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Klistra in innehållet i fältet **Certifikat** i inställningarna för enkel inloggning på CloudGate-webbportalen och klicka på Spara.

        ![Certifikatportal](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **Standardgrupp**

    * Välj **Business Admin** (Företagsadministration) från listrutan för alternativet **Standardgrupp** på CloudGate-webbportalen

        ![Standardgrupp](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **AD-identifierare och inloggnings-URL**

    * Den kopierade **inloggnings-URL:** en från Azure Portal **Konfigurera SSO-konfigurationer för Soloinsight-CloudGate** anges i avsnittet CloudGate-inställningar för webb Portal.

    * Klistra in länken för **inloggnings-URL** från Azure Portal i fältet CloudGate Web Portal **AD login URL** .

    * Klistra in länken för **Azure AD-identifieraren** från Azure Portal i fältet CloudGate Web Portal **AD Identifier**

        ![AD-inloggning](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas Britta Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `Britta Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Soloinsight-CloudGate SSO.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. Välj **Soloinsight-CloudGate SSO** i programlistan.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **Britta Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Skapa en testanvändare för Soloinsight-CloudGate SSO

För att skapa en testanvändare väljer du **Anställda** på huvudmenyn på CloudGate-webbportalen och fyller i formuläret Lägg till ny medarbetare. Auktoritetsnivån som ska tilldelas till testanvändaren är **Business Admin** (Företagsadministration). Klicka på **Skapa** när alla obligatoriska fält har fyllts i.

![Medarbetartest](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-sso"></a>Testa SSO

När du väljer SSO-panelen Soloinsight-CloudGate på åtkomst panelen, bör du loggas in automatiskt på Soloinsight-CloudGate SSO som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)