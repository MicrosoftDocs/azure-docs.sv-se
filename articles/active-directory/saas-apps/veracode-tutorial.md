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
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: f56f2dc974df58575c72c93a0609026cd7bbf88d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101652631"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Veracode

I den här självstudien får du lära dig hur du integrerar Veracode med Azure Active Directory (Azure AD). När du integrerar Veracode med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Veracode.
* Gör det möjligt för användarna att logga in automatiskt till Veracode med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En Veracode-aktiverad prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. Veracode stöder identitets leverantören som initierat SSO och just-in-Time User-etablering.

## <a name="add-veracode-from-the-gallery"></a>Lägg till Veracode från galleriet

Om du vill konfigurera integreringen av Veracode i Azure AD lägger du till Veracode från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. Skriv "Veracode" i sökrutan i avsnittet **Lägg till från galleriet** .
1. Välj **Veracode** i panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-veracode"></a>Konfigurera och testa Azure AD SSO för Veracode

Konfigurera och testa Azure AD SSO med Veracode med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk mellan en Azure AD-användare och en relaterad användare i Veracode.

Utför följande steg för att konfigurera och testa Azure AD SSO med Veracode:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning i Azure AD med B. Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
1. **[Konfigurera VERACODE SSO](#configure-veracode-sso)** för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa en Veracode-testanvändare](#create-veracode-test-user)** som ska ha en motsvarighet till B. Simon i Veracode som är länkad till Azure AD-åter givningen av användaren.
1. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **Veracode** och letar upp avsnittet **Hantera** . Välj **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

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

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Veracode.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Veracode**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-veracode-sso"></a>Konfigurera Veracode SSO

1. Logga in på din Veracode-företags webbplats som administratör i ett annat webbläsarfönster.

1. I menyn högst upp väljer du **Inställningar**  >  **administratör**.
   
    ![Skärm bild av Veracode-administration med inställnings ikon och administratör markerad](./media/veracode-tutorial/admin.png "Administration")

1. Välj fliken **SAML** .

1. I avsnittet **organisations SAML-inställningar** utför du följande steg:

    ![Skärm bild av avsnittet SAML-inställningar för organisation](./media/veracode-tutorial/saml.png "Administration")

    a.  För **utfärdare** klistrar du in värdet för den **Azure AD-identifierare** som du kopierade från Azure Portal.

    b. För **certifikat för kontrollerande signering** väljer du **Välj fil** för att ladda upp det hämtade certifikatet från Azure Portal.

    c. För **själv registrering** väljer du **Aktivera själv registrering**.

1. I avsnittet **Inställningar för själv registrering** utför du följande steg och väljer sedan **Spara**:

    ![Skärm bild av avsnittet själv registrerings inställningar med olika alternativ markerade](./media/veracode-tutorial/save.png "Administration")

    a. För **ny användar aktivering** väljer du **ingen aktivering krävs**.

    b. För **användar data uppdateringar** väljer du **Preference Veracode user data**.

    c. För **information om SAML-attribut** väljer du följande:
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

### <a name="create-veracode-test-user"></a>Skapa Veracode test användare

I det här avsnittet skapas en användare som heter B. Simon i Veracode. Veracode stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärds objekt i det här avsnittet. Om en användare inte redan finns i Veracode skapas en ny efter autentiseringen.

> [!NOTE]
> Du kan använda andra verktyg för Veracode av användar konton eller API: er som tillhandahålls av Veracode för att etablera Azure AD-användarkonton.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal så bör du loggas in automatiskt på den Veracode som du ställer in SSO för.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Veracode i Mina appar, bör du loggas in automatiskt på den Veracode som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Veracode kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).