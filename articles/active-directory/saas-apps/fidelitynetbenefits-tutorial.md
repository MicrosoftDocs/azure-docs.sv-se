---
title: 'Självstudie: Azure Active Directory integrering med Fidelity NetBenefits | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Fidelity NetBenefits.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.openlocfilehash: 199a443bf2f0c674f85fec0ecfd0083c92f17fe4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555240"
---
# <a name="tutorial-azure-active-directory-integration-with-fidelity-netbenefits"></a>Självstudie: Azure Active Directory integrering med Fidelity NetBenefits

I den här självstudien lär du dig att integrera Fidelity NetBenefits med Azure Active Directory (AD Azure).
Integreringen av Fidelity NetBenefits med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Fidelity NetBenefits.
* Du kan göra så att dina användare loggas in automatiskt på Fidelity NetBenefits (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Fidelity NetBenefits behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Fidelity NetBenefits-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Fidelity NetBenefits har stöd för **IDP**-initierad enkel inloggning

* Fidelity NetBenefits har stöd för **just-in-time**-användaretablering

## <a name="adding-fidelity-netbenefits-from-the-gallery"></a>Lägga till Fidelity NetBenefits från galleriet

För att konfigurera integreringen av Fidelity NetBenefits i Azure AD måste du lägga till Fidelity NetBenefits från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Fidelity NetBenefits från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Fidelity NetBenefits**, väljer **Fidelity NetBenefits** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Fidelity NetBenefits i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Fidelity NetBenefits baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Fidelity NetBenefits upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med Fidelity NetBenefits behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Fidelity NetBenefits](#configure-fidelity-netbenefits-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Fidelity NetBenefits-testanvändare](#create-fidelity-netbenefits-test-user)** – för att ha en motsvarighet för Britta Simon i Fidelity NetBenefits som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för Fidelity NetBenefits:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Fidelity NetBenefits** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Fidelity NetBenefits-domän och information om URL:er för enkel inloggning](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:  

    För testmiljö: `urn:sp:fidelity:geninbndnbparts20:uat:xq1`

    För produktionsmiljö: `urn:sp:fidelity:geninbndnbparts20`

    b. I textrutan **Svars-URL** skriver en URL som ska tillhandahållas av Fidelity vid tidpunkten för implementering eller kontaktar din tilldelade kundservicechef för Fidelity.

5. Fidelity NetBenefits-programmet förväntar sig SAML-försäkringar i ett specifikt format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. Fidelity NetBenefits-programmet förväntar sig att **nameidentifier** mappas med **employeeid** eller något annat anspråk som gäller för din organisation som **nameidentifier**, så du behöver redigera attributmappningen genom att klicka på ikonen **Redigera** och ändra attributmappningen.

    ![image](common/edit-attribute.png)

    >[!Note]
    >Fidelity NetBenefits har stöd för och dynamisk federation. Statisk innebär att den inte använder SAML-baserad just-in-time-användaretablering, och dynamisk innebär att den har stöd för just-in-time-användaretablering. För att använda JIT-baserade etablerings kunder måste du lägga till några fler anspråk i Azure AD som användarens födelse datum osv. Den här informationen tillhandahålls av en tilldelad **åter givning-klient Service Manager** och de måste aktivera den dynamiska federationen för din instans.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. I avsnittet **Konfigurera Fidelity NetBenefits** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-fidelity-netbenefits-single-sign-on"></a>Konfigurera enkel inloggning för Fidelity NetBenefits

För att konfigurera enkel inloggning på **Fidelity NetBenefits**-sidan behöver du skicka nedladdad **XML med federationsmetadata** och lämpliga kopierade URL:er från Azure-portalen till [supportteamet för Fidelity NetBenefits](mailto:SSOMaintenance@fmr.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Fidelity NetBenefits.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Fidelity NetBenefits**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Fidelity NetBenefits**.

    ![Länken för Fidelity NetBenefits i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-fidelity-netbenefits-test-user"></a>Skapa Fidelity NetBenefits-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Fidelity NetBenefits. Om du skapar statisk federation bör du ta hjälp av din tilldelade **kundservicechef för Fidelity** för att skapa användare på Fidelity NetBenefits-plattformen. Dessa användare måste skapas och aktiveras innan du använder enkel inloggning.

För dynamisk federation skapas användare med just-in-time-användaretablering. För att använda JIT-baserade etablerings kunder måste du lägga till några fler anspråk i Azure AD som användarens födelse datum osv. Den här informationen tillhandahålls av en tilldelad **åter givning-klient Service Manager** och de måste aktivera den dynamiska federationen för din instans.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Fidelity NetBenefits-panelen i åtkomstpanelen bör du automatiskt loggas in på Fidelity NetBenefits som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

