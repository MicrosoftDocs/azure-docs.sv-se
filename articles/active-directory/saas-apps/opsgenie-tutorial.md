---
title: 'Självstudie: Azure Active Directory integrering med OpsGenie | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och OpsGenie.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 0ebcb746e10ae910c695e52fb053fc5d0b316e70
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543915"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-opsgenie"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med OpsGenie

I den här självstudien får du lära dig hur du integrerar OpsGenie med Azure Active Directory (Azure AD). När du integrerar OpsGenie med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till OpsGenie.
* Gör det möjligt för användarna att logga in automatiskt till OpsGenie med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* OpsGenie för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* OpsGenie stöder **IDP** INITIERAd SSO
* När du har konfigurerat OpsGenie kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-opsgenie-from-the-gallery"></a>Lägga till OpsGenie från galleriet

Om du vill konfigurera integreringen av OpsGenie i Azure AD måste du lägga till OpsGenie från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **OpsGenie** i sökrutan.
1. Välj **OpsGenie** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-opsgenie"></a>Konfigurera och testa enkel inloggning med Azure AD för OpsGenie

Konfigurera och testa Azure AD SSO med OpsGenie med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i OpsGenie.

Om du vill konfigurera och testa Azure AD SSO med OpsGenie, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera OPSGENIE SSO](#configure-opsgenie-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa OpsGenie test User](#create-opsgenie-test-user)** -om du vill ha en motsvarighet till B. Simon i OpsGenie som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **OpsGenie** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://app.opsginie.com/auth/saml/<UNIQUEID>`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://app.opsginie.com/auth/saml?id=<UNIQUEID>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren och svars-URL, vilket beskrivs senare i den här självstudien.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

1. I avsnittet **Konfigurera OpsGenie** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till OpsGenie.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **OpsGenie**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-opsgenie-sso"></a>Konfigurera OpsGenie SSO

1. Öppna en annan webb läsar instans och logga sedan in på OpsGenie som administratör.

2. Klicka på **Inställningar**och klicka sedan på fliken **enkel inloggning** .
   
    ![OpsGenie enkel inloggning](./media/opsgenie-tutorial/tutorial-opsgenie-06.png)

3. Om du vill aktivera SSO väljer du **aktive rad**.
   
    ![OpsGenie-inställningar](./media/opsgenie-tutorial/tutorial-opsgenie-07.png) 

4. I avsnittet **Provider** klickar du på fliken **Azure Active Directory** .
   
    ![OpsGenie-inställningar](./media/opsgenie-tutorial/tutorial-opsgenie-08.png) 

5. Utför följande steg på dialog sidan Azure Active Directory:
   
    ![OpsGenie-inställningar](./media/opsgenie-tutorial/tutorial-opsgenie-09.png)
    
    a. Kopiera **app-ID-URI** -värdet och klistra in det i ID-rutan för **identifierare (enhets-ID)** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    a. Kopiera **svars-URL** -värdet och klistra in det i text rutan för **svars-URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    a. I text rutan **slut punkt för SAML 2,0** klistrar du in **inloggnings-URL**-värdet som du har kopierat från Azure Portal.
    
    b. I text rutan **metadata-URL:** klistrar du in URL-värdet för **app Federation-Metadata** som du har kopierat från Azure Portal.
    
    c. Aktivera Aktivera **enkel inloggning** för att aktivera SSO.

    d. Klicka på **Använd SSO-inställningar**.

### <a name="create-opsgenie-test-user"></a>Skapa OpsGenie test användare

Syftet med det här avsnittet är att skapa en användare som kallas B. Simon i OpsGenie. 

1. Logga in på din OpsGenie-klient som administratör i ett webbläsarfönster.

2. Navigera till listan användare genom att klicka på **användare** i den vänstra panelen.
   
    ![OpsGenie-inställningar](./media/opsgenie-tutorial/tutorial-opsgenie-10.png) 

3. Klicka på **Lägg till användare**.

4. I dialogrutan **Lägg till användare** utför du följande steg:
   
    ![OpsGenie-inställningar](./media/opsgenie-tutorial/tutorial-opsgenie-11.png)
   
    a. I text rutan **e-post** skriver du e-postadressen för B. Simon-adresserad i Azure Active Directory.
   
    b. Skriv **B. Simon**i text rutan **fullständigt namn** .
   
    c. Klicka på **Spara**. 

> [!NOTE]
> B. Simon får ett e-postmeddelande med anvisningar för att konfigurera profilen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen OpsGenie på åtkomst panelen, bör du loggas in automatiskt på den OpsGenie som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova OpsGenie med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)