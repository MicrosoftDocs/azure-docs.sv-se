---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med CylancePROTECT | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Splunk Enterprise och CylancePROTECT.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/27/2020
ms.author: jeedes
ms.openlocfilehash: 5457730494d509a2923ce929f41e397cb6064126
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88550095"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cylanceprotect"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med CylancePROTECT

I den här självstudien får du lära dig hur du integrerar CylancePROTECT med Azure Active Directory (Azure AD). När du integrerar CylancePROTECT med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till CylancePROTECT.
* Gör det möjligt för användarna att logga in automatiskt till CylancePROTECT med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* CylancePROTECT för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* CylancePROTECT stöder **IDP**-initierad enkel inloggning

## <a name="adding-cylanceprotect-from-the-gallery"></a>Lägga till CylancePROTECT från galleriet

För att konfigurera integreringen av CylancePROTECT till Azure AD behöver du lägga till CylancePROTECT från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **CylancePROTECT** i sökrutan.
1. Välj **CylancePROTECT** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cylanceprotect"></a>Konfigurera och testa enkel inloggning med Azure AD för CylancePROTECT

Konfigurera och testa Azure AD SSO med CylancePROTECT med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i CylancePROTECT.

Om du vill konfigurera och testa Azure AD SSO med CylancePROTECT, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera CYLANCEPROTECT SSO](#configure-cylanceprotect-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa CylancePROTECT test User](#create-cylanceprotect-test-user)** -om du vill ha en motsvarighet till B. Simon i CylancePROTECT som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **CylancePROTECT** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du värdena för följande fält:

    a. I textrutan **Identifierare** anger du URL:en:
    
    | Region | URL-värde |
    |----------|---------|
    | Asien och stillahavsområdet, nordöstra (APNE1)| `https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Asien och stillahavsområdet, sydöstra (AU) | `https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml` |
    | Europa, centrala (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Nordamerika|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Sydamerika (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

    b. I textrutan **Svars-URL** anger du URL:en:
    
    | Region | URL-värde |
    |----------|---------|
    | Asien och stillahavsområdet, nordöstra (APNE1)|`https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Asien och stillahavsområdet, sydöstra (AU)|`https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Europa, centrala (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Nordamerika|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Sydamerika (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

1. CylancePROTECT-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. CylancePROTECT-programmet förväntar sig att **NameIdentifier** mappas med **User. mail** och tar bort alla andra återstående anspråk, så du måste redigera mappningen av attribut genom att klicka på ikonen **Redigera** och ändra attributet mappning.

    ![image](common/edit-attribute.png)

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera CylancePROTECT** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

> [!NOTE]
> Öppna det nedladdade base64-kodade certifikatet i en text redigerare och kopiera bara texten mellan **Start** -och **slut** taggar för att klistra in i Cylance administrations Portal.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till CylancePROTECT.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I programlistan väljer du **CylancePROTECT**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-cylanceprotect-sso"></a>Konfigurera CylancePROTECT SSO

Om du vill konfigurera enkel inloggning på **CylancePROTECT** sida måste du skicka det hämtade **certifikatet (base64)** och lämpliga kopierade url: er från Azure Portal till [support teamet för CylancePROTECT](https://www.cylance.com/en-us/resources/support/support-overview.html). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna. Om du vill ha mer information använder du Cylance-dokumentationen: [https://support.cylance.com/s/article/Use-Microsoft-Azure-with-CylancePROTECT4](https://support.cylance.com/s/article/Use-Microsoft-Azure-with-CylancePROTECT4) .

### <a name="create-cylanceprotect-test-user"></a>Skapa CylancePROTECT-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i CylancePROTECT. Arbeta med konsoladministratören för att lägga till användarna i CylancePROTECT-plattformen. Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på CylancePROTECT-panelen i åtkomstpanelen bör du automatiskt loggas in på CylancePROTECT som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova CylancePROTECT med Azure AD](https://aad.portal.azure.com/)