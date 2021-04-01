---
title: 'Självstudie: Azure Active Directory integrering med Panorama9 | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Panorama9.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: e79d8584a7c760fa0a50a614390370ee7ff8ae15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92512505"
---
# <a name="tutorial-azure-active-directory-integration-with-panorama9"></a>Självstudie: Azure Active Directory integrering med Panorama9

I den här självstudien får du lära dig hur du integrerar Panorama9 med Azure Active Directory (Azure AD).
Genom att integrera Panorama9 med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Panorama9.
* Du kan göra det möjligt för användarna att logga in automatiskt till Panorama9 (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Panorama9 behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Panorama9-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Panorama9 stöder **SP** -INITIERAd SSO

## <a name="adding-panorama9-from-the-gallery"></a>Lägga till Panorama9 från galleriet

Om du vill konfigurera integreringen av Panorama9 i Azure AD måste du lägga till Panorama9 från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Panorama9 från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Panorama9**, väljer **Panorama9** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Panorama9 i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Panorama9 baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Panorama9 upprättas.

Om du vill konfigurera och testa enkel inloggning med Panorama9 i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Panorama9 enkel inloggning](#configure-panorama9-single-sign-on)** – om du vill konfigurera de enskilda Sign-On inställningarna på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Panorama9 test User](#create-panorama9-test-user)** – om du vill ha en motsvarighet till Britta Simon i Panorama9 som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Panorama9 i Azure AD:

1. Välj **enkel inloggning** på sidan **Panorama9** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för Panorama9-domän och URL: er](common/sp-identifier.png)

    a. I rutan **Inloggnings-URL** anger du en URL: `https://dashboard.panorama9.com/saml/access/3262`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://www.panorama9.com/saml20/<tenant-name>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Panorama9 client support team](https://support.panorama9.com/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

6. I avsnittet **SAML-signerings certifikat** kopierar du **tumavtrycket** och sparar det på din dator.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

7. I avsnittet **Konfigurera Panorama9** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-panorama9-single-sign-on"></a>Konfigurera Panorama9 Single Sign-On

1. Logga in på din Panorama9-företags webbplats som administratör i ett annat webbläsarfönster.

2. I verktygsfältet högst upp klickar du på **Hantera** och sedan på **tillägg**.
   
    ![Tillägg](./media/panorama9-tutorial/ic790023.png "Tillägg")

3. I dialog rutan **tillägg** klickar du på **enkel inloggning**.
   
    ![Enkel inloggning](./media/panorama9-tutorial/ic790024.png "för Aha!")

4. Utför följande steg i avsnittet **Inställningar** :
   
    ![Inställningar](./media/panorama9-tutorial/ic790025.png "Inställningar")
   
    a. I text rutan **URL för identitetsprovider** klistrar du in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal.
   
    b. I text rutan **finger avtryck** klistrar du in **tumavtrycket** för certifikat, som du har kopierat från Azure Portal.    
         
5. Klicka på **Spara**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Panorama9.

1. I Azure Portal väljer du **företags program**, väljer **alla program** och väljer sedan **Panorama9**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Panorama9**.

    ![Panorama9-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-panorama9-test-user"></a>Skapa Panorama9 test användare

För att Azure AD-användare ska kunna logga in på Panorama9 måste de tillhandahållas i Panorama9.  

När det gäller Panorama9 är etableringen en manuell uppgift.

**Konfigurera användaretablering genom att utföra följande steg:**

1. Logga in på din **Panorama9** -företags webbplats som administratör.

2. I menyn högst upp klickar du på **Hantera** och sedan på **användare**.
   
    ![Skärm bild som visar flikarna hantera och användare markerade.](./media/panorama9-tutorial/ic790027.png "Användare")

3. I avsnittet användare klickar du på **+** för att lägga till en ny användare.

    ![Användare](./media/panorama9-tutorial/ic790028.png "Användare")

4. Gå till avsnittet användar data, ange e-postadressen till en giltig Azure Active Directory användare som du vill etablera i text rutan för **e-post** .

5. Kom till avsnittet användare, klicka på **Spara**.
   
    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Panorama9 på åtkomst panelen, bör du loggas in automatiskt på den Panorama9 som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)