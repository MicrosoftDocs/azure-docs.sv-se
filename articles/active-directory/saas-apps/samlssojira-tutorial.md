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
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: 44077c2685ce78b7ba1ea140a34dd75dcf5c7c46
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543473"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Självstudie: Azure Active Directory integrering med SAML SSO för JIRA med hjälp av resolution GmbH

I den här självstudien får du lära dig att konfigurera SAML SSO för JIRA med hjälp av resolution GmbH med Azure Active Directory (Azure AD).
Integreringen av SAML SSO for Jira by resolution GmbH med Azure AD medför följande fördelar:

* Du kan styra i Azure AD som kan logga in på JIRA med SAML SSO-plugin-programmet med lösning GmbH.
* Du kan göra det möjligt för användarna att logga in automatiskt till JIRA med sina Azure AD-konton med hjälp av SAML SSO för JIRA med hjälp av resolution GmbH (enkel inloggning).
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering och SAML SSO för JIRA med hjälp av resolution GmbH behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En SAML SSO for Jira by resolution GmbH-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SAML SSO för JIRA av resolution GmbH stöder **SP** -och **IDP** -initierad SSO

## <a name="adding-an-enterprise-application-for-single-sign-on"></a>Lägga till ett företags program för enkel inloggning

För att kunna konfigurera enkel inloggning i Azure AD måste du lägga till ett nytt företags program. I galleriet finns en förkonfigurerad program för inställning för detta, **SAML SSO för JIRA med resolution GmbH**.

**Utför följande steg för att lägga till SAML SSO for Jira by resolution GmbH från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Navigera till **företags program**och klicka sedan på **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program klickar du på knappen **nytt program** överst i dialog rutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **SAML SSO för JIRA efter resolution GmbH**, väljer **SAML SSO för JIRA efter resolution GmbH** från resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet. Du kan också ändra namnet på företags appen.

     ![SAML SSO for Jira by resolution GmbH i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-single-sign-on-with-the-saml-sso-plugin-and-azure-ad"></a>Konfigurera och testa enkel inloggning med SAML SSO-plugin-programmet och Azure AD

I det här avsnittet ska du testa och konfigurera enkel inloggning till JIRA för en Azure AD-användare. Detta görs för en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i SAML SSO for Jira by resolution GmbH upprättas.

Om du vill konfigurera och testa enkel inloggning måste du utföra följande steg:

1. **[Konfigurera Azure AD Enterprise-programmet för enkel inloggning](#configure-the-azure-ad-enterprise-application-for-single-sign-on)** – konfigurera Azure AD Enterprise-programmet för enkel inloggning
2. **[Konfigurera SAML SSO-plugin-programmet för JIRA-instansen](#configure-the-saml-sso-plugin-of-your-jira-instance)** – konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-test användare](#create-an-azure-ad-test-user)** – skapa en test användare i Azure AD.
1. **[Tilldela test användaren Azure AD-test](#assign-the-azure-ad-test-user)** – så att test användaren kan använda den enkla inloggningen på Azure-sidan.
1. **[Skapa test användaren i JIRA](#create-the-test-user-also-in-jira)** – skapa en motsvarande test användare i JIRA för Azure AD-testanvändaren.
1. **[Testa enkel inloggning](#test-single-sign-on)** – verifiera om konfigurationen fungerar.

### <a name="configure-the-azure-ad-enterprise-application-for-single-sign-on"></a>Konfigurera Azure AD Enterprise-programmet för enkel inloggning

I det här avsnittet ställer du in den enkla inloggningen i Azure Portal.

Utför följande steg för att konfigurera enkel inloggning med SAML SSO för JIRA med hjälp av resolution GmbH:

1. I [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning** på den vänstra panelen i den nyss skapade **SAML SSO för JIRA efter resolution GmbH** Enterprise Application.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. För **Välj metod för enkel inloggning**väljer du **SAML** -läge för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. Klicka sedan på **redigerings** ikonen för att öppna dialog rutan **grundläggande SAML-konfiguration** .

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Information om enkel inloggning med SAML SSO for Jira by resolution GmbH-domäner och -URL:er](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Klicka på **Ange ytterligare URL: er** och utför följande steg om du vill konfigurera programmet i läget **SP** -initierat:

    ![Information om enkel inloggning med SAML SSO for Jira by resolution GmbH-domäner och -URL:er](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > För ID: t, svars-URL och inloggnings-URL ersätter du **\<server-base-url>** med bas-URL: en för din JIRA-instans. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen. Om du har problem kan du kontakta oss på [SAML SSO för JIRA av support teamet för resolution GmbH-klienten](https://www.resolution.de/go/support).

4. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , laddar du ned **XML-metadata för federationen** och sparar dem på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

### <a name="configure-the-saml-sso-plugin-of-your-jira-instance"></a>Konfigurera SAML SSO-plugin-programmet för din JIRA-instans 

1. Logga in på din JIRA-instans som administratör i ett annat webbläsarfönster.

2. Hovra över kugg hjuls på höger sida och klicka på **Hantera appar**.
    
    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon1.png)

3. Om du omdirigeras till sidan administratörs åtkomst anger du **lösen ordet** och klickar på knappen **Bekräfta** .

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon2.png)

4. JIRA omdirigerar normalt dig till Atlassian Marketplace. Annars klickar du på **hitta nya appar** i den vänstra panelen. Sök efter **SAML enkel inloggning (SSO) för JIRA** och klicka på knappen **Installera** för att installera SAML-plugin-programmet.

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/store.png)

5. Installationen av plugin-programmet startar. När du är färdig klickar du på knappen **Stäng** .

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/store-2.png)

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/store-3.png)

6. Klicka sedan på **Hantera**.

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/store-4.png)
    
8. Klicka sedan på **Konfigurera** för att konfigurera det nyss installerade plugin-programmet.

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/store-5.png)

9. I **konfigurations guiden för SAML SingleSignOn-plugin-programmet** klickar du på **Lägg till ny IDP** för att konfigurera Azure AD som en ny identitetsprovider.

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon4.png) 

10. På sidan **Välj SAML-identitetsprovider** utför du följande steg:

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon5a.png)
 
    a. Ange **Azure AD** som IdP-typ.
    
    b. Lägg till **namnet** på identitets leverantören (t. ex. Azure AD).
    
    c. Lägg till en (valfri) **Beskrivning** av identitets leverantören (t. ex. Azure AD).
    
    d. Klicka på **Nästa**.
    
