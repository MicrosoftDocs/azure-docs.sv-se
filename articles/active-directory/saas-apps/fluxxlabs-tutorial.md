---
title: 'Självstudie: Azure Active Directory integrering med Fluxx Labs | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Fluxx Labs.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2020
ms.author: jeedes
ms.openlocfilehash: 756bbaab1cbf359def01e371e4370607bee67ce3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554871"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fluxx-labs"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med Fluxx Labs

I den här självstudien får du lära dig att integrera Fluxx Labs med Azure Active Directory (Azure AD). När du integrerar Fluxx Labs med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Fluxx Labs.
* Gör det möjligt för användarna att logga in automatiskt till Fluxx Labs med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Fluxx Labs-aktiverad prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Fluxx Labs stöder **IDP** INITIERAd SSO
* När du har konfigurerat Fluxx Labs kan du framtvinga kontroll över sessioner, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-fluxx-labs-from-the-gallery"></a>Lägga till Fluxx Labs från galleriet

Om du vill konfigurera integreringen av Fluxx Labs i Azure AD måste du lägga till Fluxx Labs från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Fluxx Labs** i sökrutan.
1. Välj **Fluxx Labs** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-fluxx-labs"></a>Konfigurera och testa enkel inloggning med Azure AD för Fluxx Labs

Konfigurera och testa Azure AD SSO med Fluxx Labs med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Fluxx Labs.

Om du vill konfigurera och testa Azure AD SSO med Fluxx Labs slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Fluxx Labs SSO](#configure-fluxx-labs-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Fluxx Labs test User](#create-fluxx-labs-test-user)** -om du vill ha en motsvarighet till B. Simon i Fluxx Labs som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan **Fluxx Labs** application integration och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:  

    | Miljö | URL-mönster|
    |-------------|------------|
    | Produktion | `https://<subdomain>.fluxx.io` |
    | För produktion | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    | Miljö | URL-mönster|
    |-------------|------------|
    | Produktion | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | För produktion | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Fluxx Labs-klientens support team](https://fluxx.zendesk.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Fluxx Labs** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Fluxx Labs.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Fluxx Labs**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-fluxx-labs-sso"></a>Konfigurera Fluxx Labs SSO

1. Logga in på din Fluxx Labs-företags webbplats som administratör i ett annat webbläsarfönster.

2. Välj **administratör** under avsnittet **Inställningar** .

    ![Konfiguration av Fluxx Labs](./media/fluxxlabs-tutorial/config1.png)

3. I panelen admin väljer du **plugin-program**-  >  **integreringar** och väljer sedan **SAML SSO – (inaktive rad)**

    ![Konfiguration av Fluxx Labs](./media/fluxxlabs-tutorial/config2.png)

4. I avsnittet Attribute, utför följande steg:

    ![Konfiguration av Fluxx Labs](./media/fluxxlabs-tutorial/config3.png)

    a. Markera kryss rutan **SAML SSO** .

    b. Skriv **/auth/SAML**i text rutan **begär ande Sök väg** .

    c. I text rutan **återanrops Sök väg** skriver du **/auth/SAML/callback**.

    d. I text rutan för **försäkrans konsument tjänst-URL (enkel inloggning)** anger du **SVARs-URL** -värdet, som du har angett i Azure Portal.

    e. I text rutan **mål grupp (SP entitets-ID)** anger du **ID** -värdet, som du har angett i Azure Portal.

    f. I text rutan för **URL för identitets leverantörens SSO-mål** klistrar du in URL-värdet för **inloggning** , som du har kopierat från Azure Portal.

    ex. Öppna ditt bas-64-kodade certifikat i anteckningar, kopiera innehållet i det till Urklipp och klistra in det i text rutan för **identitets leverantörs certifikat** .

    h. I text rutan **namn på ID-format** anger du värdet `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` .

    i. Klicka på **Spara**.

    > [!NOTE]
    > När innehållet har sparats kommer fältet att visas som tomt för säkerhet, men värdet har sparats i konfigurationen.

### <a name="create-fluxx-labs-test-user"></a>Skapa Fluxx Labs test användare

För att Azure AD-användare ska kunna logga in på Fluxx Labs måste de tillhandahållas i Fluxx Labs. När det gäller Fluxx Labs är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din Fluxx Labs företags webbplats som administratör.

2. Klicka på **ikonen**som visas nedan.

    ![Konfiguration av Fluxx Labs](./media/fluxxlabs-tutorial/config6.png)

3. På instrument panelen klickar du på ikonen som visas nedan för att öppna kortet **nya personer** .

    ![Konfiguration av Fluxx Labs](./media/fluxxlabs-tutorial/config4.png)

4. I avsnittet **nya personer** utför du följande steg:

    ![Konfiguration av Fluxx Labs](./media/fluxxlabs-tutorial/config5.png)

    a. Fluxx Labs använder e-post som unik identifierare för SSO-inloggningar. Fyll i fältet **SSO-UID** med användarens e-postadress som matchar e-postadressen, som de använder som inloggning med SSO.

    b. Klicka på **Spara**.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Fluxx Labs på åtkomst panelen, bör du loggas in automatiskt på de Fluxx-labb som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Fluxx Labs med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Fluxx Labs med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)