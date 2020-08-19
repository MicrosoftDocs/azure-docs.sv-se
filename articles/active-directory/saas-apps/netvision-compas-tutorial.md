---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med Netvision kompositioner | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och Netvision kompositioner.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2020
ms.author: jeedes
ms.openlocfilehash: 8f92ff60dacd78687207c7523504182521700a7b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554506"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netvision-compas"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med Netvision kompositioner

I den här självstudien får du lära dig hur du integrerar Netvision-kompositioner med Azure Active Directory (Azure AD). När du integrerar Netvision kompositioner med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Netvision kompositioner.
* Gör det möjligt för användarna att logga in automatiskt till Netvision-kompositioner med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Netvision kompositioner enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Netvision kompositioner stöder **SP-och IDP** -INITIERAd SSO
* När du har konfigurerat en Netvision-komposition kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)


## <a name="adding-netvision-compas-from-the-gallery"></a>Lägga till Netvision kompositioner från galleriet

Om du vill konfigurera en integrering av Netvision-kompositioner i Azure AD måste du lägga till Netvision kompositioner från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Netvision kompositioner** i sökrutan.
1. Välj **Netvision kompositioner** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netvision-compas"></a>Konfigurera och testa enkel inloggning med Azure AD för Netvision kompositioner

Konfigurera och testa Azure AD SSO med Netvision kompositioner med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Netvision kompositioner.

Om du vill konfigurera och testa Azure AD SSO med Netvision kompositioner slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Netvision kompositioner SSO](#configure-netvision-compas-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. Konfigurera en systemdefinierad komposition som- **[test användare](#configure-netvision-compas-test-user)** – för att få en motsvarighet till B. Simon i Netvision-kompositioner som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Netvision kompositioner** -program integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<TENANT>.compas.cloud/Identity/Saml20`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Netvision kompositioner-klient support teamet](mailto:contact@net.vision) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned metadatafilen och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)



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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Netvision kompositioner.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Netvision kompositioner**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-netvision-compas-sso"></a>Konfigurera Netvision kompositioner SSO

I det här avsnittet aktiverar du SAML SSO i **Netvision kompositioner**.
1. Logga in på **Netvision kompositioner** med ett administratörs konto och få åtkomst till administrations platsen.

    ![Admin-sektion](media/netvision-compas-tutorial/admin.png)

1. Leta reda på **system** -ytan och välj **identitets leverantörer**.

    ![Admin-IDP: er](media/netvision-compas-tutorial/admin-idps.png)

1. Välj åtgärden **Lägg till** för att registrera Azure AD som en ny IDP.

    ![Lägg till IDP](media/netvision-compas-tutorial/idps-add.png)

1. Välj **SAML** som **typ av Provider**.
1. Ange meningsfulla värden för fälten **visnings namn** och **Beskrivning** .
1. Tilldela en **Netvision** -användare till IDP genom att välja från listan **tillgängliga användare** och sedan välja knappen **Lägg till markerad** . Användare kan också tilldelas IDP när de följer etablerings proceduren.
1. För SAML-alternativet **metadata** klickar du på knappen **Välj fil** och väljer den metadatafil som du sparade tidigare på datorn.
1. Klicka på **Spara**.

    ![Redigera IDP](media/netvision-compas-tutorial/idp-edit.png)


### <a name="configure-netvision-compas-test-user"></a>Konfigurera webvision kompositioner-test användare

I det här avsnittet konfigurerar du en befintlig användare i **Netvision-kompositioner** att använda Azure AD för SSO.
1. Följ anvisningarna för användar etablering av **Netvision kompositioner** , som definieras av ditt företag eller redigera ett befintligt användar konto.
1. När du definierar användarens profil kontrollerar du att användarens **e-postadress (personlig)** matchar användar namnet för Azure AD: username@companydomain.extension . Till exempel `B.Simon@contoso.com`.

    ![Redigera användare](media/netvision-compas-tutorial/user-config.png)

Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa konfigurationen för enkel inloggning i Azure AD.

### <a name="using-the-access-panel-idp-initiated"></a>Använda åtkomst panelen (IDP initierad).

När du klickar på panelen för Netvision-kompositioner på åtkomst panelen, bör du loggas in automatiskt på den Netvision kompositioner som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

### <a name="directly-accessing-netvision-compas-sp-initiated"></a>Direkt åtkomst till Netvision kompositioner (SP initierad).

1. Få åtkomst till URL: en för **Netvision kompositioner** . Till exempel `https://tenant.compas.cloud`.
1. Ange användar namnet **compe** och välj **sedan nästa**.

    ![Inloggad användare](media/netvision-compas-tutorial/login-user.png)

1. **(valfritt)** Om användaren tilldelas flera IDP: er i **Netvision-kompositioner**visas en lista över tillgängliga IDP: er. Välj den Azure AD-IDP som har kon figurer ATS tidigare i **Netvision kompositioner**.

    ![Välj inloggning](media/netvision-compas-tutorial/login-choose.png)

1. Du omdirigeras till Azure AD för att utföra autentiseringen. När du har autentiserats bör du vara inloggad automatiskt till **Netvision-kompositioner** som du ställer in SSO för.

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Testa Netvision kompositioner med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
