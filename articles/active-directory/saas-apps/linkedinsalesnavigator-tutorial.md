---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med LinkedIn Sales Navigator | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LinkedIn Sales Navigator.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.openlocfilehash: cab4cd34458e6107c653859c97dfda12486a38a1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549753"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-sales-navigator"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med LinkedIn Sales Navigator

I den här självstudien får du lära dig hur du integrerar LinkedIn Sales Navigator med Azure Active Directory (Azure AD). När du integrerar LinkedIn Sales Navigator med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till LinkedIn Sales Navigator.
* Gör det möjligt för användarna att logga in automatiskt till LinkedIn Sales Navigator med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* LinkedIn-prenumeration med enkel inloggning (SSO) för Sales Navigator

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* LinkedIn Sales Navigator stöder **SP-och IDP** -INITIERAd SSO
* LinkedIn Sales Navigator stöder **just-in-Time** User-etablering
* LinkedIn Sales Navigator stöder [ **automatiserad** användar etablering](linkedinsalesnavigator-provisioning-tutorial.md)

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>Lägga till LinkedIn Sales Navigator från galleriet

Om du vill konfigurera integrering av LinkedIn Sales Navigator i Azure AD måste du lägga till LinkedIn Sales Navigator från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från Galleri** skriver du **LinkedIn Sales Navigator** i sökrutan.
1. Välj **LinkedIn Sales Navigator** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-sales-navigator"></a>Konfigurera och testa enkel inloggning med Azure AD för LinkedIn Sales Navigator

Konfigurera och testa Azure AD SSO med LinkedIn Sales Navigator med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i LinkedIn Sales Navigator.

Om du vill konfigurera och testa Azure AD SSO med LinkedIn Sales Navigator, fyller du i följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera LinkedIn Sales Navigator SSO](#configure-linkedin-sales-navigator-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa en test användare av LinkedIn Sales Navigator](#create-linkedin-sales-navigator-test-user)** – om du vill ha en motsvarighet till B. Simon i LinkedIn Sales Navigator som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integration i **LinkedIn Sales Navigator** och letar reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du **entitets-ID** -värdet. du kopierar entitets-ID-värde från LinkedIn-portalen som beskrivs senare i den här självstudien.

    b. I text rutan **svars-URL** anger du URL-värdet för **intyg om konsument åtkomst (ACS)** , du kommer att kopiera URL-värdet för intyg om intygs åtkomst (ACS) från LinkedIn-portalen som beskrivs senare i den här självstudien.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

1. LinkedIn Sales Navigator-appen förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig LinkedIn Sales Navigator-programmet fler attribut som ska skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name | Källattribut|
    | --- | --- |
    | e-post| user.mail |
    | avdelning| user.department |
    | förnamn| user.givenname |
    | efternamn| user.surname |
    | Unik användaridentifierare | user.mail |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera LinkedIn Sales Navigator** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till LinkedIn Sales Navigator.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **LinkedIn Sales Navigator**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-linkedin-sales-navigator-sso"></a>Konfigurera LinkedIn Sales Navigator SSO

1. Logga in på din **LinkedIn Sales Navigator** -webbplats som administratör i ett annat webbläsarfönster.

1. Klicka på **Globala inställningar** under **Inställningar** i **Kontocenter**. Välj också **Sales Navigator** i list rutan.

    ![Konfigurera enkel inloggning](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

1. Klicka på **eller klicka här för att läsa in och kopiera enskilda fält från formuläret** och utför följande steg:

    ![Konfigurera enkel inloggning](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

    a. Kopiera **entitets-ID** och klistra in det i text rutan **identifierare** i den **grundläggande SAML-konfigurationen** i Azure Portal.

    b. Kopiera **intyg om kontroll konsument åtkomst (ACS) URL** och klistra in den i text rutan **SVARs-URL** i den **grundläggande SAML-konfigurationen** i Azure Portal.

1. Gå till avsnittet **LinkedIn-administratörsinställningar**. Ladda upp XML-filen som du har laddat ned från Azure Portal genom att klicka på alternativet **överför XML-fil** .

    ![Konfigurera enkel inloggning](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

1. Aktivera enkel inloggning genom att klicka på **På**. SSO-statusen ändras från **Inte ansluten** till **Ansluten**

    ![Konfigurera enkel inloggning](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-sales-navigator-test-user"></a>Skapa en test användare av LinkedIn Sales Navigator

Länkade Sales Navigator-program stöder just-in-Time (JIT)-användar etablering och när användare av autentisering skapas i programmet automatiskt. Aktivera **tilldela licenser automatiskt** för att tilldela en licens till användaren.

   ![Skapa en testanvändare för Azure AD](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för försäljnings navigering på LinkedIn i åtkomst panelen, bör du loggas in automatiskt på den LinkedIn-försäljnings navigering som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova LinkedIn Sales Navigator med Azure AD](https://aad.portal.azure.com/)