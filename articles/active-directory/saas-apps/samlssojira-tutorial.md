---
title: 'Självstudie: Azure Active Directory integrering med SAML SSO för JIRA med hjälp av resolution GmbH | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAML SSO for Jira by resolution GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/23/2021
ms.author: jeedes
ms.openlocfilehash: 450db7c01de4ea7db0133ceca962941ae1a927e5
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102173276"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Självstudie: Azure Active Directory integrering med SAML SSO för JIRA med hjälp av resolution GmbH

I den här självstudien får du lära dig hur du integrerar SAML SSO för JIRA med hjälp av resolution GmbH med Azure Active Directory (Azure AD). När du integrerar SAML SSO för JIRA med hjälp av resolution GmbH med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SAML SSO för JIRA av resolution GmbH.
* Gör det möjligt för användarna att logga in automatiskt till SAML SSO för JIRA med hjälp av resolution GmbH med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* SAML SSO för JIRA med enkel inloggning (SSO) med enkel inloggning (SSO) för enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SAML SSO för JIRA av resolution GmbH stöder **SP** -och **IDP** -initierad SSO.

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="add-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>Lägg till SAML SSO för JIRA med hjälp av resolution GmbH från galleriet

För att konfigurera integreringen av SAML SSO for Jira by resolution GmbH med Azure AD måste du lägga till SAML SSO for Jira by resolution GmbH från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **SAML SSO for JIRA efter resolution GmbH** i sökrutan.
1. Välj **SAML SSO för JIRA efter resolution GmbH** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-jira-by-resolution-gmbh"></a>Konfigurera och testa Azure AD SSO för SAML SSO för JIRA med hjälp av resolution GmbH

Konfigurera och testa Azure AD SSO med SAML SSO för JIRA efter resolution GmbH med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SAML SSO för JIRA med hjälp av resolution GmbH.

