---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med program-AG-moln | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och program-AG-molnet.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/09/2020
ms.author: jeedes
ms.openlocfilehash: c2a2446da0e1ccf1b3dab88fe898b179291ddaba
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726175"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-software-ag-cloud"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med program-AG-molnet

I den här självstudien får du lära dig hur du integrerar Software AG-molnet med Azure Active Directory (Azure AD). När du integrerar Software AG-molnet med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Software AG-molnet.
* Gör det möjligt för användarna att logga in automatiskt till Software AG-molnet med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Program vara som är aktive rad för enkel inloggning (SSO) i Software AG.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Program vara AG-molnet stöder **SP** -INITIERAd SSO
* Program varans AG-moln stöder **just-in-Time** User-etablering

## <a name="adding-software-ag-cloud-from-the-gallery"></a>Lägga till program vara AG-molnet från galleriet

Om du vill konfigurera en integrering av Software AG-molnet i Azure AD måste du lägga till Software AG-molnet från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **Software AG-molnet** i sökrutan.
1. Välj **program varan AG Cloud** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-software-ag-cloud"></a>Konfigurera och testa Azure AD SSO för Software AG-molnet

Konfigurera och testa Azure AD SSO med Software AG-molnet med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Software AG-molnet.

Utför följande steg för att konfigurera och testa Azure AD SSO med Software AG-molnet:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera programmet AG Cloud SSO](#configure-software-ag-cloud-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa program vara för program](#create-software-ag-cloud-test-user)** -till-moln test – om du vill ha en motsvarighet till B. Simon i Software AG-molnet som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **program varan AG Cloud** application integration i, letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    1. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: 

        `https://*.softwareag.cloud/auth/realms/TENANT-NAME/broker/IDENTITY-PROVIDER-NAME/endpoint`

    1. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: 

        `https://*.softwareag.cloud/auth/realms/TENANT-NAME`

        > [!NOTE]
        > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [program varan AG Cloud client support team](mailto:support@softwareag.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera Software AG-molnet** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Ett exempel är `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till program AG-molnet.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. Välj **Software AG-moln** i listan program.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-software-ag-cloud-sso"></a>Konfigurera Software AG-moln SSO

1. Logga in på webbplatsen för program-AG som administratör i ett annat webbläsarfönster.

1.  Klicka på **Administration**

    ![Konfigurera Cloud administration för program-AG](./media/software-ag-cloud-tutorial/admin.png)

1. Gå till **> för enkel inloggning för att lägga till identitets leverantör**

    ![Konfigurerar Software AG Cloud Identity Provider](./media/software-ag-cloud-tutorial/add-identity-provider.png)

1. Utför följande steg på följande sida.

    ![Konfigurera program AG-moln Följ steg](./media/software-ag-cloud-tutorial/saml-1.png)

    a. I text rutan **visnings namn för identitetsprovider** anger du namnet som `azure ad` .

    b. Ange ett unikt namn för identitets leverantören i den **unika identifieraren för identitets leverantören som ska användas i text rutan för Software AG-moln omdirigering** Fältet **programs AG Cloud Redirect URI** kommer att uppdateras och fyllas i med URI: n. Kopiera denna URI och Använd den för att konfigurera **enhets-ID** och annan information i Azure Portal enligt de mönster som definierats.

    c. Importera **XML-** filen för federationsmetadata i **Identity Provider-konfigurationen** och klicka på **Nästa**.

    d. Gå till **konfigurations** sidan och fyll i fälten efter behov.

### <a name="create-software-ag-cloud-test-user"></a>Skapa program vara för programag-moln test

I det här avsnittet skapas en användare som kallas Britta Simon i Software AG-molnet. Program varans AG-moln stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Software AG-molnet skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ. 

Om du antar att Microsoft Azure har kon figurer ATS som en provider i Software AG-molnet navigerar du till `www.softwareag.cloud` och klickar på knappen Logga in och anger miljö namnet. Klicka på länken Logga in med på nästa skärm <IDP NAME> och ange autentiseringsuppgifterna. När du har autentiserat dig är du inloggad och tas till program varan AG-molnets start sida.

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Software AG-molnet kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).