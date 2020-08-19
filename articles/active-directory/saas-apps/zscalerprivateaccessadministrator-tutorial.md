---
title: 'Självstudie: Azure Active Directory integration med Zscaler Private Access Administrator | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zscaler privat åtkomst administratör.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: 5507c25510a84dcfeafc1b2db6c2a774153a59c3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545751"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Självstudie: Azure Active Directory integration med Zscaler Private Access Administrator

I den här självstudien får du lära dig att integrera Zscaler Private Access-administratör med Azure Active Directory (Azure AD).
Att integrera Zscaler Private Access-administratör med Azure AD ger följande fördelar:

* Du kan kontrol lera Azure AD som har åtkomst till Zscaler Private Access Administrator.
* Du kan låta dina användare bli inloggade automatiskt till Zscaler Private Access Administrator (enkel inloggning) med deras Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Zscaler Private Access Administrator behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Zscaler privat åtkomst administratör för enkel inloggning aktive rad prenumeration

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Zscaler-administratör för privat åtkomst har stöd för **SP** -och **IDP** -initierad SSO

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Lägga till Zscaler Private Access-administratör från galleriet

Om du vill konfigurera integrationen av Zscaler Private Access-administratör i Azure AD måste du lägga till Zscaler Private Access-administratör från galleriet till listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Zscaler Private Access-administratör från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Zscaler Private Access Administrator**, väljer **Zscaler Private Access Administrator** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

    ![Zscaler privat åtkomst administratör i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Zscaler Private Access-administratör baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Zscaler Private Access-administratör upprättas.

Om du vill konfigurera och testa enkel inloggning med Azure AD med Zscaler Private Access Administrator måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Zscaler Private Access-administratör enkel inloggning](#configure-zscaler-private-access-administrator-single-sign-on)** – så här konfigurerar du inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Zscaler Private Access administratör test User](#create-zscaler-private-access-administrator-test-user)** -för att få en motsvarighet till Britta Simon i Zscaler Private Access-administratör som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med Zscaler Private Access-administratör:

1. I [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning**på sidan **Zscaler Private Access Administrator** program integration.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Zscaler privat åtkomst administratörs domän och enkel inloggnings information för URL: er](common/idp-relay.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<subdomain>.private.zscaler.com/auth/metadata`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<subdomain>.private.zscaler.com/auth/sso`

    c. Klicka på **Ange ytterligare URL:er**.

    d. Skriv en URL i textrutan **Vidarebefordransstatus**: `idpadminsso`

5.  Utför följande steg om du vill konfigurera programmet i **SP** initierat läge:

    ![Zscaler privat åtkomst administratörs domän och enkel inloggnings information för URL: er](common/both-signonurl.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<subdomain>.private.zscaler.com/auth/sso`   

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Zscaler Private Access Administrator support team](https://help.zscaler.com/zpa-submit-ticket) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. I avsnittet **Konfigurera Zscaler Private Access-administratör** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-zscaler-private-access-administrator-single-sign-on"></a>Konfigurera enkel inloggning för Zscaler Private Access-administratör

1. Logga in på Zscaler Private Access Administrator som administratör i ett annat webbläsarfönster.

2. Klicka på **Administration** och gå till avsnittet **autentisering** högst upp och klicka på **IDP konfiguration**.

    ![Administratörs administratör för Zscaler-privat åtkomst](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Klicka på **Lägg till IDP-konfiguration**i det övre högra hörnet. 

    ![Zscaler Private Access Administrator addidp](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

4. På sidan **Lägg till konfiguration av IDP** utför du följande steg:
 
    ![Zscaler Private Access Administrator idpselect](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Klicka på **Välj fil** för att ladda upp den hämtade metadatafilen från Azure AD i fältet **IDP för fil överföring i metadata** .

    b. Den läser **IDP metadata** från Azure AD och fyller all fält information som visas nedan.

    ![Zscaler Private Access Administrator idpconfig](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Välj **enkel inloggning** som **administratör**.

    d. Välj din domän från fältet **domäner** .
    
    e. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** `brittasimon@yourcompanydomain.extension` . Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Zscaler Private Access Administrator.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Zscaler privat åtkomst administratör**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Zscaler Private Access Administrator**.

    ![Länken Zscaler Private Access Administrator i listan program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-zscaler-private-access-administrator-test-user"></a>Skapa Zscaler Private Access Administrator test User

Om du vill att Azure AD-användare ska kunna logga in på Zscaler Private Access Administrator måste de tillhandahållas i administratör för Zscaler-privat åtkomst. När det gäller Zscaler Private Access-administratör är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på företags platsen för din Zscaler privata åtkomst administratör som administratör.

2. Klicka på **Administration** och gå till avsnittet **autentisering** högst upp och klicka på **IDP konfiguration**.

    ![Administratörs administratör för Zscaler-privat åtkomst](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Klicka på **Administratörer** från vänster sida av menyn.

    ![Administratör för Zscaler privat åtkomst administratör](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. Klicka på **Lägg till administratör**i det övre högra hörnet:

    ![Zscaler privat åtkomst administratör Lägg till administratör](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. På sidan **Lägg till administratör** utför du följande steg:

    ![Användar administratör för Zscaler privat åtkomst administratör](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. I text rutan **användar namn** anger du e-postmeddelandet till en användare BrittaSimon@contoso.com .

    b. Skriv lösen ordet i text rutan **lösen ord** .

    c. Skriv lösen ordet i text rutan **Bekräfta lösen ord** .

    d. Välj **roll** som **Zscaler privat åtkomst administratör**.

    e. I textrutan **E-post** anger du e-postadressen för användaren, t.ex. BrittaSimon@contoso.com.

    f. Skriv telefonnumret i text rutan **telefon** .

    ex. I text rutan **tidszon** väljer du tids zonen.

    h. Klicka på **Spara**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Zscaler privat åtkomst administratör i åtkomst panelen, bör du loggas in automatiskt till den Zscaler-privata åtkomst administratör som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

