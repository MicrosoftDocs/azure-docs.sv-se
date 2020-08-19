---
title: 'Självstudie: Azure Active Directory integrering med TOPdesk-Public | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TOPdesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.openlocfilehash: a18cb583b246c3cfbf09f43b9921497de37a3613
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88524297"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Självstudie: Azure Active Directory integrering med TOPdesk-Public

I den här självstudien får du lära dig att integrera TOPdesk-Public med Azure Active Directory (Azure AD).
Genom att integrera TOPdesk-offentliga med Azure AD får du följande fördelar:

* Du kan styra Azure AD som har åtkomst till TOPdesk-Public.
* Du kan göra det möjligt för användarna att logga in automatiskt till TOPdesk (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med TOPdesk-Public behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* TOPdesk-aktiverad prenumeration för enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* TOPdesk – offentlig stöder **SP** -INITIERAd SSO

## <a name="adding-topdesk---public-from-the-gallery"></a>Lägga till TOPdesk-Public från galleriet

Om du vill konfigurera integreringen av TOPdesk i Azure AD måste du lägga till TOPdesk-Public från galleriet i listan över hanterade SaaS-appar.

**Gör så här om du vill lägga till TOPdesk-offentlig från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **TOPdesk-Public**, väljer **TOPdesk-Public** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![TOPdesk – offentligt i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med TOPdesk-offentligt baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i TOPdesk-offentliga.

Om du vill konfigurera och testa enkel inloggning med TOPdesk i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera TOPdesk-offentlig enkel inloggning](#configure-topdesk---public-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa TOPdesk-Public test User](#create-topdesk---public-test-user)** – om du vill ha en motsvarighet till Britta Simon i TOPdesk-Public som är länkat till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med TOPdesk-Public:

1. På sidan [Azure Portal](https://portal.azure.com/)på sidan för **TOPdesk-offentlig** program integrering väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4.  I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstleverantör**:

    >[!NOTE]
    >Du hämtar metadata- **filen för tjänst leverantören** från avsnittet **Konfigurera TOPdesk-offentlig enkel inloggning** som beskrivs senare i självstudien.

    a. Klicka på **Ladda upp metadatafil**.
    
    ![Ladda upp metadatafil](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![välj metadatafil](common/browse-upload-metadata.png)

    c. När metadatafilen har laddats upp, fylls **ID** och **svars-URL** -värden automatiskt i avsnittet grundläggande SAML-konfiguration.

    ![Information om enkel inloggning för TOPdesk-offentliga domäner och URL: er](common/sp-identifier-reply.png)

    d. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<companyname>.topdesk.net`

    e. I text rutan **ID för identitets-URL** fyller du i URL: en för TOPdesk metadata som du kan hämta från TOPdesk-konfigurationen. Den bör använda följande mönster: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`
    
    f. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<companyname>.topdesk.net/tas/public/login/verify`
    
    > [!NOTE] 
    > Om **ID** -och **svars-URL** -värdena inte fylls i automatiskt måste du ange dem manuellt. För identifierare följer du mönstret som nämnts ovan och du får svars-URL-värdet från avsnittet **Konfigurera TOPdesk-offentlig enkel inloggning** som beskrivs senare i självstudien. **Inloggnings-URL** -värdet är inte verkligt, så du måste uppdatera värdet med den faktiska inloggnings-URL: en. Kontakta [TOPdesk – support teamet för offentliga klienter](https://help.topdesk.com/saas/enterprise/user/) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera TOPdesk-Public** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-topdesk---public-single-sign-on"></a>Konfigurera TOPdesk-offentlig enkel inloggning

1. Logga in på din **TOPdesk-offentliga** företags webbplats som administratör.

2. På menyn **TOPdesk** klickar du på **Inställningar**.
   
    ![Inställningar](./media/topdesk-public-tutorial/ic790598.png "Inställningar")

3. Klicka på **Inloggningsinställningar**.
   
    ![Inloggnings inställningar](./media/topdesk-public-tutorial/ic790599.png "Inloggnings inställningar")

4. Expandera menyn **Inloggningsinställningar** och klicka sedan på **Allmänt**.
   
    ![Allmänt](./media/topdesk-public-tutorial/ic790600.png "Allmänt")

5. I avsnittet **offentligt** i avsnittet konfiguration av **SAML-inloggning** utför du följande steg:
   
    ![Tekniska inställningar](./media/topdesk-public-tutorial/ic790601.png "Tekniska inställningar")
   
    a. Klicka på **Ladda ned** för att ladda ned den offentliga metadatafilen, och spara den lokalt på datorn.
   
    b. Öppna den hämtade metadatafilen och leta upp noden **AssertionConsumerService** .

    ![AssertionConsumerService](./media/topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Kopiera värdet **AssertionConsumerService** och klistra in det här värdet i text rutan **svars-URL** i avsnittet **grundläggande SAML-konfiguration** .      
   
6. Skapa en certifikatfil genom att utföra följande steg:
    
    ![Certifikatmallens](./media/topdesk-public-tutorial/ic790606.png "Certifikat")
    
    a. Öppna den nedladdade metadatafilen från Azure-portalen.
    
    b. Expandera den **RoleDescriptor**-nod som har en **xsi:type** som är **fed:ApplicationServiceType**.
    
    c. Kopiera värdet för noden **X509Certificate**.
    
    d. Spara det kopierade **X509Certificate**-värdet lokalt på datorn i en fil.

7. I avsnittet **Offentligt** klickar du på **Lägg till**.
    
    ![SAML-inloggning](./media/topdesk-public-tutorial/ic790625.png "SAML-inloggning")

8. I dialogrutan **SAML-konfigurationsassistent** utför du följande steg:
    
    ![Konfigurations assistent för SAML](./media/topdesk-public-tutorial/ic790608.png "Konfigurations assistent för SAML")
    
    a. Ladda upp den nedladdade metadatafilen från Azure-portalen genom att gå till **Federation Metadata** och klicka på **Bläddra**.

    b. Ladda upp certifikatfilen genom att gå till **Certifikat (RSA)** och klicka på **Bläddra**.

    c. Ladda upp den logotypfil som du fick från TOPdesk-supportteamet genom att gå till **logotypikonen** och klicka på **Bläddra**.

    d. I textrutan **Användarnamnsattribut** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. I textrutan **Visningsnamn** skriver du ett namn för konfigurationen.

    f. Klicka på **Spara**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till TOPdesk-Public.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **TOPdesk-Public**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **TOPdesk-Public**.

    ![Länken TOPdesk-offentlig i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-topdesk---public-test-user"></a>Skapa TOPdesk-offentlig test användare

För att Azure AD-användare ska kunna logga in på TOPdesk-offentliga måste de tillhandahållas i TOPdesk-Public. Vid TOPdesk-offentlig är etableringen en manuell uppgift.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Konfigurera användaretablering genom att utföra följande steg:

1. Logga in på din **TOPdesk-offentliga** företags webbplats som administratör.

2. Klicka på **TOPdesk \> nya \> stödfiler \> **på menyn högst upp.
   
    ![Person](./media/topdesk-public-tutorial/ic790628.png "Person")

3. I dialog rutan ny person utför du följande steg:
   
    ![Ny person](./media/topdesk-public-tutorial/ic790629.png "Ny person")
   
    a. Klicka på fliken Allmänt.

    b. I text rutan efter **namn** skriver du efter namn för användaren som Simon
 
    c. Välj en **plats** för kontot.
 
    d. Klicka på **Spara**.

> [!NOTE]
> Du kan använda andra TOPdesk-verktyg eller API: er som tillhandahålls av TOPdesk-Public för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen TOPdesk-offentlig i åtkomst panelen, bör du loggas in automatiskt på den TOPdesk-offentliga som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
