---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Exium | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Exium.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2021
ms.author: jeedes
ms.openlocfilehash: 01b3bbc0a4f0c7d53ed0a9ba2449cf5985b2ab86
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104610263"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-exium"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Exium

I den här självstudien får du lära dig hur du integrerar Exium med Azure Active Directory (Azure AD). När du integrerar Exium med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Exium.
* Gör det möjligt för användarna att logga in automatiskt till Exium med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Exium för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Exium stöder **SP** -initierad SSO.

## <a name="adding-exium-from-the-gallery"></a>Lägga till Exium från galleriet

Om du vill konfigurera integreringen av Exium i Azure AD måste du lägga till Exium från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Exium** i sökrutan.
1. Välj **Exium** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-exium"></a>Konfigurera och testa Azure AD SSO för Exium

Konfigurera och testa Azure AD SSO med Exium med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Exium.

Utför följande steg för att konfigurera och testa Azure AD SSO med Exium:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera EXIUM SSO](#configure-exium-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Exium test User](#create-exium-test-user)** -om du vill ha en motsvarighet till B. Simon i Exium som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **Exium** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://subapi.exium.net/saml/<WORKSPACE_ID>/metadata`
    
    b.  Skriv en URL i text rutan **svars-URL** med följande mönster: `https://subapi.exium.net/saml/<WORKSPACE_ID>/acs`

    c. I rutan **Inloggnings-URL** anger du följande URL: `https://service.exium.net/sign-in`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Exium client support team](mailto:support@exium.net) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Exium.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Exium**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-exium-sso"></a>Konfigurera Exium SSO

1. Logga in på Exium företags webbplats som administratör.

1. I **Administratörs konsolen** väljer du **företags profil** panel.

    ![skärm bild för administratörs konsol](./media/exium-tutorial/company-profile.png)

1. I **profilen** klickar du på **SSO-inställningar** och **redigerar** den.

1. Utför stegen nedan i avsnittet **SSO-inställningar** .

    ![skärm bild för SSO-inställningar](./media/exium-tutorial/update.png)

    a. Välj **SSO-typ** som **AzureAD** i list rutan.

    b. Klistra in URL-värdet för **app Federation-Metadata** i fältet **SAML 2,0 IDP metadata URL** .

    c. Kopiera URL-värde för **SAML 2,0 SSO** , klistra in det här värdet i text rutan **SVARs-URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    d. Kopiera **saml 2,0 SP entitets-ID** -värde, klistra in det här värdet i text rutan **identifierare** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.  

    e. Klicka på **Uppdatera**.

### <a name="create-exium-test-user"></a>Skapa Exium test användare

1. Logga in på Exium företags webbplats som administratör.

1. Gå till **användar hantering – > användare** och klicka på **Lägg till användare**.

    ![skärm bild för skapa test användare](./media/exium-tutorial/add-user.png)

1. Ange de obligatoriska fälten på följande sida och klicka på **Spara**.

    ![skärm bild för skapa test användar fält med knappen Spara](./media/exium-tutorial/add-user-2.png)

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Exium-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till Exium-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Exium i Mina appar omdirigeras det till Exium-inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Exium kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


