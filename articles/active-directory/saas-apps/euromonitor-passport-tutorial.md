---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med Euromonitor Passport | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Euromonitor Passport.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeedes
ms.openlocfilehash: a7b5a1433e2d94d68c0512aa7c09909cb4e7fee5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555315"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-euromonitor-passport"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Euromonitor Passport

I den här självstudien får du lära dig att integrera Euromonitor Passport med Azure Active Directory (Azure AD). När du integrerar Euromonitor Passport med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Euromonitor Passport.
* Gör det möjligt för användarna att logga in automatiskt till Euromonitor Passport med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Euromonitor Passport-aktiverad prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Euromonitor Passport stöder **SP- och IDP**-initierad enkel inloggning

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-euromonitor-passport-from-the-gallery"></a>Lägga till Euromonitor Passport från galleriet

För att konfigurera integrering av Euromonitor Passport med Azure AD behöver du lägga till Euromonitor Passport från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Euromonitor Passport** i sökrutan.
1. Välj **Euromonitor Passport** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-euromonitor-passport"></a>Konfigurera och testa enkel inloggning med Azure AD för Euromonitor Passport

Konfigurera och testa Azure AD SSO med Euromonitor Passport med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Euromonitor Passport.

Om du vill konfigurera och testa Azure AD SSO med Euromonitor Passport slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Euromonitor Passport SSO](#configure-euromonitor-passport-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa Euromonitor Passport-test](#create-euromonitor-passport-test-user)** för att få en motsvarighet till B. Simon i Euromonitor Passport som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan för **Euromonitor Passport** -programintegration i [Azure Portal](https://portal.azure.com/)letar du reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

1. Om du vill konfigurera programmet i **SP**-initierat läge måste du hämta inloggnings-URL:en från [Euromonitor Passport-supportteamet](mailto:passport.support@euromonitor.com). När du får inloggnings-URL:en av Euromonitor Passport -supportteamet klickar du på **Ange ytterligare URL:er** och utföra följande steg:

    Klistra in det erhållna inloggnings-URL-värdet från Euromonitor Passport-supportteamet i textrutan Inloggnings-URL.

1. Klicka på **Spara**.

1. Euromonitor Passport-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig Euromonitor Passport-program att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name | Källattribut|
    | ---------------| --------- |
    | Namnidentifierarvärde | user.userprincipalname |

    > [!NOTE]
    > Klientadministratörer kan lägga till/ändra attribut enligt sina behov.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **URL: en för appens Federations-metadata** och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Euromonitor Passport.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I programlistan väljer du **Euromonitor Passport**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-euromonitor-passport-sso"></a>Konfigurera Euromonitor Passport SSO

För att konfigurera enkel inloggning på **Euromonitor Passport**-sidan behöver du skicka **URL för appens federationsmetadata** till [supportteamet för Euromonitor Passport](mailto:passport.support@euromonitor.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-euromonitor-passport-test-user"></a>Skapa Euromonitor Passport-testanvändare

I det här avsnittet skapar du en användare som heter B. Simon i Euromonitor Passport. Ta hjälp från [supportteamet för Euromonitor Passport](mailto:passport.support@euromonitor.com) och lägg till användarna på Euromonitor Passport-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Euromonitor Passport-panelen i åtkomstpanelen bör du automatiskt loggas in på Euromonitor Passport som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Euromonitor Passport med Azure AD](https://aad.portal.azure.com/)