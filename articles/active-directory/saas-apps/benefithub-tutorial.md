---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med BenefitHub | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och BenefitHub.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/31/2019
ms.author: jeedes
ms.openlocfilehash: d66eac504b6bdee4d6d9f4682f1c9d36d9d7e692
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88537278"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-benefithub"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med BenefitHub

I den här självstudien får du lära dig hur du integrerar BenefitHub med Azure Active Directory (Azure AD). När du integrerar BenefitHub med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till BenefitHub.
* Gör det möjligt för användarna att logga in automatiskt till BenefitHub med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* BenefitHub för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* BenefitHub stöder **IDP**-initierad enkel inloggning

## <a name="adding-benefithub-from-the-gallery"></a>Lägga till BenefitHub från galleriet

För att konfigurera integreringen mellan BenefitHub och Azure AD måste du lägga till BenefitHub från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **BenefitHub** i sökrutan.
1. Välj **BenefitHub** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-benefithub"></a>Konfigurera och testa enkel inloggning med Azure AD för BenefitHub

Konfigurera och testa Azure AD SSO med BenefitHub med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i BenefitHub.

Om du vill konfigurera och testa Azure AD SSO med BenefitHub, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera BENEFITHUB SSO](#configure-benefithub-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa BenefitHub test User](#create-benefithub-test-user)** -om du vill ha en motsvarighet till B. Simon i BenefitHub som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **BenefitHub** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du värdena för följande fält:

    a. I text rutan **identifierare** skriver du in värdet: `urn:benefithub:passport`

    b. I textrutan **Svars-URL** skriver du värdet: `https://passport.benefithub.info/saml/post/ac`

1. BenefitHub-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig BenefitHub-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name | Källattribut|
    | ----------- | --------------- |
    | organizationid | < organizationid > |
    | | |

    > [!NOTE]
    > Det här attributvärdet är inte det riktiga värdet. Uppdatera värdet med det faktiska organizationid-värdet. Kontakta [BenefitHub-supportteamet](https://www.benefithub.com/Home/ContactUs) och be dem om organizationid-värdet. Innan du kan konfigurera SAML-försäkran måste du kontakta [supporten för BenefitHub](https://www.benefithub.com/Home/ContactUs) och be om värdet för det unika identifierarattributet för din klientorganisation. Du behöver det här värdet för att konfigurera det anpassade anspråket för ditt program.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera BenefitHub** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till BenefitHub.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan med program väljer du **BenefitHub**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-benefithub-sso"></a>Konfigurera BenefitHub SSO

För att konfigurera enkel inloggning på **BenefitHub**-sidan måste du skicka den nedladdade **XML:en med federationsmetadata** och lämpliga kopierade URL:er från Azure-portalen till [BenefitHub-supportteamet](https://www.benefithub.com/Home/ContactUs). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-benefithub-test-user"></a>Skapa en BenefitHub-testanvändare

I det här avsnittet skapar du en användare som heter B. Simon i BenefitHub. Arbeta med  [BenefitHub-supportteamet](https://www.benefithub.com/Home/ContactUs) när du lägger till användare till BenefitHub-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på BenefitHub-panelen på åtkomstpanelen bör du loggas in automatiskt i BenefitHub som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova BenefitHub med Azure AD](https://aad.portal.azure.com/)