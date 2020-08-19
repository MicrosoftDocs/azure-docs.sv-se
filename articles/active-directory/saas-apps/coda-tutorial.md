---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med CODA | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och CODA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/23/2020
ms.author: jeedes
ms.openlocfilehash: 718afec8b36a27bfd36e2a018b39f480144bf822
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545360"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coda"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med CODA

I den här självstudien får du lära dig hur du integrerar CODA med Azure Active Directory (Azure AD). När du integrerar CODA med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till CODA.
* Gör det möjligt för användarna att logga in automatiskt till CODA med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* CODA-aktiverad (Single Sign-on)-prenumeration (Enterprise) med GDrive-integrering inaktive rad. Kontakta [CODA support team](mailto:support@coda.io) för att inaktivera GDrive-integrering för din organisation om den är aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* CODA stöder **IDP** -INITIERAd SSO

* CODA stöder **just-in-Time** User-etablering

* När du har konfigurerat CODA kan du framtvinga sessionsnycklar som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-coda-from-the-gallery"></a>Lägga till CODA från galleriet

Om du vill konfigurera integreringen av CODA i Azure AD måste du lägga till CODA från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från Galleri skriver du** **CODA** i sökrutan.
1. Välj **CODA** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-coda"></a>Konfigurera och testa enkel inloggning med Azure AD för CODA

Konfigurera och testa Azure AD SSO med CODA med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i CODA.

Om du vill konfigurera och testa Azure AD SSO med CODA slutför du följande Bygg stenar:

1. **[Börja konfigurera CODA SSO](#begin-configuration-of-coda-sso)** -för att börja konfigurera SSO i CODA.
1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
   * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
   * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera CODA SSO](#configure-coda-sso)** – för att slutföra konfigurationen av inställningar för enkel inloggning i CODA.
   * **[Skapa CODA-testanvändare](#create-coda-test-user)** – om du vill ha en motsvarighet till B. Simon i CODA som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="begin-configuration-of-coda-sso"></a>Börja konfigurera CODA SSO

Följ de här stegen i CODA för att börja.

1. I CODA öppnar du panelen **organisations inställningar** .

   ![Öppna organisations inställningar](media/coda-tutorial/org-settings.png)

1. Se till att din organisation har inaktiverat GDrive-integrering. Om den är aktive rad kan du kontakta [CODA support-teamet](mailto:support@coda.io) för att hjälpa dig att migrera GDrive.

   ![GDrive inaktiverat](media/coda-tutorial/gdrive-off.png)

1. Under **autentisera med enkel inloggning (SAML)** väljer du alternativet **Konfigurera SAML** .

   ![SAML-inställningar](media/coda-tutorial/saml-settings-link.png)

1. Observera värdena för **entitets-ID** och **URL för SAML-svar**, som du behöver i efterföljande steg.

   ![Entitets-ID och SAML-svars-URL som ska användas i Azure](media/coda-tutorial/azure-settings.png)

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan **CODA** -programintegration och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du värdena för följande fält:

   a. I text rutan **identifierare** anger du "ENTITETS-ID" ovan. Det ska följa mönstret: `https://coda.io/samlId/<CUSTOMID>`

   b. I text rutan **svars-URL** anger du URL för SAML-svar från ovan. Det ska följa mönstret: `https://coda.io/login/sso/saml/<CUSTOMID>/consume`

   > [!NOTE]
   > Värdena skiljer sig från ovanstående. Du kan hitta värdena i CODA-konsolen "Configure SAML". Uppdatera dessa värden med den faktiska identifieraren och svars-URL.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

   ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera CODA** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till CODA.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **CODA**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

   ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-coda-sso"></a>Konfigurera CODA SSO

Slutför installationen genom att ange värden från Azure Active Directory i CODA- **konfigurationen för SAML** .

1. I CODA öppnar du panelen **organisations inställningar** .
1. Under **autentisera med enkel inloggning (SAML)** väljer du alternativet **Konfigurera SAML** .
1. Ange att **SAML-providern** ska **Azure Active Directory**.
1. I **inloggnings-URL för identitets leverantör**klistrar du in **inloggnings-URL:** en från Azure-konsolen.
1. I **identitets leverantörens utfärdare**klistrar du in **Azure AD-identifieraren** från Azure-konsolen.
1. I **offentligt certifikat för identitetsprovider**väljer du alternativet **överför certifikat** och väljer den certifikat fil som du laddade ned tidigare.
1. Välj **Spara**.

Detta slutför det arbete som krävs för installationen av SAML SSO-anslutningen.

### <a name="create-coda-test-user"></a>Skapa CODA-test användare

I det här avsnittet skapas en användare som kallas Britta Simon i CODA. CODA stöder just-in-Time-etablering av användare, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i CODA skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på CODA-panelen i åtkomst panelen, bör du loggas in automatiskt på det CODA som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Testa CODA med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Skydda CODA med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
