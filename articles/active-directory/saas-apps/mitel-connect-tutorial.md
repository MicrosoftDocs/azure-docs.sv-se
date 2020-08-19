---
title: 'Självstudie: Azure Active Directory integrering med spets anslutning | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och avgränsnings anslutning.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/31/2020
ms.author: jeedes
ms.openlocfilehash: 63f1d1d40d8aff21641f3fa4ee10a289de40800d
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552619"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect-or-cloudlink-platform"></a>Självstudie: Azure Active Directory integrering med MiCloud Connect eller CloudLink Platform

I den här självstudien får du lära dig hur du använder den särskilda Connect-appen för att integrera Azure Active Directory (Azure AD) med MiCloud Connect eller CloudLink-plattformen. Den spets Connect-appen är tillgänglig i Azure-galleriet. Genom att integrera Azure AD med MiCloud Connect-eller CloudLink-plattformen får du följande fördelar:

* Du kan styra användarnas åtkomst till MiCloud Connect-appar och till CloudLink-appar i Azure AD med sina företags uppgifter.
* Du kan låta användare på ditt konto loggas in automatiskt till MiCloud Connect eller CloudLink (enkel inloggning) med hjälp av deras Azure AD-konton.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar integrera Azure AD med MiCloud Connect eller CloudLink-plattformen.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med MiCloud Connect behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Ett MiCloud-konto för anslutning eller CloudLink, beroende på vilket program som du vill konfigurera.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien får du konfigurera och testa enkel inloggning (SSO) för Azure AD.

* Spetsig anslutning stöder **SP** -INITIERAd SSO
* När du har konfigurerat tusentals anslutning kan du framtvinga kontroll av sessionen, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-mitel-connect-from-the-gallery"></a>Lägg till spetsig anslutning från galleriet

Om du vill konfigurera integrering av tusentals anslutning till Azure AD måste du lägga till ett spets koppling från galleriet till listan över hanterade SaaS-appar i Azure Portal.

