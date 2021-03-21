---
title: 'Självstudie: Azure Active Directory integrering med MobileIron | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och MobileIron.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2021
ms.author: jeedes
ms.openlocfilehash: c47092b1488a79805db69308bcb9a8efde1c0d58
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653081"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>Självstudie: Azure Active Directory integrering med MobileIron

 I den här självstudien får du lära dig hur du integrerar MobileIron med Azure Active Directory (Azure AD). När du integrerar MobileIron med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till MobileIron.
* Gör det möjligt för användarna att logga in automatiskt på MobileIron med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* MobileIron för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* MobileIron stöder **SP-och IDP** -initierad SSO.

## <a name="add-mobileiron-from-the-gallery"></a>Lägg till MobileIron från galleriet

För att konfigurera integrering av MobileIron i Azure AD behöver du lägga till MobileIron från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I den vänstra rutan väljer du **Azure Active Directory**.
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **MobileIron** i sökrutan.
1. Välj **MobileIron** från resultaten och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-mobileiron"></a>Konfigurera och testa Azure AD SSO för MobileIron

Konfigurera och testa Azure AD SSO med MobileIron, genom att använda en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länkad relation mellan en Azure AD-användare och den relaterade användaren i MobileIron.

Utför följande steg för att konfigurera och testa Azure AD SSO med MobileIron:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
     1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
2. **[Konfigurera MOBILEIRON SSO](#configure-mobileiron-sso)** – om du vill konfigurera inställningar för enskilda Sign-On på program sidan.
    1. **[Skapa MobileIron-testanvändare](#create-mobileiron-test-user)** – för att ha en motsvarighet för Britta Simon i MobileIron som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

I det här avsnittet aktiverar du Azure AD SSO i Azure Portal.
 
1. I Azure Portal går du till sidan för program integrering i **MobileIron** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj en enda Sign-On metod** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** väljer du Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du vill konfigurera programmet i **IDP**-initierat läge:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://www.MobileIron.com/<key>`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<host>.MobileIron.com/saml/SSO/alias/<key>`

    c. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

     I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<host>.MobileIron.com/user/login.html`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Du får du värdena för nyckeln och värden från den administrativa MobileIron-portalen, som beskrivs senare i självstudien.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

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

I det här avsnittet aktiverar du B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till MobileIron.

1. I Azure Portal väljer du **företags program**  >  **alla program**.
1. I listan program väljer du **MobileIron**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare**. I dialog rutan **Lägg till tilldelning** väljer du sedan **användare och grupper**.
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan över användare. Välj sedan **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-mobileiron-sso"></a>Konfigurera MobileIron SSO

1. Logga in som administratör på företagswebbplatsen för MobileIron i ett annat webbläsarfönster.

2. Gå till **Administratörs**  >  **identitet** och välj **AAD** -alternativ i fältet **information om Cloud IDP-inställningar** .

    ![Skärm bild som visar fliken admin på MobileIron-platsen med den valda identiteten.](./media/MobileIron-tutorial/tutorial_MobileIron_admin.png)

3. Kopiera värdena för **nyckel** och **värd** och klistra in dem så att webbadresserna i avsnittet **Grundläggande SAML-konfiguration** på Azure-portalen blir kompletta.

    ![Skärm bild som visar alternativet Konfigurera SAML med en nyckel och ett värd värde.](./media/MobileIron-tutorial/key.png)

4. I fältet **Exportera metadatafil från AAD och importera till MobileIron-moln** klickar du på **Välj fil** för att ladda upp hämtade metadata från Azure-portalen. Klicka på **Klar** när överföringen har slutförts.

    ![Konfigurera metadataknapp för enkel administratörsinloggning](./media/MobileIron-tutorial/tutorial_MobileIron_adminmetadata.png)


### <a name="create-mobileiron-test-user"></a>Skapa MobileIron-testanvändare

Om du vill ge Azure AD-användare möjlighet att logga in på MobileIron måste de först etableras i MobileIron.  
När det gäller MobileIron är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in som administratör på företagswebbplatsen för MobileIron.

1. Gå till **Användare** och klicka på **Lägg till** > **enskild användare**.

    ![Konfigurera knapp för enkel användarinloggning](./media/MobileIron-tutorial/tutorial_MobileIron_user.png)

1. Utför följande steg på dialogsidan **”Enkel användare”**:

    ![Konfigurera knapp för tillägg av användare för enkel inloggning](./media/MobileIron-tutorial/tutorial_MobileIron_useradd.png)

    a. I text rutan e-postadress anger du e- **postadressen** till användaren brittasimon@contoso.com .

    b. I text rutan **förnamn** anger du det första namnet på användaren, t. ex. Britta.

    c. I text rutan **efter namn** anger du det senaste namnet på användaren som Simon.

    d. Klicka på **Klar**.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till MobileIron-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till MobileIron-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den MobileIron som du ställer in SSO för.

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen MobileIron i Mina appar, om det kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det har kon figurer ATS i IDP-läge, bör du loggas in automatiskt på MobileIron som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat MobileIron kan du framtvinga sessionsnycklar som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
