---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med smarta | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Clever.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.openlocfilehash: 4c87ee92a2bc30dc2923127241013601cf3f4419
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88519874"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-clever"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med smarta

I den här självstudien får du lära dig hur du integrerar smarta med Azure Active Directory (Azure AD). När du integrerar smarta med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till smarta.
* Gör det möjligt för användarna att logga in automatiskt till smarta med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Smarta för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Clever har stöd för **SP**-initierad enkel inloggning

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-clever-from-the-gallery"></a>Lägga till Clever från galleriet

För att konfigurera integreringen av Clever i Azure AD måste du lägga till Clever från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **smarta** i sökrutan.
1. Välj **smarta** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-clever"></a>Konfigurera och testa enkel inloggning med Azure AD för smarta

Konfigurera och testa Azure AD SSO med smarta med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i smarta.

Om du vill konfigurera och testa Azure AD SSO med smarta, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera smarta SSO](#configure-clever-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa smarta test User](#create-clever-test-user)** -om du vill ha en motsvarighet till B. Simon i smarta som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **smarta** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://clever.com/in/<companyname>`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://clever.com/oauth/saml/metadata.xml`

    > [!NOTE]
    > Värdet för inloggnings-URL är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [kundsupporten för Clever](https://clever.com/about/contact/) om be om värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till smarta.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I programlistan väljer du **Clever**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-clever-sso"></a>Konfigurera smarta SSO

1. I ett annat webbläsarfönster loggar du in på din smarta-företags webbplats som administratör.

1. I verktygsfältet klickar du på **Instant Login** (Direkt inloggning).

    ![Omedelbar inloggning](./media/clever-tutorial/ic798984.png "Omedelbar inloggning")

    > [!NOTE]
    > Innan du kan testa enkel inloggning måste du kontakta [kundsupporten för Clever](https://clever.com/about/contact/) och aktivera enkel inloggning för Office 365 i serverdelen.

1. På sidan **Instant Login** (Direkt inloggning) utför du följande steg:
 
    ![Omedelbar inloggning](./media/clever-tutorial/ic798985.png "Omedelbar inloggning")

    a. Skriv **inloggnings-URL**.

    >[!NOTE]
    >**Inloggnings-URL** är ett anpassat värde. Kontakta [kundsupporten för Clever](https://clever.com/about/contact/) om be om det här värdet.

    b. Som **Identity System** (Identitetssystemet) väljer du **ADFS**.

    c. I textrutan **Metadata-URL** klistrar du in värdet för den **URL för appfederationsmetadata** som du har kopierat från Azure-portalen.

    d. Klicka på **Spara**.

### <a name="create-clever-test-user"></a>Skapa Clever-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på smarta måste de tillhandahållas i smarta.

När det gäller Clever kontaktar du [kundsupporten för Clever](https://clever.com/about/contact/) för att lägga till användarna i Clever-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

> [!NOTE]
> Du kan använda andra verktyg eller API:er för Clever-kontoskapande som tillhandahålls av Clever för att etablera AAD-användarkonton.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Clever-panelen i åtkomstpanelen bör du automatiskt loggas in på Clever som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova smarta med Azure AD](https://aad.portal.azure.com/)