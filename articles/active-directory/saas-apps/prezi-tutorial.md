---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Prezi | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Prezi.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: jeedes
ms.openlocfilehash: 994d427fb91f04ea0d2002a2c95e12366f4c6420
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553575"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-prezi"></a>Självstudie: Azure Active Directory integration med enkel inloggning med Prezi

I den här självstudien får du lära dig hur du integrerar Prezi med Azure Active Directory (Azure AD). När du integrerar Prezi med Azure AD kan du:

* Kontrol lera vem som har åtkomst till Prezi i Azure AD.
* Gör det möjligt för användarna att logga in automatiskt på Prezi med sina Azure AD-konton.
* Hantera dina konton i Azure Portal.

Mer information om SaaS-appar (Software as a Service) med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En Prezi-prenumeration som är aktive rad med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Prezi stöder SP-och IDP-initierad SSO.
* Prezi stöder just-in-Time-etablering av användare.
* När du har konfigurerat Prezi kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. Mer information finns i [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-prezi-from-the-gallery"></a>Lägg till Prezi från galleriet

Om du vill konfigurera integreringen av Prezi i Azure AD måste du lägga till Prezi från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) genom att använda antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I rutan längst till vänster väljer du **Azure Active Directory**.
1. Gå till **företags program**och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , ange **Prezi** i sökrutan.
1. Välj **Prezi** i panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-prezi"></a>Konfigurera och testa Azure AD SSO för Prezi

Konfigurera och testa Azure AD SSO med Prezi med hjälp av en test användare som heter B. Simon. För att SSO ska fungera upprättar du en länk relation mellan en Azure AD-användare och den relaterade användaren i Prezi.

Om du vill konfigurera och testa Azure AD SSO med Prezi, slutför du följande Bygg stenar:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso) så att användarna kan använda den här funktionen.
    1. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa Azure AD SSO med B. Simon.
    1. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) för att aktivera B. Simon att använda Azure AD SSO.
1. [Konfigurera PREZI SSO](#configure-prezi-sso) för att konfigurera SSO-inställningar på program sidan.
    1. [Skapa en Prezi](#create-a-prezi-test-user) för att få en motsvarighet till B. Simon i Prezi som är länkat till användarens Azure AD-representation.
1. [Testa SSO](#test-sso) för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Aktivera Azure AD SSO i Azure Portal:

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Prezi** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du **Redigera** -ikonen för att redigera inställningarna för **grundläggande SAML-konfiguration**.

   ![Redigera grundläggande konfigurations inställningar för SAML](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** behöver användaren inte göra något steg, eftersom appen redan är Förintegrerad med Azure.

1. Välj **Ange ytterligare URL: er**och gör följande om du vill konfigurera programmet i **SP**-initierat läge:

    I rutan **inloggnings-URL** anger du URL: en `https://prezi.com/login/sso/` .

1. Välj **Spara**.

1. Prezi-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![Användarattribut & anspråk](common/default-attributes.png)

1. Prezi-programmet förväntar sig också några fler attribut som ska skickas tillbaka i SAML-svar, som visas här. Dessa attribut är också förifyllda, men du kan granska dem utifrån dina behov.
    
    | Name | Källattribut|
    | ---------------| --------------- |
    | given_name | user.givenname |
    | family_name | user.surname |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **certifikat (base64)**. Välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länken Hämta certifikat](common/certificatebase64.png)

1. I avsnittet **Konfigurera Prezi** kopierar du lämpliga URL: er baserat på dina krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. Välj **Azure Active Directory**i rutan till vänster i rutan Azure Portal. Gå till **användare**och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I användar egenskaperna följer du de här stegen:
   1. I rutan **namn** anger du **B. Simon**.
   1. I rutan **användar namn** anger du exempelvis `username@companydomain.extension` , till exempel `B.Simon@contoso.com` .
   1. Markera kryss rutan **Visa lösen ord** . Skriv ned värdet som visas i rutan **lösen ord** .
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B. Simon för att använda Azure SSO genom att bevilja åtkomst till Prezi.

1. I Azure Portal väljer du **företags program**  >  **alla program**.
1. I listan program väljer du **Prezi**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken Användare och grupper](common/users-groups-blade.png)

1. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML-intyget väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-prezi-sso"></a>Konfigurera Prezi SSO

1. Logga in på Prezi med ditt team-konto i ett annat webbläsarfönster och gå till [Administratörs konsolen](https://prezi.com/organizations/manage).

1. I **Administratörs konsolen**väljer du fliken **Inställningar** .

    ![Fliken Inställningar](./media/prezi-tutorial/settings-image.png)

1. Gå till avsnittet **enkel inloggning (SSO)** och aktivera växla för att aktivera SSO.
    
    ![Växla enkel inloggning (SSO)](./media/prezi-tutorial/single-signon.png)

1. I avsnittet **enkel inloggning (SSO)** följer du dessa steg:

    ![Avsnittet enkel inloggning (SSO)](./media/prezi-tutorial/configuration.png)

    1. I rutan **identifierare eller utfärdare URL** klistrar du in värdet för **Azure AD-identifieraren** , som du kopierade från Azure Portal.

    1. I rutan **SAML 2,0-slutpunkt (http)** klistrar du in värdet för **inloggnings-URL: en** som du kopierade från Azure Portal.

    1. Öppna det hämtade **certifikatet (base64)** från Azure Portal i anteckningar. Kopiera innehållet i certifikatet och klistra in innehållet i rutan **certifikat (X. 509)** .

    1. Välj **Spara**.

### <a name="create-a-prezi-test-user"></a>Skapa en Prezi-test användare

I det här avsnittet skapas en användare som kallas Britta Simon i Prezi. Prezi stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärds objekt i det här avsnittet. Om en användare inte redan finns i Prezi skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa din Azure AD SSO-konfiguration med hjälp av åtkomst panelen.

När du väljer panelen Prezi på åtkomst panelen, bör du loggas in automatiskt på det Prezi-konto som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Prova Prezi med Azure AD](https://aad.portal.azure.com/)
- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [Så här skyddar du Prezi med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

