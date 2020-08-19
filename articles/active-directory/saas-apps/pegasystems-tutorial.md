---
title: 'Självstudie: Azure Active Directory integrering med PEGA-system | Microsoft Docs'
description: I den här självstudien får du lära dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och PEGA-system.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 3dd09770ef5f747f008090368c35b1f274374c5f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549083"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Självstudie: Azure Active Directory integrering med PEGA-system

I den här självstudien får du lära dig att integrera PEGA-system med Azure Active Directory (Azure AD).

Den här integrationen ger följande fördelar:

* Du kan använda Azure AD för att kontrol lera vem som har åtkomst till PEGA-system.
* Du kan göra det möjligt för användarna att logga in automatiskt till PEGA-system (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure Portal.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med PEGA-system måste du ha:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du registrera dig för en [utvärderings version](https://azure.microsoft.com/pricing/free-trial/)på en månad.
* En PEGA system prenumeration som har enkel inloggning aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning i Azure AD i en test miljö.

* PEGA-system har stöd för SP-initierad och IdP-initierad SSO.

## <a name="add-pega-systems-from-the-gallery"></a>Lägg till PEGA system från galleriet

Om du vill konfigurera integrering av PEGA-system i Azure AD måste du lägga till PEGA-system från galleriet i listan över hanterade SaaS-appar.

1. I [Azure Portal](https://portal.azure.com)väljer du **Azure Active Directory**i den vänstra rutan:

    ![Välj Azure Active Directory](common/select-azuread.png)

2. Gå till **företags program**  >  **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett program väljer du **nytt program** överst i fönstret:

    ![Välj nytt program](common/add-new-app.png)

4. I rutan Sök anger du **PEGA-system**. Välj **PEGA system** i Sök resultaten och välj sedan **Lägg till**.

     ![Sökresultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning med Azure AD med PEGA-system med hjälp av en test användare som heter Britta Simon.
Om du vill aktivera enkel inloggning måste du upprätta en relation mellan en Azure AD-användare och motsvarande användare i PEGA-system.

Du måste utföra följande steg för att konfigurera och testa enkel inloggning med PEGA-system i Azure AD:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** för att aktivera funktionen för dina användare.
2. **[Konfigurera enkel inloggning för PEGA-system](#configure-pega-systems-single-sign-on)** på program sidan.
3. **[Skapa en Azure AD test-användare](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera enkel inloggning med Azure AD för användaren.
5. **[Skapa ett PEGA Systems test användare](#create-a-pega-systems-test-user)** som är länkad till användarens Azure AD-representation.
6. **[Testa enkel inloggning](#test-single-sign-on)** för att verifiera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet ska du aktivera enkel inloggning med Azure AD i Azure Portal.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med PEGA-system:

1. I [Azure Portal](https://portal.azure.com/)på sidan **PEGA Systems** program integration väljer du **enkel inloggning**:

    ![Välj enkel inloggning](common/select-sso.png)

2. I dialog rutan **Välj metod för enkel inloggning** väljer du **SAML/WS-utfodras** läge för att aktivera enkel inloggning:

    ![Välj en metod för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** väljer du ikonen **Redigera** för att öppna dialog rutan **grundläggande SAML-konfiguration** :

    ![Redigera-ikonen](common/edit-urls.png)

4. I dialog rutan **grundläggande SAML-konfiguration** kan du utföra följande steg om du vill konfigurera programmet i IDP-initierat läge.

    ![Dialog rutan grundläggande SAML-konfiguration](common/idp-intiated.png)

    1. I rutan **identifierare** anger du en URL i det här mönstret:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. I rutan **svars-URL** anger du en URL i följande mönster:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Om du vill konfigurera programmet i SP-initierat läge väljer du **Ange ytterligare URL: er** och utför följande steg.

    ![Information om enkel inloggning för PEGA Systems-domäner och URL: er](common/both-advanced-urls.png)

    1. I rutan **inloggnings-URL** anger du URL-värdet för inloggning.

    1. I rutan **relä tillstånd** anger du en URL i det här mönstret: `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > De värden som anges här är plats hållare. Du måste använda den faktiska identifieraren, svars-URL: en, inloggnings-URL: en och URL: en för Relay status. Du kan hämta ID-och svars-URL-värden från ett PEGA-program, som beskrivs senare i den här självstudien. Kontakta [PEGA Systems support team](https://www.pega.com/contact-us)för att hämta värdet för relä status. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. PEGA Systems-programmet måste ha SAML-försäkran som ett särskilt format. För att få dem i rätt format måste du lägga till anpassade mappningar för attribut i konfigurationen för SAML-token. Följande skärm bild visar standardattributen. Välj **redigerings** ikonen för att öppna dialog rutan **användarattribut** :

    ![Användarattribut](common/edit-attribute.png)

7. Förutom de attribut som visas i föregående skärm bild, kräver PEGA Systems applikationen några fler attribut att skickas tillbaka i SAML-svaret. I avsnittet **användar anspråk** **i dialog rutan användarattribut utför** du följande steg för att lägga till dessa SAML-token:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Dessa värden är bara aktuella för din organisation. Ange lämpliga värden.

    1. Välj **Lägg till nytt anspråk** för att öppna dialog rutan **hantera användar anspråk** :

    ![Välj Lägg till nytt anspråk](common/new-save-attribute.png)

    ![Dialogrutan Hantera användaranspråk](common/new-attribute-details.png)

    1. I textrutan **Namn** anger du det attributnamn som visas för den raden.

    1. Lämna rutan **namn område** tom.

    1. För **källan**väljer du **attribut**.

    1. I listan **källattribut** väljer du det attributvärde som visas för den raden.

    1. Välj **OK**.

    1. Välj **Spara**.

8. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du länken **Hämta** bredvid XML för **federationsmetadata**, enligt dina krav och sparar certifikatet på datorn:

    ![Länk för nedladdning av certifikat](common/metadataxml.png)

9. I avsnittet **Konfigurera PEGA system** kopierar du lämpliga URL: er baserat på dina krav.

    ![Kopiera URL: en för konfigurationen](common/copy-configuration-urls.png)

    1. **Inloggnings-URL**.

    1. **Azure AD-identifierare**.

    1. **Utloggnings-URL**.

### <a name="configure-pega-systems-single-sign-on"></a>Konfigurera enkel inloggning för PEGA-system

1. Om du vill konfigurera enkel inloggning på **PEGA system** -sidan loggar du in på PEGA-portalen med ett administratörs konto i ett annat webbläsarfönster.

2. Välj **skapa**  >  **sysadmin**  >  **-autentiseringstjänst**:

    ![Välj autentiseringstjänst](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Utför följande steg på skärmen **skapa autentiseringstjänst** .

    ![Fönstret Skapa autentiseringstjänst](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. I listan **typ** väljer du **SAML 2,0**.

    1. I rutan **namn** anger du ett namn (till exempel **Azure AD SSO**).

    1. Ange en beskrivning i rutan **kort beskrivning** .  

    1. Välj **skapa och öppna**.
    
4. I avsnittet **information om identitets leverantör (IdP)** väljer du **Importera IDP metadata** och bläddrar till den metadatafil som du laddade ned från Azure Portal. Klicka på **Skicka** för att läsa in metadata:

    ![Avsnittet information om identitets leverantör (IdP)](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    Importen fyller i IdP-data så som visas här:

    ![Importerade IdP-data](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. Slutför följande steg i avsnittet **tjänst leverantör (SP) inställningar** .

    ![Inställningar för tjänst leverantör](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. Kopiera **entitetens identifierings** värde och klistra in det i rutan **identifierare** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    1. Kopiera plats värdet för **intygs mottagar tjänsten (ACS)** och klistra in det i rutan **svars-URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    1. Välj **inaktivera signering av förfrågningar**.

7. Välj **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare med namnet Britta Simon i Azure Portal.

1. I Azure Portal väljer du **Azure Active Directory** i den vänstra rutan, väljer **användare**och väljer sedan **alla användare**:

    ![Välj alla användare](common/users.png)

2. Välj **ny användare** överst på skärmen:

    ![Välj ny användare](common/new-user.png)

3. Utför följande steg i dialog rutan **användare** .

    ![Dialog rutan användare](common/user-properties.png)

    a. I rutan **Namn** anger du **BrittaSimon**.
  
    b. I rutan **användar namn** anger du **brittasimon@ \<yourcompanydomain> . \<extension> **. (Till exempel BrittaSimon@contoso.com .)

    c. Välj **Visa lösen ord**och skriv sedan ned värdet i rutan **lösen ord** .

    d. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda enkel inloggning med Azure genom att ge henne till gång till PEGA-system.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan PEGA- **system**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **PEGA system**.

    ![Lista över program](common/all-applications.png)

3. I den vänstra rutan väljer **du användare och grupper**:

    ![Välj Användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Välj Lägg till användare](common/add-assign-user.png)

5. I dialog rutan **användare och grupper** väljer du **Britta Simon** i listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan. Klicka på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-a-pega-systems-test-user"></a>Skapa en test användare för PEGA Systems

Därefter måste du skapa en användare med namnet Britta Simon i PEGA-system. Arbeta med [PEGA Systems support-teamet](https://www.pega.com/contact-us) för att skapa användare.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

Nu måste du testa konfigurationen för enkel inloggning med Azure AD med hjälp av åtkomst panelen.

När du väljer panelen PEGA Systems på åtkomst panelen, bör du loggas in automatiskt på PEGA Systems-instansen som du ställer in SSO för. Mer information finns i [komma åt och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)