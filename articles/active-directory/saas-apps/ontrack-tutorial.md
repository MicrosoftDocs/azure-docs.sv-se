---
title: 'Självstudie: Azure Active Directory integrering med OnTrack | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och OnTrack.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: 8470455f3d72ea101336e8368b420a9340aac317
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92522408"
---
# <a name="tutorial-azure-active-directory-integration-with-ontrack"></a>Självstudie: Azure Active Directory integrering med OnTrack

I den här självstudien får du lära dig att integrera OnTrack med Azure Active Directory (Azure AD).
Genom att integrera OnTrack med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till OnTrack.
* Du kan göra det möjligt för användarna att logga in automatiskt till OnTrack (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med OnTrack behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Enkel inloggning aktive rad OnTrack-prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* OnTrack stöder **IDP** -INITIERAd SSO

## <a name="adding-ontrack-from-the-gallery"></a>Lägga till OnTrack från galleriet

Om du vill konfigurera integrering av OnTrack i Azure AD måste du lägga till OnTrack från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till OnTrack från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Ontrack**, väljer **Ontrack** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![OnTrack i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med OnTrack baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i OnTrack upprättas.

Om du vill konfigurera och testa enkel inloggning med OnTrack för Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning med Ontrack](#configure-ontrack-single-sign-on)** för att konfigurera de enskilda Sign-On inställningarna på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Ontrack test User](#create-ontrack-test-user)** -om du vill ha en motsvarighet till Britta Simon i Ontrack som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med OnTrack:

1. I [Azure Portal](https://portal.azure.com/)på sidan **Ontrack** program integration väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Information om enkel inloggning med OnTrack-domän och URL: er](common/idp-intiated.png)

    a. I text rutan **identifierare** :

    Ange URL: en för test miljön: `https://staging.insigniagroup.com/sso`

    Ange URL: en för produktions miljön: `https://oeaccessories.com/sso`

    b. I text rutan **svars-URL** :

    Ange URL: en för test miljön: `https://indie.staging.insigniagroup.com/sso/autonation.aspx`

    Ange URL: en för produktions miljön: `https://igaccessories.com/sso/autonation.aspx`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Ontrack client support team](mailto:CustomerService@insigniagroup.com) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. OnTrack-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade attribut mappningar i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på **Redigera** ikon för att öppna dialog rutan **användarattribut** .

    ![Skärm bild som visar dialog rutan "användarattribut" med ikonen "redigera" markerad i det övre högra hörnet.](common/edit-attribute.png)

6. Förutom ovan förväntar sig OnTrack-program att fler attribut skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Name | Källattribut|
    | -------------- | ----------------|    
    | User-Role      | "42F432" |
    | Hyperion-Code  | "12345" |

    > [!NOTE]
    > Attributen för **användarens roll** och **Hyperion** mappas med den här användar rollen och åter Förhandlarens kod. Dessa värden är endast exempel, Använd rätt kod för din integrering. Du kan kontakta [AutoNation-supporten](mailto:CustomerService@insigniagroup.com) för dessa värden.

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![Skärm bild som visar dialog rutan "användar anspråk" med åtgärderna "Lägg till nytt anspråk" och "Spara" markerade.](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **OK**

    ex. Klicka på **Spara**.

7. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

8. I avsnittet **Konfigurera Ontrack** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-ontrack-single-sign-on"></a>Konfigurera OnTrack enkla Sign-On

Om du vill konfigurera enkel inloggning på **Ontrack** -sidan måste du skicka den hämtade **XML-metadata för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till [Ontrack support team](mailto:CustomerService@insigniagroup.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till OnTrack.

1. I Azure Portal väljer du **företags program**, väljer **alla program** och väljer sedan **Ontrack**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Ontrack** i listan program.

    ![Länken OnTrack i listan program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-ontrack-test-user"></a>Skapa OnTrack test användare

I det här avsnittet skapar du en användare som heter Britta Simon i OnTrack. Arbeta med [Ontrack support team](mailto:CustomerService@insigniagroup.com) för att lägga till användarna i Ontrack-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen OnTrack på åtkomst panelen bör du loggas in automatiskt på den OnTrack som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)