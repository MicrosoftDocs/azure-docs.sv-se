---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med Parkalot Park Management | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och Parkalot Park-hantering.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 0098d28d2b211ad9e4bbe60a44c5de4058f2b96b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651443"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-parkalot---car-park-management"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med Parkalot Park Management

I den här självstudien får du lära dig hur du integrerar Parkalot Park-hantering med Azure Active Directory (Azure AD). När du integrerar Parkalot Park-hantering med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Parkalot Park-hantering.
* Gör det möjligt för användarna att logga in automatiskt till Parkalot Park hantering med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Parkalot-prenumeration med enkel inloggning (SSO) för Car Park-hantering.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Hantering av Parkalot-Car stöder **SP** -INITIERAd SSO

* Hantering av Parkalot-Car stöder **just-in-Time** User-etablering

## <a name="adding-parkalot---car-park-management-from-the-gallery"></a>Lägga till Parkalot Park Management från galleriet

Om du vill konfigurera integreringen av Parkalot Park-hantering i Azure AD måste du lägga till Parkalot hanterings hantering från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Parkalot Park Management** i sökrutan.
1. Välj **Parkalot Park Management** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-parkalot---car-park-management"></a>Konfigurera och testa Azure AD SSO för Parkalot – Car Park Management

Konfigurera och testa Azure AD SSO med hantering av Parkalot-Car med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i Parkalot Park Management.

Utför följande steg för att konfigurera och testa Azure AD SSO med Parkalot Park Management:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Parkalot-Car Park Management SSO](#configure-parkalot-car-park-management-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Parkalot-Car Park-hanterings test användare](#create-parkalot-car-park-management-test-user)** – för att få en motsvarighet till B. Simon i Parkalot Park-hanteringen som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan för hantering av program på **Parkalot-Car Park** , letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I text rutan **identifierare (enhets-ID)** anger du en URL med något av följande mönster:

    | Identifierare (entitets-ID) |
    | -------------- |
    | `https://parkalot.io` |
    | `https://<CUSTOMERNAME>.parkalot.io` |
    |

    b. I text rutan **svars-URL** skriver du en URL med något av följande mönster:

    | Svars-URL |
    | -------------- |
    | `https://<CUSTOMERNAME>.parkalot.io` |
    | `https://parkalot-saml.firebaseapp.com/__/auth/handler` |
    | `https://parkalot-saml.web.app/__/auth/handler` |
    | `https://<CustomerName>.parkalot.io/__/auth/handler` |
    |

    c. I text rutan **inloggnings-URL** skriver du en URL med något av följande mönster:

    | Inloggnings-URL |
    | -------------- |
    | `https://<CUSTOMERNAME>.parkalot.io/#/login` |
    | `https://parkalot-saml.firebaseapp.com/#/login` |
    | `https://parkalot-saml.web.app/#/login` |
    |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Parkalot-support teamet för Car Park Management](mailto:contact-us@parkalot.io) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Parkalot-Car Park-hantering** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Parkalot Park Management.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Parkalot Park Management**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-parkalot-car-park-management-sso"></a>Konfigurera Parkalot-Car Park-hantering SSO

1. I ett annat webbläsarfönster loggar du in på företags platsen för Parkalot Park hantering som administratör.

1. Välj **Konfigurera SAML** och klicka på ikonen **Redigera** på **Lägg till nytt** kort.

    ![Lägg till ny REDIGERINGS ikon.](./media/parkalot-car-park-management-tutorial/setup-saml.png)

1. Utför nedanstående steg på följande sida.

    ![Konfigurera Parkalot Park Management SSO.](./media/parkalot-car-park-management-tutorial/configuration.png)

    a. Ange ett giltigt namn i text rutan **visnings namn** .

    b. I text rutan för **entitets-ID för IDP** klistrar du in värdet för **Azure AD-identifieraren** , som du har kopierat från Azure Portal.

    c. I text rutan **SSO-URL** klistrar du in värdet för **inloggnings-URL** : en som du har kopierat från Azure Portal.

    d. Öppna det hämtade **certifikatet (base64)** från Azure Portal i anteckningar och klistra in innehållet i text rutan **certifikat** .

    e. Klicka på **Spara**.

### <a name="create-parkalot-car-park-management-test-user"></a>Skapa Parkalot-Car Park-hantering test användare

I det här avsnittet skapas en användare som kallas Britta Simon i hantering av Parkalot-Car-Park. Hantering av Parkalot-Car stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Parkalot Park-hantering, skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till inloggnings-URL: en för Parkalot-Car Park där du kan starta inloggnings flödet. 

* Gå till inloggnings-URL: en för Parkalot-Car Park direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Parkalot Park hantering i Mina appar omdirigeras den till URL: en för inloggning med Parkalot-Car Park Management. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Parkalot Park-hantering kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).