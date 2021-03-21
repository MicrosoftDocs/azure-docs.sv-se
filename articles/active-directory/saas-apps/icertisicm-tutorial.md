---
title: 'Självstudie: Azure Active Directory integrering med Icertis för avtals hanterings plattform | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Icertis för avtals hantering.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: f6b1ea00684495333ba7b04dbf146aa6104fa589
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92460520"
---
# <a name="tutorial-azure-active-directory-integration-with-icertis-contract-management-platform"></a>Självstudie: Azure Active Directory integrering med Icertis för avtals hanterings plattform

I den här självstudien får du lära dig att integrera Icertis för avtals hantering med Azure Active Directory (Azure AD).
Genom att integrera Icertis för avtals hantering med Azure AD får du följande fördelar:

* Du kan kontrol lera Azure AD som har åtkomst till Icertis-hanterings plattform.
* Du kan göra det möjligt för användarna att logga in automatiskt till Icertis för avtals hantering (enkel inloggning) med deras Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Icertis-kontrakts hanterings plattform behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Icertis för avtals hanterings plattform med enkel inloggning aktive rad

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Icertis kontrakts hanterings plattform stöder **SP** -INITIERAd SSO

## <a name="adding-icertis-contract-management-platform-from-the-gallery"></a>Lägga till Icertis för avtals hantering från galleriet

Om du vill konfigurera integreringen av Icertis för avtals hantering i Azure AD måste du lägga till Icertis för avtals hanterings plattform från galleriet till listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Icertis för avtals hanterings plattform från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Icertis för avtals hanterings plattform**, väljer **Icertis avtals hanterings plattform** från resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

     ![Icertis för avtals hanterings plattform i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Icertis för avtals hantering baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Icertis för avtals hantering etableras.

Om du vill konfigurera och testa enkel inloggning i Azure AD med Icertis-hanterings plattform måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Icertis för hantering av kontrakts hanterings plattform – om](#configure-icertis-contract-management-platform-single-sign-on)** du vill konfigurera enskilda Sign-On inställningar på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Icertis-test av kontrakts hanterings plattform](#create-icertis-contract-management-platform-test-user)** för att få en motsvarighet till Britta Simon i Icertiss avtals hanterings plattform som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med Icertis-kontrakts hanterings plattform:

1. I [Azure Portal](https://portal.azure.com/)på sidan för **hantering av plattforms program för Icertis på avtals** sidan väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om Icertis-kontrakts hanterings plattform och URL-adresser för enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<company name>.icertis.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<company name>.icertis.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [support teamet för Icertis-kontrakts hanterings plattform](https://www.icertis.com/company/contact/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera Icertis för avtals hantering** kopierar du lämpliga URL: er enligt ditt krav. För **inloggnings-URL** använder du värdet med följande mönster: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ är klient-ID: t för Azure AD-prenumerationen.

    ![Kopiera konfigurations-URL:er](media/icertisicm-tutorial/configurls.png)

    a. Azure AD-identifierare

    b. Utloggnings-URL

### <a name="configure-icertis-contract-management-platform-single-sign-on"></a>Konfigurera Icertis för avtals hanterings plattform Sign-On

Om du vill konfigurera enkel inloggning på Icertis-sidan för **avtals hanterings plattform** måste du skicka den hämtade **XML-metadata för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till [Icertis kontrakts hantering plattform support team](https://www.icertis.com/company/contact/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** `brittasimon@yourcompanydomain.extension` . Till exempel BrittaSimon@contoso.com.

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Icertis-kontrakt för hanterings plattform.

1. I Azure Portal väljer du **företags program**, väljer **alla program** och väljer sedan **Icertis kontrakts hanterings plattform**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Icertis kontrakt hanterings plattform**.

    ![Länken Icertis-kontrakts hanterings plattform i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-icertis-contract-management-platform-test-user"></a>Skapa Icertis-test användare av kontrakts hanterings plattform

I det här avsnittet ska du skapa en användare med namnet Britta Simon i Icertis-avtal för hanterings plattform. Arbeta med [support teamet för Icertis-kontrakts hanterings plattform](https://www.icertis.com/company/contact/) för att lägga till användare i Icertis-plattform för avtals hantering. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för Icertis av kontrakts hantering i åtkomst panelen, bör du loggas in automatiskt på den Icertis avtals hanterings plattform som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)