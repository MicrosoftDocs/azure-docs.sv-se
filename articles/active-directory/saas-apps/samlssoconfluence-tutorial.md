---
title: 'Självstudie: Azure Active Directory integrering med SAML SSO för Confluence med hjälp av resolution GmbH | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAML SSO for Confluence by resolution GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: 9290272920bbb20144f4e0d957ba2d9ce60d06a9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101651288"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Självstudie: Azure Active Directory integrering med SAML SSO för Confluence med hjälp av resolution GmbH

I den här självstudien får du lära dig hur du integrerar SAML SSO för Confluence med hjälp av resolution GmbH med Azure Active Directory (Azure AD). När du integrerar SAML SSO för Confluence med hjälp av resolution GmbH med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SAML SSO för Confluence av resolution GmbH.
* Gör det möjligt för användarna att logga in automatiskt till SAML SSO för Confluence med hjälp av resolution GmbH med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* SAML SSO för Confluence med enkel inloggning (SSO) med enkel inloggning (SSO) för enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* SAML SSO for Confluence by resolution GmbH stöder **SP- och IDP**-initierad enkel inloggning

## <a name="add-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>Lägg till SAML SSO för Confluence med hjälp av resolution GmbH från galleriet

För att konfigurera integreringen av SAML SSO for Confluence by resolution GmbH med Azure AD måste du lägga till SAML SSO for Confluence by resolution GmbH från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **SAML SSO for Confluence efter resolution GmbH** i sökrutan.
1. Välj **SAML SSO för Confluence efter resolution GmbH** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-confluence-by-resolution-gmbh"></a>Konfigurera och testa Azure AD SSO för SAML SSO för Confluence med hjälp av resolution GmbH

Konfigurera och testa Azure AD SSO med SAML SSO för Confluence efter resolution GmbH med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SAML SSO för Confluence med hjälp av resolution GmbH.

