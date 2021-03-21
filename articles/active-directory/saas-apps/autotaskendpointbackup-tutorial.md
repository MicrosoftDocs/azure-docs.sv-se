---
title: 'Självstudie: Azure Active Directory integration med slut punkts säkerhets kopiering för aktivitet | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Autotask Endpoint Backup.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 1/19/2019
ms.author: jeedes
ms.openlocfilehash: 349a77d2f2cd5e1c492de63919d84efb436a51a3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97673712"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-endpoint-backup"></a>Självstudie: Azure Active Directory integration med aktivitets slut punkts säkerhets kopiering

I den här självstudien får du lära dig hur du integrerar Autotask Endpoint Backup med Azure Active Directory (Azure AD).
Integreringen av Autotask Endpoint Backup med Azure AD medför följande fördelar:

* Du kan styra vem som har åtkomst till Autotask Endpoint Backup från Azure AD.
* Du kan göra så att användarna automatiskt loggas in i Autotask Endpoint Backup (enkel inloggning) med deras Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Autotask Endpoint Backup behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En Autotask Endpoint Backup-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Autotask Endpoint Backup stöder **IDP**-initierad enkel inloggning

## <a name="adding-autotask-endpoint-backup-from-the-gallery"></a>Lägga till Autotask Endpoint Backup från galleriet

För att konfigurera integreringen av Autotask Endpoint Backup med Azure AD måste du lägga till Autotask Endpoint Backup från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Autotask Endpoint Backup från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Autotask Endpoint Backup**, väljer **Autotask Endpoint Backup** i resultatrutan och klickar på **Lägg till** för att lägga till programmet.

    ![Autotask Endpoint Backup i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning för Azure AD med Autotask Endpoint Backup baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Autotask Endpoint Backup upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med Autotask Endpoint Backup måste du utföra följande uppgifter:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Autotask Endpoint Backup](#configure-autotask-endpoint-backup-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa en testanvändare för Autotask Endpoint Backup](#create-autotask-endpoint-backup-test-user)** – för att skapa en motsvarighet till Britta Simon i Autotask Endpoint Backup som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med Autotask Endpoint Backup:

1. Välj **Enkel inloggning** på sidan för programintegration med **Autotask Endpoint Backup** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Information om domäner och URL:er för enkel inloggning med Autotask Endpoint Backup](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<subdomain>.backup.autotask.net/singlesignon/saml/metadata`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<subdomain>.backup.autotask.net/singlesignon/saml/SSO`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Autotask Endpoint Backup-kundsupporten](https://backup.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga URL:er baserat på dina behov i avsnittet **Konfigurera Autotask Endpoint Backup**.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-autotask-endpoint-backup-single-sign-on"></a>Konfigurera enkel inloggning för Autotask Endpoint Backup

För att konfigurera enkel inloggning på **Autotask Endpoint Backup**-sidan måste du skicka den nedladdade **XML:en med fedarationsmetadata** och lämpliga kopierade URL:er från Azure-portalen till [Autotask Endpoint Backup-supportteamet](https://backup.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du **brittasimon \@ yourcompanydomain. extension**  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att bevilja åtkomst till Autotask Endpoint Backup.

1. På Azure-portalen väljer du **Företagsprogram**, väljer **Alla program** och sedan **Autotask Endpoint Backup**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **Autotask Endpoint Backup**.

    ![Autotask Endpoint Backup-länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-autotask-endpoint-backup-test-user"></a>Skapa en testanvändare för Autotask Endpoint Backup

I det här avsnittet ska du skapa en användare med namnet Britta Simon i Autotask Endpoint Backup. Arbeta med arbets plats för [slut punkts support för aktiviteter](https://backup.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) för att lägga till användare i aktivitets slut punkts säkerhets kopierings plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för Autotask Endpoint Backup på åtkomstpanelen bör du loggas in automatiskt till det Autotask Endpoint Backup-program som du konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)