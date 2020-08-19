---
title: 'Självstudie: Azure Active Directory-integrering med Workspot-kontroll | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning för Azure Active Directory-och Workspot-kontroll.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: bfbecc71638e6feaaf29809f09dda752dd29b2ae
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88526643"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Självstudie: Azure Active Directory integrering med Workspot-kontroll

I den här självstudien får du lära dig att integrera Workspot-kontroll med Azure Active Directory (Azure AD). När du integrerar Workspot-kontrollen med Azure AD kan du:

* Använd Azure AD för att kontrol lera vem som har åtkomst till Workspot-kontrollen.
* Gör det möjligt för användare att automatiskt logga in på Workspot-kontroll (enkel inloggning [SSO]) med hjälp av deras Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [enkel inloggning till program i Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Workspot-kontroll behöver du följande saker:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).

* En Workspot-aktiverad prenumeration med enkel inloggning.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

> [!Note]
> Workspot-kontrollen stöder SP-initierad och IDP-initierad SSO.


## <a name="adding-workspot-control-from-the-gallery"></a>Lägga till Workspot-kontroll från galleriet

Om du vill konfigurera integrering av Workspot-kontroll i Azure AD måste du lägga till Workspot-kontroll från galleriet i listan över hanterade SaaS-appar.

**Följ dessa steg om du vill lägga till Workspot-kontroll från galleriet:**

