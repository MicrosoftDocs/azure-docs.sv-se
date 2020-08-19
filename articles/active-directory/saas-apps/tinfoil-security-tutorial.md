---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med TINFOIL-säkerhet | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TINFOIL-säkerhet.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.openlocfilehash: 35ff822b4439a48a922fe5ccd4530dfb5d40ffae
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552049"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tinfoil-security"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med TINFOIL-säkerhet

I den här självstudien får du lära dig hur du integrerar TINFOIL SECURITY med Azure Active Directory (Azure AD). När du integrerar TINFOIL-säkerhet med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till TINFOIL-säkerhet.
* Gör det möjligt för användarna att logga in automatiskt för att TINFOIL säkerhet med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* TINFOIL SECURITY-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* TINFOIL-säkerhet stöder **IDP** INITIERAd SSO

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-tinfoil-security-from-the-gallery"></a>Lägga till TINFOIL-säkerhet från galleriet

Om du vill konfigurera integreringen av TINFOIL-säkerhet i Azure AD måste du lägga till TINFOIL-säkerhet från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **TINFOIL Security** i sökrutan.
1. Välj **TINFOIL Security** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tinfoil-security"></a>Konfigurera och testa enkel inloggning med Azure AD för TINFOIL-säkerhet

Konfigurera och testa Azure AD SSO med TINFOIL-säkerhet med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i TINFOIL SECURITY.

Om du vill konfigurera och testa Azure AD SSO med TINFOIL-säkerhet slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera TINFOIL Security SSO](#configure-tinfoil-security-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa TINFOIL Security test User](#create-tinfoil-security-test-user)** – för att få en motsvarighet till B. Simon i tinfoil Security som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **TINFOIL Security** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** är programmet förkonfigurerat och de nödvändiga URL: erna är redan ifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på knappen **Spara** .

1. I programmet för att gå till gång förväntas SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig programmet att få fler attribut att skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name | Källattribut |
    | ------------------- | -------------|
    | accountId | UXXXXXXXXXXXXX |

    > [!NOTE]
    > Du får det accountid-värde som beskrivs senare i självstudien.

1. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

1. I avsnittet **SAML-signeringscertifikat** , kopierar du **värdet tumavtryck** och sparar det på din dator.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

1. I avsnittet **Konfigurera TINFOIL-säkerhet** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till TINFOIL-säkerhet.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **TINFOIL Security**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-tinfoil-security-sso"></a>Konfigurera TINFOIL SECURITY SSO

1. Logga in på din TINFOIL-säkerhets företags webbplats som administratör i ett annat webbläsarfönster.

1. I verktygsfältet högst upp klickar du på **mitt konto**.

    ![Instrumentpanel](./media/tinfoil-security-tutorial/ic798971.png "Instrumentpanel")

1. Klicka på **Säkerhet**.

    ![Säkerhet](./media/tinfoil-security-tutorial/ic798972.png "Säkerhet")

1. På sidan för konfiguration av **Enkel inloggning** utför du följande steg:

    ![Enkel inloggning](./media/tinfoil-security-tutorial/ic798973.png "för Aha!")

    a. Välj **Aktivera SAML**.

    b. Klicka på **manuell konfiguration**.

    c. I text rutan för **SAML post-URL** klistrar du in värdet för **inloggnings webb adress** som du har kopierat från Azure Portal

    d. I text rutan **finger avtryck för SAML-certifikat** klistrar du in värdet för **tumavtryck** som du har kopierat från avsnittet **SAML-signerings certifikat** .
  
    e. Kopiera **ditt konto-ID-** värde och klistra in värdet i text rutan för **källattribut** under avsnittet **användarattribut & Claims** i Azure Portal.

    f. Klicka på **Spara**.

### <a name="create-tinfoil-security-test-user"></a>Skapa TINFOIL SECURITY test User

För att Azure AD-användare ska kunna logga in på TINFOIL säkerhet måste de tillhandahållas i TINFOIL-säkerhet. När det gäller TINFOIL-säkerhet är etableringen en manuell uppgift.

**Utför följande steg för att få en användare etablerad:**

1. Om användaren är en del av ett företags konto måste du [Kontakta TINFOIL Security Support-teamet](https://www.tinfoilsecurity.com/contact) för att få det användar konto som skapats.

1. Om användaren är en vanlig TINFOIL SECURITY SaaS-användare kan användaren lägga till en medarbetare till någon av användarens webbplatser. Detta utlöser en process för att skicka en inbjudan till det angivna e-postmeddelandet för att skapa ett nytt TINFOIL säkerhets användar konto.

> [!NOTE]
> Du kan använda andra TINFOIL-verktyg eller API: er för skapande av säkerhet som tillhandahålls av TINFOIL-säkerhet för att etablera Azure AD-användarkonton.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen TINFOIL säkerhet i åtkomst panelen, bör du loggas in automatiskt till den TINFOIL-säkerhet som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova TINFOIL SECURITY med Azure AD](https://aad.portal.azure.com/)