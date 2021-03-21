---
title: 'Självstudie: Azure Active Directory integrering med växthus | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Greenhouse.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/25/2020
ms.author: jeedes
ms.openlocfilehash: 77f72d6c63231f0854b58470f86c65ffc81c9775
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98731927"
---
# <a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>Självstudie: Azure Active Directory integrering med växthus

I den här självstudien får du lära dig hur du integrerar växthus med Azure Active Directory (Azure AD). När du integrerar växthus med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till växthus.
* Gör det möjligt för användarna att logga in automatiskt till växthus med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Prenumeration som är aktive rad för växthus enkel inloggning (SSO).

> [!NOTE] 
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet. 

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Greenhouse stöder **SP**-initierad enkel inloggning

## <a name="adding-greenhouse-from-the-gallery"></a>Lägga till Greenhouse från galleriet

För att konfigurera integreringen av Greenhouse i Azure AD måste du lägga till Greenhouse från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från Galleri** skriver du **växthus** i sökrutan.
1. Välj panelen **växthus** från resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-greenhouse"></a>Konfigurera och testa Azure AD SSO för växthus

Konfigurera och testa Azure AD SSO med växthus med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i växthus.

Utför följande steg för att konfigurera och testa Azure AD SSO med växthus:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
2. **[Konfigurera en växthus-SSO](#configure-greenhouse-sso)** -konfiguration för att konfigurera enskilda Sign-On inställningar på program sidan.
    1. **[Skapa Greenhouse-testanvändare](#create-greenhouse-test-user)** – för att ha en motsvarighet för Britta Simon i Greenhouse som är länkad till en Azure AD-representation av användaren.
3. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan för hantering av **växthus** program på sidan Azure Portal letar du reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.greenhouse.io`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<companyname>.greenhouse.io`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [supportteamet för Greenhouse](https://www.greenhouse.io/contact) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera Greenhouse** kopierar du lämpliga URL:er enligt dina behov.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till växthus.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan med program väljer du **Greenhouse**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-greenhouse-sso"></a>Konfigurera växthus SSO

1. Logga in på en växthus-webbplats som administratör i ett annat webbläsarfönster.

1. Gå till **konfigurera > dev Center > enkel inloggning**.

    ![skärm bild för SSO-Sidan](./media/greenhouse-tutorial/configure.png)

1. Utför följande steg på sidan enskild Sign-On.

    ![skärm bild för konfigurations sidan för SSO](./media/greenhouse-tutorial/sso-page.png)

    a. Kopiera URL-värde för **SSO Assertion-konsumenten** , klistra in det här värdet i text rutan **SVARs-URL** i den **grundläggande SAML-konfigurationen** i Azure Portal.

    b. I text rutan **entitets-ID/Issuer** klistrar du in det ID-värde för **Azure AD** som du har kopierat från Azure Portal.

    c. I text rutan **URL för enskild Sign-On** klistrar du in URL-värdet för **inloggning** som du har kopierat från Azure Portal.

    d. Öppna den hämtade **federationens metadata-XML** från Azure Portal i anteckningar och klistra in innehållet i text rutan för **finger avtryck för IDP-certifikat** .

    e. Välj format värde för **namn identifierare** i list rutan.

    f. Klicka på **påbörja testning**.

    >[!NOTE]
    >Alternativt kan du också ladda upp **XML-** filen med federationsmetadata genom att klicka på alternativet **Välj fil** .

### <a name="create-greenhouse-test-user"></a>Skapa Greenhouse-testanvändare

För att Azure AD-användare ska kunna logga in i Greenhouse måste de etableras till Greenhouse. I Greenhouse görs etablering manuellt.

>[!NOTE]
>Du kan använda andra verktyg för skapande av växthus-användarkonto eller API: er från växthus för att etablera Azure AD-användarkonton. 

**Utför följande steg för att tillhandahålla ett användarkonto:**

1. Logga in på **Greenhouse**-företagsplatsen som administratör.

2. Gå till **konfigurera > användare > nya användare**
   
    ![Användare](./media/greenhouse-tutorial/create-user-1.png "Användare")

4. I avsnittet **Lägg till nya användare** utför du följande steg:
   
    ![Lägg till ny användare](./media/greenhouse-tutorial/create-user-2.png "Lägg till ny användare")

    a. I textrutan för att **ange användarens e-postadress** skriver du e-postadressen för ett giltigt Azure Active Directory-konto som du vill etablera.

    b. Klicka på **Spara**.    
   
      >[!NOTE]
      >Azure Active Directory-kontoinnehavaren får ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktivt.

### <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigerar till den växthus inloggnings-URL: en där du kan starta inloggnings flödet. 

* Gå till inloggnings-URL: n för växthus direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen för växthus i Mina appar omdirigeras den till den växthus inloggnings-URL: en. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat växthus kan du framtvinga kontroll över sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).