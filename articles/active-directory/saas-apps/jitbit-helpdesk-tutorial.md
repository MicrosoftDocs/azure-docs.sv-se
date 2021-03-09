---
title: 'Självstudie: Azure Active Directory integrering med Jitbit-supportavdelningen | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och Jitbit-supportavdelningen.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: jeedes
ms.openlocfilehash: 26d9321f028bc8b9293e9d7d46616fff2f3b60ce
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102486878"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Självstudie: Azure Active Directory integrering med Jitbit-supportavdelningen

I den här självstudien får du lära dig hur du integrerar Jitbit-supportavdelningen med Azure Active Directory (Azure AD). När du integrerar Jitbit-supportavdelningen med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Jitbit-supportavdelningen.
* Gör det möjligt för användarna att logga in automatiskt för att Jitbit supportavdelningen med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Jitbit support för enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Jitbit Helpdesk stöder **SP** -initierad SSO.

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="add-jitbit-helpdesk-from-the-gallery"></a>Lägg till Jitbit-supportavdelningen från galleriet

Om du vill konfigurera integreringen av Jitbit-supportavdelningen i Azure AD måste du lägga till Jitbit-supportavdelningen från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Jitbit supportavdelningen** i sökrutan.
1. Välj **Jitbit supportavdelningen** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-jitbit-helpdesk"></a>Konfigurera och testa Azure AD SSO för Jitbit-supportavdelningen

Konfigurera och testa Azure AD SSO med Jitbit Helpdesk med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Jitbit-supportavdelningen.

Utför följande steg för att konfigurera och testa Azure AD SSO med Jitbit supportavdelningen:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Jitbit Helpdesk SSO](#configure-jitbit-helpdesk-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Jitbit support test User](#create-jitbit-helpdesk-test-user)** – om du vill ha en motsvarighet till B. Simon i Jitbit-supportavdelningen som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för **Jitbit-Helpdesk** -programintegration, letar reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I text rutan **inloggnings-URL** skriver du en av URL: erna med följande mönster:
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Det här värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [support teamet för Jitbit supportavdelningen](https://www.jitbit.com/support/) för att få det här värdet.

    b. I textrutan **Identifierare (entitets-ID)** anger du URL:en: `https://www.jitbit.com/web-helpdesk/`

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Jitbit support** kopierar du lämpliga URL: er enligt ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Jitbit-supportavdelningen.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Jitbit supportavdelningen**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-jitbit-helpdesk-sso"></a>Konfigurera Jitbit Helpdesk SSO

1. Logga in på företags platsen för Jitbit-supportavdelningen som administratör i ett annat webbläsarfönster.

1. I verktygsfältet högst upp klickar du på **Administration**.

    ![Administration](./media/jitbit-helpdesk-tutorial/settings.png "Administration")

1. Klicka på **allmänna inställningar**.

    ![Skärm bild som visar länken allmänna inställningar.](./media/jitbit-helpdesk-tutorial/general.png "Användare, företag och behörigheter")

1. I avsnittet konfiguration av **autentiseringsinställningar** utför du följande steg:

    ![Autentiseringsinställningar](./media/jitbit-helpdesk-tutorial/authentication.png "Autentiseringsinställningar")

    a. Välj **Aktivera SAML 2,0 enkel inloggning** för att logga in med enkel Sign-On (SSO) med **OneLogin**.

    b. I text rutan **slut punkts-URL** klistrar du in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal.

    c. Öppna ditt **bas-64-** kodade certifikat i anteckningar, kopiera innehållet i det till Urklipp och klistra in det i rutan **X. 509-certifikat**

    d. Klicka på **Spara ändringar**.

### <a name="create-jitbit-helpdesk-test-user"></a>Skapa test användare av Jitbit-supportavdelningen

För att kunna göra det möjligt för Azure AD-användare att logga in på Jitbit-supportavdelningen måste de tillhandahållas i Jitbit-supportavdelningen. När det gäller Jitbit-supportavdelningen är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din **Jitbit-Helpdesk** -klient.

1. Klicka på **Administration** i menyn högst upp.

    ![Administration](./media/jitbit-helpdesk-tutorial/settings.png "Administration")

1. Klicka på **användare, företag och behörigheter**.

    ![Användare, företag och behörigheter](./media/jitbit-helpdesk-tutorial/users.png "Användare, företag och behörigheter")

1. Klicka på **Lägg till användare**.

    ![Lägg till användare](./media/jitbit-helpdesk-tutorial/add.png "Lägg till användare")

1. I avsnittet Skapa anger du data för det Azure AD-konto som du vill etablera enligt följande:

    ![Skapa](./media/jitbit-helpdesk-tutorial/create-section.png "Skapa")

   a. I text rutan **användar namn** anger du användar namnet för användaren som **BrittaSimon**.

   b. Skriv e-post för användaren i text rutan **e-post** **BrittaSimon@contoso.com** .

   c. I text rutan **förnamn** skriver du förnamn för användaren som **Britta**.

   d. I text rutan **efter namn** skriver du efter namnet på användaren som **Simon**.

   e. Klicka på **Skapa**.

> [!NOTE]
> Du kan använda andra verktyg för att skapa användar konton eller API: er för Jitbit-Helpdesk som tillhandahålls av Jitbit-supportavdelningen för att etablera Azure AD-användarkonton.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Jitbit Helpdesk-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till inloggnings-URL: en för Jitbit supportavdelningen direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på Jitbit supportavdelningen i Mina appar omdirigeras det till URL: en för inloggning med Jitbit supportavdelningen. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Jitbit-supportavdelningen kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
