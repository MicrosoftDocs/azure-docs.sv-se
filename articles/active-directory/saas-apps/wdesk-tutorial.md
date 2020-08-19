---
title: 'Självstudie: Azure Active Directory integrering med Wdesk | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Wdesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2020
ms.author: jeedes
ms.openlocfilehash: 9e64d76e29ba98181aac12e1e3167351a9c1aa95
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88524025"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Wdesk

I den här självstudien får du lära dig hur du integrerar Wdesk med Azure Active Directory (Azure AD). När du integrerar Wdesk med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Wdesk.
* Gör det möjligt för användarna att logga in automatiskt till Wdesk med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Wdesk för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Wdesk stöder **SP** -och **IDP** -initierad SSO
* När du har konfigurerat Wdesk kan du framtvinga sessionshantering, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-wdesk-from-the-gallery"></a>Lägga till Wdesk från galleriet

Om du vill konfigurera integreringen av Wdesk i Azure AD måste du lägga till Wdesk från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Wdesk** i sökrutan.
1. Välj **Wdesk** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Wdesk baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Wdesk upprättas.

Om du vill konfigurera och testa Azure AD SSO med Wdesk, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera WDESK SSO](#configure-wdesk-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Wdesk test User](#create-wdesk-test-user)** -om du vill ha en motsvarighet till B. Simon i Wdesk som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Wdesk i Azure AD:

1. Välj **enkel inloggning**på sidan **Wdesk** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Information om enkel inloggning för Wdesk-domän och URL: er](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om enkel inloggning för Wdesk-domän och URL: er](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Du får dessa värden från WDesk-portalen när du konfigurerar SSO.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera Wdesk** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** brittasimon@yourcompanydomain.extension . Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Wdesk.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Wdesk**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Wdesk**.

    ![Wdesk-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-wdesk-sso"></a>Konfigurera Wdesk SSO

1. Logga in på Wdesk som säkerhets administratör i ett annat webbläsarfönster.

2. Längst ned till vänster klickar du på **admin** och väljer **konto administratör**:
 
     ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. I Wdesk-administratör navigerar du till **säkerhet**, sedan **SAML**  >  **SAML Settings**:

    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

1. Under **Inställningar för SAML-användar-ID**, kontrol lera **SAML User ID är Wdesk username**.

    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/wdesk-username.png)

4. Under **allmänna inställningar**markerar du **aktivera enkel inloggning med SAML**:

    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. Utför följande steg under **tjänst leverantörs information**:

    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Kopiera **inloggnings-URL** och klistra in den i text rutan för **inloggnings-URL** i Azure Portal.
   
      b. Kopiera **URL: en för metadata** och klistra in den i text rutan **identifierare** i Azure Portal.
       
      c. Kopiera **konsument-URL: en** och klistra in den i text rutan **svars-URL** på Azure Portal.
   
      d. Spara ändringarna genom att klicka på **Spara** på Azure Portal.      

6. Klicka på **Konfigurera IDP inställningar** för att öppna dialog rutan **Redigera inställningar för IDP** . Klicka på **Välj fil** för att hitta **Metadata.xml** filen som du sparade från Azure Portal och ladda sedan upp den.
    
    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
7. Klicka på **Spara ändringar**.

    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

### <a name="create-wdesk-test-user"></a>Skapa Wdesk test användare

Om du vill att Azure AD-användare ska kunna logga in på Wdesk måste de tillhandahållas i Wdesk. I Wdesk är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på Wdesk som säkerhets administratör.

2. Gå till **Administratörs**  >  **konto administratör**.

     ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Klicka på **medlemmar** under **personer**.

4. Klicka nu på **Lägg till medlem** för att öppna dialog rutan **Lägg till medlem** . 
   
    ![Skapa en testanvändare för Azure AD](./media/wdesk-tutorial/createuser1.png)  

5. I text rutan **användare** anger du användar namnet för användaren, till exempel b.simon@contoso.com och klickar på knappen **Fortsätt** .

    ![Skapa en testanvändare för Azure AD](./media/wdesk-tutorial/createuser3.png)

6.  Ange informationen på det sätt som visas nedan:
  
    ![Skapa en testanvändare för Azure AD](./media/wdesk-tutorial/createuser4.png)
 
    a. I text rutan **e-postadress** anger du e-postadressen till användaren b.simon@contoso.com .

    b. I text rutan **förnamn** anger du det första namnet på användaren, t. ex. **B**.

    c. I text rutan **efter namn** anger du det senaste namnet på användaren som **Simon**.

7. Klicka på knappen **Spara medlem** .  

    ![Skapa en testanvändare för Azure AD](./media/wdesk-tutorial/createuser5.png)

### <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Wdesk på åtkomst panelen, bör du loggas in automatiskt på den Wdesk som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)