1. I den vänstra rutan i [Azure Portal](https://portal.azure.com)väljer du **Azure Active Directory**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program** och välj **alla program**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

3. Välj **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Workspot-kontroll**, väljer **Workspot-kontroll** i panelen resultat och väljer sedan **Lägg till**.

     ![Fönstret Lägg till från galleriet](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Workspot-kontroll för en test användare, Britta Simon.
För att enkel inloggning ska fungera måste du upprätta en länk mellan en Azure AD-användare och en relaterad användare i Workspot-kontrollen.

Om du vill konfigurera och testa enkel inloggning med Workspot-kontrollen i Azure AD måste du utföra följande uppgifter:

1. [Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on) så att användarna kan använda funktionen.
2. [Konfigurera Workspot styr enkel inloggning](#configure-workspot-control-single-sign-on) för att konfigurera inställningarna för enkel inloggning på program sidan.
3. [Skapa en Azure AD test-användare](#create-an-azure-ad-test-user) för att testa enkel inloggning med Azure AD för Britta Simon.
4. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) så att Britta Simon kan använda enkel inloggning i Azure AD.
5. [Skapa en Workspot för kontroll test](#create-a-workspot-control-test-user) för att upprätta en motsvarighet till Britta Simon i Workspot Control som är länkad till användarens Azure AD-representation.
6. [Testa enkel inloggning](#test-single-sign-on) för att verifiera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Följ dessa steg för att konfigurera enkel inloggning för Azure AD med Workspot-kontroll:

1. På sidan **Workspot Control** application integration i [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I fönstret **Välj en enkel inloggnings metod** väljer du **SAML** -läge för att aktivera enkel inloggning.

    ![Välj ett enda inloggnings metods fönster](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** väljer du ikonen **Redigera** (blyertspenna) för att få åtkomst till **grundläggande SAML-konfiguration**.

    ![Redigerings ikonen är markerad i "grundläggande SAML-konfiguration"](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i IDP läge, följer du dessa steg:

    ![Workspot kontrol lera domän och URL-information för enkel inloggning](common/idp-intiated.png)

    1. I text rutan **identifierare** anger du en URL i följande mönster:<br/>
    ***https://<<i></i> instans namn>-SAML.Workspot.com/SAML/metadata***

    1. I text rutan **svars-URL** anger du en URL i följande mönster:<br/>
    ***https://<<i></i> instans namn>-SAML.Workspot.com/SAML/Assertion***

5. Om du vill konfigurera programmet i SP-initierat läge väljer du **Ange ytterligare URL: er**.

    ![Workspot kontrol lera domän och URL-information för enkel inloggning](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** anger du en URL i följande mönster:<br/>
    ***https://<<i></i> instans namn>-SAML.Workspot.com/***

    > [!NOTE]
    > Dessa värden är inte verkliga. Ersätt värdena med den faktiska identifieraren, svars-URL: en och inloggnings-URL: en. Kontakta [support teamet för Workspot-kontroll](mailto:support@workspot.com) för att hämta dessa värden. Eller också kan du referera till mönstren i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

6. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du **Ladda ned** för att ladda ned **certifikat (base64)** från de tillgängliga alternativen enligt dina krav. Spara den på din dator.

    ![Hämtnings länken för certifikatet (base64)](common/certificatebase64.png)

7. I avsnittet **Konfigurera Workspot-kontroll** kopierar du lämpliga URL: er enligt dina krav:

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    - **Inloggnings-URL**

    - **Azure AD-identifierare**

    - **Utloggnings-URL**

### <a name="configure-workspot-control-single-sign-on"></a>Konfigurera Workspot-kontroll för enkel inloggning

1. Logga in på Workspot-kontroll som säkerhets administratör i ett annat webbläsarfönster.

2. I verktygsfältet högst upp på sidan väljer du **konfiguration** och sedan **SAML**.

    ![Installationsalternativ](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. I fönstret **Security Assertion Markup Language konfiguration** följer du dessa steg:
 
    ![Security Assertion Markup Language konfigurations fönster](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. I rutan **entitets-ID** klistrar du in **Azure AD-identifieraren** som du kopierade från Azure Portal.

    1. I rutan **inloggning service-URL** klistrar du in den **inloggnings-URL** som du kopierade från Azure Portal.

    1. I rutan **utloggnings tjänstens URL** klistrar du in den **utloggnings-URL** som du kopierade från Azure Portal.

    1. Välj **uppdaterings fil** som ska överföras till X. 509-certifikatet det bas-64-kodade certifikatet som du laddade ned från Azure Portal.

    1. Välj **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, **användare**och sedan **alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst i fönstret.

    ![Knappen "ny användare"](common/new-user.png)

3. Följ de här stegen i egenskaperna för användaren:

    ![Fönstret användar egenskaper](common/user-properties.png)

    1. I fältet **namn** anger du **BrittaSimon**.
  
    1. I fältet **användar namn** anger du **brittasimon@* yourcompanydomain. extension * * *. Ange till exempel ** BrittaSimon@contoso . <i></i> com**.

    1. Markera kryss rutan **Visa lösen ord** . Skriv sedan ned värdet som visas i rutan **lösen ord** .

    1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet beviljar du Britta Simon-åtkomst till Workspot-kontrollen så att hon kan använda enkel inloggning i Azure.

1. I Azure Portal väljer du **företags program**, **alla program**och sedan **Workspot Control**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Workspot Control**.

    ![Workspot-kontroll länken i program listan](common/all-applications.png)

3. Välj **användare och grupper**på menyn på vänster sida.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Välj knappen **Lägg till användare** . Välj sedan **användare och grupper** i fönstret **Lägg till tilldelning** .

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I fönstret **användare och grupper** väljer du **Britta Simon** från listan **användare** . Klicka sedan på **Välj**.

6. Om du förväntar dig ett roll värde i SAML-intyget väljer du lämplig roll för användaren i listan i fönstret **Välj roll** . Klicka sedan på **Välj** längst ned.

7. I fönstret **Lägg till tilldelning** väljer du **tilldela**.

### <a name="create-a-workspot-control-test-user"></a>Skapa en Workspot-kontroll test användare

För att Azure AD-användare ska kunna logga in på Workspot-kontroll måste de tillhandahållas i Workspot-kontrollen. Etableringen är en manuell uppgift.

**Följ dessa steg om du vill etablera ett användar konto:**

1. Logga in på Workspot-kontroll som säkerhets administratör.

2. I verktygsfältet högst upp på sidan väljer **du användare** och sedan **Lägg till användare**.

    ![Alternativ för "användare"](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. I fönstret **Lägg till en ny användare** följer du dessa steg:

    ![Fönstret "Lägg till en ny användare"](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. I rutan **förnamn** anger du det första namnet på en användare, t. ex. **Britta**.

    1. I text rutan **efter namn** anger du användarens efter namn, till exempel **Simon**.

    1. I rutan **e-** postadress anger du användarens e-postadress, till exempel ** Brittasimon@contoso . <i></i> com**.

    1. Välj lämplig användar roll i list rutan **roll** .

    1. Välj lämplig användar grupp i list rutan **grupp** .

    1. Välj **Lägg till användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar vi vår konfiguration för enkel inloggning med Azure AD via *åtkomst panelen*.

När du klickar på **kontroll panelen Workspot** i åtkomst panelen, bör du loggas in automatiskt till den Workspot-kontroll som du ställer in SSO för. Mer information finns i [Introduktion till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

- [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
