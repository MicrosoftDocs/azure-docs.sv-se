---
title: 'Självstudie: Azure Active Directory integration med Cisco-paraply | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cisco Umbrella.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: cfb8ee9972d19ce07e2d681533d30c4794ddca28
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101649089"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella"></a>Självstudie: Azure Active Directory integrering med Cisco paraply

I den här självstudien får du lära dig att integrera Cisco-paraply med Azure Active Directory (Azure AD). När du integrerar Cisco paraply med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Cisco paraply.
* Gör det möjligt för användarna att logga in automatiskt till Cisco-paraply med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Den aktiverade prenumerationen för Cisco-paraply enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Cisco paraply stöder **SP-och IDP** -initierad SSO.

## <a name="add-cisco-umbrella-from-the-gallery"></a>Lägg till Cisco-paraply från galleriet

För att konfigurera integrering av Cisco Umbrella i Azure AD måste du lägga till Cisco Umbrella från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Cisco paraply** i sökrutan.
1. Välj **Cisco paraply** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-cisco-umbrella"></a>Konfigurera och testa Azure AD SSO för Cisco paraply

Konfigurera och testa Azure AD SSO med Cisco-paraply med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Cisco-paraply.

Utför följande steg för att konfigurera och testa Azure AD SSO med Cisco paraply:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Cisco paraply SSO](#configure-cisco-umbrella-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Ciscos test användare](#create-cisco-umbrella-test-user)** – för att få en motsvarighet till B. Simon i Cisco-paraply som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för **Ciscos** program integration och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

    a. Utför följande steg om du vill konfigurera programmet i **SP** initierat läge:

    b. Klicka på **Ange ytterligare URL:er**.

    c. I text rutan **inloggnings-URL** anger du URL: en: `https://login.umbrella.com/sso`

5. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Metadata XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **om att konfigurera Cisco Umbrella** kopierar du lämpliga URL:er enligt dina behov.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Cisco paraply.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Cisco paraply**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-cisco-umbrella-sso"></a>Konfigurera Cisco paraply SSO

1. I ett annat webbläsarfönster loggar du in på företagswebbplatsen Cisco Umbrella som administratör.

2. Till vänster i menyn klickar du på **Administratör** och går till **Autentisering** och klickar sedan på **SAML**.

    ![Administratören](./media/cisco-umbrella-tutorial/admin.png)

3. Välj **Other** (Övrigt) och klicka på **NÄSTA**.

    ![Övrigt](./media/cisco-umbrella-tutorial/other.png)

4. På sidan med **Cisco Umbrella-metadata** klickar du på **NÄSTA**.

    ![Metadata](./media/cisco-umbrella-tutorial/metadata.png)

5. Om du redan har konfigurerat SAML på fliken **Upload Metadata** (Ladda upp metadata) väljer du alternativet **Click here to change them** (Klicka här om du vill ändra dem) och följ stegen nedan.

    ![Nästa](./media/cisco-umbrella-tutorial/next.png)

6. I **alternativ A: Ladda upp XML-filen** laddar du upp **XML-** filen med federationsmetadata som du laddade ned från Azure Portal och efter att ha laddat ned metadata, fylls nedanstående värden i automatiskt och sedan på **Nästa**.

    ![Choosefile](./media/cisco-umbrella-tutorial/choose-file.png)

7. Under **Validate SAML Configuration** (Validera SAML-konfiguration) klickar du på **TEST YOUR SAML CONFIGURATION** (Testa din SAML-konfiguration).

    ![Testet](./media/cisco-umbrella-tutorial/test.png)

8. Klicka på **Spara**.

### <a name="create-cisco-umbrella-test-user"></a>Skapa testanvändare för Cisco Umbrella

Om du vill göra det möjligt för Azure AD-användare att logga in på Cisco Umbrella måste de etableras i Cisco Umbrella.  
När det gäller Cisco Umbrella sker etablering manuellt.

**Gör följande för att etablera ett användarkonto:**

1. I ett annat webbläsarfönster loggar du in på företagswebbplatsen Cisco Umbrella som administratör.

2. Till vänster i menyn klickar du på **Administratör** och går till **Konton**.

    ![Kontot](./media/cisco-umbrella-tutorial/account.png)

3. På sidan **Konton** klickar du på **Lägg till** överst till höger på sidan och utför följande steg.

    ![Användaren](./media/cisco-umbrella-tutorial/create-user.png)

    a. I fältet **Förnamn** anger förnamnet, som **Britta**.

    b. I fältet **Efternamn** anger du efternamnet, som **simon**.

    c. Välj din roll från **Välj delegerad administratörsroll**.

    d. I fältet **e-postadress** anger du EmailAddress för den användare som **brittasimon \@ contoso.com**.

    e. I fältet **Lösenord** anger du ditt lösenord.

    f. I fältet **Bekräfta lösenord** anger du ditt lösenord igen.

    ex. Klicka på **skapa**.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Cisco paraply-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till Cisco paraply-inloggnings-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt till det Cisco-paraply som du ställer in SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen Cisco parasoll i Mina appar, om det kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du loggas in automatiskt på det Cisco-paraply som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Cisco-parasollet kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).