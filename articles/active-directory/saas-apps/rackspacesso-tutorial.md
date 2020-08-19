---
title: 'Självstudie: Azure Active Directory integrering med Rackspace SSO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Rackspace SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: a2c2e7358c32453daf53c40a9322df4fe30642d8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548910"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Självstudie: Azure Active Directory integrering med Rackspace SSO

I den här självstudien får du lära dig att integrera Rackspace SSO med Azure Active Directory (Azure AD).
Att integrera Rackspace SSO med Azure AD ger följande fördelar:

* Du kan styra Azure AD som har åtkomst till Rackspace SSO.
* Du kan göra det möjligt för användarna att logga in automatiskt till Rackspace SSO (enkel inloggning) med deras Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Rackspace SSO behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Rackspace SSO-aktiverad prenumeration för enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Rackspace SSO stöder **SP** -INITIERAd SSO

## <a name="adding-rackspace-sso-from-the-gallery"></a>Lägga till Rackspace SSO från galleriet

Om du vill konfigurera integreringen av Rackspace SSO i Azure AD måste du lägga till Rackspace SSO från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Rackspace SSO från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **RACKSPACE SSO**, väljer **Rackspace SSO** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Rackspace SSO i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Rackspace SSO baserat på en test användare som kallas **Britta Simon**.
När enkel inloggning används med Rackspace skapas Rackspace-användare automatiskt första gången de loggar in på Rackspace-portalen. 

Om du vill konfigurera och testa enkel inloggning med Rackspace SSO i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera RACKSPACE SSO enkel inloggning-om](#configure-rackspace-sso-single-sign-on)** du vill konfigurera inställningar för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera attributmappning i kontroll panelen Rackspace](#set-up-attribute-mapping-in-the-rackspace-control-panel)** – för att tilldela Rackspace-roller till Azure AD-användare.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Rackspace SSO i Azure AD:

1. I [Azure Portal](https://portal.azure.com/)på sidan **Rackspace SSO** Application Integration väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** laddar du upp **filen med tjänst leverantörens metadata** som du kan ladda ned från [URL: en](https://login.rackspace.com/federate/sp.xml) och utföra följande steg:

    a. Klicka på **Ladda upp metadatafil**.

    ![image](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![image](common/browse-upload-metadata.png)

    c. När metadatafilen har laddats upp hämtas de nödvändiga URL: erna automatiskt.

    d. Skriv en URL i text rutan **inloggnings-URL** : `https://login.rackspace.com/federate/`

    ![Information om enkel inloggning för Rackspace SSO-domän och URL: er](common/sp-signonurl.png)   

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

Den här filen laddas upp till Rackspace för att fylla i nödvändiga konfigurations inställningar för identitets Federation.

### <a name="configure-rackspace-sso-single-sign-on"></a>Konfigurera Rackspace SSO enkel inloggning

Så här konfigurerar du enkel inloggning på **RACKSPACE SSO** -sida:

1. Se dokumentationen i [lägga till en identitetsprovider på kontroll panelen](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/)
1. Det leder dig genom stegen för att:
    1. Skapa en ny identitetsprovider
    1. Ange en e-postdomän som användare ska använda för att identifiera ditt företag vid inloggning.
    1. Ladda upp **XML-metadata för federationsmetadata** som tidigare hämtats från Azures kontroll panel.

Detta konfigurerar de grundläggande SSO-inställningar som krävs för Azure och Rackspace för att ansluta.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Rackspace SSO.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Rackspace SSO**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **RACKSPACE SSO**.

    ![Länken Rackspace SSO i listan program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Konfigurera attributmappning i kontroll panelen för Rackspace

Rackspace använder en **princip för mappning av attribut** för att tilldela Rackspace-roller och grupper till användarna med enkel inloggning. **Attribut mappnings principen** översätter Azure AD SAML-anspråk till användar konfigurations fälten som Rackspace kräver. Mer dokumentation finns i dokumentationen för Rackspace- [attribut mappning](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/). Några saker att tänka på:

* Om du vill tilldela varierande nivåer av Rackspace-åtkomst med hjälp av Azure AD-grupper måste du aktivera grupp anspråk i inställningarna för enkel inloggning med Azure **RACKSPACE SSO** . **Mappnings principen för attribut** kommer sedan att användas för att matcha grupperna med önskade Rackspace-roller och grupper:

    ![Inställningarna för grupp anspråk](common/sso-groups-claim.png)

* Som standard skickar Azure AD UID för Azure AD-grupper i SAML-anspråk, jämfört med namnet på gruppen. Men om du synkroniserar din lokala Active Directory till Azure AD har du möjlighet att skicka de faktiska namnen på grupperna:

    ![Inställningarna för grupp anspråks namn](common/sso-groups-claims-names.png)

Följande exempel på **attribut mappnings princip** visar:
1. Anger Rackspace-användarens namn till `user.name` SAML-anspråket. Alla anspråk kan användas, men det är vanligt att ange detta till ett fält som innehåller användarens e-postadress.
1. Ange Rackspace-roller `admin` och `billing:admin` för en användare genom att matcha en Azure AD-grupp, antingen efter grupp namn eller grupp-UID. En *ersättning* av `"{0}"` i `roles` fältet används och ersätts av resultaten av `remote` regel uttrycken.
1. Använd `"{D}"` *standard ersättningen* för att låta RACKSPACE hämta ytterligare SAML-fält genom att söka efter standard-och välkända SAML-anspråk i SAML Exchange.

```yaml
---
mapping:
    rules:
    - local:
        user:
          domain: "{D}"
          name: "{At(http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name)}"
          email: "{D}"
          roles:
              - "{0}"
          expire: "{D}"
      remote:
          - path: |
              (
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='7269f9a2-aabb-9393-8e6d-282e0f945985') then ('admin', 'billing:admin') else (),
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='MyAzureGroup') then ('admin', 'billing:admin') else ()
              )
            multiValue: true
  version: RAX-1
```
> [!TIP]
> Se till att du använder en text redigerare som validerar YAML-syntax när du redigerar princip filen.

Mer information finns i dokumentationen för Rackspace- [Mappning för attribut](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/) .

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Rackspace SSO-panelen på åtkomst panelen, bör du loggas in automatiskt på Rackspace SSO som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Du kan också använda knappen **Verifiera** i **Rackspace SSO** -inställningar för enkel inloggning:

   ![Knappen verifiera SSO](common/sso-validate-sign-on.png)

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

