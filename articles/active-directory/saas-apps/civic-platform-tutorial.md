---
title: 'Självstudie: Azure Active Directory integration med Civic-plattformen | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och Civic-plattformen.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: jeedes
ms.openlocfilehash: 47ffaef940b3fe2ffe033405a712195c5c74d774
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88520391"
---
# <a name="tutorial-integrate-civic-platform-with-azure-active-directory"></a>Självstudie: integrera Civic-plattformen med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Civic-plattformen med Azure Active Directory (Azure AD). När du integrerar Civic-plattformen med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Civic-plattformen.
* Gör det möjligt för användarna att logga in automatiskt till Civic-plattformen med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få en månads kostnads fri utvärderings version [här](https://azure.microsoft.com/pricing/free-trial/).
* Civic plattform enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Civic-plattformen stöder **SP** -INITIERAd SSO





## <a name="adding-civic-platform-from-the-gallery"></a>Lägga till Civic-plattform från galleriet

Om du vill konfigurera integreringen av Civic-plattformen i Azure AD måste du lägga till Civic-plattformen från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Civic-plattformen** i sökrutan.
1. Välj **Civic-plattform** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Civic-plattformen med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren på Civic-plattformen.

Om du vill konfigurera och testa Azure AD SSO med Civic-plattformen slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Civic-plattformen SSO](#configure-civic-platform-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
4. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
5. **[Skapa Civic Platform test User](#create-civic-platform-test-user)** – för att få en motsvarighet till B. Simon på Civic-plattformen som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Civic Platform** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.accela.com`

    b. I text rutan **identifierare (enhets-ID)** anger du en URL: `civicplatform.accela.com`

    > [!NOTE]
    > Värdet för inloggnings-URL är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Civic Platform client support team](mailto:skale@accela.com) för att få det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

1. Gå till **Azure Active Directory**  >  **Appregistreringar** i Azure AD och välj ditt program.

1. Kopiera **katalog-ID: t** och lagra det i anteckningar.

    ![Kopiera katalogen (klient-ID) och lagra den i din app Code](media/civic-platform-tutorial/directoryid.png)

1. Kopiera **program-ID: t** och spara det i anteckningar.

   ![Kopiera program-ID: t (klient)](media/civic-platform-tutorial/applicationid.png)

1. Gå till **Azure Active Directory**  >  **Appregistreringar** i Azure AD och välj ditt program. Välj **certifikat & hemligheter**.

1. Välj **klient hemligheter – > ny klient hemlighet**.

1. Ange en beskrivning av hemligheten och en varaktighet. När du är färdig väljer du **Lägg till**.

   > [!NOTE]
   > När klient hemligheten har sparats visas värdet för klient hemligheten. Kopiera det här värdet eftersom du inte kan hämta nyckeln senare.

   ![Kopiera det hemliga värdet eftersom du inte kan hämta det senare](media/civic-platform-tutorial/secretkey.png)

### <a name="configure-civic-platform-sso"></a>Konfigurera Civic-plattform SSO

1. Öppna ett nytt webbläsarfönster och logga in på Atlassian-molnets företags webbplats som administratör.

1. Klicka på **standard alternativ**.

    ![Länk för nedladdning av certifikatet](media/civic-platform-tutorial/standard-choices.png)

1. Skapa ett standard alternativ **ssoconfig**.

1. Sök efter **ssoconfig**  och skicka.

    ![Länk för nedladdning av certifikatet](media/civic-platform-tutorial/sso-config.png)

1. Expandera SSOCONFIG genom att klicka på röd prick.

    ![Länk för nedladdning av certifikatet](media/civic-platform-tutorial/sso-config01.png)

1. Ange SSO-relaterad konfigurations information i följande steg:

    ![Länk för nedladdning av certifikatet](media/civic-platform-tutorial/sso-config02.png)

    1. I fältet **ApplicationId** anger du **programmets ID-** värde, som du har kopierat från Azure Portal.

    1. I fältet **clientSecret** anger du det **hemliga** värdet, som du har kopierat från Azure Portal.

    1. I fältet **directoryId** anger du **katalogens ID-** värde, som du har kopierat från Azure Portal.

    1. Ange idpName. Ex:- `Azure` .

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Civic-plattformen.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Civic-plattform**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-civic-platform-test-user"></a>Skapa Civic Platform test User

I det här avsnittet skapar du en användare som heter B. Simon i Civic-plattformen. Arbeta med Civic Platform support team för att lägga till användare i [support teamet för Civic Platform-klienten](mailto:skale@accela.com). Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Civic plattform på åtkomst panelen, bör du loggas in automatiskt på den Civic-plattform som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

