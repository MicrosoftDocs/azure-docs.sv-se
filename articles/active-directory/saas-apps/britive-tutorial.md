---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Britive | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Britive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2020
ms.author: jeedes
ms.openlocfilehash: 02358dcffa6f757e3c61b3c1ae0e7c5298f7d9ca
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88542691"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-britive"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Britive

I den här självstudien får du lära dig hur du integrerar Britive med Azure Active Directory (Azure AD). När du integrerar Britive med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Britive.
* Gör det möjligt för användarna att logga in automatiskt till Britive med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Britive för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Britive stöder **SP** -INITIERAd SSO
* När du har konfigurerat Britive kan du framtvinga sessionsnycklar som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-britive-from-the-gallery"></a>Lägga till Britive från galleriet

Om du vill konfigurera integreringen av Britive i Azure AD måste du lägga till Britive från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Britive** i sökrutan.
1. Välj **Britive** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-britive"></a>Konfigurera och testa enkel inloggning med Azure AD för Britive

Konfigurera och testa Azure AD SSO med Britive med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Britive.

Om du vill konfigurera och testa Azure AD SSO med Britive, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera BRITIVE SSO](#configure-britive-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Britive test User](#create-britive-test-user)** -om du vill ha en motsvarighet till B. Simon i Britive som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Britive** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<TENANTNAME>.britive-app.com/sso`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `urn:amazon:cognito:sp:<UNIQUE_ID>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska inloggnings-URL: en och identifieraren, som beskrivs senare i den här självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera Britive** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Britive.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Britive**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-britive-sso"></a>Konfigurera Britive SSO

1. Logga in på Britive-webbplatsen som administratör i ett annat webbläsarfönster.

1. Klicka på **ikonen för administratörs inställningar** och välj **säkerhet**.

    ![Britive-konfiguration](./media/britive-tutorial/configure1.png)

1. Välj **SSO-konfiguration** och utför följande steg:

    ![Britive-konfiguration](./media/britive-tutorial/configure2.png)

    a. Kopiera **mål grupp/entitets-ID-** värde och klistra in det i text rutan **identifierare (enhets-ID)** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    b. Kopiera URL-värde för **webb adress för enkel inloggning** och klistra in det i text rutan för **INLOGGNINGs-URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    c. Klicka på **överför SAML-metadata** för att ladda upp den hämtade XML-filen med METADATA från Azure Portal. När du har laddat upp metadatafilen fylls ovanstående värden i automatiskt och ändringarna sparas.

### <a name="create-britive-test-user"></a>Skapa Britive test användare

1. Logga in på Britive-webbplatsen som administratör i ett annat webbläsarfönster.

1. Klicka på **ikonen för administratörs inställningar** och välj **användar administration**.

    ![Britive-konfiguration](./media/britive-tutorial/user1.png)

1. Klicka på **Lägg till användare**.

    ![Britive-konfiguration](./media/britive-tutorial/user2.png)

1. Fyll i all nödvändig information om användaren enligt organisationens krav och klicka på **Lägg till användare**.

    ![Britive-konfiguration](./media/britive-tutorial/user3.png)

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Britive på åtkomst panelen, bör du loggas in automatiskt på den Britive som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Britive med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)