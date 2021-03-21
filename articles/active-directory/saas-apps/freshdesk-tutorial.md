---
title: 'Självstudie: Azure Active Directory integrering med FreshDesk | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och FreshDesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: e1394eafdfd733b5d69a4d4abbb6b218b4c8c10d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651961"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Självstudie: Azure Active Directory integrering med FreshDesk

I den här självstudien får du lära dig hur du integrerar FreshDesk med Azure Active Directory (Azure AD). När du integrerar FreshDesk med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till FreshDesk.
* Gör det möjligt för användarna att logga in automatiskt till FreshDesk med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:
 
* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En FreshDesk-aktiverad prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* FreshDesk stöder **IDP**-initierad enkel inloggning

## <a name="add-freshdesk-from-the-gallery"></a>Lägg till FreshDesk från galleriet

För att konfigurera integreringen av FreshDesk till Azure AD behöver du lägga till FreshDesk från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **FreshDesk** i sökrutan.
1. Välj **FreshDesk** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-freshdesk"></a>Konfigurera och testa Azure AD SSO för FreshDesk

Konfigurera och testa Azure AD SSO med FreshDesk med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i FreshDesk.

Utför följande steg för att konfigurera och testa Azure AD SSO med FreshDesk:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera FRESHDESK SSO](#configure-freshdesk-sso)** – om du vill konfigurera inställningar för enskilda Sign-On på program sidan.
    1. **[Skapa FreshDesk-testanvändare](#create-freshdesk-test-user)** – för att ha en motsvarighet för Britta Simon i FreshDesk som är länkad till en Azure AD-representation av användaren.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

1. I Azure Portal går du till sidan för program integrering i **FreshDesk** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<tenant-name>.freshdesk.com` eller något annat värde FreshDesk har föreslagit.

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<tenant-name>.freshdesk.com` eller något annat värde så Freshdesk har föreslagit.
     
    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<tenant-name>.freshdesk.com/login/saml`
    
    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL, identifierare och svars-URL. Hämta dessa värden genom att kontakta [supportteamet för FreshDesk-klienten](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. FreshDesk-programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. Följande skärm bild visar en lista över standardattribut, medan **unika användar identifierare** mappas med **User. UserPrincipalName** , men FreshDesk förväntar sig att detta anspråk ska mappas med **User. mail**, så du måste redigera mappningen av attributet genom att klicka på ikonen Redigera och ändra attributet mappning.

    ![image](common/edit-attribute.png)

1. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**. Klicka på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **set-up-FreshDesk** kopierar du lämpliga URL: er enligt ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till FreshDesk.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **FreshDesk**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-freshdesk-sso"></a>Konfigurera FreshDesk SSO

1. I ett annat webbläsarfönster loggar du in på din Freshdesk-företagsplats som administratör.

2. Välj **säkerhets ikonen** och i avsnittet **säkerhet** , utför följande steg:

    ![Enkel inloggning](./media/freshdesk-tutorial/configure-1.png "Enkel inloggning")
  
    a. För **enkel inloggning** väljer du **på**.

    b. I **inloggnings metoden** väljer du **SAML SSO**.

    c. I det **entitets-ID som tillhandahålls av** text rutan IDP klistrar du in **entitets-ID** -värdet, som du har kopierat från Azure Portal.

    d. I text rutan för **SAML SSO URL** klistrar du in **inloggnings-URL** -värde, som du har kopierat från Azure Portal.

    e. I **signerings alternativen** väljer du **enbart signerade intyg** från List rutan.

    f. I textrutan **Utloggnings-URL** klistrar du värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen.

    ex. I text rutan **säkerhetscertifikat** , klistra in **certifikat (base64)** som du har hämtat tidigare.
  
    h. Klicka på **Spara**.

## <a name="create-freshdesk-test-user"></a>Skapa Freshdesk-testanvändare

För att göra det möjligt för Azure AD-användare att logga in på FreshDesk måste de etableras i FreshDesk.  
När det gäller FreshDesk är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på **Freshdesk**-klientorganisationen.

1. I menyn till vänster klickar du på **admin** och på fliken **allmänna inställningar** klickar du på **agenter**.
  
    ![Agenter](./media/freshdesk-tutorial/create-user-1.png "Agenter")

1. Klicka på **Ny Agent**.

    ![Ny agent](./media/freshdesk-tutorial/create-user-2.png "Ny agent")

1. I dialog rutan agent information anger du de obligatoriska fälten och klickar på **Skapa Agent**.

    ![Agent information](./media/freshdesk-tutorial/create-user-3.png "Agent information")

    >[!NOTE]
    >Azure AD-kontoinnehavaren får ett e-postmeddelande som innehåller en länk för att bekräfta kontot innan det aktiveras.
    >
    >[!NOTE]
    >Du kan använda andra verktyg för FreshDesk av användar konton eller API: er som tillhandahålls av FreshDesk för att etablera Azure AD-användarkonton till FreshDesk.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till FreshDesk-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till FreshDesk-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen FreshDesk i Mina appar, bör du loggas in automatiskt på den FreshDesk som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat FreshDesk kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).