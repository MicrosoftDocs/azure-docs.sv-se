---
title: 'Självstudie: Azure Active Directory integrering med asana | Microsoft Docs'
description: Läs hur du konfigurerar enkel inloggning mellan Azure Active Directory och Asana.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: a46396d25519decdd45764acbea485bdd0571782
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428888"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Självstudie: Azure Active Directory integrering med asana

I den här självstudien får du lära dig hur du integrerar asana med Azure Active Directory (Azure AD). När du integrerar asana med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till asana.
* Gör det möjligt för användarna att logga in automatiskt till asana med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Asana för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Asana stöder **SP**-initierad enkel inloggning

* Asana stöder [**automatisk** användaretablering](asana-provisioning-tutorial.md)

## <a name="add-asana-from-the-gallery"></a>Lägg till asana från galleriet

Om du vill konfigurera integrering av Asana i Azure AD så behöver du lägga till Asana från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **asana** i sökrutan.
1. Välj **asana** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-asana"></a>Konfigurera och testa Azure AD SSO för asana

Konfigurera och testa Azure AD SSO med asana med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i asana.

Utför följande steg för att konfigurera och testa Azure AD SSO med asana:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera ASANA SSO](#configure-asana-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa asana test User](#create-asana-test-user)** -om du vill ha en motsvarighet till B. Simon i asana som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **asana** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning på Asana-domäner och URL:er](common/sp-identifier.png)

    a. I rutan **Inloggnings-URL** anger du följande URL: `https://app.asana.com/`

    b. I textrutan **Identifierare (entitets-ID)** anger du URL:en: `https://app.asana.com/`

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Asana** kopierar du lämpliga URL:er enligt dina behov.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till asana.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I programlistan väljer du **Asana**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="configure-asana-sso"></a>Konfigurera asana SSO

1. Logga in i ditt Asana-program i en annan webbläsare. Om du vill konfigurera enkel inloggning i Asana så öppnar du arbetsyteinställningarna genom att klicka på arbetsytans namn i det övre högra hörnet på skärmen. Klicka sedan på **\<your workspace name\> Inställningar**.

    ![Inställningar för enkel inloggning i Asana](./media/asana-tutorial/settings.png)

2. I fönstret **Organisationsinställningar** så klickar du på **Administration**. Klicka därefter på **Medlemmar måste logga in via SAML** för att aktivera konfigurationen för enkel inloggning. Utför följande steg:

    ![Konfigurera inställningar för enkel inloggningsorganisation](./media/asana-tutorial/save.png)  

    a. I textrutan **URL för inloggningssida** så klistrar du in **inloggnings-URL**.

    b. Högerklicka på det certifikat som hämtats från Azure-portalen och öppna sedan certifikatfilen med Anteckningar eller din önskade textredigerare. Kopiera innehållet mellan start- och slutcertifikatets titel och klistra in det i textrutan **X.509-certifikat**.

3. Klicka på **Spara**. Gå till [Asana-guide för att konfigurera enkel inloggning](https://asana.com/guide/help/premium/authentication#gl-saml) om du behöver ytterligare hjälp.

### <a name="create-asana-test-user"></a>Skapa en Asana-testanvändare

Målet med det här avsnittet är att skapa en användare som heter Britta Simon i Asana. Asana stöder automatisk användaretablering, vilket är aktiverat som standard. Du hittar mer information [här](asana-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du behöver skapa användare manuellt så gör du följande:**

I det här avsnittet skapar du en användare som heter Britta Simon i Asana.

1. I **Asana** så går du till **Team**-avsnittet i den vänstra panelen. Klicka på plustecken-knappen.

    ![Skapa en testanvändare för Azure AD](./media/asana-tutorial/teams.png)

2. Skriv e-postadressen till användaren som **Britta. simon \@ contoso.com** i text rutan och välj sedan **Bjud in**.

3. Klicka på **Skicka inbjudan**. Den nya användaren får ett e-postmeddelande på sitt e-postkonto. användaren måste skapa och validera kontot.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till asana-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till asana-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen asana i Mina appar omdirigeras det till asana-inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat asana kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
