---
title: 'Självstudie: Azure Active Directory integrering med absorberande LMS | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Absorb LMS.
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
ms.openlocfilehash: 5e962592779494e1d60d03e9e8a167d53ac8bda2
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364178"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Självstudie: Azure Active Directory integrering med absorberande LMS

I den här självstudien får du lära dig att integrera absorberande LMS med Azure Active Directory (Azure AD). När du integrerar absorberande LMS med Azure AD kan du:

* Kontroll i Azure AD som har till gång till absorberande LMS.
* Gör det möjligt för användarna att logga in automatiskt för att absorbera LMS med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure Active Directory-integrering med Absorb LMS, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Absorberande prenumeration för enkel inloggning med LMS.

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Absorb LMS stöder **IDP**-initierad enkel inloggning

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="add-absorb-lms-from-the-gallery"></a>Lägg till absorberande LMS från galleriet

För att konfigurera integrering av Absorb LMS i Azure Active Directory, behöver du lägga till Absorb LMS från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från Galleri skriver du** **absorbera LMS** i sökrutan.
1. Välj **absorbera LMS** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-absorb-lms"></a>Konfigurera och testa Azure AD SSO för att absorbera LMS

Konfigurera och testa Azure AD SSO med absorberande LMS med en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i absorberande LMS.

Utför följande steg för att konfigurera och testa Azure AD SSO med absorberande LMS:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera absorberande LMS SSO](#configure-absorb-lms-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa en absorberande LMS-test](#create-absorb-lms-test-user)** för att få en motsvarighet till B. Simon i absorberande LMS som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **absorberad LMS** -programintegration, letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    Om du använder **Absorb 5 – UI** använder du följande konfiguration:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster:: `https://<SUBDOMAIN>.myabsorb.com/account/saml`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster:: `https://<SUBDOMAIN>.myabsorb.com/account/saml`

    Om du använder **Absorb 5 – New Learner Experience** använder du följande konfiguration:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster:: `https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Supportteamet för Absorb LMS](https://support.absorblms.com/hc/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**.

    ![image](common/edit-attribute.png)

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. I avsnittet **Konfigurera Absorb LMS** kopierar du lämpliga URL:er enligt dina behov.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till absorberande LMS.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **absorbera LMS**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-absorb-lms-sso"></a>Konfigurera absorberande LMS SSO

1. I ett nytt webbläsarfönster, loggar du in på din Absorb LMS-företagsplats som administratör.

2. Välj knappen **Konto** längst upp till höger.

    ![Knappen Konto](./media/absorblms-tutorial/account.png)

3. I rutan Konto väljer du **Portalinställningar**.

    ![Exportera länken Portalinställningar](./media/absorblms-tutorial/portal.png)

4. Välj fliken **Hantera inställningar för enkel inloggning**.

    ![Fliken Användare](./media/absorblms-tutorial/sso.png)

5. På sidan **Hantera inställningar för enkel inloggning** gör du följande:

    ![Konfigurationssidan för enkel inloggning](./media/absorblms-tutorial/settings.png)

    a. I textrutan **Namn** anger du namnet till Azure AD Marketplace SSO.

    b. Välj **SAML** som en **Metod**.

    c. I anteckningar, öppnar du det certifikat som du laddade ned från Azure Portal. Ta bort taggarna **---BEGIN CERTIFICATE---** och **---END CERTIFICATE---**. I rutan **Nyckel** klistrar du in det återstående innehållet.

    d. I rutan **Läge** väljer du **Identitetsprovidern initierad**.

    e. I rutan **Id-egenskap** väljer du det attribut som du har konfigurerat som användaridentifierare i Azure AD. Om du till exempel har valt *NameIdentifier* i Azure AD väljer du **användar namn**.

    f. Välj **Sha256** som en **Signaturtyp**.

    ex. I rutan **inloggnings-URL** klistrar du in **URL för användaråtkomst** från programmets sida **Egenskaper** i Azure Portal.

    h. I **URL för utloggning**, klistrar du in det värde för **URL:en för utloggning** som du kopierade från fönstret **Konfigurera inloggning** i Azure Portal.

    i. Växla **Omdirigera automatiskt** till **På**.

6. Välj **Spara.**

    ![Växla Endast tillåta SSO-inloggning](./media/absorblms-tutorial/save.png)

### <a name="create-absorb-lms-test-user"></a>Skapa Absorb LMS-testanvändare

För att Azure AD-användare ska kunna logga in på Absorb LMS, måste de konfigureras i Absorb LMS. När det gäller Absorb LMS är etablering en manuell aktivitet.

**Konfigurera användaretablering genom att utföra följande steg:**

1. Logga in på din Absorb LMS-företagsplats som administratör.

2. I fönstret **Användare** väljer du **Användare**.

    ![Länken Användare](./media/absorblms-tutorial/users.png)

3. Välj fliken **Användare**.

    ![Listrutan Lägg till ny](./media/absorblms-tutorial/add.png)

4. På sidan **Lägg till användare** gör du följande:

    ![Sidan Lägg till användare](./media/absorblms-tutorial/user.png)

    a. I rutan **Förnamn** skriver du in förnamn, till exempel **Britta**.

    b. I rutan **Efternamn** skriver du in efternamn, till exempel **Simon**.

    c. I rutan **Användarnamn** skriver du in hela namnet, till exempel **Britta Simon**.

    d. I rutan **Lösenord** skriver du in lösenordet.

    e. I rutan **Bekräfta lösenord** skriver du in lösenordet igen.

    f. Ange växlingsknappen **Är aktiv** till **Aktiv**.

5. Välj **Spara.**

    ![Växla Endast tillåta SSO-inloggning](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > Som standard är användarförsörjning inte aktiverat i enkel inloggning. Om kunden vill aktivera den här funktionen, behöver hen konfigurera den enligt [denna](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) dokumentation. Tänk på att användarförsörjning bara är tillgänglig på **Absorb 5 – New Learner Experience** med ACS URL-`https://company.myabsorb.com/api/rest/v2/authentication/saml`

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal så bör du loggas in automatiskt på de absorberade LMS för vilka du ställer in SSO.

* Du kan använda Microsoft Mina appar. När du klickar på den absorberade LMS-panelen i Mina appar, bör du loggas in automatiskt på de absorberande LMS som du konfigurerade SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat absorberad LMS kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