11. Klicka på **Nästa**på sidan **konfiguration av identitetsprovider** .
 
    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon5b.png)

12. Utför följande steg på sidan **Importera SAML IdP-metadata**:

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon5c.png)

    a. Klicka på **XML-filen för val av metadata** och välj **XML-** filen för federationsmetadata som du laddade ned tidigare.

    b. Klicka på knappen **Importera** .
     
    c. Vänta en stund tills importen lyckas.  
     
    d. Klicka på knappen **Nästa**.
    
13. Klicka på knappen **Nästa** på sidan **användar-ID-attribut och omvandling** .

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon5d.png)
    
14. På sidan **skapa och uppdatera användare** klickar du på **Spara & bredvid** Spara inställningarna.
    
    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon6a.png)
    
15. På sidan **Testa inställningarna** klickar du på **hoppa över test & konfigurerar manuellt** för att hoppa över användar testet för tillfället. Detta kommer att utföras i nästa avsnitt och kräver vissa inställningar i Azure Portal.
    
    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon6b.png)
    
16. Klicka på **OK** för att hoppa över varningen.
    
    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon. Med användaren kan du testa enkel inloggning.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. I **användar egenskaperna**utför du följande steg:

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **namn** anger du **Britta Simon**.
  
    b. I fältet **användar namn** anger du <b>BrittaSimon@contoso.com</b> .

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet lägger du till Britta Simon i företags programmet, vilket gör att henne kan använda enkel inloggning.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**. 

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Sök efter det företags program som du skapade i början av den här självstudien i listan program. Om du följer stegen i självstudien kallas den **SAML SSO för JIRA av resolution GmbH**. Om du har fått ett annat namn söker du efter namnet.

    ![SAML SSO for Jira by resolution GmbH-länken i listan över program](common/all-applications.png)