Utför följande steg för att konfigurera och testa Azure AD SSO med SAML SSO för Confluence med hjälp av resolution GmbH:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
2. **[Konfigurera SAML SSO för Confluence med hjälp av resolution GmbH SSO](#configure-saml-sso-for-confluence-by-resolution-gmbh-sso)** – för att konfigurera enskilda Sign-On inställningar på program sidan.
    1. **[Skapa SAML SSO for Confluence by resolution GmbH-testanvändare](#create-saml-sso-for-confluence-by-resolution-gmbh-test-user)** – för att skapa en motsvarighet till Britta Simon i SAML SSO for Confluence by resolution GmbH som är länkad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal **går du till** sidan för **SAML-SSO för Confluence efter lösning GmbH** program integration och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [kundsupporten för SAML SSO for Confluence by resolution GmbH](https://www.resolution.de/go/support) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)


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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SAML SSO för Confluence med hjälp av resolution GmbH.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **SAML SSO för Confluence efter resolution GmbH**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .


## <a name="configure-saml-sso-for-confluence-by-resolution-gmbh-sso"></a>Konfigurera SAML SSO för Confluence med hjälp av resolution GmbH SSO

1. Öppna ett nytt webbläsarfönster och logga in på **administrationsportalen för SAML SSO for Confluence by resolution GmbH** som administratör.

2. Hovra över kugghjulet och klicka på **Tillägg**.
    
    ![Skärm bild som visar ikonen "kugg hjuls" vald och "tillägg" valda i list rutan.](./media/saml-sso-confluence-tutorial/add-on-1.png)

3. Du omdirigeras till sidan för administratörsåtkomst. Ange lösenordet och klicka på **Bekräfta**.

    ![Skärm bild som visar sidan "administratörs åtkomst" med knappen "bekräfta" vald.](./media/saml-sso-confluence-tutorial/add-on-2.png)

4. Klicka på **Hitta nya tillägg** under fliken **ATLASSIAN MARKETPLACE**. 

    ![Skärm bild som visar fliken "Attlassian Marketplace" med alternativet "hitta nya tillägg" markerat.](./media/saml-sso-confluence-tutorial/add-on.png)

5. Sök efter **SAML Single Sign On (SSO) for Confluence** och klicka på **Installera** för att installera det nya SAML-plugin-programmet.

    ![Skärm bild som visar sidan "hitta nya tilläggsprogram" med "S A M L enkel inloggning (S S) för Confluence" i sökrutan och knappen "installera" markerad.](./media/saml-sso-confluence-tutorial/add-on-7.png)

6. Installationen av plugin-programmet startar. Klicka på **Stäng**.

    ![Skärm bild som visar dialog rutan "installera".](./media/saml-sso-confluence-tutorial/add-on-8.png)

    ![Skärm bild som visar "installerad och redo att gå!" dialog rutan med åtgärden "Stäng" vald.](./media/saml-sso-confluence-tutorial/add-on-9.png)

7.  Klicka på **Hantera**.

    ![Skärm bild som visar appen "S A M L enkel inloggning (S-S) för Confluence" med knappen "hantera" markerad.](./media/saml-sso-confluence-tutorial/add-on-10.png)
    
8. Klicka på **Konfigurera** för att konfigurera det nya plugin-programmet.

    ![Skärm bild som visar sidan "hantera" med knappen "Konfigurera" vald.](./media/saml-sso-confluence-tutorial/add-on-11.png)

9. Det nya plugin-programmet visas även under fliken **Användare och säkerhet**.

    ![Skärm bild som visar fliken "användare & säkerhet" med "S A M L SingleSignOn" vald.](./media/saml-sso-confluence-tutorial/add-on-3.png)
    
10. Klicka på **Lägg till ny IdP** på **konfigurationssidan för plugin-programmet SAML SingleSignOn** för att konfigurera inställningarna för identitetsprovidern.

    ![Skärm bild som visar sidan "S A M L SingleSignOn-plugin-program" med knappen "Lägg till nytt I d P" vald.](./media/saml-sso-confluence-tutorial/add-on-4.png)

11. Utför följande steg på sidan **Välj din SAML-identitetsprovider**:

    ![Skärm bild som visar sidan "Välj din S A M L identitetsprovider" med text rutorna "I d P-typ", "namn" och "Beskrivning" markerade.](./media/saml-sso-confluence-tutorial/add-on-5-a.png)
 
    a. Ange **Azure AD** som IdP-typ.
    
    b. Lägg till ett **namn** för identitetsprovidern (t.ex. Azure AD).
    
    c. Lägg till en **beskrivning** av identitetsprovidern (t.ex. Azure AD).
    
    d. Klicka på **Nästa**.
    
12. Klicka på knappen **Nästa** på **konfigurationssidan för identitetsprovidern**.

    ![Skärm bild som visar sidan "konfiguration av identitetsprovider" med knappen "Nästa" vald.](./media/saml-sso-confluence-tutorial/add-on-5-b.png)

13. Utför följande steg på sidan **Importera SAML IdP-metadata**:

    ![Skärm bild som visar sidan "Importera S A M L I d P metadata" med knapparna "Importera", "Läs in fil" och "Nästa" markerade.](./media/saml-sso-confluence-tutorial/add-on-5-c.png)

    a. Klicka på **Läs in fil** och välj XML-filen med metadata som du hämtade i steg 5.

    b. Klicka på **Importera**.
    
    c. Vänta en kort stund tills importen slutförts.
    
    d. Klicka på **Nästa**.
    
14. Klicka på **Nästa** på sidan för **attribut för användar-ID och transformering**.

    ![Skärm bild som visar sidan "User ID-attribut och transformering" med knappen "Nästa" vald.](./media/saml-sso-confluence-tutorial/add-on-5-d.png)
    
15. Spara inställningarna genom att klicka på **Spara och Nästa** på sidan för **användargenerering och uppdatering**.   
    
    ![Skärm bild som visar sidan "skapa och uppdatera användare" med knappen "Spara & nästa" markerat.](./media/saml-sso-confluence-tutorial/add-on-6-a.png)
    
16. Hoppa över användartestningen så länge genom att klicka på **Hoppa över test och konfigurera manuellt** på sidan **Testa dina inställningar**. Du ska köra testningen i nästa avsnitt, vilket kräver ytterligare några inställningar på Azure-portalen. 
    
    ![Skärm bild som visar sidan "testa dina inställningar" med knappen "hoppa över test & konfigurera manuellt" markerat.](./media/saml-sso-confluence-tutorial/add-on-6-b.png)
    
17. I dialog rutan visas, **hoppar du över testet...**, klickar du på **OK**.
    
    ![Konfigurera enkel inloggning](./media/saml-sso-confluence-tutorial/add-on-6-c.png)


### <a name="create-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>Skapa SAML SSO for Confluence by resolution GmbH-testanvändare

För att Azure AD-användare ska kunna logga in i SAML SSO for Confluence by resolution GmbH måste de vara etablerade i SAML SSO for Confluence by resolution GmbH.  
I SAML SSO for Confluence by resolution GmbH är etableringen en manuell åtgärd.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din SAML SSO for Confluence by resolution GmbH-företagswebbplats som administratör.

2. Hovra över kugghjulet och klicka på **Användarhantering**.

    ![Skärm bild som visar ikonen "kugg hjuls" vald och "användar hantering" vald på menyn.](./media/saml-sso-confluence-tutorial/user-1.png) 

3. Under avsnittet användare klickar du på fliken **Lägg till användare** . Utför följande steg på dialog sidan **Lägg till en användare** :

    ![Lägga till medarbetare](./media/saml-sso-confluence-tutorial/user-2.png) 

    a. I textrutan **Användarnamn** skriver du e-postadressen för användaren: Britta Simon.

    b. I textrutan **Fullständigt namn** skriver du det fullständiga namnet för användaren: Britta Simon.

    c. I textrutan **E-post** skriver du e-postadressen för användaren: Brittasimon@contoso.com.

    d. I textrutan **Lösenord** skriver du lösenordet för Britta Simon.

    e. Klicka på **Bekräfta lösenord** för att ange lösenordet igen.
    
    f. Klicka på knappen **Lägg till**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till SAML SSO för Confluence av resolution GmbH-inloggnings-URL: en där du kan starta inloggnings flödet.  

* Gå till SAML SSO för Confluence med hjälp av resolution GmbH-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt till SAML SSO för Confluence efter resolution GmbH som du ställer in SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen SAML SSO för Confluence efter resolution GmbH i Mina appar, om det kon figurer ATS i SP-läge, kommer du att omdirigeras till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt till SAML SSO för Confluence enligt resolution GmbH som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat SAML SSO för Confluence med lösning GmbH kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).