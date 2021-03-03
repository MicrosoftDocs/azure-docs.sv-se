---
title: 'Självstudie: Azure Active Directory integrering med SAML SSO för Bamboo med hjälp av resolution GmbH | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAML SSO för Bamboo med resolution GmbH.
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
ms.openlocfilehash: 8ec834d76692f78d1d7bc60ddbd4c73fe4adaede
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646129"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Självstudie: Azure Active Directory integrering med SAML SSO för Bamboo med hjälp av resolution GmbH

I den här självstudien får du lära dig hur du integrerar SAML SSO för Bamboo med hjälp av resolution GmbH med Azure Active Directory (Azure AD). När du integrerar SAML SSO för Bamboo med hjälp av resolution GmbH med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SAML SSO för Bamboo av resolution GmbH.
* Gör det möjligt för användarna att logga in automatiskt till SAML SSO för Bamboo med hjälp av resolution GmbH med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SAML SSO för Bamboo med hjälp av resolution GmbH behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* SAML SSO för Bamboo med enkel inloggning aktive rad prenumeration i resolution GmbH

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SAML SSO för Bamboo av resolution GmbH stöder **SP-och IDP** -INITIERAd SSO
* SAML SSO för Bamboo efter resolution GmbH stöder **just-in-Time** User-etablering

## <a name="add-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>Lägg till SAML SSO för Bamboo med hjälp av resolution GmbH från galleriet

Om du vill konfigurera integreringen av SAML SSO för Bamboo med lösnings-GmbH i Azure AD, måste du lägga till SAML SSO för Bamboo via resolution GmbH från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **SAML SSO for Bamboo efter resolution GmbH** i sökrutan.
1. Välj **SAML SSO för Bamboo efter resolution GmbH** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Konfigurera och testa Azure AD SSO med SAML SSO för Bamboo med hjälp av resolution GmbH

Konfigurera och testa Azure AD SSO med SAML SSO för Bamboo med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länkad relation mellan en Azure AD-användare och den relaterade användaren i SAML SSO för Bamboo med hjälp av resolution GmbH.

Utför följande steg för att konfigurera och testa Azure AD SSO med SAML SSO för Bamboo med hjälp av resolution GmbH:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
     1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
     1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
2. **[Konfigurera SAML SSO för Bamboo med hjälp av resolution GmbH SSO](#configure-saml-sso-for-bamboo-by-resolution-gmbh-sso)** – för att konfigurera enskilda Sign-On inställningar på program sidan.
    1. **[Skapa SAML SSO för Bamboo efter resolution GmbH test User](#create-saml-sso-for-bamboo-by-resolution-gmbh-test-user)** -om du vill ha en motsvarighet till Britta Simon i SAML SSO för Bamboo genom resolution GmbHby resolution GmbH som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

I det här avsnittet aktiverar du Azure AD SSO i Azure Portal.
 
1. I Azure Portal **går du till** sidan för **SAML-SSO för Bamboo efter lösning GmbH** program integration och väljer **enkel inloggning**.
1. På sidan **Välj en enda Sign-On metod** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** väljer du Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)
4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

     I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [SAML SSO för Bamboo efter resolution GmbH-klientens support team](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. I avsnittet **Konfigurera SAML SSO för Bamboo efter resolution GmbH** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryss rutan **Visa lösen ord** och skriv sedan lösen ordet.
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SAML SSO för Bamboo med hjälp av resolution GmbH.

1. I Azure Portal väljer du **företags program**  >  **alla program**.
1. I listan program väljer du **SAML SSO för Bamboo efter resolution GmbH**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare**. I dialog rutan **Lägg till tilldelning** väljer du sedan **användare och grupper**.
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan över användare. Välj sedan **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="configure-saml-sso-for-bamboo-by-resolution-gmbh-sso"></a>Konfigurera SAML SSO för Bamboo med hjälp av resolution GmbH SSO

1. Logga in på din SAML SSO för Bamboo av resolution GmbH företags webbplats som administratör.

1. Klicka på **inställnings** tillägg på höger sida av huvud verktygsfältet  >  .

    ![Inställningarna](./media/bamboo-tutorial/tutorial_bamboo_setings.png)

1. Gå till avsnittet säkerhet, klicka på **SAML-SingleSignOn** på Meny raden.

    ![Samlsingle](./media/bamboo-tutorial/tutorial_bamboo_samlsingle.png)

1. På **konfigurationssidan för SAML SIngleSignOn-plugin-programmet** klickar du på **Lägg till IdP**.

    ![Lägg till IdP](./media/bamboo-tutorial/tutorial_bamboo_addidp.png)

1. På sidan **Välj din SAML-identitetsprovider** utför du följande steg:

    ![Identitetsprovidern](./media/bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. Välj **IdP-typ** as **AZURE AD**.

    b. I textrutan **Namn** skriver du namnet.

    c. I textrutan **Beskrivning** skriver du beskrivningen.

    d. Klicka på **Nästa**.

1. Klicka på **Nästa** på sidan **konfiguration av identitetsprovider** .

    ![Identitetskonfigurationen](./media/bamboo-tutorial/tutorial_bamboo_identityconfig.png)

1. På sidan **Importera SAML IdP-metadata** klickar du på **Läs in fil** för att ladda upp den **METADATA XML**-fil som du har laddat ned från Azure-portalen.

    ![IdP-metadata](./media/bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

1. Klicka på **Nästa**.

1. Klicka **Spara inställningar**.

### <a name="create-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>Skapa SAML SSO för Bamboo av test användaren för resolution GmbH

Syftet med det här avsnittet är att skapa en användare med namnet Britta Simon i SAML SSO för Bamboo med hjälp av resolution GmbH. SAML SSO för Bamboo efter resolution GmbH stöder just-in-Time-etablering och även användare kan skapas manuellt, kontakta [SAML SSO för Bamboo med support från resolution GmbH klient support team](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) enligt ditt krav.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till SAML SSO för Bamboo av resolution GmbH-inloggnings-URL: en där du kan starta inloggnings flödet.  

* Gå till SAML SSO för Bamboo med hjälp av resolution GmbH-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt till SAML SSO för Bamboo efter resolution GmbH som du ställer in SSO för.

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen SAML SSO för Bamboo efter resolution GmbH i Mina appar, om det kon figurer ATS i SP-läge, kommer du att omdirigeras till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt till SAML SSO för Bamboo enligt resolution GmbH som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat SAML SSO för Bamboo med lösning GmbH kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).