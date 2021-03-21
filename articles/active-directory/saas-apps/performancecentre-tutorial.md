---
title: 'Självstudie: Azure Active Directory integrering med PerformanceCentre | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PerformanceCentre.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 79255b98312b16fa21e07abad701e08578c6ca89
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515516"
---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>Självstudie: Azure Active Directory integrering med PerformanceCentre

I den här självstudien får du lära dig hur du integrerar PerformanceCentre med Azure Active Directory (Azure AD).
Genom att integrera PerformanceCentre med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till PerformanceCentre.
* Du kan göra det möjligt för användarna att logga in automatiskt till PerformanceCentre (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med PerformanceCentre behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* PerformanceCentre-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* PerformanceCentre stöder **SP** -INITIERAd SSO

## <a name="adding-performancecentre-from-the-gallery"></a>Lägga till PerformanceCentre från galleriet

Om du vill konfigurera integreringen av PerformanceCentre i Azure AD måste du lägga till PerformanceCentre från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till PerformanceCentre från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **PerformanceCentre**, väljer **PerformanceCentre** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![PerformanceCentre i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med PerformanceCentre baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i PerformanceCentre upprättas.

Om du vill konfigurera och testa enkel inloggning med PerformanceCentre i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera PerformanceCentre enkel inloggning](#configure-performancecentre-single-sign-on)** – om du vill konfigurera de enskilda Sign-On inställningarna på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa PerformanceCentre test User](#create-performancecentre-test-user)** – om du vill ha en motsvarighet till Britta Simon i PerformanceCentre som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med PerformanceCentre i Azure AD:

1. Välj **enkel inloggning** på sidan **PerformanceCentre** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för PerformanceCentre-domän och URL: er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `http://<companyname>.performancecentre.com/saml/SSO`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `http://<companyname>.performancecentre.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [PerformanceCentre client support team](https://www.performio.co/contact-us) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera PerformanceCentre** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-performancecentre-single-sign-on"></a>Konfigurera PerformanceCentre Single Sign-On

1. Logga in på din **PerformanceCentre** företags webbplats som administratör.

2. På fliken till vänster klickar du på **Konfigurera**.
   
    ![Skärm bild som visar "PerformanceCenter"-menyn med alternativet "Konfigurera" valt.][10]

3. Klicka på **Diverse** på fliken till vänster och klicka sedan på **enkel inloggning**.
   
    ![Skärm bild som visar fliken "Konfigurera" med "enkel inloggning" vald från menyn "Diverse".][11]

4. Som **protokoll** väljer du **SAML**.
   
    ![Skärm bild som visar avsnittet "enkel Sign-On konfiguration" med "S A M L" valt från "protokoll"-menyn.][12]

5. Öppna den hämtade metadatafilen i anteckningar, kopiera innehållet, klistra in det i text rutan för **identitetsprovider** och klicka sedan på **Spara**.
   
    ![Skärm bild som visar text rutan "Identity Provider metadata".][13]

6. Kontrol lera att värdena för **entitetens bas-URL** och **entitets-ID: t** är korrekta.
    
     ![Azure AD Single Sign-On][14]

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till PerformanceCentre.

1. I Azure Portal väljer du **företags program**, väljer **alla program** och väljer sedan **PerformanceCentre**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **PerformanceCentre**.

    ![PerformanceCentre-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-performancecentre-test-user"></a>Skapa PerformanceCentre test användare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i PerformanceCentre.

**Utför följande steg för att skapa en användare med namnet Britta Simon i PerformanceCentre:**

1. Logga in på din PerformanceCentre företags webbplats som administratör.

2. Klicka på **relaterad** på menyn till vänster och klicka sedan på **skapa deltagare**.
   
    ![Skärm bild som visar sidan "PerformanceCenter" företags webbplats "relaterad till deltagare" med knappen "skapa deltagare" vald.][400]

3. I dialog rutan **interrelatera – skapa deltagare** utför du följande steg:
   
    ![Skapa användare][401]
    
    a. Ange de attribut som krävs för Britta Simon i relaterade text rutor.
    
    >[!IMPORTANT]
    >Britta för användar namn i PerformanceCentre måste vara samma som användar namnet i Azure AD.
    
    b. Välj **klient administratör** som **rollen Välj roll**.
    
    c. Klicka på **Spara**. 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen PerformanceCentre på åtkomst panelen, bör du loggas in automatiskt på den PerformanceCentre som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)

<!--Image references-->

[10]: ./media/performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/performancecentre-tutorial/tutorial_performancecentre_10.png
[400]: ./media/performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/performancecentre-tutorial/tutorial_performancecentre_12.png