3. Klicka på **användare och grupper**i den vänstra panelen.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Välj **Lägg till användare**och välj sedan  **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialog rutan **användare och grupper** väljer du **Britta Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-the-test-user-also-in-jira"></a>Skapa test användaren även i JIRA

För att Azure AD-användare ska kunna logga in på SAML SSO för JIRA med hjälp av resolution GmbH, måste de tillhandahållas till SAML SSO för JIRA med hjälp av resolution GmbH. För den här självstudien måste du göra etableringen manuellt. Det finns dock andra etablerings modeller som är tillgängliga för SAML SSO-plugin-programmet genom matchning, till exempel **just-in-Time** -etablering. Läs dokumentationen om [SAML SSO med resolution GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Om du har en fråga om det kan du kontakta supporten vid [lösnings supporten](https://www.resolution.de/go/support).

**Utför följande steg för att etablera ett användar konto manuellt:**

1. Logga in på JIRA-instansen som administratör.

2. Hovra över kugg hjuls och välj **användar hantering**.

   ![Lägga till medarbetare](./media/samlssojira-tutorial/user1.png)

3. Om du omdirigeras till sidan administratörs åtkomst anger du **lösen ordet** och klickar på knappen **Bekräfta** .

    ![Lägga till medarbetare](./media/samlssojira-tutorial/user2.png) 

4. Klicka på **skapa användare**under fliken **användar hantering** .

    ![Lägga till medarbetare](./media/samlssojira-tutorial/user3-new.png) 

5. Utför följande steg på dialog sidan **Skapa ny användare** . Du måste skapa användaren precis som i Azure AD:

    ![Lägga till medarbetare](./media/samlssojira-tutorial/user4-new.png) 

    a. I text rutan **e-postadress** anger du användarens e-postadress:  <b>BrittaSimon@contoso.com</b> .

    b. I text rutan **fullständigt namn** skriver du det fullständiga namnet på användaren: **Britta Simon**.

    c. Skriv e-postadressen för användaren i text rutan **användar namn** : <b>BrittaSimon@contoso.com</b> . 

    d. I text rutan **lösen ord** anger du användarens lösen ord.

    e. Klicka på **skapa användare** för att slutföra skapandet av användaren.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för SAML SSO for Jira by resolution GmbH på åtkomstpanelen bör du loggas in automatiskt i SAML SSO for Jira by resolution GmbH-programmet som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Du kan också testa enkel inloggning om du navigerar till `https://<server-base-url>/plugins/servlet/samlsso` . Ersätt **\<server-base-url>** med bas-URL: en för din JIRA-instans.


## <a name="enable-single-sign-on-redirection-for-jira"></a>Aktivera omdirigering av enkel inloggning för JIRA

Som det anges i avsnittet tidigare finns det för närvarande två sätt att utlösa enkel inloggning. Antingen genom att använda **Azure Portal** eller genom **att använda en särskild länk till din JIRA-instans**. Med SAML SSO-plugin med lösning GmbH kan du också utlösa enkel inloggning genom att bara **komma åt en URL som pekar på JIRA-instansen**.

I själva verket omdirigeras alla användare som kommer åt JIRA till enkel inloggning när du har aktiverat ett alternativ i plugin-programmet.

Om du vill aktivera SSO-omdirigering gör du följande i **din JIRA-instans**:

1. Öppna konfigurations sidan för SAML SSO-plugin-programmet i Jira.
1. Klicka på **omdirigering** i den vänstra panelen.

   ![Skärm bild som visar JIRA för SAML SingleSignOn-plugin-programmet som markerar länken för omdirigering i det vänstra navigerings fältet.](./media/samlssojira-tutorial/ssore1.png)

1. Ticket **Aktivera SSO-omdirigering**.

   ![Skärm bild av konfigurations sidan för JIRA SAML SingleSignOn som marker ATS med kryss rutan Aktivera SSO-omdirigering.](./media/samlssojira-tutorial/ssore2.png) 

1. Tryck på knappen **Spara inställningar** i det övre högra hörnet.

När du har aktiverat alternativet kan du fortfarande få frågan om användar namn/lösen ord om alternativet **Aktivera Nosso** är nedkryssat genom att gå till `https://\<server-base-url>/login.jsp?nosso` . Ersätt alltid **\<server-base-url>** med bas-URL: en.


## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

