---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med EAB navigera implementering | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och EAB navigera implementering.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.openlocfilehash: a3f1982e72fb572f75789f6005b32de557c0b37d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92454498"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-eab-navigate-impl"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med EAB navigera implementering

I den här självstudien får du lära dig att integrera EAB navigera implementering med Azure Active Directory (Azure AD). När du integrerar EAB navigerar implementering med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till EAB navigera implementering.
* Gör det möjligt för användarna att logga in automatiskt för att EAB navigera implementering med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* EAB navigera implementering enkel inloggning (SSO)-prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* EAB navigera implementering stöder **SP** -INITIERAd SSO

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-eab-navigate-impl-from-the-gallery"></a>Lägga till EAB navigera implementering från galleriet

Om du vill konfigurera integreringen av EAB navigera implementering till Azure AD måste du lägga till EAB navigera implementering från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **EAB navigera implementering** i sökrutan.
1. Välj **EAB navigera implementering** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-eab-navigate-impl"></a>Konfigurera och testa enkel inloggning med Azure AD för EAB navigera implementering

Konfigurera och testa Azure AD SSO med EAB navigera implementering med en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i EAB navigera implementering.

Om du vill konfigurera och testa Azure AD SSO med EAB navigera implementering slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera EAB navigera implementering SSO](#configure-eab-navigate-impl-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa EAB navigera implementering test User](#create-eab-navigate-impl-test-user)** -om du vill ha en motsvarighet till B. Simon i EAB navigera implementering som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan [Azure Portal](https://portal.azure.com/)navigera implementering program integration på sidan för **EAB navigerar** du till avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält: i text rutan **identifierare (enhets-ID)** anger du exakt följande värde:  `https://impl.bouncer.eab.com`
    
    I text rutan **svars-URL: en (intyg om mottagar tjänst)** anger du båda följande värden som separata rader: `https://impl.bouncer.eab.com/sso/saml2/acs`
    `https://impl.bouncer.eab.com/sso/saml2/acs/`
    
    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<SUBDOMAIN>.navigate.impl.eab.com/`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [EAB navigera implementering client support team](mailto:EABTechSupport@eab.com) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **URL: en för appens Federations-metadata** och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till EAB navigera implementering.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **EAB navigera implementering**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-eab-navigate-impl-sso"></a>Konfigurera EAB navigera implementering SSO

Om du vill konfigurera enkel inloggning på **EAB navigera implementering** sida måste du skicka **URL: en för appens Federations-metadata** till [EAB navigera implementering support team](mailto:EABTechSupport@eab.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-eab-navigate-impl-test-user"></a>Skapa EAB navigera implementering test User

I det här avsnittet skapar du en användare som heter B. Simon i EAB navigera implementering. Arbeta med [EAB navigera implementering support team](mailto:EABTechSupport@eab.com) för att lägga till användarna i EAB navigera implementering-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen EAB navigera implementering på åtkomst panelen, bör du loggas in automatiskt till EAB navigera implementering som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](./tutorial-list.md)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)

- [Prova EAB navigera implementering med Azure AD](https://aad.portal.azure.com/)