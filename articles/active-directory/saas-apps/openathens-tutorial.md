---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med openaten | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och OpenAthens.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: fc60552fadf10dde88c0e6d8c6f46bd25c68cf2f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543949"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-openathens"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med openaten

I den här självstudien får du lära dig hur du integrerar openaten med Azure Active Directory (Azure AD). När du integrerar openaten med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till openaten.
* Gör det möjligt för användarna att logga in automatiskt för att openaten med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Openaten enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* OpenAthens stöder **IDP**-initierad enkel inloggning
* OpenAthens stöder **just-in-time**-användaretablering

## <a name="adding-openathens-from-the-gallery"></a>Lägga till OpenAthens från galleriet

För att konfigurera integrering av OpenAthens i Azure AD behöver du lägga till OpenAthens från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **openaten** i sökrutan.
1. Välj **openaten** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-openathens"></a>Konfigurera och testa enkel inloggning med Azure AD för openaten

Konfigurera och testa Azure AD SSO med openaten med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i openaten.

Om du vill konfigurera och testa Azure AD SSO med openaten slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Openaten SSO](#configure-openathens-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa openaten test-användare](#create-openathens-test-user)** – för att få en motsvarighet till B. Simon i openaten som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan **openaten** program integration och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** laddar du upp **metadatafilen för tjänstleverantör**. Hur du gör det beskrivs senare i den här självstudien.

    a. Klicka på **Ladda upp metadatafil**.

    ![openathens upload metadata](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![Openathens browse upload metadata](common/browse-upload-metadata.png)

    c. När metadatafilen har laddats upp fylls **identifierarvärdet** i automatiskt i textrutan i avsnittet **Grundläggande SAML-konfiguration**:

    ![Information om enkel inloggning med OpenAthens-domäner och URL:er](common/idp-identifier.png)

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera openaten** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till openaten.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **openaten**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-openathens-sso"></a>Konfigurera openaten SSO

1. I ett annat webbläsarfönster loggar du in på din openaten-företags webbplats som administratör.

1. Välj **Anslutningar** i listan på fliken **Hantering**.

    ![Konfigurera enkel inloggning](./media/openathens-tutorial/tutorial_openathens_application1.png)

1. Välj **SAML 1.1/2.0**, och klicka sedan på knappen **Konfigurera**.

    ![Konfigurera enkel inloggning](./media/openathens-tutorial/tutorial_openathens_application2.png)

1. Om du vill lägga till konfigurationen väljer du knappen **Bläddra** för att överföra .xml-metadatafilen som du laddade ned från Azure-portalen, och väljer sedan **Lägg till**.

    ![Konfigurera enkel inloggning](./media/openathens-tutorial/tutorial_openathens_application3.png)

1. Utför följande steg under fliken **Information**.

    ![Konfigurera enkel inloggning](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. I **Mappning för visningsnamn** väljer du **Använd attribut**.

    b. I textrutan **Attribut för visningsnamn** fyller du i värdet `http://schema.microsoft.com/identity/claims/displayname`.

    c. I **Unik användarmappning** väljer du **Använd attribut**.

    d. I textrutan **Unikt användarattribut** fyller du i värdet `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. Markera alla tre kryssruta i **Status**.

    f. I **Skapa lokala konton** väljer du **automatiskt**.

    ex. Välj **Spara ändringar**.

    h. Från fliken **</> Förlitande part** ska du nu kopiera **Metadata-URL:en** och öppna den i webbläsaren för att ladda ned **XML-filen för SP-metadata**. Ladda upp SP-metadatafilen på avsnittet **Grundläggande SAML-konfiguration** i Azure AD.

    ![Konfigurera enkel inloggning](./media/openathens-tutorial/tutorial_openathens_application5.png)

### <a name="create-openathens-test-user"></a>Skapa OpenAthens-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i OpenAthens. OpenAthens stöder **just-in-time-etablering av användare**, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i OpenAthens skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på OpenAthens-panelen i åtkomstpanelen bör du automatiskt loggas in på OpenAthens som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova openaten med Azure AD](https://aad.portal.azure.com/)