1. Välj **Azure Active Directory**på den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Välj **Nytt program**.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **avgränsning** i Sök fältet, Välj säga **Anslut** från resultat panelen och välj sedan **Lägg till**.

     ![Spets koppling i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD SSO med MiCloud Connect eller CloudLink Platform baserat på en test användare med namnet **_Britta Simon_**. För att enkel inloggning ska fungera måste en länk upprättas mellan användaren i Azure AD-portalen och motsvarande användare på spetsig plattform. I följande avsnitt hittar du information om hur du konfigurerar och testar Azure AD SSO med MiCloud Connect eller CloudLink-plattformen.
* Konfigurera och testa Azure AD SSO med MiCloud Connect
* Konfigurera och testa Azure AD SSO med CloudLink-plattformen

## <a name="configure-and-test-azure-ad-sso-with-micloud-connect"></a>Konfigurera och testa Azure AD SSO med MiCloud Connect

Så här konfigurerar och testar du enkel inloggning med MiCloud Connect för Azure AD:

1. **[Konfigurera MiCloud Connect för enkel inloggning med Azure AD](#configure-micloud-connect-for-sso-with-azure-ad)** – så att användarna kan använda den här funktionen och konfigurera SSO-inställningar på program sidan.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
4. **[Skapa en MiCloud Connect-test](#create-a-mitel-micloud-connect-test-user)** för att få en motsvarighet till Britta Simon på ditt MiCloud Connect-konto som är länkat till användarens Azure AD-representation.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

## <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Konfigurera MiCloud Connect för enkel inloggning med Azure AD

I det här avsnittet ska du aktivera enkel inloggning med Azure AD för MiCloud Connect i Azure Portal och konfigurera ditt MiCloud Connect-konto för att tillåta enkel inloggning med Azure AD.

Om du vill konfigurera MiCloud ansluta med SSO för Azure AD är det enklast att öppna Azure Portal och säga konto portalen sida vid sida. Du måste kopiera lite information från Azure Portal till den säga konto portalen och vissa från den säga konto portalen till Azure Portal.


1. Öppna sidan konfiguration i [Azure Portal](https://portal.azure.com/):

    1. På sidan för att **ansluta** program integrering väljer du **enkel inloggning**.

       ![Konfigurera länk för enkel inloggning](common/select-sso.png)

    1. I dialog rutan **Välj metod för enkel inloggning** väljer du **SAML**.
    
       ![Välja läge för enkel inloggning](common/select-saml-option.png)
    
       Sidan SAML-baserad inloggning visas.

2. Öppna dialog rutan konfiguration på den säga konto portalen:

    1. På **telefon system** -menyn väljer du **tilläggs funktioner**.

    1. Till höger om **enkel inloggning**väljer du **Aktivera** eller **Inställningar**.
    
    Dialog rutan Anslut inställningar för enkel inloggning visas.
    
3. Markera kryss rutan **aktivera enkel inloggning** .
    
    ![Skärm bild som visar sidan för att ansluta enkla inloggnings inställningar med kryss rutan Aktivera enkel inloggning markerad.](./media/mitel-connect-tutorial/mitel-connect-enable.png)

4. I Azure Portal väljer du ikonen **Redigera** i avsnittet **grundläggande SAML-konfiguration** .
   
    ![image](common/edit-urls.png)

    Dialog rutan grundläggande SAML-konfiguration visas.

5.  Kopiera URL: en från området **avgränsnings identifierare (enhets-ID)** i den säga konto portalen och klistra in den i fältet **identifierare (enhets-id)** i Azure Portal.

6. Kopiera URL: en från fältet **svars-URL (intyg om mottagar tjänst)** i den säga konto portalen och klistra in den i fältet **svars-URL (intyg om mottagar tjänst-url)** i Azure Portal.

   ![image](./media/mitel-connect-tutorial/mitel-azure-basic-configuration.png)

7. I text rutan **inloggnings-URL** skriver du en av följande URL: er:

    1. **https://portal.shoretelsky.com** – Om du vill använda en spetsig konto portal som standard-avgränsnings program
    1. **https://teamwork.shoretel.com** – Om du vill använda lag lag som ditt standard avgränsnings program

    > [!NOTE]
    > Standard avgränsnings programmet är det program som nås när en användare väljer panel för att ansluta på åtkomst panelen. Detta är också det program som nås när du gör en test installation från Azure AD.

8. Välj **Spara** i dialog rutan **grundläggande SAML-konfiguration** i Azure Portal.

9. I avsnittet **SAML-signeringscertifikat** på sidan **SAML-baserad inloggning** i Azure Portal väljer du **Hämta** bredvid **certifikat (base64)** för att ladda ned **signerings certifikatet** och spara det på din dator.

    ![image](./media/mitel-connect-tutorial/azure-signing-certificate.png)

10. Öppna signerings certifikat filen i en text redigerare, kopiera alla data i filen och klistra sedan in data i fältet **signerings certifikat** på konto portalen. 

      ![image](./media/mitel-connect-tutorial/mitel-connect-signing-certificate.png)

11. I avsnittet **installations avgränsning** på sidan för **SAML-baserad inloggning** i Azure Portal:

     1. Kopiera URL: en från fältet **inloggnings-URL** och klistra in den i fältet **inloggnings-URL** i den högra konto portalen.

     1. Kopiera URL: en från fältet för **Azure AD-identifieraren** och klistra in den i fältet **entitets-ID** på mål portalen.
         
         ![image](./media/mitel-connect-tutorial/mitel-azure-set-up-connect.png)

12. Välj **Spara** i dialog rutan **Anslut inställningar för enkel inloggning** på den högra konto portalen.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

I det här avsnittet ska du skapa en test användare med namnet Britta Simon i Azure Portal.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. I dialog rutan användar egenskaper utför du följande steg:

    ![Dialogrutan Användare](common/user-properties.png)

    1. I fältet **namn** skriver du **BrittaSimon**.
  
    1. I fältet **användar namn** skriver du brittasimon@ \<yourcompanydomain\> . \<extension\> .  Till exempel BrittaSimon@contoso.com.

    1. Markera kryss rutan **Visa lösen ord** och skriv sedan ned värdet som visas i rutan **lösen ord** .

    1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till spets anslutning.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **spetsig anslutning**.

    ![Länken för att ansluta i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialog rutan **användare och grupper** väljer du **Britta Simon** i listan **användare** och väljer sedan **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och väljer sedan **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Skapa en spetsig MiCloud Connect test User

I det här avsnittet skapar du en användare med namnet Britta Simon på ditt MiCloud Connect-konto. Användare måste skapas och aktive ras innan enkel inloggning används.

Mer information om hur du lägger till användare på den säga konto portalen finns i artikeln [lägga till en användare](https://oneview.mitel.com/s/article/Adding-a-User-092815) i den spetsa kunskaps basen.

Skapa en användare på ditt MiCloud Connect-konto med följande information:

* **Namn:** Britta Simon
* **Företagets e-post adress:**`brittasimon@<yourcompanydomain>.<extension>`   
  (Exempel: [brittasimon@contoso.com](mailto:brittasimon@contoso.com) )
* **Användar namn:**`brittasimon@<yourcompanydomain>.<extension>`  
  (Exempel: [brittasimon@contoso.com](mailto:brittasimon@contoso.com) ; användarens användar namn är vanligt vis detsamma som användarens e-postadress till företaget)

> [!NOTE]
> Användarens MiCloud Connect-användarnamn måste vara identiskt med användarens e-postadress i Azure.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska du testa konfigurationen för enkel inloggning med Azure AD via åtkomst panelen.

När du väljer panelen för att ansluta på åtkomst panelen bör du omdirigeras automatiskt för att logga in på MiCloud Connect-programmet som du konfigurerade som standard i fältet **inloggnings-URL** . Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="configure-and-test-azure-ad-sso-with-cloudlink-platform"></a>Konfigurera och testa Azure AD SSO med CloudLink-plattformen

I det här avsnittet beskrivs hur du aktiverar Azure AD SSO för CloudLink-plattformen i Azure Portal och hur du konfigurerar ditt CloudLink-plattforms konto för att tillåta enkel inloggning med Azure AD.

Om du vill konfigurera CloudLink-plattformen med enkel inloggning för Azure AD rekommenderar vi att du öppnar Azure Portal-och CloudLink-konto portalen sida vid sida. då måste du kopiera viss information från Azure Portal till CloudLink-konto portalen och vice versa.

1. Öppna sidan konfiguration i [Azure Portal](https://portal.azure.com/):

    1. På sidan för att **ansluta** program integrering väljer du **enkel inloggning**.

       ![Konfigurera länk för enkel inloggning](common/select-sso.png)

    1. I dialog rutan **Välj metod för enkel inloggning** väljer du **SAML**.

       ![Välja läge för enkel inloggning](common/select-saml-option.png)
    
       Den **SAML-baserade inloggnings** sidan öppnas och visar avsnittet **grundläggande SAML-konfiguration** .

       ![image](./media/mitel-connect-tutorial/mitel-azure-saml-settings.png)

2. För att få åtkomst till konfigurations panelen för **enkel inloggning i Azure AD** i CloudLink-konto portalen:

    1. Gå till sidan **konto information** för det kund konto som du vill aktivera integreringen med.

    1. I avsnittet **integreringar** väljer du **+ Lägg till ny**. En popup-skärm visar panelen **integrationer** .

    1. Välj fliken **tredje part** . En lista över program från tredje part som stöds visas. Välj knappen **Lägg till** som är kopplad till **enkel inloggning med Azure AD**och välj **slutförd**.

       ![image](./media/mitel-connect-tutorial/mitel-cloudlink-integrations.png)

       **Enkel inloggning för Azure AD** har Aktiver ATS för kund kontot och läggs till i avsnittet **integreringar** på sidan **konto information** .   

   1. Välj **Slutför installationen**.
    
      ![image](./media/mitel-connect-tutorial/mitel-cloudlink-complete-setup.png)
      
      Konfigurations panelen för **enkel inloggning i Azure AD** öppnas.
      
       ![image](./media/mitel-connect-tutorial/mitel-cloudlink-sso-setup.png)
       
       Man rekommenderar att kryss rutan **Aktivera avgränsnings information (valfritt)** i det valfria avsnittet om **autentiseringsuppgifter** inte är markerad. Markera bara den här kryss rutan om du vill att användaren ska logga in på CloudLink-programmet med hjälp av de specifika autentiseringsuppgifterna förutom alternativet för enkel inloggning.

3. På sidan Azure Portal, på sidan **SAML-baserad inloggning** , väljer du ikonen **Redigera** i avsnittet **grundläggande SAML-konfiguration** . Den **grundläggande konfigurations** panelen för SAML öppnas.

    ![image](./media/mitel-connect-tutorial/mitel-azure-saml-basic.png)
 
 4. Kopiera URL: en från **avgränsnings identifieraren (enhets-ID)** i CloudLink-konto portalen och klistra in den i fältet **identifierare (enhets-id)** i Azure Portal.

 5. Kopiera URL: en från fältet **svars-URL (intyg om mottagar tjänst)** i CloudLink-konto portalen och klistra in den i fältet **svars-URL (intyg om mottagar tjänst-url)** i Azure Portal.  
    
    ![image](./media/mitel-connect-tutorial/mitel-cloudlink-saml-mapping.png) 

 6. I text rutan **inloggnings-URL** anger du URL: en `https://accounts.mitel.io` för att använda CloudLink-konto portalen som standard.
     
     ![image](./media/mitel-connect-tutorial/mitel-cloudlink-sign-on-url.png)
  
     > [!NOTE]
     > Standard avgränsnings programmet är det program som öppnas när en användare väljer panel för att ansluta på åtkomst panelen. Detta är också det program som nås när användaren konfigurerar en test installation från Azure AD.

7. Välj **Spara** i dialog rutan **grundläggande SAML-konfiguration** .

8. I avsnittet **SAML-signeringscertifikat** på sidan **SAML-baserad inloggning** i Azure Portal väljer du **Hämta** bredvid **certifikat (base64)** för att ladda ned **signerings certifikatet**. Spara certifikatet på datorn.
  
    ![image](./media/mitel-connect-tutorial/mitel-cloudlink-save-certificate.png)

9. Öppna signerings certifikat filen i en text redigerare, kopiera alla data i filen och klistra in dem i fältet **signerings certifikat** i CloudLink-konto portalen.  

    > [!NOTE]
    > Om du har fler än ett certifikat rekommenderar vi att du klistrar in dem efter det andra. 
       
    ![image](./media/mitel-connect-tutorial/mitel-cloudlink-enter-certificate.png)

10. I avsnittet **Konfigurera avgränsnings anslutning** på sidan för **SAML-baserad inloggning** i Azure Portal:

     1. Kopiera URL: en från fältet **inloggnings-URL** och klistra in den i fältet **inloggnings-URL** i CloudLink-konto portalen.

     1. Kopiera URL: en från fältet för **Azure AD-identifieraren** och klistra in den i fältet **IDP-ID (enhets-ID)** i CloudLink-konto portalen.
     
        ![image](./media/mitel-connect-tutorial/mitel-cloudlink-copy-settings.png)

11. Välj **Spara** på panelen för **enkel inloggning i Azure AD på** CloudLink-konto portalen.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

I det här avsnittet ska du skapa en test användare med namnet Britta Simon i Azure Portal.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. I dialog rutan användar egenskaper utför du följande steg:

    ![Dialogrutan Användare](common/user-properties.png)

    1. I fältet **namn** skriver du **BrittaSimon**.
  
    1. I fältet **användar namn** skriver du brittasimon@ \<yourcompanydomain\> . \<extension\> .  Till exempel BrittaSimon@contoso.com.

    1. Markera kryss rutan **Visa lösen ord** och skriv sedan ned värdet som visas i rutan **lösen ord** .

    1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till spets anslutning.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **spetsig anslutning**.

    ![Länken för att ansluta i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialog rutan **användare och grupper** väljer du **Britta Simon** i listan **användare** och väljer sedan **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och väljer sedan **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-a-cloudlink-test-user"></a>Skapa en CloudLink-test användare

I det här avsnittet beskrivs hur du skapar en test användare med namnet **_Britta Simon_** på din CloudLink-plattform. Användare måste skapas och aktive ras innan de kan använda enkel inloggning.

Mer information om hur du lägger till användare i CloudLink Accounts-portalen finns i **_Hantera användare_** i [CloudLink Accounts-dokumentationen](https://www.mitel.com/document-center/technology/cloudlink/all-releases/en/cloudlink-accounts-html).

Skapa en användare på CloudLink-konto portalen med följande information:

* Namn: Britta Simon
* Förnamn: Britta
* Efter namn: Simon
* E-post: BrittaSimon@contoso.com

> [!NOTE]
> Användarens e-postadress för CloudLink måste vara identisk med **användarens huvud namn** i Azure Portal.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska du testa din Azure AD SSO-konfiguration med hjälp av åtkomst panelen.

När du väljer panelen för att ansluta på åtkomst panelen omdirigeras du automatiskt för att logga in till det CloudLink-program som du konfigurerade som standard i fältet **inloggnings-URL** . Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)