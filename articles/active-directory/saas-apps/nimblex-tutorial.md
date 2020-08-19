---
title: 'Självstudie: Azure Active Directory integrering med NimbleX | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och NimbleX.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: jeedes
ms.openlocfilehash: 595977635937ec5dcf4a45a1d57524c1d3f41a6e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554387"
---
# <a name="tutorial-azure-active-directory-integration-with-nimblex"></a>Självstudie: Azure Active Directory integrering med NimbleX

I den här självstudien får du lära dig hur du integrerar NimbleX med Azure Active Directory (Azure AD).
Genom att integrera NimbleX med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till NimbleX.
* Du kan göra det möjligt för användarna att logga in automatiskt till NimbleX (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med NimbleX behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* NimbleX-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* NimbleX stöder **SP** -INITIERAd SSO

* NimbleX stöder **just-in-Time** User-etablering

## <a name="adding-nimblex-from-the-gallery"></a>Lägga till NimbleX från galleriet

Om du vill konfigurera integreringen av NimbleX i Azure AD måste du lägga till NimbleX från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till NimbleX från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **NimbleX**, väljer **NimbleX** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![NimbleX i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med NimbleX baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i NimbleX upprättas.

Om du vill konfigurera och testa enkel inloggning med NimbleX i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera NimbleX-enkel inloggning](#configure-nimblex-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa NimbleX test User](#create-nimblex-test-user)** – om du vill ha en motsvarighet till Britta Simon i NimbleX som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med NimbleX i Azure AD:

1. Välj **enkel inloggning**på sidan **NimbleX** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för NimbleX-domän och URL: er](common/sp-identifier-reply.png)

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<YOUR APPLICATION PATH>/Login.aspx`

    b. I rutan **identifierare** anger du en URL med följande mönster: `https://<YOUR APPLICATION PATH>/`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<path-to-application>/SamlReply.aspx`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [NimbleX client support team](mailto:support@ebms.com.au) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**. Klicka på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera NimbleX** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-nimblex-single-sign-on"></a>Konfigurera NimbleX enkel inloggning

1. Logga in på NimbleX som säkerhets administratör i ett annat webbläsarfönster.

2. Klicka på **Inställningar** logo typ på den övre högra sidan av sidan.

    ![NimbleX-inställningar](./media/nimblex-tutorial/tutorial_nimblex_settings.png)

3. På **kontroll panelen** går du till avsnittet **säkerhet** och klickar på **enkel inloggning**.

    ![NimbleX-inställningar](./media/nimblex-tutorial/tutorial_nimblex_single.png)

4. På sidan **hantera enkel inloggning** väljer du ditt instans namn och klickar på **Redigera**.

    ![NimbleX SAML](./media/nimblex-tutorial/tutorial_nimblex_saml.png)

5. Utför följande steg på sidan **Redigera SSO-Provider** :

    ![NimbleX SAML](./media/nimblex-tutorial/tutorial_nimblex_sso.png)

    a. I text rutan **Beskrivning** skriver du namnet på din instans.

    b. I anteckningar öppnar du det bas-64-kodade certifikatet som du laddade ned från Azure Portal, kopierar innehållet och klistrar in det i rutan **certifikat** .

    c. Klistra in värdet för **inloggnings-URL**: en som du har kopierat från Azure Portal i text rutan för **URL för identitets leverantörens SSO-mål** .

    d. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du **brittasimon@yourcompanydomain.extension**  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till NimbleX.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **NimbleX**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **NimbleX**.

    ![NimbleX-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-nimblex-test-user"></a>Skapa NimbleX test användare

I det här avsnittet skapas en användare som kallas Britta Simon i NimbleX. NimbleX stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i NimbleX skapas en ny efter autentiseringen.

>[!Note]
>Om du behöver skapa en användare manuellt kontaktar du [NimbleX client support team](mailto:support@ebms.com.au).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen NimbleX på åtkomst panelen, bör du loggas in automatiskt på den NimbleX som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

