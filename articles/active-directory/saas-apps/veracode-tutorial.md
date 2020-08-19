---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Veracode | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Veracode.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: jeedes
ms.openlocfilehash: a8c7c70e7956868bf069704eb5fe34db014dd135
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88532475"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Veracode

I den här självstudien får du lära dig hur du integrerar Veracode med Azure Active Directory (Azure AD). När du integrerar Veracode med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Veracode.
* Gör det möjligt för användarna att logga in automatiskt till Veracode med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

Om du vill veta mer om SaaS (Software as a Service) med Azure AD, se [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En Veracode-aktiverad prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. Veracode stöder identitets leverantören som initierat SSO och just-in-Time User-etablering.

## <a name="add-veracode-from-the-gallery"></a>Lägg till Veracode från galleriet

Om du vill konfigurera integreringen av Veracode i Azure AD lägger du till Veracode från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. Skriv "Veracode" i sökrutan i avsnittet **Lägg till från galleriet** .
1. Välj **Veracode** i panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-veracode"></a>Konfigurera och testa enkel inloggning med Azure AD för Veracode

Konfigurera och testa Azure AD SSO med Veracode med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk mellan en Azure AD-användare och en relaterad användare i Veracode.

Om du vill konfigurera och testa Azure AD SSO med Veracode, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning i Azure AD med B. Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
1. **[Konfigurera VERACODE SSO](#configure-veracode-sso)** för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa en Veracode-testanvändare](#create-veracode-test-user)** som ska ha en motsvarighet till B. Simon i Veracode som är länkad till Azure AD-åter givningen av användaren.
1. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Veracode** och letar upp avsnittet **Hantera** . Välj **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Skärm bild av konfiguration av enkel inloggning med SAML med Penn ikonen markerad](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** är programmet förkonfigurerat och de nödvändiga URL: erna är redan ifyllda med Azure. Välj **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **certifikat (base64)**. Välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Skärm bild av avsnittet SAML-signerings certifikat med hämtnings länken markerad](common/certificatebase64.png)

1. Veracode förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![Skärm bild av avsnittet användarattribut &-anspråk](common/default-attributes.png)

1. Veracode förväntar sig också några fler attribut som ska skickas tillbaka i SAML-svaret. Dessa attribut har också fyllts i automatiskt, men du kan granska dem enligt dina krav.

    | Name | Källattribut|
    | ---------------| --------------- |
    | förnamn |User. givenName |
    | efternamn |Användare. efter namn |
    | e-post |User.mail |

1. I avsnittet **Konfigurera Veracode** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Skärm bild av avsnittet Konfigurera Veracode med konfigurations-URL: er markerade](common/copy-configuration-urls.png)

## <a name="configure-veracode-sso"></a>Konfigurera Veracode SSO

1. Logga in på din Veracode-företags webbplats som administratör i ett annat webbläsarfönster.

1. I menyn högst upp väljer du **Inställningar**  >  **administratör**.
   
    ![Skärm bild av Veracode-administration med inställnings ikon och administratör markerad](./media/veracode-tutorial/ic802911.png "Administration")

1. Välj fliken **SAML** .

1. I avsnittet **organisations SAML-inställningar** utför du följande steg:

    ![Skärm bild av avsnittet SAML-inställningar för organisation](./media/veracode-tutorial/ic802912.png "Administration")

    a.  För **utfärdare**klistrar du in värdet för den **Azure AD-identifierare** som du kopierade från Azure Portal.

    b. För **certifikat för kontrollerande signering**väljer du **Välj fil** för att ladda upp det hämtade certifikatet från Azure Portal.

    c. För **själv registrering**väljer du **Aktivera själv registrering**.

1. I avsnittet **Inställningar för själv registrering** utför du följande steg och väljer sedan **Spara**:

    ![Skärm bild av avsnittet själv registrerings inställningar med olika alternativ markerade](./media/veracode-tutorial/ic802913.png "Administration")

    a. För **ny användar aktivering**väljer du **ingen aktivering krävs**.

    b. För **användar data uppdateringar**väljer du **Preference Veracode user data**.

    c. För **information om SAML-attribut**väljer du följande:
      * **Användar roller**
      * **Princip administratör**
      * **Granskare**
      * **Säkerhets lead**
      * **Chef**
      * **Jobbets avsändare**
      * **Creator**
      * **Alla genomsöknings typer**
      * **Grupp medlemskap**
      * **Standard team**

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**  > **användare**  >  **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:

   1. Som **namn**anger du `B.Simon` .  
   1. För **användar namn**anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Välj **Visa lösen ord**och skriv sedan ned värdet som visas.
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Veracode.

1. I Azure Portal väljer du **företags program**  >  **alla program**.
1. I listan program väljer du **Veracode**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Skärm bild av avsnittet hantera med användare och grupper markerade](common/users-groups-blade.png)

1. Välj **Lägg till användare**. I dialog rutan **Lägg till tilldelning** väljer **du användare och grupper**.

    ![Skärm bild av sidan användare och grupper där Lägg till användare är markerat](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon**från **användare**. Välj sedan **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML-intyget väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** . Välj sedan **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-veracode-test-user"></a>Skapa Veracode test användare

För att kunna logga in på Veracode måste Azure AD-användare tillhandahållas i Veracode. Den här uppgiften är automatiserad och du behöver inte göra något manuellt. Användare skapas automatiskt vid behov under det första försöket vid enkel inloggning.

> [!NOTE]
> Du kan använda andra verktyg för Veracode av användar konton eller API: er som tillhandahålls av Veracode för att etablera Azure AD-användarkonton.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du din konfiguration av enkel inloggning för Azure AD med hjälp av Åtkomstpanelen.

När du väljer **Veracode** i åtkomst panelen bör du loggas in automatiskt på den Veracode som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Veracode med Azure AD](https://aad.portal.azure.com/)