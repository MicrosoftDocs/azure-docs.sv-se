---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Lenses.io | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Lenses.io.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/02/2020
ms.author: jeedes
ms.openlocfilehash: 0b8d736ab169ad07bd23a21d3a420bb6a044bf01
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88528666"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-the-lensesio-dataops-portal"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Lenses.io DataOps-portalen.

I den här självstudien får du lära dig att integrera [lenses.io](https://lenses.io/) DataOps-portalen med Azure Active Directory (Azure AD). När du integrerar Lenses.io med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Lenses.io-portalen.
* Gör det möjligt för användarna att logga in automatiskt till linser med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En instans av en linser-Portal. Du kan distribuera en linser-Portal på [olika sätt](https://lenses.io/product/deployment/).
* En Lenses.io- [licens](https://lenses.io/product/pricing/) som stöder enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Lenses.io stöder **SP** -INITIERAd SSO

* När du har konfigurerat Lenses.io kan du framtvinga sessionshantering, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-lensesio-from-the-gallery"></a>Lägga till Lenses.io från galleriet

Om du vill konfigurera integreringen av Lenses.io i Azure AD måste du lägga till Lenses.io från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **lenses.io** i sökrutan.
1. Välj **lenses.io** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-lensesio"></a>Konfigurera och testa Azure AD SSO för Lenses.io

Konfigurera och testa Azure AD SSO med Lenses.io-portalen med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Lenses.io.

Om du vill konfigurera och testa Azure AD SSO med Lenses.io, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare och grupp](#create-an-azure-ad-test-user-and-group)** – för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera LENSES.io SSO](#configure-lensesio-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa lenses.io test Group-behörigheter](#create-lensesio-test-group-permissions)** – för att kontrol lera vad B. Simon ska ha åtkomst till i lenses.IO (auktorisering).
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **lenses.io** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<CUSTOMER_LENSES_BASE_URL>` t. ex. `https://lenses.my.company.com`

    b. I text rutan **identifierare (enhets-ID)** anger du en URL med följande mönster: `https://<CUSTOMER_LENSES_BASE_URL>` t. ex. `https://lenses.my.company.com`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<CUSTOMER_LENSES_BASE_URL>/api/v2/auth/saml/callback?client_name=SAML2Client`
    otillräcklig. `https://lenses.my.company.com/api/v2/auth/saml/callback?client_name=SAML2Client`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska inloggnings-URL: en, svars-URL och identifierare, baserat på bas-URL: en för din linser-Portal instans. Du hittar mer information i [LENSES.io SSO-dokumentationen](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0).

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **konfigurera lenses.io** använder du XML-filen ovan för att konfigurera linser mot din Azure SSO.

### <a name="create-an-azure-ad-test-user-and-group"></a>Skapa en Azure AD-test användare och-grupp

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon. Du kommer också att skapa en test grupp för B. Simon som ska användas för att kontrol lera vilka Access B. Simon som har i linser.
Du kan ta reda på hur linser använder mappning av grupp medlemskap för auktorisering i [SSO-dokumentationen för linser](https://docs.lenses.io/install_setup/configuration/security.html#id3)

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

Så här skapar du gruppen:
1. Gå tillbaka till **Azure Active Directory**, Välj **grupper**
1. Välj **ny grupp** överst på skärmen.
1. I **grupp egenskaperna**följer du dessa steg:
   1. I fältet **typ av grupp** väljer du `Security` .
   1. I fältet **grupp namn** anger du `LensesUsers`
   1. Klicka på **Skapa**.
1. Välj gruppen `LensesUsers` och anteckna **objekt-ID** (t. ex. `f8b5c1ec-45de-4abd-af5c-e874091fb5f7` ). Detta ID kommer att användas i linser för att mappa användare av gruppen till [rätt behörigheter](https://docs.lenses.io/install_setup/configuration/security.html#id3).  
   
Så här tilldelar du gruppen till test användaren: 
1. Gå tillbaka till **Azure Active Directory**och välj **användare**.
1. Välj test användare `B.Simon` .
1. Välj **grupper**.
1. Välj **Lägg till medlemskap** överst på skärmen.
1. Sök efter `LensesUsers` och markera det.
1. Klicka på **Välj**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Lenses.io.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **lenses.io**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-lensesio-sso"></a>Konfigurera Lenses.io SSO

Om du vill konfigurera enkel inloggning på **lenses.io** -portalen installerar du den hämtade **XML-koden för federationsmetadata** på objektiv-instansen och [konfigurerar objektiv för att aktivera SSO](https://docs.lenses.io/install_setup/configuration/security.html#configure-lenses). 

### <a name="create-lensesio-test-group-permissions"></a>Skapa Lenses.io test Group-behörigheter

I det här avsnittet skapar du en grupp i linser med **objekt-ID: t** för den `LensesUsers` grupp som vi noterade i [avsnittet](#create-an-azure-ad-test-user-and-group)om att skapa användare.
Du tilldelar önskade behörigheter som `B.Simon` ska ha i linser.
Du hittar mer information om [grupp mappningen för Azure-linser](https://docs.lenses.io/install_setup/configuration/security.html#azure-groups).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Lenses.io på åtkomst panelen, bör du loggas in automatiskt på din Lenses.io-portal som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Konfigurera SSO i din Lenses.io-instans ](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Lenses.io med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Lenses.io med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
