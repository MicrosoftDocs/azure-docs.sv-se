---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Catchpoint'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Catchpoint.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.openlocfilehash: 649396b81402e9229eb9ea2c627b60f249f8c601
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88530315"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-catchpoint"></a>Självstudie: Azure Active Directory integration med enkel inloggning med Catchpoint

I den här självstudien får du lära dig hur du integrerar Catchpoint med Azure Active Directory (Azure AD). När du integrerar Catchpoint med Azure AD kan du:

* Kontrol lera användar åtkomst till Catchpoint från Azure AD.
* Aktivera automatisk Catchpoint-inloggning för användare med Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En Catchpoint-prenumeration med enkel inloggning aktive rad (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Catchpoint stöder SP-initierad och IDP-initierad SSO.
* Catchpoint stöder just-in-Time (JIT)-användar etablering.
* När du har konfigurerat Catchpoint kan du framtvinga kontroll av sessionen. Den här försiktighets åtgärden skyddar mot exfiltrering och intrånget av organisationens känsliga data i real tid. Session Control är en utökning av villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-catchpoint-from-the-gallery"></a>Lägg till Catchpoint från galleriet

Om du vill konfigurera integreringen av Catchpoint i Azure AD lägger du till Catchpoint i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets-, skol-eller personligt Microsoft-konto.
1. I den vänstra rutan väljer du tjänsten **Azure Active Directory** .
1. Gå till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Catchpoint** i sökrutan.
1. Välj **Catchpoint** på panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-catchpoint"></a>Konfigurera och testa enkel inloggning med Azure AD för Catchpoint

För att SSO ska fungera måste du länka en Azure AD-användare till en användare i Catchpoint. I den här självstudien konfigurerar vi en test användare som kallas **B. Simon**. 

Slutför följande avsnitt:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso)för att aktivera den här funktionen för dina användare.
    * [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)för att testa enkel inloggning i Azure AD med B. Simon.
    * [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)för att aktivera B. Simon att använda enkel inloggning med Azure AD.
1. [Konfigurera CATCHPOINT SSO](#configure-catchpoint-sso)för att konfigurera inställningarna för enkel inloggning på program sidan.
    * [Skapa Catchpoint test User](#create-a-catchpoint-test-user)för att tillåta länkning av B. Simon Azure AD-testkontot till ett liknande användar konto i Catchpoint.
1. [Testa SSO](#test-sso)för att verifiera att konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen i Azure Portal för att aktivera Azure AD SSO:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. På sidan **Catchpoint** program integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du Penn ikonen för att redigera de grundläggande inställningarna för **SAML-konfigurationen** .

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Konfigurera det initierade läget för Catchpoint:
   - Ange värden för följande fält för **IDP**-initierat läge:
     - För **identifierare**: `https://portal.catchpoint.com/SAML2`
     - För **svars-URL**: `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`
   - För **SP**-initierat läge väljer du **Ange ytterligare URL: er** och anger följande värde:
     - För **inloggnings-URL**: `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Catchpoint-programmet förväntar sig SAML-intyg i ett särskilt format. Lägg till anpassade mappningar av attribut i konfigurationen av SAML-token-attribut. Följande tabell innehåller en lista över standardattribut:

    | Name | Källattribut|
    | ------------ | --------- |
    | GivenName | User. givenneame |
    | Efternamn | user.surname |
    | EmailAddress | user.mail |
    | Name | user.userprincipalname |
    | Unik användaridentifierare | user.userprincipalname |

    ![Skärm bild för användarattribut & lista över anspråk](common/default-attributes.png)

1. Dessutom förväntar sig Catchpoint-programmet att ett annat attribut skickas i ett SAML-svar. Se följande tabell. Det här attributet är också ifyllt, men du kan granska och uppdatera det så att det passar dina behov.

    | Name | Källattribut|
    | ------------ | --------- |
    | namnområde | user.assignedrole |

    > [!NOTE]
    > `namespace`Anspråket måste mappas med konto namnet. Det här konto namnet måste konfigureras med en roll i Azure AD för att kunna skickas tillbaka i SAML-svar. Mer information om roller i Azure AD finns i [Konfigurera roll anspråk som utfärdats i SAML-token för företags program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management).

1. Gå till sidan **Konfigurera enkel inloggning med SAML** . I avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)**. Välj **Ladda ned** för att spara certifikatet på din dator.

    ![Länken Hämta certifikat](common/certificatebase64.png)

1. I avsnittet **Konfigurera Catchpoint** kopierar du de webb adresser som du behöver i ett senare steg.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet använder du Azure Portal för att skapa en Azure AD-test användare som heter B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Ange till exempel `B.Simon@contoso.com`.
   1. Markera kryss rutan **Visa lösen ord** . Observera värdet för lösen ord som visas.
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Catchpoint.

1. I Azure Portal väljer du **företags program**  >  **alla program**.
1. I listan program väljer du **Catchpoint**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Länken "Lägg till användare"](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan över användare. Klicka på **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML-kontrollen, tittar du i dialog rutan **Välj roll** och väljer användarens roll i listan. Klicka på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-catchpoint-sso"></a>Konfigurera Catchpoint SSO

1. Logga in på Catchpoint-programmet som administratör i ett annat webbläsarfönster.

1. Välj **inställnings** ikonen och sedan **SSO Identity Provider**.

    ![Skärm bild för Catchpoint inställningar med SSO-identitetsprovider vald](./media/catchpoint-tutorial/configuration1.png)

1. Ange följande fält på sidan **enkel inloggning** :

   ![Skärm bild av Catchpoint enkel inloggning](./media/catchpoint-tutorial/configuration2.png)

   Fält | Värde
   ----- | ----- 
   **Namnområde** | Ett giltigt namn områdes värde.
   **Utfärdare av identitets leverantör** | `Azure AD Identifier`Värdet från Azure Portal.
   **URL för enkel inloggning** | `Login URL`Värdet från Azure Portal.
   **Certifikatmallens** | Innehållet i den hämtade `Certificate (Base64)` filen från Azure Portal. Använd anteckningar för att visa och kopiera.

   Du kan också ladda upp **XML-metadata för federationsmetadata** genom att välja alternativet **Ladda upp metadata** .

1. Välj **Spara**.

### <a name="create-a-catchpoint-test-user"></a>Skapa en Catchpoint-test användare

Catchpoint stöder just-in-Time-etablering, som är aktiverat som standard. Du har inga åtgärds objekt i det här avsnittet. Om B. Simon inte redan finns som en användare i Catchpoint, skapas det efter autentiseringen.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med hjälp av portalen Mina appar.

När du väljer panelen Catchpoint i portalen Mina appar, bör du loggas in automatiskt till Catchpoint-appen med SSO konfigurerat. Mer information om mina apps-portalen finns i [Logga in och starta appar från portalen Mina appar](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

> [!NOTE]
> När du är inloggad på Catchpoint-programmet via inloggnings sidan anger du det giltiga **namn område** svärdet i fältet **företagets autentiseringsuppgifter (SSO)** efter att ha angett **Catchpoint-autentiseringsuppgifter**och väljer **Logga in**.
> 
> ![Catchpoint-konfiguration](./media/catchpoint-tutorial/loginimage.png)

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Catchpoint med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
