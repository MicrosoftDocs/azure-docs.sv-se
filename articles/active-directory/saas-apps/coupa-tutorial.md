---
title: 'Självstudie: Azure Active Directory integration med kopplings funktion | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Coupa.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: d6a686b38c9b67ed8b1a7801c2a6ba95ef29558c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653013"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Självstudie: Azure Active Directory integration med koppling

I den här självstudien får du lära dig att integrera sömlösa med Azure Active Directory (Azure AD). När du integrerar sömlöst med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till kopplings anslutning.
* Gör det möjligt för användarna att logga in automatiskt för att koppla in sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Coupa har stöd för **SP**-initierad enkel inloggning

## <a name="add-coupa-from-the-gallery"></a>Lägg till koppling från galleriet

För att konfigurera integreringen av Coupa i Azure AD måste du lägga till Coupa från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** **skriver du** koppling i rutan Sök.
1. Välj **kopplingen** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-coupa"></a>Konfigurera och testa Azure AD SSO för koppling

Konfigurera och testa Azure AD SSO med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i koppling.

Utför följande steg för att konfigurera och testa Azure AD SSO med koppling:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera](#configure-coupa-sso)** samtidiga SSO-om du vill konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa kopplings test användare](#create-coupa-test-user)** – om du vill ha en motsvarighet till B. Simon som är kopplat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal **går du till sidan kopplings** program integration och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<companyname>.coupahost.com`

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [kundsupporten för Coupa](https://success.coupa.com/Support/Contact_Us?) och be om det här värdet.

    b. I rutan **identifierare** anger du URL: en:

    | Miljö  | URL |
    |:-------------|----|
    | Begränsat läge | `sso-stg1.coupahost.com`|
    | Produktion | `sso-prd1.coupahost.com`|
    | | |

    c. Skriv webb adressen i text rutan **svars-URL** :

    | Miljö | URL |
    |------------- |----|
    | Begränsat läge | `https://sso-stg1.coupahost.com/sp/ACS.saml2`|
    | Produktion | `https://sso-prd1.coupahost.com/sp/ACS.saml2`|
    | | |

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera Coupa** kopierar du lämpliga URL:er enligt dina behov.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till kopplings behörighet.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I programlistan väljer du **Coupa**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-coupa-sso"></a>Konfigurera koppling via enkel inloggning

1. Logga in på din Coupa-företagsplats som administratör.

2. Gå till **Setup \> Security Control** (Konfiguration > Säkerhetskontroll).

    ![Säkerhets kontroller](./media/coupa-tutorial/setup.png "Säkerhetskontroller")

3. I avsnittet **Log in using Coupa credentials** (Logga in med autentiseringsuppgifter för Coupa) utför du följande steg:

    ![Kopplings-SP-metadata](./media/coupa-tutorial/login.png "Kopplings-SP-metadata")

    a. Välj **Log in using SAML** (Logga in med hjälp av SAML).

    b. Klicka på **Bläddra** för att ladda upp de metadata som du laddade ned från Azure-portalen.

    c. Klicka på **Spara**.

### <a name="create-coupa-test-user"></a>Skapa Coupa-testanvändare

För att Azure AD-användare ska kunna logga in i Coupa måste de etableras till Coupa.  

* När det gäller Coupa är etablering en manuell uppgift.

**Konfigurera användaretablering genom att utföra följande steg:**

1. Logga in på din **Coupa**-företagsplats som administratör.

2. På menyn längst upp klickar du på **Setup** (Konfiguration) och sedan på **Användare**.

    ![Användare](./media/coupa-tutorial/user.png "Användare")

3. Klicka på **Skapa**.

    ![Skapa användare](./media/coupa-tutorial/create.png "Skapa användare")

4. I avsnittet **Skapa användare** utför du följande steg:

    ![Användar information](./media/coupa-tutorial/details.png "Användar information")

    a. Skriv attributen **Inloggning**, **Förnamn**, **Efternamn**, **ID för enkel inloggning** och **E-post** för ett giltigt Azure Active Directory-konto som du vill etablera i de relaterade textrutorna.

    b. Klicka på **Skapa**.

    >[!NOTE]
    >Azure Active Directory-kontoinnehavaren får ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktivt.
    >

>[!NOTE]
>Du kan använda andra verktyg för skapande av användar konton eller API: er som tillhandahålls av kopplings verktyg för att etablera Azure AD-användarkonton.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till kopplings-URL: n där du kan starta inloggnings flödet. 

* Gå till kopplings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen koppling i Mina appar, bör du loggas in automatiskt på de kopplings bara för vilka du ställer in SSO. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat kopplings behörighet kan du genomdriva en fjärrstyrningssession som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app)