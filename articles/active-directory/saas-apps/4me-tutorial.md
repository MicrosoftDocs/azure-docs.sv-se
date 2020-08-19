---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med 4me | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och 4me.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2019
ms.author: jeedes
ms.openlocfilehash: 80d5b153e66ab109ac1f2496464bbd8f557f8d1b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88538582"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-4me"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med 4me

I den här självstudien får du lära dig hur du integrerar 4me med Azure Active Directory (Azure AD). När du integrerar 4me med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till 4me.
* Gör det möjligt för användarna att logga in automatiskt till 4me med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* 4me för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* 4me har stöd för **SP**-initierad enkel inloggning
* 4me har stöd för etablering av användare enligt principen **just-in-time**

## <a name="adding-4me-from-the-gallery"></a>Lägga till 4me från galleriet

När du ska konfigurera integreringen av 4me i Azure AD måste du lägga till 4me från galleriet i listan med hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **4me** i sökrutan.
1. Välj **4me** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-4me"></a>Konfigurera och testa enkel inloggning med Azure AD för 4me

Konfigurera och testa Azure AD SSO med 4me med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i 4me.

Om du vill konfigurera och testa Azure AD SSO med 4me, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera 4ME SSO](#configure-4me-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa 4me test User](#create-4me-test-user)** -om du vill ha en motsvarighet till B. Simon i 4me som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **4me** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: 

    | Miljö| URL|
    |---|---|
    | PRODUKTION | `https://<SUBDOMAIN>.4me.com`|
    | QA| `https://<SUBDOMAIN>.4me.qa`|
    | | |

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: 

    | Miljö| URL|
    |---|---|
    | PRODUKTION | `https://<SUBDOMAIN>.4me.com`|
    | QA| `https://<SUBDOMAIN>.4me.qa`|
    | | |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [4me-supportteamet](mailto:support@4me.com) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. 4me-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig 4me-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name | Källattribut|
    | ---------------| --------------- |
    | first_name | user.givenname |
    | last_name | user.surname |

1. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

1. Kopiera **Tumavtryck** i avsnittet **SAML-signeringscertifikat** och spara det på datorn.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

1. I avsnittet **Konfigurera 4me** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till 4me.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. Välj **4me** i listan med program.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-4me-sso"></a>Konfigurera 4me SSO

1. Logga in på 4me som administratör i ett annat webbläsarfönster.

1. Klicka på **Inställningar** uppe till vänster och på **Enkel inloggning** i det vänstra sidofältet.

    ![4me-inställningar](./media/4me-tutorial/tutorial_4me_settings.png)

1. På sidan **Enkel inloggning** utför du följande steg:

    ![4me enkel inloggning](./media/4me-tutorial/tutorial_4me_singlesignon.png)

    a. Välj alternativet **Aktiverad**.

    b. I textrutan **Remote Logout URL** (URL för fjärrutloggning) klistrar du in värdet för **Utloggnings-URL** som du kopierade från Azure-portalen.

    c. Under avsnittet **SAML**, i textrutan **SAML SSO URL** (URL för enkel inloggning med SAML) klistrar du in värdet för **Inloggnings-URL** som du kopierade från Azure Portal.

    d. I textrutan **Fingeravtryck för certifikat** klistrar du in **TUMAVTRYCK**-värdet avgränsat med kolon i dubbletter (AA:BB:CC:DD:EE:FF:GG:HH:II), som du kopierade från Azure Portal.

    e. Klicka på **Spara**.

### <a name="create-4me-test-user"></a>Skapa 4me-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i 4me. 4me har stöd för användaretablering enligt just-in-time, och det är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om användaren inte redan finns i 4me skapas en ny efter autentisering.

> [!Note]
> Kontakta [4me support team](mailto:support@4me.com)om du behöver skapa en användare manuellt.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på 4me-panelen på åtkomstpanelen bör du automatiskt loggas in i det 4me som du konfigurerade enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova 4me med Azure AD](https://aad.portal.azure.com/)
