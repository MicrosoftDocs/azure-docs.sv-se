---
title: 'Självstudie: Azure Active Directory integrering med SAML SSO för BitBucket med hjälp av resolution GmbH | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAML SSO for Bitbucket by resolution GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: jeedes
ms.openlocfilehash: aa5e3e88ceb957728799f27482de7477ba6b7b48
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621254"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Självstudie: Azure Active Directory integrering med SAML SSO för BitBucket med hjälp av resolution GmbH

I den här självstudien får du lära dig hur du integrerar SAML SSO för BitBucket med hjälp av resolution GmbH med Azure Active Directory (Azure AD). När du integrerar SAML SSO för BitBucket med hjälp av resolution GmbH med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst toSAML SSO för BitBucket med resolution GmbH.
* Gör det möjligt för användarna att logga in automatiskt i toSAML SSO för BitBucket med hjälp av resolution GmbH med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.


## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med SAML SSO for Bitbucket by resolution GmbH behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* SAML SSO for Bitbucket by resolution GmbH-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SAML SSO for Bitbucket by resolution GmbH stöder **SP- och IDP**-initierad enkel inloggning
* SAML SSO for Bitbucket by resolution GmbH stöder **just in time**-initierad enkel inloggning


## <a name="add-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>Lägg till SAML SSO för BitBucket med hjälp av resolution GmbH från galleriet

För att konfigurera integreringen av SAML SSO for Bitbucket by resolution GmbH med Azure AD måste du lägga till SAML SSO for Bitbucket by resolution GmbH från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I den vänstra rutan väljer du **Azure Active Directory**.
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **SAML SSO for BitBucket efter resolution GmbH** i sökrutan.
1. Välj **SAML SSO för BitBucket efter resolution GmbH** från resultaten och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Konfigurera och testa Azure AD SSO för SAML SSO för BitBucket med hjälp av resolution GmbH

Konfigurera och testa Azure AD SSO med SAML SSO för BitBucket med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länkad relation mellan en Azure AD-användare och den relaterade användaren i SAML SSO för BitBucket med hjälp av resolution GmbH.

Utför följande steg för att konfigurera och testa Azure AD SSO med SAML SSO för BitBucket med hjälp av resolution GmbH:


1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
2. **[Konfigurera SAML SSO för BitBucket med hjälp av resolution GmbH SSO](#configure-saml-sso-for-bitbucket-by-resolution-gmbh-sso)** – för att konfigurera enskilda Sign-On inställningar på program sidan.
    1. **[Skapa SAML SSO for Bitbucket by resolution GmbH-testanvändare](#create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)** – för att ha en motsvarighet för Britta Simon i SAML SSO for Bitbucket by resolution GmbH som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

I det här avsnittet aktiverar du Azure AD SSO i Azure Portal.
 
1. I Azure Portal **går du till** sidan för **SAML-SSO för BitBucket efter lösning GmbH** program integration och väljer **enkel inloggning**.
1. På sidan **Välj en enda Sign-On metod** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** väljer du Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du vill konfigurera programmet i **IDP**-initierat läge:


    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:
    
    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [kundsupporten för SAML SSO for Bitbucket by resolution GmbH](https://marketplace.atlassian.com/apps/1217045/saml-single-sign-on-sso-bitbucket?hosting=server&tab=support) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Ett exempel är `B.Simon@contoso.com`.
   1. Markera kryss rutan **Visa lösen ord** och skriv sedan lösen ordet.
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SAML SSO för BitBucket med hjälp av resolution GmbH.

1. I Azure Portal väljer du **företags program**  >  **alla program**.
1. I listan program väljer du **SAML SSO för BitBucket efter resolution GmbH**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare**. I dialog rutan **Lägg till tilldelning** väljer du sedan **användare och grupper**.
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan över användare. Välj sedan **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-saml-sso-for-bitbucket-by-resolution-gmbh-sso"></a>Konfigurera SAML SSO för BitBucket med hjälp av resolution GmbH SSO

1. Logga in på din SAML SSO for Bitbucket by resolution GmbH-företagswebbplats som administratör.

2. Till höger i huvudverktygsfältet klickar du på **Inställningar**.

3. Gå till avsnittet KONTON och klicka på **SAML SingleSignOn** på menyraden.

    ![Samlsingle](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

4. På **konfigurationssidan för SAML SIngleSignOn-plugin-programmet** klickar du på **Lägg till IdP**. 

    ![Lägg till IdP](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

5. På sidan **Välj din SAML-identitetsprovider** utför du följande steg:

    ![Identitetsprovidern](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. Välj **IdP-typ** as **AZURE AD**.

    b. I textrutan **Namn** skriver du namnet.

    c. I textrutan **Beskrivning** skriver du beskrivningen.

    d. Klicka på **Nästa**.

6. På **konfigurationssidan för identitetsprovider** klickar du på **Nästa**.

    ![Identitetskonfigurationen](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

7.  På sidan **Importera SAML IdP-metadata** klickar du på **Läs in fil** för att ladda upp den **METADATA XML**-fil som du har laddat ned från Azure-portalen.

    ![IdP-metadata](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)

8. Klicka på **Nästa**.

9. Klicka **Spara inställningar**.

    ![Spara](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)


## <a name="create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>Skapa SAML SSO for Bitbucket by resolution GmbH-testanvändare

Målet med det här avsnittet är att skapa en användare som heter Britta Simon i SAML SSO for Bitbucket by resolution GmbH. SAML SSO for Bitbucket by resolution GmbH stöder just-in-time-etablering och manuellt skapande av användare. Kontakta [kundsupporten för SAML SSO for Bitbucket by resolution GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support) enligt dina behov.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till SAML SSO för BitBucket av resolution GmbH-inloggnings-URL: en där du kan starta inloggnings flödet.  

* Gå till SAML SSO för BitBucket med hjälp av resolution GmbH-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt till SAML SSO för BitBucket efter resolution GmbH som du ställer in SSO för.

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen SAML SSO för BitBucket efter resolution GmbH i Mina appar, om det kon figurer ATS i SP-läge, kommer du att omdirigeras till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt till SAML SSO för BitBucket enligt resolution GmbH som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat SAML SSO för BitBucket med lösning GmbH kan du genomdriva sessionsnycklar, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).