---
title: 'Självstudie: Azure Active Directory integrering med MVISION Cloud Azure AD SSO-konfiguration | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och MVISION-molnet i Azure AD SSO-konfigurationen.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: jeedes
ms.openlocfilehash: d529123b52e0e90702839f6822a586cf53dffa52
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546727"
---
# <a name="tutorial-integrate-mvision-cloud-azure-ad-sso-configuration-with-azure-active-directory"></a>Självstudie: integrera MVISION Cloud Azure AD SSO-konfiguration med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar MVISION Cloud Azure AD SSO-konfiguration med Azure Active Directory (Azure AD). När du integrerar MVISION Cloud Azure AD SSO-konfiguration med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till MVISION Cloud Azure AD SSO-konfiguration.
* Gör det möjligt för användarna att logga in automatiskt till MVISION Cloud Azure AD SSO-konfiguration med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få en månads kostnads fri utvärderings version [här](https://azure.microsoft.com/pricing/free-trial/).
* MVISION Cloud Azure AD SSO-konfiguration enkel inloggning (SSO) aktive rad prenumeration.


## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* MVISION Cloud Azure AD SSO-konfiguration stöder **SP-och IDP** -INITIERAd SSO
* När du har konfigurerat Dropbox kan du framtvinga kontroll över sessioner, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-mvision-cloud-azure-ad-sso-configuration-from-the-gallery"></a>Lägga till MVISION Cloud Azure AD SSO-konfiguration från galleriet

Om du vill konfigurera integrering av MVISION Cloud Azure AD SSO-konfigurationen i Azure AD måste du lägga till MVISION Cloud Azure AD SSO-konfiguration från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **MVision Cloud Azure AD SSO Configuration** i sökrutan.
1. Välj **MVision Cloud Azure AD SSO-konfiguration** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med MVISION Cloud Azure AD SSO-konfiguration med hjälp av en test användare som kallas **Britta Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i MVISION Cloud AD SSO-konfiguration.

Om du vill konfigurera och testa Azure AD SSO med MVISION Cloud Azure AD SSO-konfiguration, fyller du i följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
    4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera MVision Cloud Azure AD SSO-konfiguration SSO](#configure-mvision-cloud-azure-ad-sso-configuration-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa MVision Cloud Azure AD SSO-konfiguration testa användare](#create-mvision-cloud-azure-ad-sso-configuration-test-user)** – för att få en motsvarighet till Britta Simon i MVision Cloud Azure AD SSO-konfiguration som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Datadog** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)


4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<ENV>.myshn.net/shndash/response/saml-postlogin`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![MVISION Cloud Azure AD SSO konfigurations domän och enkel inloggnings information för URL: er](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [MVision Cloud Azure AD SSO Configuration Client support team](mailto:support@skyhighnetworks.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera MVision Cloud AD SSO-konfiguration** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till MVISION Cloud Azure AD SSO-konfiguration.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **MVision Cloud Azure AD SSO Configuration**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **MVision Cloud Azure AD SSO-konfiguration**.

    ![Länken MVISION Cloud Azure AD SSO Configuration i listan program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.


## <a name="configure-mvision-cloud-azure-ad-sso-configuration-sso"></a>Konfigurera MVISION Cloud Azure AD SSO SSO

Om du vill konfigurera enkel inloggning på **MVision Cloud Azure AD SSO-konfigurations** sidan måste du skicka det hämtade **certifikatet (base64)** och lämpliga kopierade url: er från Azure Portal till [MVision Cloud Azure AD SSO Configuration support team](mailto:support@skyhighnetworks.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.


### <a name="create-mvision-cloud-azure-ad-sso-configuration-test-user"></a>Skapa MVISION Cloud Azure AD SSO-konfiguration test användare

I det här avsnittet skapar du en användare som heter B. Simon i MVISION Cloud Azure AD SSO-konfiguration. Arbeta med [MVision Cloud Azure AD SSO Configuration support team](mailto:support@skyhighnetworks.com) för att lägga till användarna i MVision-molnet konfigurations plattform för enkel inloggning i Azure AD. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på ikonen MVISION Cloud Azure AD SSO i åtkomst panelen, bör du loggas in automatiskt till MVISION Cloud Azure AD SSO-konfigurationen som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova MVISION Cloud Azure AD SSO Configuration med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)