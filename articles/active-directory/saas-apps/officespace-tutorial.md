---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med OfficeSpace-programvara | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och OfficeSpace-programvara.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: jeedes
ms.openlocfilehash: ae8a1fa68e61e160ce08b93bf66776a050b45613
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554204"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-officespace-software"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med OfficeSpace-programvara

I den här självstudien får du lära dig att integrera OfficeSpace-program med Azure Active Directory (Azure AD). När du integrerar OfficeSpace-program med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till OfficeSpace-programvaran.
* Gör det möjligt för användarna att logga in automatiskt för att OfficeSpace program vara med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* OfficeSpace program vara för enkel inloggning (SSO) med aktiverade prenumerationer.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* OfficeSpace-programvara stöder **SP** -INITIERAd SSO


* OfficeSpace-programvaran stöder **just-in-Time** User-etablering


## <a name="adding-officespace-software-from-the-gallery"></a>Lägga till OfficeSpace-programvara från galleriet

Om du vill konfigurera integreringen av OfficeSpace-programvaran i Azure AD måste du lägga till OfficeSpace-program från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **OfficeSpace Software** i sökrutan.
1. Välj **OfficeSpace-program** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-officespace-software"></a>Konfigurera och testa enkel inloggning med Azure AD för OfficeSpace-programvara

Konfigurera och testa Azure AD SSO med OfficeSpace-programvara med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i OfficeSpace-programvaran.

Om du vill konfigurera och testa Azure AD SSO med OfficeSpace-programvaran slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera OfficeSpace Software SSO](#configure-officespace-software-sso)** – för att konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa OfficeSpace Software test User](#create-officespace-software-test-user)** – för att få en motsvarighet till B. Simon i OfficeSpace-programvaran som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för **program** integrering i OfficeSpace och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `<company name>.officespacesoftware.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [OfficeSpace Software client support team](mailto:support@officespacesoftware.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. OfficeSpace program vara förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. OfficeSpace program vara förväntar sig att **NameIdentifier** mappas med **User. mail**, så du måste redigera mappningen av attribut genom att klicka på ikonen **Redigera** och ändra attributet mappning.

    ![image](common/edit-attribute.png)

1. Utöver ovan förväntar sig OfficeSpace-programprogram att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem efter behov.

    | Name | Källattribut|
    | ---------------| --------------- |
    | e-post | user.mail |
    | name | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

1. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

1. I avsnittet **SAML-signeringscertifikat** , kopierar du **värdet tumavtryck** och sparar det på din dator.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

1. I avsnittet **Konfigurera OfficeSpace-programvara** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till OfficeSpace-programvaran.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **OfficeSpace-programvara**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="configure-officespace-software-sso"></a>Konfigurera OfficeSpace Software SSO

1. Logga in på din OfficeSpace-program vara som administratör i ett annat webbläsarfönster.

2. Gå till **Inställningar** och klicka på **kopplingar**.

    ![Konfigurera enkel inloggning på appsidan](./media/officespace-tutorial/tutorial_officespace_002.png)

3. Klicka på **SAML-autentisering**.

    ![Konfigurera enkel inloggning på appsidan](./media/officespace-tutorial/tutorial_officespace_003.png)

4. Gör följande i avsnittet **SAML-autentisering**:

    ![Konfigurera enkel inloggning på appsidan](./media/officespace-tutorial/tutorial_officespace_004.png)

    a. I text rutan **utloggnings leverantörens URL** klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure Portal.

    b. I text rutan **mål-URL för IDP** klistrar du in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal.

    c. Klistra in det **tumavtryck** -värde som du kopierade från Azure Portal i text rutan **finger avtryck för klient IDP-certifikat** . 

    d. Klicka på **Spara inställningar**.

### <a name="create-officespace-software-test-user"></a>Skapa OfficeSpace program vara test användare

I det här avsnittet skapas en användare som heter B. Simon i OfficeSpace-programvaran. OfficeSpace-programvaran stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i OfficeSpace-programvaran skapas en ny efter autentiseringen.

> [!NOTE]
> Om du behöver skapa en användare manuellt måste du kontakta [OfficeSpace Software support-teamet](mailto:support@officespacesoftware.com).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på program panelen OfficeSpace på åtkomst panelen, bör du loggas in automatiskt till OfficeSpace-programvaran som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova OfficeSpace-program med Azure AD](https://aad.portal.azure.com/)

