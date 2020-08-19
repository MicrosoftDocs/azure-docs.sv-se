---
title: 'Självstudie: Azure Active Directory integration med SAP Cloud Platform | Microsoft Docs'
description: Läs hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP Cloud Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.openlocfilehash: c2738e1a6168440adee79ebaa599a313600153a4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546771"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Självstudie: Azure Active Directory integration med SAP Cloud Platform

I den här självstudien får du lära dig hur du integrerar SAP Cloud Platform med Azure Active Directory (Azure AD).
Integrering av SAP Cloud Platform med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD vem som har åtkomst till SAP Cloud Platform.
* Du kan låta dina användare automatiskt loggas in på SAP Cloud Platform (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SAP Cloud Platform så behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* SAP Cloud Platform-prenumeration med enkel inloggning aktiverad

När du har slutfört den här självstudien så kommer de Azure AD-användare som du har tilldelat till SAP Cloud Platform att kunna använda enkel inloggning i programmet med [Introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

>[!IMPORTANT]
>Du måste distribuera ditt eget program eller prenumerera på ett program på ditt SAP Cloud Platform-konto för att testa enkel inloggning. I den här självstudien distribueras ett program i kontot.
> 

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SAP Cloud Platform stöder **SP**-initierad enkel inloggning

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Lägga till SAP Cloud Platform från galleriet

Om du vill konfigurera integreringen av SAP Cloud Platform i Azure AD så måste du lägga till SAP Cloud Platform från galleriet till din lista över hanterade SaaS-appar.

**Lägg till SAP Cloud Platform från galleriet genom utföra följande steg:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **SAP Cloud Platform**, väljer **SAP Cloud Platform** från resultatpanelen och klicka sedan på **Lägg till** för att lägga till programmet.

     ![SAP Cloud Platform i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet får du konfigurera och testa enkel inloggning i Azure AD med SAP Cloud Platform baserat på testanvändaren **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i SAP Cloud Platform upprättas.

Om du vill konfigurera och testa enkel inloggning i Azure AD med SAP Cloud Platform så måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för SAP Cloud Platform](#configure-sap-cloud-platform-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa en SAP Cloud Platform-testanvändare](#create-sap-cloud-platform-test-user)** – för att få en motpart Britta Simon i SAP Cloud Platform som är länkad till Azure AD-representationen för användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Konfigurera enkel inloggning i Azure AD med SAP Cloud Platform genom att utföra följande steg:

1. I [Azure-portalen](https://portal.azure.com/), på **SAP Cloud Platform**-programintegreringssidan, väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om SAP Cloud Platform och URL:er för enkel inloggning](common/sp-identifier-reply.png)

    a. I textrutan **inloggning på URL:en**, skriver du in den URL som används av dina användare för att logga in på ditt **SAP Cloud Platform**-program. Det är den kontospecifika URL:en för en skyddad resurs i ditt SAP Cloud Platform-program. URL:en är baserad på följande mönster: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
    >[!NOTE]
    >Det här är URL:en i ditt SAP Cloud Platform-program som kräver att användaren autentiseras.
    > 

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`

    b. I **identifierare**-textrutan så anger du din SAP Cloud Platforms typ, en URL med en av följande mönster: 

    - `https://hanatrial.ondemand.com/<instancename>`
    - `https://hana.ondemand.com/<instancename>`
    - `https://us1.hana.ondemand.com/<instancename>`
    - `https://ap1.hana.ondemand.com/<instancename>`

    c. I textrutan **Svars-URL** skriver du en URL med följande mönster: 

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL:en, identifierare och svars-URL. Kontakta [supportteamet för SAP Cloud Platform-klienten](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) för att få inloggnings-URL och identifierare. Svars-URL:en kan du hämta från förtroendehanteringsavsnittet som beskrivs senare i självstudien.
    > 
4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

### <a name="configure-sap-cloud-platform-single-sign-on"></a>Konfigurera enkel inloggning för SAP Cloud Platform

1. I ett annat webbläsarfönster, loggar du in på SAP Cloud Platform Cockpit på `https://account.<landscape host>.ondemand.com/cockpit`(till exempel: https://account.hanatrial.ondemand.com/cockpit).

2. Klicka på **Förtroende**-fliken.
   
    ![Förtroende](./media/sap-hana-cloud-platform-tutorial/ic790800.png "Förtroende")

3. I avsnittet förtroendehantering under **Lokal tjänstleverantör**, utför du följande steg:

    ![Förtroende hantering](./media/sap-hana-cloud-platform-tutorial/ic793931.png "Förtroende hantering")
   
    a. Klicka på **Redigera**.

    b. Som **Konfigurationstyp**, väljer du **Anpassad**.

    c. Som **Namn på lokal leverantör**, lämna standardvärdet. Kopiera det här värdet och klistra in det i fältet **Identifierare** i Azure AD-konfigurationen för SAP Cloud Platform.

    d. Generera en **Signeringsnyckel** och ett **Signeringscertifikat**-nyckelpar genom att klicka på **Generera nyckelpar**.

    e. Som **Huvudsaklig spridning** så väljer du **Inaktiverad**.

    f. Som **Tvinga autentisering** så väljer du **Inaktiverad**.

    ex. Klicka på **Spara**.

4. När du har sparat inställningarna för **Lokal tjänstleverantör** så utför du följande för att hämta svars-URL:en:
   
    ![Hämta metadata](./media/sap-hana-cloud-platform-tutorial/ic793930.png "Hämta metadata")

    a. Hämta SAP Cloud Platform-metadatafilen genom att klicka på **Hämta metadata**.

    b. Öppna den hämtade XML-filen för SAP Cloud Platform-metadata och hitta sedan taggen **ns3:AssertionConsumerService**.
 
    c. Kopiera värdet för attributet **Plats** och klistra in det i fältet **Svars-URL** i Azure AD-konfigurationen för SAP Cloud Platform.

5. Klicka på fliken **Betrodd identitetsprovider** och sedan på **Lägg till betrodd identitetsprovider**.
   
    ![Förtroende hantering](./media/sap-hana-cloud-platform-tutorial/ic790802.png "Förtroende hantering")
   
    >[!NOTE]
    >Om du vill hantera listan med betrodda identitetsprovidrar så måste du ha valt typ av anpassad konfiguration i avsnittet lokal tjänstleverantör. Som standardkonfigurationstyp har du ett icke-redigerbart och implicit förtroende till SAP-ID-tjänsten. För Ingen så har du inga förtroendeinställningar.
    > 
    > 

6. Klicka på **Allmänt**-fliken och sedan på **Bläddra** för att överföra den hämtade metadatafilen.
    
    ![Förtroende hantering](./media/sap-hana-cloud-platform-tutorial/ic793932.png "Förtroende hantering")
    
    >[!NOTE]
    >När du har överfört metadatafilen så fylls värdena för **URL för enkel inloggning**, **URL för enkel utloggning** och **Signeringscertifikat** i automatiskt.
    > 
     
7. Klicka på fliken **Attribut**.

8. På **Attribut**-fliken, utför du följande steg:
    
    ![Attribut](./media/sap-hana-cloud-platform-tutorial/ic790804.png "Attribut") 

    a. Klicka på **Lägg till försäkransbaserat attribut** och lägg sedan till följande försäkransbaserade attribut:
       
    | Försäkransattribut | Huvudnamnsattribut |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |förnamn |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |efternamn |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |e-post |
   
     >[!NOTE]
     >Konfigurationen av attributen beror på hur programmen på SCP utvecklas, det vill säga vilka attribut som de förväntar sig i SAML-svaret och under vilket namn (huvudnamnsattribut) de har åtkomst till det här attributet i koden.
     > 
    
    b. **Standardattributet** i skärmbilden är bara i illustrativt syfte. Det krävs inte för att få scenariot att fungera.  
 
    c. Namnen och värdena för **huvudnamnsattributet** som visas i skärmbilden beror på hur programmet utvecklas. Det är möjligt att ditt program kräver olika mappningar.

### <a name="assertion-based-groups"></a>Försäkransbaserade grupper

Som ett valfritt steg så kan du konfigurera försäkransbaserade grupper för din Azure Active Directory-identitetsprovider.

Med grupper på SAP Cloud Platform så kan du dynamiskt tilldela en eller flera användare till en eller flera roller i dina SAP Cloud Platform-program, baserat på värden för attribut i SAML 2.0-försäkran. 

Om försäkran till exempel innehåller attributet ”*contract=temporary*” så kan du vilja att alla berörda användare läggs till i gruppen ”*TEMPORARY*”. Gruppen ”*TEMPORARY*” kan innehålla en eller flera roller från en eller flera program som distribueras i ditt SAP Cloud Platform-konto.
 
Använd försäkransbaserade grupper när du vill tilldela många användare samtidigt till en eller flera roller av program i ditt SAP Cloud Platform-konto. Om du bara vill tilldela en enskild eller ett litet antal användare till specifika roller så rekommenderar vi att du tilldelar dem direkt i fliken **Auktoriseringar** i SAP Cloud Platform-cockpiten.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du **brittasimon \@ yourcompanydomain. extension**  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet låter du Britta Simon använda enkel inloggning i Azure genom att bevilja henne åtkomst till SAP Cloud Platform.

1. I Azure-portalen så väljer du **Företagsprogram**, **Alla program** och därefter **SAP Cloud Platform**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan, skriver och väljer du **SAP Cloud Platform**.

    ![SAP Cloud Platform-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-sap-cloud-platform-test-user"></a>Skapa SAP Cloud Platform-testanvändare

För att låta Azure AD-användare logga in till SAP Cloud Platform så måste du tilldela roller i SAP Cloud Platform till dem.

**Om du vill tilldela en roll till en användare så utför du följande steg:**

1. Logga in på din **SAP Cloud Platform**-cockpit.

2. Utför följande:
   
    ![Auktoriseringar](./media/sap-hana-cloud-platform-tutorial/ic790805.png "Auktoriseringar")
   
    a. Klicka på **Auktorisering**.

    b. Klicka på fliken **användare** .

    c. I textrutan **Användare** så skriver du in användarens e-postadress.

    d. Klicka på **Tilldela** för att tilldela användaren till en roll.

    e. Klicka på **Spara**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på SAP Cloud Platform-panelen i åtkomstpanelen så bör du automatiskt loggas in på den SAP Cloud Platform som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