Utför följande steg för att konfigurera och testa Azure AD SSO med SAML SSO för JIRA med hjälp av resolution GmbH:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera SAML SSO för JIRA med hjälp av resolution GmbH SSO](#configure-saml-sso-for-jira-by-resolution-gmbh-sso)** – för att konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa SAML SSO för JIRA efter resolution GmbH test User](#create-saml-sso-for-jira-by-resolution-gmbh-test-user)** -om du vill ha en motsvarighet till B. Simon i SAML SSO för JIRA med resolution GmbH som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal **går du till** sidan för **SAML-SSO för JIRA efter lösning GmbH** program integration och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Klicka på **Ange ytterligare URL: er** och utför följande steg om du vill konfigurera programmet i läget **SP** -initierat:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > För ID: t, svars-URL och inloggnings-URL ersätter du **\<server-base-url>** med bas-URL: en för din JIRA-instans. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen. Om du har problem kan du kontakta oss på [SAML SSO för JIRA av support teamet för resolution GmbH-klienten](https://www.resolution.de/go/support).

4. På sidan **Konfigurera en enskild Sign-On med SAML** , i avsnittet **SAML-signeringscertifikat** , laddar du ned **XML-metadata för federationen** och sparar dem på din dator.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SAML SSO för JIRA med hjälp av resolution GmbH.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **SAML SSO för JIRA efter resolution GmbH**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-saml-sso-for-jira-by-resolution-gmbh-sso"></a>Konfigurera SAML SSO för JIRA med hjälp av resolution GmbH SSO 

1. Logga in på din JIRA-instans som administratör i ett annat webbläsarfönster.

2. Hovra över kugg hjuls på höger sida och klicka på **Hantera appar**.
    
    ![Skärm bild som visar en pil som pekar på ikonen "kugg hjuls" och "hantera appar" valt i list rutan.](./media/samlssojira-tutorial/add-on-1.png)

3. Om du omdirigeras till sidan administratörs åtkomst anger du **lösen ordet** och klickar på knappen **Bekräfta** .

    ![Skärm bild som visar sidan "administratörs åtkomst".](./media/samlssojira-tutorial/add-on-2.png)

4. JIRA omdirigerar normalt dig till Atlassian Marketplace. Annars klickar du på **hitta nya appar** i den vänstra panelen. Sök efter **SAML enkel inloggning (SSO) för JIRA** och klicka på knappen **Installera** för att installera SAML-plugin-programmet.

    ![Skärm bild som visar sidan "Atlassian Marketplace for JIRA" med en pil som pekar på knappen "installera" för JIRA "S A M L Single Sign on (S S O), S A M L/S S O.](./media/samlssojira-tutorial/store.png)

5. Installationen av plugin-programmet startar. När du är färdig klickar du på knappen **Stäng** .

    ![Skärm bild som visar dialog rutan "installera".](./media/samlssojira-tutorial/store-2.png)

    ![Skärm bild som visar "installerad och redo att gå!" dialog rutan med knappen Stäng markerad.](./media/samlssojira-tutorial/store-3.png)

6. Klicka sedan på **Hantera**.

    ![Skärm bild som visar "S A M L enkel inloggning (S S O) JIRA, S A M L/S S O"-appen med knappen "hantera" markerad.](./media/samlssojira-tutorial/store-4.png)
    
7. Klicka sedan på **Konfigurera** för att konfigurera det nyss installerade plugin-programmet.

    ![Skärm bild som visar sidan "hantera appar" med knappen "Konfigurera" vald för appen "S A M L SingleSignOn for JIRA".](./media/samlssojira-tutorial/store-5.png)

8. I **konfigurations guiden för SAML SingleSignOn-plugin-programmet** klickar du på **Lägg till ny IDP** för att konfigurera Azure AD som en ny identitetsprovider.

    ![Skärm bilden visar sidan "Välkommen" och knappen "Lägg till nytt I d P" markerad.](./media/samlssojira-tutorial/add-on-4.png) 

9. På sidan **Välj SAML-identitetsprovider** utför du följande steg:

    ![Skärm bild som visar sidan "Välj din S A M L identitetsprovider" med text rutorna "I d P Type" och "name" markerade och knappen "Nästa" är markerad.](./media/samlssojira-tutorial/identity-provider.png)
 
    a. Ange **Azure AD** som IdP-typ.
    
    b. Lägg till **namnet** på identitets leverantören (t. ex. Azure AD).
    
    c. Lägg till en (valfri) **Beskrivning** av identitets leverantören (t. ex. Azure AD).
    
    d. Klicka på **Nästa**.
    
10. Klicka på **Nästa** på sidan **konfiguration av identitetsprovider** .
 
    ![Skärm bild som visar sidan "identitets leverantörs konfiguration".](./media/samlssojira-tutorial/configuration.png)

11. Utför följande steg på sidan **Importera SAML IdP-metadata**:

    ![Skärm bild som visar sidan "Importera S A M L I d P metadata" med åtgärden "Välj metadata X M L File" markerat.](./media/samlssojira-tutorial/metadata.png)

    a. Klicka på **XML-filen för val av metadata** och välj **XML-** filen för federationsmetadata som du laddade ned tidigare.

    b. Klicka på knappen **Importera** .
     
    c. Vänta en stund tills importen lyckas.  
     
    d. Klicka på knappen **Nästa**.
    
12. Klicka på knappen **Nästa** på sidan **användar-ID-attribut och omvandling** .

    ![Skärm bild som visar sidan "användare I D-attribut och transformering" med knappen "Nästa" vald.](./media/samlssojira-tutorial/transformation.png)
    
13. På sidan **skapa och uppdatera användare** klickar du på **Spara & bredvid** Spara inställningarna.
    
    ![Skärm bild som visar sidan "skapa och uppdatera användare" med knappen "Spara & nästa" markerat.](./media/samlssojira-tutorial/update.png)
    
14. På sidan **Testa inställningarna** klickar du på **hoppa över test & konfigurerar manuellt** för att hoppa över användar testet för tillfället. Detta kommer att utföras i nästa avsnitt och kräver vissa inställningar i Azure Portal.
    
    ![Skärm bild som visar sidan "testa dina inställningar" med knappen "hoppa över test & konfigurera manuellt" markerat.](./media/samlssojira-tutorial/test.png)
    
15. Klicka på **OK** för att hoppa över varningen.
    
    ![Skärm bild som visar varnings dialog rutan med knappen "O K" vald.](./media/samlssojira-tutorial/warning.png)

### <a name="create-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>Skapa SAML SSO for Jira by resolution GmbH-testanvändare

För att Azure AD-användare ska kunna logga in på SAML SSO för JIRA med hjälp av resolution GmbH, måste de tillhandahållas till SAML SSO för JIRA med hjälp av resolution GmbH. För den här självstudien måste du göra etableringen manuellt. Det finns dock andra etablerings modeller som är tillgängliga för SAML SSO-plugin-programmet genom matchning, till exempel **just-in-Time** -etablering. Läs dokumentationen om [SAML SSO med resolution GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Om du har en fråga om det kan du kontakta supporten vid [lösnings supporten](https://www.resolution.de/go/support).

**Utför följande steg för att etablera ett användar konto manuellt:**

1. Logga in på JIRA-instansen som administratör.

2. Hovra över kugg hjuls och välj **användar hantering**.

   ![Skärm bild som visar en pil som pekar på ikonen "kugg hjuls" med "användar hantering" som valts i list rutan.](./media/samlssojira-tutorial/user-1.png)

3. Om du omdirigeras till sidan administratörs åtkomst anger du **lösen ordet** och klickar på knappen **Bekräfta** .

    ![Skärm bild som visar sidan "administratörs åtkomst" där text rutan "lösen ord" är markerad.](./media/samlssojira-tutorial/user-2.png) 

4. Klicka på **skapa användare** under fliken **användar hantering** .

    ![Skärm bild som visar fliken "användar hantering" med knappen "skapa användare" vald.](./media/samlssojira-tutorial/user-3-new.png) 

5. Utför följande steg på dialog sidan **Skapa ny användare** . Du måste skapa användaren precis som i Azure AD:

    ![Lägga till medarbetare](./media/samlssojira-tutorial/user-4-new.png) 

    a. I text rutan **e-postadress** anger du användarens e-postadress:  <b>BrittaSimon@contoso.com</b> .

    b. I text rutan **fullständigt namn** skriver du det fullständiga namnet på användaren: **Britta Simon**.

    c. Skriv e-postadressen för användaren i text rutan **användar namn** : <b>BrittaSimon@contoso.com</b> . 

    d. I text rutan **lösen ord** anger du användarens lösen ord.

    e. Klicka på **skapa användare** för att slutföra skapandet av användaren.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till SAML SSO för JIRA av resolution GmbH-inloggnings-URL: en där du kan starta inloggnings flödet.  

* Gå till SAML SSO för JIRA med hjälp av resolution GmbH-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt till SAML SSO för JIRA efter resolution GmbH som du ställer in SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen SAML SSO för JIRA efter resolution GmbH i Mina appar, om det kon figurer ATS i SP-läge, kommer du att omdirigeras till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt till SAML SSO för JIRA enligt resolution GmbH som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="enable-sso-redirection-for-jira"></a>Aktivera SSO-omdirigering för JIRA

Som det anges i avsnittet tidigare finns det för närvarande två sätt att utlösa enkel inloggning. Antingen genom att använda **Azure Portal** eller genom **att använda en särskild länk till din JIRA-instans**. Med SAML SSO-plugin med lösning GmbH kan du också utlösa enkel inloggning genom att bara **komma åt en URL som pekar på JIRA-instansen**.

I själva verket omdirigeras alla användare som kommer åt JIRA till enkel inloggning när du har aktiverat ett alternativ i plugin-programmet.

Om du vill aktivera SSO-omdirigering gör du följande i **din JIRA-instans**:

1. Öppna konfigurations sidan för SAML SSO-plugin-programmet i Jira.
1. Klicka på **omdirigering** i den vänstra panelen.

   ![Skärm bild som visar JIRA för SAML SingleSignOn-plugin-programmet som markerar länken för omdirigering i det vänstra navigerings fältet.](./media/samlssojira-tutorial/configure-1.png)

1. Ticket **Aktivera SSO-omdirigering**.

   ![Skärm bild av konfigurations sidan för JIRA SAML SingleSignOn som marker ATS med kryss rutan Aktivera SSO-omdirigering.](./media/samlssojira-tutorial/configure-2.png) 

1. Tryck på knappen **Spara inställningar** i det övre högra hörnet.

När du har aktiverat alternativet kan du fortfarande få frågan om användar namn/lösen ord om alternativet **Aktivera Nosso** är nedkryssat genom att gå till `https://<server-base-url>/login.jsp?nosso` . Ersätt alltid **\<server-base-url>** med bas-URL: en.

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat SAML SSO för JIRA med lösning GmbH kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).