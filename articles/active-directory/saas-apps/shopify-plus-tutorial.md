---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Shopify plus | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Shopify plus.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/18/2020
ms.author: jeedes
ms.openlocfilehash: 61d2eb05f7953cda16664f6b4e3317feb46aa8ad
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548605"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-shopify-plus"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Shopify plus

I den här självstudien får du lära dig att integrera Shopify plus med Azure Active Directory (Azure AD). När du integrerar Shopify plus med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Shopify plus.
* Gör det möjligt för användarna att logga in automatiskt till Shopify plus med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Shopify plus enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Shopify Plus stöder **SP-och IDP** -INITIERAd SSO

* När du har konfigurerat Shopify Plus kan du framtvinga kontroll av sessioner, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-shopify-plus-from-the-gallery"></a>Lägga till Shopify plus från galleriet

Om du vill konfigurera integreringen av Shopify plus i Azure AD måste du lägga till Shopify plus från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Shopify plus** i sökrutan.
1. Välj **Shopify plus** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-shopify-plus"></a>Konfigurera och testa enkel inloggning med Azure AD för Shopify plus

Konfigurera och testa Azure AD SSO med Shopify plus med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Shopify plus.

Om du vill konfigurera och testa Azure AD SSO med Shopify plus slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Shopify plus SSO](#configure-shopify-plus-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Shopify plus-test User](#create-shopify-plus-test-user)** -om du vill ha en motsvarighet till B. Simon i Shopify plus som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan **Shopify plus** program integration i [Azure Portal](https://portal.azure.com/)letar du reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    Skriv en URL i text rutan **svars-URL** med följande mönster:  `https://accounts.shopify.com/saml/consume/organization/<ORGANIZATION_ID>`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I rutan **Inloggnings-URL** anger du följande URL: `https://shopify.plus/login`

    > [!NOTE]
    > Värdet för svars-URL:en är inte verkligt. Uppdatera värdet för med den faktiska svars-URL:en. Kontakta [Shopify plus support teamet](mailto:plus-user-management@shopify.com) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Shopify Plus-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig Shopify plus fler attribut att skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name | Källattribut|
    | ---- | --------------- |
    | e-post | user.mail |

1. Ändra **namn-ID-** formatet till **beständigt**. Välj alternativet **unik användar identifierare (namn-ID)** och välj sedan formatet **namn identifierare** . Välj **beständig** för det här alternativet. Spara ändringarna.
1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du kopierings knappen för att kopiera URL för **metadata för app Federation** och sparar den på din dator.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Shopify plus.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Shopify plus**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-shopify-plus-sso"></a>Konfigurera Shopify plus SSO

Om du vill visa fullständiga steg läser du [Shopify-dokumentationen om hur du konfigurerar SAML-integreringar](https://help.shopify.com/en/manual/shopify-plus/saml).

Om du vill konfigurera enkel inloggning på **Shopify plus** -sidan kopierar du **URL: en för appens federationens metadata** från Azure Active Directory. Logga sedan in på [organisationens administratör](https://shopify.plus) och gå till **användarens**  >  **säkerhet**. Välj **Konfigurera konfiguration**och klistra in URL: en för din app Federation-metadata i avsnittet **metadata-URL för identitetsprovider** . Välj **Lägg till** för att slutföra det här steget.

### <a name="create-shopify-plus-test-user"></a>Skapa Shopify plus-test användare

I det här avsnittet skapar du en användare som heter B. Simon i Shopify plus. Gå tillbaka till avsnittet **användare** och Lägg till en användare genom att ange deras e-post och behörigheter. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="enforce-saml-authentication"></a>Framtvinga SAML-autentisering

> [!NOTE]
> Vi rekommenderar att du testar integrationen genom att använda enskilda användare innan du använder dem på ett brett spektrum.

Enskilda användare:
1. Gå till en enskild användares sida i Shopify plus med en e-postdomän som hanteras av Azure AD och verifieras i Shopify plus.
1. I avsnittet SAML-autentisering väljer du **Redigera**, Välj **obligatoriskt**och välj sedan **Spara**.
1. Testa att den här användaren kan logga in via idP-initierade och SP-initierade flöden.

För alla användare under en e-postdomän:
1. Gå tillbaka till sidan **säkerhet** .
1. Välj **krävs** för inställningen för SAML-autentisering. Detta tillämpar SAML för alla användare med den e-postdomänen över Shopify plus.
1. Välj **Spara**.

> [!IMPORTANT]
> Att aktivera SAML för alla användare under en e-postdomän påverkar alla användare som använder det här programmet. Användare kan inte logga in med hjälp av sin vanliga inloggnings sida. De kommer bara att kunna komma åt appen via Azure Active Directory. Shopify tillhandahåller inte en inloggnings-URL för säkerhets kopiering där användarna kan logga in med sitt normala användar namn och lösen ord. Du kan kontakta Shopify-supporten om du vill inaktivera SAML om det behövs.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Shopify plus i åtkomst panelen, bör du loggas in automatiskt på Shopify plus för vilken du ställer in SSO. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Testa Shopify plus med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Shopify plus med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
